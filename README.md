# jackie

![Jackie — Claude Skill](jackie-skill.png)

**Jackie is a Claude skill — a set of specialized instructions that turns Claude into a sharp, opinionated product,  design and engineering counterpart.**

You load it into Claude once. After that, every conversation runs with the full reasoning framework, four operating modes, and eleven knowledge files active. No generic answers. No hollow validation.

**Not a chatbot. Not a suggestion engine. A peer who will tell you your nav structure is a haunted house and explain exactly why.**

This is a Claude skill for people who build things that real humans have to use. It knows when your onboarding flow is optimistic fiction. It knows the difference between a pattern that tests well and a pattern that survives contact with a stressed user at 11pm on a phone with 8% battery and no patience. It has opinions, and it will share them whether you asked for the sharp version or not.

It will not tell you your design is "seamless." It will not say your interface is "intuitive." It will not describe anything as "delightful" unless it has earned that word. It will not produce five equally valid options when one is clearly stronger — that is not balance, that is abdication. And it will not pretend your modal with seven CTAs represents a considered design decision.

What it will do: think through the problem with you, argue against your assumptions when they deserve to be argued against, build the component if you need the component built, and tell you exactly what you are giving up with every choice you make. Design tools that agree with everything you do are easy to find. This is the other kind.

<!-- This README was reviewed by Jackie. It had fourteen comments. Six were about word choice. Three were about the file tree. One was about this sentence. -->

---

## What the Difference Looks Like

**A weak prompt. Two different answers.**

> *"Should I use tabs or a sidebar for my B2B dashboard navigation?"*

**A normal LLM:**
> "Great question! Both tabs and sidebars have their advantages. Tabs work well for a small number of sections and give users a clear overview. Sidebars are better for complex navigation with many items. Consider your users' needs and the number of navigation items when making your decision. You might also want to test both options with your users!"

**Jackie — Direct Mode:**
> Use a sidebar. B2B dashboards with more than five navigation destinations, complex hierarchies, or role-based section visibility require a navigation pattern that scales — tabs do not. A sidebar gives you vertical space to grow, room for section grouping, and the ability to collapse to icon-only for users who want more screen real estate. Tabs work in dashboards with three to five flat destinations and no need for nesting. If you are building a B2B analytics tool, you are almost certainly not in that situation.

That's the difference. One validates the question. The other answers it.

---

## What You're Actually Getting

Four operating modes, eleven knowledge files, a vocabulary enforcement layer, and a reasoning architecture that holds a position when challenged — packaged as a Claude Project, an API system prompt, or a Claude Code skill you install locally and invoke from the command line.

You wire it in once. Then you have a counterpart who reads your flows, does not perform enthusiasm, and has apparently never heard the phrase "that's a great point."

Think of it as a product team in a box. Covering the ground that would typically require several specialists:

| Role | What Jackie covers |
|---|---|
| UX strategist | Problem framing, trade-off analysis, decision rationale |
| UX researcher | Assumption pressure-testing, method selection, evidence standards |
| UI / visual designer | Component specs, visual systems, all states, dark mode, accessibility |
| UX writer | Button labels, error messages, empty states, confirmation dialogs, voice and tone |
| Accessibility specialist | WCAG AA/AAA, keyboard navigation, focus management, cognitive load |
| Design systems engineer | React/TypeScript components, CVA, Radix UI, token architecture |
| AI interface specialist | RAD framework: 34 components for responsible, accountable, disclosed agentic UI |
| Design critic | The mode that argues against your design before you ship it |

This doesn't replace a team. A team does things Jackie cannot: run live research sessions, build production code, navigate organizational politics, and carry accountability across a quarter. What it replaces is the gap — the moment when you need a second opinion at 9pm, a component spec before the morning standup, or a pressure test before a stakeholder presentation and there is no one available to give you one.

---

## Four Ways It Thinks

**Strategic Mode** (default for analysis)
Pull up a chair. This mode works through the problem — trade-offs, downstream consequences, the thing you haven't noticed yet. Use it when a wrong decision now becomes expensive later.

**Direct Mode**
One direction. Reasons included but compressed. No philosophical detour. Use it when you need to move.

**Provocative Mode**
The agent argues against your design. Your assumptions are the target. Use it before you ship something you feel good about — especially then.

**Build Mode**
Execution mode. Give it a component, a flow, or a screen to design and it will produce the full artifact — UX strategy first, then CSS, specs, copy, all states, dark mode, accessibility. No skipped steps, no random values. Use it when you need something built to a professional standard, not described.

---

## Who Finds It Useful

- Engineers making implementation decisions that have UX consequences — component architecture, state management, error handling — and wanting a counterpart who speaks both languages
- Designers at the part of the project where "it depends" is not a useful answer
- PMs making trade-offs between what users need and what the roadmap allows
- Researchers who want a second reader who won't just validate
- Anyone about to ship something and wanting one last pressure test from something that won't tell them it looks great

---

## What It Knows

| Domain | Depth |
|---|---|
| Interaction patterns and heuristics | Applied, not academic — failure modes included |
| Behavioral psychology | How users actually decide, not how they report deciding |
| Visual design systems | 8pt grid, type scales, 60-30-10 color, component specs, dark mode |
| Cross-industry patterns | Onboarding, auth, e-commerce, dashboards — and what adjacent industries do better |
| UI components | Full specs: buttons, inputs, cards, tables, modals, nav, toasts — all states |
| CSS and animation | Design tokens, GPU-accelerated motion, easing functions, polish techniques |
| Agentic AI interface design | RAD framework: Responsible, Accountable, Disclosed |
| Accessibility | WCAG AA/AAA, cognitive load, motor considerations |
| Ethics in interface design | Dark patterns, consent theater, algorithmic fairness |
| Product strategy | How UX decisions compound into technical and business debt |
| Stakeholder communication | Translating design rationale into revenue and risk language |
| Research and evidence | When to trust qualitative signal, when to demand quantitative proof |

---

## Setup

**What you need first**

- A free account at [claude.ai](https://claude.ai) — required for Options 1 and 2
- The files from this repo — click the green **Code** button at the top of this page → **Download ZIP**, then unzip the folder somewhere you can find it

---

Three ways to use Jackie. Pick the one that fits how you work.

| | Quickest Start | Claude Projects | API |
|---|---|---|---|
| **Time to set up** | 2 minutes | 10 minutes | Varies |
| **What you need** | claude.ai account | claude.ai account | Anthropic API key |
| **Full knowledge base** | No | Yes | No |
| **Stays set up between sessions** | No — paste each time | Yes — always on | Yes |
| **Best for** | Trying it out | Daily use | Building it into a product |

---

### Option 1 — Quickest Start

No configuration. Works immediately.

1. Open [claude.ai](https://claude.ai) and start a new conversation
2. In the unzipped folder, open the `prompts` folder and then open `system-prompt.md` in any text editor (Notepad, TextEdit, VS Code — anything works)
3. Select all the text (Ctrl+A on Windows, Cmd+A on Mac) and copy it
4. Paste it into the Claude conversation as your first message
5. Add your question on the next line and send

> **What you get:** All four modes and the full reasoning framework are active.
> **Trade-off:** The knowledge files aren't loaded, so Build Mode won't have access to exact design tokens, component specs, and CSS — it will reason from first principles instead. Strategic, Direct, and Provocative modes are unaffected.

---

### Option 2 — Claude Projects (Recommended)

One-time setup. Jackie is available in every conversation inside the project, with the full knowledge base loaded.

1. Go to [claude.ai](https://claude.ai) and click **Projects → New Project**
2. Name it "Jackie" or anything you'll recognize
3. Click **Edit project instructions**
4. Open [`prompts/system-prompt.md`](prompts/system-prompt.md), copy all the text, paste it into the instructions field, and save
5. Click **Add content → Files**
6. Upload all eleven files from the `/knowledge` folder in this repo
7. Start a conversation inside the project — Jackie is now active

> **What you get:** The complete experience. Build Mode can pull exact token values, component specs, and CSS patterns. No pasting required — Jackie is active in every conversation inside this project.

---

### Option 3 — API

For developers integrating Jackie into a product or workflow.

```python
import anthropic

with open("prompts/system-prompt.md", "r") as f:
    system_prompt = f.read()

client = anthropic.Anthropic(api_key="your-api-key")

response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=4000,
    system=system_prompt,
    messages=[
        {"role": "user", "content": "Use Strategic Mode. Give me three alternative approaches to reducing drop-off in enterprise onboarding."}
    ]
)
print(response.content[0].text)
```

> **`max_tokens` guidance:** Set to 4000 minimum. Build Mode responses with full component specs, CSS, and all states regularly exceed 2500 tokens. For Strategic, Direct, and Provocative mode queries, 1500 is sufficient if you want to manage cost.

---

## Things Worth Asking

```
Give me three alternative approaches to [problem or scenario].

I'm choosing between [option A] and [option B] for [feature]. What am I not seeing?

Challenge my assumptions about [design decision you feel confident about].

We need to balance [business goal] against [user need] in [context]. Where do we land?

What does this flow get wrong? [describe it]

Use Provocative Mode: here's the design I'm planning to ship next week.

Build me a [component name] component: [describe variants, context, constraints].

Design the onboarding flow for [product type and user]. All states, all edge cases.

Audit this checkout for visual and UX quality: [describe or paste the design].

Tell me my design is good.
```

That last one will get a response. It will not be the response you were hoping for.

---

## Rules It Operates By

**Takes a position.** Not five equally valid options. A direction, with reasons, and an acknowledgment of what you give up by choosing it.

**Evidence before assertion.** Every claim is grounded in a principle, a pattern, or a documented failure mode. Hunches are not offered as facts.

**Holds under pressure.** If you push back, the agent examines your argument. It updates if you've added new reasoning. It holds if you've only added volume.

**No decorative hedging.** "It depends" is not a complete sentence. Context gets accounted for; then a position gets taken.

**Does not celebrate.** You will not receive a "Great choice!" You will receive the next problem to solve. This is, arguably, more respectful.

---

## A Note on Banned Words

The skill enforces a vocabulary constraint. Words that pad without adding meaning — *seamless*, *intuitive*, *robust*, *leverage* — are not used. See `docs/vocabulary-constraints.md` for the full list and the reasoning behind it.

---

## What's in the Box

```
/
├── README.md                         ← You are here
├── skill.md                          ← The full agent definition
├── prompts/
│   ├── system-prompt.md              ← Claude Project Instructions or Anthropic API system prompt
│   ├── starter-prompts.md            ← First questions that work well
│   ├── strategic-mode.md             ← Strategic Mode prompt layer
│   ├── direct-mode.md                ← Direct Mode prompt layer
│   └── provocative-mode.md           ← Provocative Mode prompt layer
├── examples/
│   └── worked-examples.md            ← All four modes in action across enterprise contexts
├── knowledge/
│   ├── ux-principles.md              ← Core knowledge base
│   ├── ux-writing.md                 ← Interface copy: button labels, errors, empty states, voice and tone
│   ├── heuristics.md                 ← Nielsen's heuristics with applied commentary
│   ├── ai-ux-considerations.md       ← AI interface reasoning guide
│   ├── rad-design-system.md          ← Full RAD component catalog (34 components)
│   ├── psychology-deep-dive.md       ← Cognitive science, decision architecture, animation timing
│   ├── patterns-and-flows.md         ← Cross-industry patterns: onboarding, auth, e-commerce, nav
│   ├── design-tokens.md              ← Complete CSS token scales: spacing, color, type, shadows
│   ├── component-library.md          ← Full component specs: buttons, inputs, cards, modals, toasts
│   ├── polish-and-craft.md           ← Advanced visual techniques with copy-paste CSS
│   └── ui-engineering.md             ← React/TS, shadcn/ui, CVA, Radix, component architecture
├── integrations/
│   ├── claude-skill.md               ← Claude Project and API setup
│   ├── custom-gpt.md                 ← OpenAI custom GPT deployment (degraded — see note)
│   └── rad-integration.md            ← Using this agent with the RAD design system
└── docs/
    ├── contribution-guide.md         ← How to extend the skill
    ├── vocabulary-constraints.md     ← Words that are banned and why
    └── changelog.md                  ← Version history
```

---

## License

MIT. Use it, modify it, ship it, teach it. A nod in the credits is nice but not required.

---

Made by [Jackie Curry](https://github.com/jackiecurry)
