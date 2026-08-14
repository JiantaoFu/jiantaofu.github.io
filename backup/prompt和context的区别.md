原文： https://claude.com/blog/the-new-rules-of-context-engineering-for-claude-5-generation-models

prompt只是context的一部分。context的构成包括：

- 系统prompt: 一些上下文信息，要来做什么

- CLAUDE.md： 保持轻量级，描述repo用来做什么的。不要描述一些模型查看repo文件就知道的信息。可以引用一些其他的文件，这样模型可以按需去加载。

- skills：轻量级的向导（主张，知识，最佳实践），让Claude能找到需要的信息。对于复杂的skill，拆分成多个文件。

- 引用：使用@来引用其他源

和prompt不同的是，context会跨越多个请求，所以context不是特定的指令，而是一些通用的prompt，这里难点在于你不知道用户的prompt是啥。如果模型强大，对prompt的依赖会更少。可以用`claude doctor`命令来查看最佳实践。

# use judgement

模型没那么聪明的时候，就需要给特别明确的指令（怎么做），比如

```
In code: default to writing no comments. Never write multi-paragraph docstrings or multi-line comment blocks — one short line max. Don't create planning, decision, or analysis documents unless the user asks for them — work from conversation context, not intermediate files.
```

现在不需要了，只需要给出准则即可（评价标准）:

```
Write code that reads like the surrounding code: match its comment density, naming, and idiom.
```

# Design interfaces

对于工具调用，过去要给一堆的例子，对新模型而言，反而会限制其探索工具的使用方式。现在只需要写清楚参数，告诉模型使用工具的基本原则即可。

# Use progressive disclosure

这个大致的意思就是不要一下把一堆信息都给到模型，只在需要的时候给到它，claude code的agent能按需去查询获取更多需要的信息。

# Simple tool descriptions

之前的工具信息要在系统prompt里，也要在工具描述里，模型才能很稳定的遵循指令。新模型不需要在系统prompt里面重复了。

# Auto-memory

之前需要用户手动的去触发模型记录一些重要的信息在claude.md中，现在模型会自己判断并记录。

# Rich references

plan模式依赖markdown文件，另外有时候也需要在代码仓库里存spec文件，对于那些需要耗时很久的任务，需要这个来参考。但实际上claude可以支持其他格式的文件，包括html，代码，甚至评估准则。