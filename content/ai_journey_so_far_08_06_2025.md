# My AI Journey So Far

I've been using AI for frontend development (React/React Native) for a while now. Thought I'd document how this has evolved - from early skeptic days to reliable everyday tool.

## Early Days with ChatGPT and Copilot
*Around 1.5-2 years ago*

Back then, AI could understand what I wanted but couldn't really solve complex problems. For rare or genuinely new issues, I'd skip asking AI entirely.

**What worked:**
- Quick CSS queries
- Basic JavaScript refactoring  
- Simple helper functions

These were the easy wins you could outsource. It was still a optional tool, you can still live without it.

**GitHub Copilot** had IDE integration but the autocomplete was meh. Chat was not that great, felt like having ChatGPT in my editor, which I was already using in browser anyway. Ended up turning it off.

**Claude** started showing up around this time too. Decent for coding tasks.

## The Game Changer - Deepseek R1 thinking

This is where things got interesting.

**Deepseek R1** disrupted the industry. Even now, its "thinking" process sometimes beats other leading models. 

Real example: I was building an iOS app as a side project. Had a medium-difficulty problem that should've been solvable. ChatGPT and Claude both gave garbage results. Deepseek nailed it perfectly. Huge confidence boost since iOS development is trickier - less documentation compared to React world.

- AI models started adding "thinking" models
- Claude 3.7 Sonnet thinking become my default choice
- At [Udaan](https://udaan.com/) (where I work), serious AI adoption discussions began. We got GitHub Copilot Business with premium models
- My interaction became like supercharged Google Search: query → answer → understand → implement → iterate

**Discovering Windsurf**

Frontend peers started using Windsurf. Company allowed monthly tool changes, so I got premium access for windsurf instead of copilot.

This was the real upgrade:

**Intelligent autocomplete** - Often felt like it read my mind. Just hit Tab.

**Context-aware assistance** - LLM directly in editor. Asking questions about specific code or reimplementing logic based on other files became trivial.

**Precise instructions** - I could give it rules, making suggestions highly relevant instead of generic.

**Agentic behavior** - Key breakthrough. When the LLM made mistakes, I'd create rules (like teaching a junior dev). More it "broke" and we "taught" it, more reliable it became. It will give you answer and apply it to your codebase, there is no need for copy paste. And if there is eslint/lsp errors it will fix those (need for importing anything or something in that direction), it would iterate untill it is ready for executable format. This removes a lot of manual work, there is no going back to stone age browser copy paste days.

**Figma to code** - Started feeding it component screenshots with instructions to use existing components or add placeholders. Actually worked! Generated initial layouts for tedious tasks, then I'd manually refine.

**Automating boring workflows** - You can outsource boring worflows to agents. An example from my codebase: adding new screen requires: create file from template, update navigation, import screen. So I created a workflow, these are just plain text instruction, low effort to create. Now I type `/create-screen screenName AccountDetails` and it's done.

## Daily work impact

The daily work impact is significant:

**Reduced decision fatigue** - Normal workday has many small and big decisions. Previously left me tired by evening. Automating boring stuff offloaded these decisions to LLM for a extend.I will give a 
analogy,  If you start with 100 energy units and mundane tasks consume 20, automating those means you reclaim that energy. Use that "extra 20" for complex problem-solving. Get more done.

Really excited to see where AI development goes next.