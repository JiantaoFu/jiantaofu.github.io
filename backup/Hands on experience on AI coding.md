I started using ChatGTP on writing some test code since last year, and recently I've heard people keep mentioning cursor, and used it to [make a app](https://www.reddit.com/r/cursor/comments/1h35uh6/nocode_developers_success_story_built_appstores_1/) in one hour and climbed to top 20 paid app in apple store, and he claim had no coding experience, maybe some just marketing, but still very impressive.

I started to explorer the AI coding tools, like blot.new, windsurf, cursor, and finally became a windsurf paid user (tried for one month), and end up with building two tools, one Android app and one Web app. 

The Android app idea comes up TikTok Refugees' requirement, they switch to RedNote but there was language barriers there b/c most users are Chinese, so I came up with an idea of [screen translation](https://play.google.com/store/apps/details?id=com.lomoware.screen_translate&pli=1), I later thought I probably should do some research first before I did this, but since this starts with experimenting on AI coding tools, so I just launch and to see whether I can finish that within one week, the prototype is ready in 2 days, and I have no experience on flutter, I choose flutter on purpose b/c I'd like to see whether it's doable with zero experience, but I do have limited experience on Android/iOS development, but not expert. As for the prototype it just can capture the screen and do the translation, show overlay text, simple. However, it's far away from a product, there are a lot of improvements need to done to make it usable:

1.  support multiple languages
2. detect scrolling so that it can translate the new content
3. support rotation
4. background color auto detection so that we can pick the overlay color
5. different modes (auto translation/manual translation/original text)

Another tool I built with AI is https://insightly.top, an tool to "Uncover Deep Insights from App Reviews", again, the tech stack is not something I familiar with, but I do have some basic knowledge on the frontend html+css+javascript. And similar time frame, around 1.5 week to go to production.

I'm 100% that people with zero coding experience will not able to do this, some of those bugs are really tricky and I don't think AI can fix, and in the beginning when I use this tool, I thought it as reliable FSD, but it turns out to be a level2 auto pilot. I need to understand the code structure, the logic of how it works, and jump directly into some bug fixes, do the code review, but it DOES help improve the efficiency, and I don't think I can do the same thing within the same time frame. Some people mention AI coding are senior SDE now, I don't agree, it's not some equivalent, it's still a tool, without soul or creativity, still need human by its side to guide it, even though it can write code faster, and may have more knowledge than us. So basically those AI coding tools are just LLM + workflow integration, you just pay some convenient fees by saving the context switch effort.

There are some prompts that helps to use the coding tools:

- https://github.com/PatrickJS/awesome-cursorrules
- https://cursor.directory/
- https://cursorlist.com/

They are helpful, but not enough, what I found that is sometimes we need play with multiple roles when working the project, you need to work on architect, frontend, backend, test, devops, and need to switch between different roles, and those prompts are just assuming you work on project with specific tech stack and that is not the future, in the future, AI will play different roles, either we have different agents with  different roles to work together, or a full stack 10x AI agent can handle everything. The missing part is how to adapt the whole engineering process to AI era. 

> Software engineering is the process of designing, building, testing, and maintaining software applications. It's a branch of computer science that uses engineering principles and programming languages to create software solutions. 
What do software engineers do? 

> -   Apply engineering principles to design software solutions
> -   Use programming languages to build software
> -   Understand algorithms, databases, and system design
> -   Solve problems
> -   Work in teams

Anyway, I'm still exploring, and I won't go back to coding IDE without AI support, just like I won't go back to drive a car without [openpliot](https://github.com/commaai/openpilot), even though it's not perfect, it does helps, and it's the future.