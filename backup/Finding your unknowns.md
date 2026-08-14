原文：https://claude.com/blog/a-field-guide-to-claude-fable-finding-your-unknowns

A field guide to Claude Fable 5: Finding your unknowns

总结：即便是最先进的大模型，也是需要有正确的使用方法，还是需要人来对整体流程进行把控，需要人理解项目相关的知识盲区（也就是风险点），并对这些知识做整理。整个流程，人都是和大模型一起来协作。

使用大模型，需要给足够的上下文信息，有些信息是自己不知道自己不知道的信息(unknown unkowns)，还有些是自己知道自己不知道的信息(known unknowns)。

在刚开始的时候，就是要弄清楚这些盲区，可以让模型来帮助你。

“I'm working on adding a new auth provider but I know nothing about the auth modules in this codebase. Can you do a blind spot pass to help me figure out my relevant unknown unknowns and help me prompt you better.”

“I don’t know what color grading is but I need to grade this video. Can you teach me to understand my unknown unknowns about color grading, so that I can prompt better?”

如果你一开始有很多不清楚的地方，最好就是先和模型一起脑爆，做些原型，而不是一开始就让模型开干，避免后期改动成本太高。

"I want a dashboard for this data but I have no visual taste and don't know what's possible. Make me an HTML page with 4 wildly different design directions so I can react to them.”

“Before wiring anything up, make a single HTML file mocking the new editor toolbar with fake data. I want to react to the layout before you touch the real app."

"Here's my rough problem: users churn after onboarding. Search the codebase and brainstorm 10 places we could intervene, from cheapest to most ambitious. I'll tell you which ones resonate."

为了彻底的消除盲区，可以让模型再来问你问题：

"Interview me one question at a time about anything ambiguous, prioritize questions where my answer would change the architecture."

如果一件事情非常复杂，你描述不清楚，就给模型原始的引用即可。

"This Rust crate in vendor/rate-limiter implements the exact backoff behavior I want. Read it and reimplement the same semantics in our TypeScript API client."

当你对盲区都搞清楚了后，可以让模型出一份具体的Plan给你来review

"Write an implementation plan in HTML, but lead with the decisions I'm most likely to tweak with: data model changes, new type interfaces, and anything user-facing. Bury the mechanical refactoring at the bottom, I trust you on that part."

有了plan之后，就可以开始具体实施了，这时候应该新开一个会话，给模型之前的步骤收集整理后的信息（Plan, Spec）。实现过程中肯定还会碰到一些未知的问题，需要把这些都记录下来，保存为知识库，避免下次重新再来。

"Keep an implementation-notes.md file. If you hit an edge case that forces you to deviate from the plan, pick the conservative option, log it under 'Deviations', and keep going."

实现完成之后，最终要交付，还是需要人来review的，为了方便review，需要准备一些材料，让其他人也能知道你碰到的那些盲区，让他们知道你对这些问题是有考虑到的，方便他们更快的批准。

"Package the prototype, the spec, and the implementation notes into a single doc I can drop in Slack to get buy-in. Lead with the demo GIF."

最终还是需要人来负责，你作为owner，需要的对AI的工作有些了解，可以让模型来给你做些测验，确保你知道模型都干了些什么事情。测验通过后，再合并代码。

“I want to make sure I understand everything that's happened in this change. Give me a HTML report on the changes for me to read and understand with context, intuition, what was done, etc. and a quiz at the bottom on the changes that I must pass.”

