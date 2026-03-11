# Jackie UX

**Not a chatbot. Not a suggestion engine. A peer who will tell you your nav structure is a haunted house and explain exactly why.**

This is a reasoning agent for people who build things that real humans have to use. It knows when your onboarding flow is optimistic fiction. It knows the difference between a pattern that tests well and a pattern that survives contact with a stressed user at 11pm on a phone with 8% battery and no patience. It has opinions, and it will share them whether you asked for the sharp version or not.

It will not tell you your design is "seamless." It will not say your interface is "intuitive." It will not describe anything as "delightful" unless it has earned that word. It will not produce five equally valid options when one is clearly stronger — that is not balance, that is abdication. And it will not pretend your modal with seven CTAs represents a considered design decision.

What it will do: think through the problem with you, argue against your assumptions when they deserve to be argued against, build the component if you need the component built, and tell you exactly what you are giving up with every choice you make. Design tools that agree with everything you do are easy to find. This is the other kind.

---

## What the Difference Looks Like

**A weak prompt. Two different answers.**

> *"Should I use tabs or a sidebar for my B2B dashboard navigation?"*

**A normal LLM:**
> "Great question! Both tabs and sidebars have their advantages. Tabs work well for a small number of sections and give users a clear overview. Sidebars are better for complex navigation with many items. Consider your users' needs and the number of navigation items when making your decision. You might also want to test both options with your users!"

**Jackie UX — Direct Mode:**
> Use a sidebar. B2B dashboards with more than five navigation destinations, complex hierarchies, or role-based section visibility require a navigation pattern that scales — tabs do not. A sidebar gives you vertical space to grow, room for section grouping, and the ability to collapse to icon-only for users who want more screen real estate. Tabs work in dashboards with three to five flat destinations and no need for nesting. If you are building a B2B analytics tool, you are almost certainly not in that situation.

That's the difference. One validates the question. The other answers it.

---

## What You're Actually Getting

A structured agent specification — the cognitive architecture, knowledge base, vocabulary rules, and four operating modes that turn a general-purpose language model into something with a point of view and, when necessary, a ruler.

You wire it in once. Then you have a counterpart who reads your flows, does not perform enthusiasm, and has apparently never heard the phrase "that's a great point."

---

## Four Ways It Thinks

**The Long View** (default for analysis)
Pull up a chair. This mode works through the problem — trade-offs, downstream consequences, the thing you haven't noticed yet. Use it when a wrong decision now becomes expensive later.

**The Short Answer**
One direction. Reasons included but compressed. No philosophical detour. Use it when you need to move.

**The Adversary**
The agent argues against your design. Your assumptions are the target. Use it before you ship something you feel good about — especially then.

**The Builder**
Execution mode. Give it a component, a flow, or a screen to design and it will produce the full artifact — UX strategy first, then CSS, specs, copy, all states, dark mode, accessibility. No skipped steps, no random values. Use it when you need something built to a professional standard, not described.

---

## Who Finds It Useful

- Designers at the part of the project where "it depends" is not a useful answer
- PMs making trade-offs between what users need and what the roadmap allows
- Researchers who want a second reader who won't just validate
- Anyone about to hand a design to engineers and wanting one last pressure test

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

## What's in the Box

```
/
├── README.md                         ← You are here
├── skill.md                          ← The full agent definition
├── prompts/
│   ├── system-prompt.md              ← Claude Project Instructions or Anthropic API system prompt
│   ├── starter-prompts.md            ← First questions that work well
│   ├── strategic-mode.md             ← The Long View prompt layer
│   ├── direct-mode.md                ← The Short Answer prompt layer
│   └── provocative-mode.md           ← The Adversary prompt layer
├── examples/
│   ├── onboarding-analysis.md        ← Long View in action
│   ├── checkout-friction.md          ← Short Answer in action
│   └── navigation-challenge.md       ← Adversary in action
├── knowledge/
│   ├── ux-principles.md              ← Core knowledge base
│   ├── heuristics.md                 ← Nielsen's heuristics with applied commentary
│   ├── ai-ux-considerations.md       ← AI interface reasoning guide
│   ├── rad-design-system.md          ← Full RAD component catalog (34 components)
│   ├── psychology-deep-dive.md       ← Cognitive science, decision architecture, animation timing
│   ├── patterns-and-flows.md         ← Cross-industry patterns: onboarding, auth, e-commerce, nav
│   ├── design-tokens.md              ← Complete CSS token scales: spacing, color, type, shadows
│   ├── component-library.md          ← Full component specs: buttons, inputs, cards, modals, toasts
│   └── polish-and-craft.md           ← Advanced visual techniques with copy-paste CSS
├── integrations/
│   ├── claude-skill.md               ← Claude Project and API setup
│   ├── custom-gpt.md                 ← OpenAI custom GPT deployment
│   ├── api-usage.md                  ← Direct API system prompt injection
│   └── rad-integration.md            ← Using this agent with the RAD design system
└── docs/
    ├── contribution-guide.md         ← How to extend the skill
    ├── vocabulary-constraints.md     ← Words that are banned and why
    └── changelog.md                  ← Version history
```

---

## Plug It In

**Claude Projects (claude.ai)**

1. Create a new Project
2. Paste `prompts/system-prompt.md` into Project Instructions
3. Upload all nine files from `/knowledge` as project knowledge files — this is what lets the Builder mode pull exact token values, component specs, and CSS rather than inventing them
4. Ask it something hard

**API**

```python
import anthropic

with open("prompts/system-prompt.md", "r") as f:
    system_prompt = f.read()

client = anthropic.Anthropic()

message = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=4000,  # Builder mode responses can exceed 2500 tokens
    system=system_prompt,
    messages=[
        {"role": "user", "content": "Give me three alternative approaches to reducing drop-off in enterprise onboarding. Long View mode."}
    ]
)
print(message.content[0].text)
```

---

## Things Worth Asking

```
Give me three alternative approaches to [problem or scenario].

I'm choosing between [option A] and [option B] for [feature]. What am I not seeing?

Challenge my assumptions about [design decision you feel confident about].

We need to balance [business goal] against [user need] in [context]. Where do we land?

What does this flow get wrong? [describe it]

Use Adversary mode: here's the design I'm planning to ship next week.

Build me a [component name] component: [describe variants, context, constraints].

Design the onboarding flow for [product type and user]. All states, all edge cases.

Audit this checkout for visual and UX quality: [describe or paste the design].
```

---

## Rules It Operates By

**Takes a position.** Not five equally valid options. A direction, with reasons, and an acknowledgment of what you give up by choosing it.

**Evidence before assertion.** Every claim is grounded in a principle, a pattern, or a documented failure mode. Hunches are not offered as facts.

**Holds under pressure.** If you push back, the agent examines your argument. It updates if you've added new reasoning. It holds if you've only added volume.

**No decorative hedging.** "It depends" is not a complete sentence. Context gets accounted for; then a position gets taken.

---

## A Note on Banned Words

The skill enforces a vocabulary constraint. Words that pad without adding meaning — *seamless*, *intuitive*, *robust*, *leverage* — are not used. See `docs/vocabulary-constraints.md` for the full list and the reasoning behind it.

---

## License

MIT. Use it, modify it, ship it, teach it. A nod in the credits is nice but not required.

---

Made by [Jackie Curry](https://github.com/jackiecurry)
