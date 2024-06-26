# 大语言模型（LLM）学习路径和资料汇总

> Author: **ninehills**  
> Labels: **blog**  
> Created: **2023-06-27T13:42:33Z**  
> Link and comments: <https://github.com/ninehills/blog/issues/97>  


## 0x00 学习路径

本文分为四个章节，各章节的学习目标如下。请注意本文主要是面向工程界撰写，学术部分较少。

- 入门篇：
  - 了解大语言模型的基础知识和常见术语。
  - 学会使用编程语言访问 OpenAI API 等常见大语言模型接口。
- 提高篇：
  - 了解机器学习、神经网络、NLP 的基础知识。
  - 了解 Transformer 以及典型 Decoder-only 语言模型的基础结构和简单原理。
  - 了解大语言模型发展历史，以及业界主流模型（含开源模型）进展。
- 应用篇：
  - 可以在本地环境搭建开源模型的推理环境。
  - Prompt 工程。
  - 使用已有框架（如Langchain）或自行开发，结合大语言模型结果，开发生产应用。
- 深入篇：（本文涉及少量资料）
  - 掌握 Continue Pre-train、Fine-tuning 已有开源模型的能力。
  - 掌握 Lora、QLora 等低资源高效模型训练的能力。
  - 掌握大语言模型微调以及预训练数据准备的能力。
  - 深入了解大模型背后的技术原理。
  - 了解生产环境部署大模型的相关技术点。

读者可以根据自己需要选择对应的章节，如对大语言模型的原理不感兴趣，可只关注入门篇和应用篇。
考虑到阅读背景，本文尽可能提供中文资料或有中文翻译的资料。

## 0x10 入门篇

> 在入门之前，请申请 OpenAI API，并具备良好的国际互联网访问条件。

- [大语言模型综述](https://github.com/LLMBook-zh/LLMBook-zh.github.io)
  - 大语言模型迄今为止最好的学术向中文综述和书籍
  - 作为入门资料偏难，**看不懂的部分可以等到后面章节再回头重看**。
- [ChatGPT Prompt Engineering for Developers](https://learn.deeplearning.ai/chatgpt-prompt-eng/lesson/1/introduction)
  - 虽然是 Prompt 工程，但是内容比较简单，适合入门者。
  - 中英双语字幕：https://github.com/GitHubDaily/ChatGPT-Prompt-Engineering-for-Developers-in-Chinese
- [OpenAI Quickstart](https://platform.openai.com/docs/quickstart)
  -  OpenAI 官方 Quickstart 文档。
  - 以及 [API Reference](https://platform.openai.com/docs/api-reference)
- State of GPT：GPT 联合创始人做的演示，极好的总结了 GPT 的训练和应用。
  - 视频：https://www.youtube.com/watch?v=bZQun8Y4L2A
  - PPT：https://karpathy.ai/stateofgpt.pdf

## 0x20 提高篇

- [3brown1blue 系列视频](https://www.youtube.com/watch?v=wjZofJX0v4M)：动画做的很好
- [清华大模型公开课](https://www.bilibili.com/video/BV1UG411p7zv)：从NLP到大模型的综合课程，挑选感兴趣的了解。
- [深度学习：台湾大学李宏毅](https://www.bilibili.com/video/BV1J94y1f7u5/)：台湾大学李宏毅，国语教程里最好的，讲的很清楚，也比较有趣。
- [Understanding large language models](https://www.wandb.courses/courses/take/building-llm-powered-apps/lessons/44341580-understanding-large-language-models) ：理解大语言模型。
- [The Illustrated GPT-2 (Visualizing Transformer Language Models)](https://jalammar.github.io/illustrated-gpt2/)：图解 GPT2
  - 中文翻译：https://zhuanlan.zhihu.com/p/139840113
- [InstructGPT: Training language models to follow instructions with human feedback](https://cdn.openai.com/papers/Training_language_models_to_follow_instructions_with_human_feedback.pdf)：著名的 InstructGPT 论文。
  - 另外一篇中文介绍：https://huggingface.co/blog/zh/rlhf
- [Huggingface NLP Course](https://huggingface.co/learn/nlp-course/chapter1/1)：NLP 入门课程

## 0x30 应用篇

- [Building Systems with the ChatGPT API](https://learn.deeplearning.ai/chatgpt-building-system/lesson/1/introduction)
  -  中文字幕：https://www.bilibili.com/video/BV1gj411X72B/
- [Langchain](https://python.langchain.com/)
  - Langchain 是大语言模型最火的应用框架。即使不使用，也可以借鉴。
  - [LangChain for LLM Application Development](https://learn.deeplearning.ai/langchain/lesson/1/introduction)
    - 中文字幕：https://www.bilibili.com/video/BV1Ku411x78m/ 
- [GPT best practices](https://platform.openai.com/docs/guides/gpt-best-practices/gpt-best-practices)：OpenAI 官方出的最佳实践。
- [openai-cookbook](https://github.com/openai/openai-cookbook)：OpenAI 官方 Cookbook。
- [Brex's Prompt Engineering Guide](https://github.com/brexhq/prompt-engineering)：Prompt 工程简介

## 0x40 深入篇

- [Huggingface Transformer 文档](https://huggingface.co/docs/transformers/index)：Transformer 官方文档
- [复杂推理：大语言模型的北极星能力](https://yaofu.notion.site/6dafe3f8d11445ca9dcf8a2ca1c5b199) ：略学术，解释大语言模型能力的来源。
- [GPT，GPT-2，GPT-3 论文精读](https://www.bilibili.com/video/BV1AF411b7xQ)：视频精读。
- [Building LLM applications for production](https://huyenchip.com/2023/04/11/llm-engineering.html)：在生产环境中构建 LLM 应用。