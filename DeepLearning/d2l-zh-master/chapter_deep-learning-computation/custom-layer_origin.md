# Custom Layers
One factor behind deep learning's success
is the availability of a wide range of layers
that can be composed in creative ways
to design architectures suitable
for a wide variety of tasks.
For instance, researchers have invented layers
specifically for handling images, text,
looping over sequential data,
and
performing dynamic programming.
Sooner or later, you will encounter or invent
a layer that does not exist yet in the deep learning framework.
In these cases, you must build a custom layer.
In this section, we show you how.
## Layers without Parameters
To start, we construct a custom layer
that does not have any parameters of its own.
This should look familiar if you recall our
introduction to block in :numref:`sec_model_construction`.
The following `CenteredLayer` class simply
subtracts the mean from its input.
To build it, we simply need to inherit
from the base layer class and implement the forward propagation function.
```{.python .input}
#@tab pytorch
import torch
from torch import nn
class CenteredLayer(nn.Module):
    def __init__(self):
        super().__init__()
    def forward(self, X):
        return X - X.mean()
```
Let us verify that our layer works as intended by feeding some data through it.
```{.python .input}
#@tab pytorch
layer = CenteredLayer()
layer(torch.FloatTensor([1, 2, 3, 4, 5]))
```
We can now incorporate our layer as a component
in constructing more complex models.
```{.python .input}
#@tab pytorch
net = nn.Sequential(nn.Linear(8, 128), CenteredLayer())
```
As an extra sanity check, we can send random data
through the network and check that the mean is in fact 0.
Because we are dealing with floating point numbers,
we may still see a very small nonzero number
due to quantization.
```{.python .input}
#@tab pytorch
Y = net(torch.rand(4, 8))
Y.mean()
```
## Layers with Parameters
Now that we know how to define simple layers,
let us move on to defining layers with parameters
that can be adjusted through training.
We can use built-in functions to create parameters, which
provide some basic housekeeping functionality.
In particular, they govern access, initialization,
sharing, saving, and loading model parameters.
This way, among other benefits, we will not need to write
custom serialization routines for every custom layer.
Now let us implement our own version of the  fully-connected layer.
Recall that this layer requires two parameters,
one to represent the weight and the other for the bias.
In this implementation, we bake in the ReLU activation as a default.
This layer requires to input arguments: `in_units` and `units`, which
denote the number of inputs and outputs, respectively.
```{.python .input}
#@tab pytorch
class MyLinear(nn.Module):
    def __init__(self, in_units, units):
        super().__init__()
        self.weight = nn.Parameter(torch.randn(in_units, units))
        self.bias = nn.Parameter(torch.randn(units,))
    def forward(self, X):
        return torch.matmul(X, self.weight.data) + self.bias.data
```
Next, we instantiate the `MyDense` class
and access its model parameters.
```{.python .input}
#@tab pytorch
dense = MyLinear(5, 3)
dense.weight
```
We can directly carry out forward propagation calculations using custom layers.
```{.python .input}
#@tab pytorch
dense(torch.randn(2, 5))
```
We can also construct models using custom layers.
Once we have that we can use it just like the built-in fully-connected layer.
```{.python .input}
#@tab pytorch
net = nn.Sequential(MyLinear(64, 8), nn.ReLU(), MyLinear(8, 1))
net(torch.randn(2, 64))
```
## Summary
* We can design custom layers via the basic layer class. This allows us to define flexible new layers that behave differently from any existing layers in the library.
* Once defined, custom layers can be invoked in arbitrary contexts and architectures.
* Layers can have local parameters, which can be created through built-in functions.
## Exercises
1. Design a layer that takes an input and computes a tensor reduction,
   i.e., it returns $y_k = \sum_{i, j} W_{ijk} x_i x_j$.
1. Design a layer that returns the leading half of the Fourier coefficients of the data.
:begin_tab:`mxnet`
[Discussions](https://discuss.d2l.ai/t/58)
:end_tab:
:begin_tab:`pytorch`
[Discussions](https://discuss.d2l.ai/t/59)
:end_tab:
:begin_tab:`tensorflow`
[Discussions](https://discuss.d2l.ai/t/279)
:end_tab: