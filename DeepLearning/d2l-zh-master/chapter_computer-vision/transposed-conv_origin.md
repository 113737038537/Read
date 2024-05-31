# Transposed Convolution
:label:`sec_transposed_conv`
The CNN layers we have seen so far,
such as convolutional layers (:numref:`sec_conv_layer`) and pooling layers (:numref:`sec_pooling`),
typically reduce (downsample) the spatial dimensions (height and width) of the input,
or keep them unchanged.
In semantic segmentation
that classifies at pixel-level,
it will be convenient if
the spatial dimensions of the
input and output are the same.
For example,
the channel dimension at one output pixel 
can hold the classification results
for the input pixel at the same spatial position.
To achieve this, especially after 
the spatial dimensions are reduced by CNN layers,
we can use another type
of CNN layers
that can increase (upsample) the spatial dimensions
of intermediate feature maps.
In this section,
we will introduce 
*transposed convolution*, which is also called *fractionally-strided convolution* :cite:`Dumoulin.Visin.2016`, 
for reversing downsampling operations
by the convolution.
```{.python .input}
#@tab pytorch
import torch
from torch import nn
from d2l import torch as d2l
```
## Basic Operation
Ignoring channels for now,
let us begin with
the basic transposed convolution operation
with stride of 1 and no padding.
Suppose that
we are given a 
$n_h \times n_w$ input tensor
and a $k_h \times k_w$ kernel.
Sliding the kernel window with stride of 1
for $n_w$ times in each row
and $n_h$ times in each column
yields 
a total of $n_h n_w$ intermediate results.
Each intermediate result is
a $(n_h + k_h - 1) \times (n_w + k_w - 1)$
tensor that are initialized as zeros.
To compute each intermediate tensor,
each element in the input tensor
is multiplied by the kernel
so that the resulting $k_h \times k_w$ tensor
replaces a portion in
each intermediate tensor.
Note that
the position of the replaced portion in each
intermediate tensor corresponds to the position of the element
in the input tensor used for the computation.
In the end, all the intermediate results
are summed over to produce the output.
As an example,
:numref:`fig_trans_conv` illustrates
how transposed convolution with a $2\times 2$ kernel is computed for a $2\times 2$ input tensor.
![Transposed convolution with a $2\times 2$ kernel. The shaded portions are a portion of an intermediate tensor as well as the input and kernel tensor elements used for the  computation.](../img/trans_conv.svg)
:label:`fig_trans_conv`
We can (**implement this basic transposed convolution operation**) `trans_conv` for a input matrix `X` and a kernel matrix `K`.
In contrast to the regular convolution (in :numref:`sec_conv_layer`) that *reduces* input elements
via the kernel,
the transposed convolution
*broadcasts* input elements 
via the kernel, thereby
producing an output
that is larger than the input.
We can construct the input tensor `X` and the kernel tensor `K` from :numref:`fig_trans_conv` to [**validate the output of the above implementation**] of the basic two-dimensional transposed convolution operation.
Alternatively,
when the input `X` and kernel `K` are both
four-dimensional tensors,
we can [**use high-level APIs to obtain the same results**].
```{.python .input}
#@tab pytorch
X, K = X.reshape(1, 1, 2, 2), K.reshape(1, 1, 2, 2)
tconv = nn.ConvTranspose2d(1, 1, kernel_size=2, bias=False)
tconv.weight.data = K
tconv(X)
```
## [**Padding, Strides, and Multiple Channels**]
Different from in the regular convolution
where padding is applied to input,
it is applied to output
in the transposed convolution.
For example,
when specifying the padding number
on either side of the height and width 
as 1,
the first and last rows and columns
will be removed from the transposed convolution output.
```{.python .input}
#@tab pytorch
tconv = nn.ConvTranspose2d(1, 1, kernel_size=2, padding=1, bias=False)
tconv.weight.data = K
tconv(X)
```
In the transposed convolution,
strides are specified for intermediate results (thus output), not for input.
Using the same input and kernel tensors
from :numref:`fig_trans_conv`,
changing the stride from 1 to 2
increases both the height and weight
of intermediate tensors, hence the output tensor
in :numref:`fig_trans_conv_stride2`.
![Transposed convolution with a $2\times 2$ kernel with stride of 2. The shaded portions are a portion of an intermediate tensor as well as the input and kernel tensor elements used for the  computation.](../img/trans_conv_stride2.svg)
:label:`fig_trans_conv_stride2`
The following code snippet can validate the transposed convolution output for stride of 2 in :numref:`fig_trans_conv_stride2`.
```{.python .input}
#@tab pytorch
tconv = nn.ConvTranspose2d(1, 1, kernel_size=2, stride=2, bias=False)
tconv.weight.data = K
tconv(X)
```
For multiple input and output channels,
the transposed convolution
works in the same way as the regular convolution.
Suppose that
the input has $c_i$ channels,
and that the transposed convolution
assigns a $k_h\times k_w$ kernel tensor
to each input channel.
When multiple output channels 
are specified,
we will have a $c_i\times k_h\times k_w$ kernel for each output channel.
As in all, if we feed $\mathsf{X}$ into a convolutional layer $f$ to output $\mathsf{Y}=f(\mathsf{X})$ and create a transposed convolutional layer $g$ with the same hyperparameters as $f$ except 
for the number of output channels 
being the number of channels in $\mathsf{X}$,
then $g(Y)$ will have the same shape as $\mathsf{X}$.
This can be illustrated in the following example.
```{.python .input}
#@tab pytorch
X = torch.rand(size=(1, 10, 16, 16))
conv = nn.Conv2d(10, 20, kernel_size=5, padding=2, stride=3)
tconv = nn.ConvTranspose2d(20, 10, kernel_size=5, padding=2, stride=3)
tconv(conv(X)).shape == X.shape
```
## [**Connection to Matrix Transposition**]
:label:`subsec-connection-to-mat-transposition`
The transposed convolution is named after
the matrix transposition.
To explain,
let us first
see how to implement convolutions
using matrix multiplications.
In the example below, we define a $3\times 3$ input `X` and a $2\times 2$ convolution kernel `K`, and then use the `corr2d` function to compute the convolution output `Y`.
Next, we rewrite the convolution kernel `K` as
a sparse weight matrix `W`
containing a lot of zeros. 
The shape of the weight matrix is ($4$, $9$),
where the non-zero elements come from
the convolution kernel `K`.
Concatenate the input `X` row by row to get a vector of length 9. Then the matrix multiplication of `W` and the vectorized `X` gives a vector of length 4.
After reshaping it, we can obtain the same result `Y`
from the original convolution operation above:
we just implemented convolutions using matrix multiplications.
Likewise, we can implement transposed convolutions using
matrix multiplications.
In the following example,
we take the $2 \times 2$ output `Y` from the above
regular convolution
as the input to the transposed convolution.
To implement this operation by multiplying matrices,
we only need to transpose the weight matrix `W`
with the new shape $(9, 4)$.
Consider implementing the convolution
by multiplying matrices.
Given an input vector $\mathbf{x}$
and a weight matrix $\mathbf{W}$,
the forward propagation function of the convolution
can be implemented
by multiplying its input with the weight matrix
and outputting a vector 
$\mathbf{y}=\mathbf{W}\mathbf{x}$.
Since backpropagation
follows the chain rule
and $\nabla_{\mathbf{x}}\mathbf{y}=\mathbf{W}^\top$,
the backpropagation function of the convolution
can be implemented
by multiplying its input with the 
transposed weight matrix $\mathbf{W}^\top$.
Therefore, 
the transposed convolutional layer
can just exchange the forward propagation function
and the backpropagation function of the convolutional layer:
its forward propagation  
and backpropagation functions
multiply their input vector with 
$\mathbf{W}^\top$ and $\mathbf{W}$, respectively.
## Summary
* In contrast to the regular convolution that reduces input elements via the kernel, the transposed convolution broadcasts input elements via the kernel, thereby producing an output that is larger than the input.
* If we feed $\mathsf{X}$ into a convolutional layer $f$ to output $\mathsf{Y}=f(\mathsf{X})$ and create a transposed convolutional layer $g$ with the same hyperparameters as $f$ except for the number of output channels being the number of channels in $\mathsf{X}$, then $g(Y)$ will have the same shape as $\mathsf{X}$.
* We can implement convolutions using matrix multiplications. The transposed convolutional layer can just exchange the forward propagation function and the backpropagation function of the convolutional layer.
## Exercises
1. In :numref:`subsec-connection-to-mat-transposition`, the convolution input `X` and the transposed convolution output `Z` have the same shape. Do they have the same value? Why?
1. Is it efficient to use matrix multiplications to implement convolutions? Why?
:begin_tab:`mxnet`
[Discussions](https://discuss.d2l.ai/t/376)
:end_tab:
:begin_tab:`pytorch`
[Discussions](https://discuss.d2l.ai/t/1450)
:end_tab: