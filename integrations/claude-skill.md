# Claude Integration Guide

## Using Jackie UX Agent with Claude

---

## Method 1: Claude Project Instructions (Recommended)

Claude Projects allow you to set persistent instructions that apply to every conversation within a project. Uploading the companion knowledge files gives the agent access to specific token scales, component specs, and CSS on demand — significantly improving output quality for Build Mode tasks.

**Steps:**

1. Open [claude.ai](https://claude.ai) and navigate to Projects
2. Create a new project named "Jackie UX Agent"
3. Click "Edit project instructions"
4. Copy the full contents of `prompts/system-prompt.md` and paste them into the instructions field
5. Save the project instructions
6. In the project, click "Add content" → "Files"
7. Upload all ten files from the `knowledge/` directory:
   - `ux-principles.md`
   - `heuristics.md`
   - `ai-ux-considerations.md`
   - `rad-design-system.md`
   - `psychology-deep-dive.md`
   - `patterns-and-flows.md`
   - `design-tokens.md`
   - `component-library.md`
   - `polish-and-craft.md`
   - `ui-engineering.md`
8. All conversations within this project will operate as Jackie UX Agent with the full knowledge base available

**Advantages:** Persistent across sessions. No need to re-paste the prompt. Knowledge files are retrieved on demand — the agent can pull exact token values, component specs, and CSS patterns without hallucinating values.

---

## Method 2: Anthropic API — Direct System Prompt

For programmatic access, pass the system prompt as the `system` parameter in the messages API.

```python
import anthropic

with open("prompts/system-prompt.md", "r") as f:
    system_prompt = f.read()

client = anthropic.Anthropic(api_key="your-api-key")

response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=2048,
    system=system_prompt,
    messages=[
        {
            "role": "user",
            "content": "Give me three alternative UX solutions for reducing drop-off during enterprise onboarding."
        }
    ]
)

print(response.content[0].text)
```

---

## Method 3: Multi-Turn Conversation via API

For conversations with memory across turns:

```python
import anthropic

with open("prompts/system-prompt.md", "r") as f:
    system_prompt = f.read()

client = anthropic.Anthropic(api_key="your-api-key")

conversation_history = []

def chat(user_message: str) -> str:
    conversation_history.append({
        "role": "user",
        "content": user_message
    })
    
    response = client.messages.create(
        model="claude-opus-4-6",
        max_tokens=2048,
        system=system_prompt,
        messages=conversation_history
    )
    
    assistant_message = response.content[0].text
    
    conversation_history.append({
        "role": "assistant",
        "content": assistant_message
    })
    
    return assistant_message

# Example usage
print(chat("Use Strategic Mode. I'm redesigning checkout for a B2B procurement tool. What are the key friction points I should prioritize?"))
print(chat("Now use Provocative Mode to challenge the direction I just described."))
```

---

## Method 4: Streaming Responses

For long analytical responses, streaming improves the user experience in interfaces you build:

```python
import anthropic

with open("prompts/system-prompt.md", "r") as f:
    system_prompt = f.read()

client = anthropic.Anthropic(api_key="your-api-key")

with client.messages.stream(
    model="claude-opus-4-6",
    max_tokens=2048,
    system=system_prompt,
    messages=[
        {
            "role": "user",
            "content": "Use Strategic Mode. Challenge my assumptions about progressive disclosure in complex enterprise forms."
        }
    ]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

---

## Recommended Model

Use `claude-opus-4-6` for complex strategic and provocative mode responses. This model handles long-form analytical reasoning and maintains the agent's positioning standards across a full response more reliably than smaller models.

Use `claude-sonnet-4-6` for direct mode responses and shorter interactions where response time is a priority.

---

## Mode Activation in Prompts

When building interfaces on top of this skill, you can pre-activate a mode by prefixing the user's message or by including the mode instruction in your request:

```python
user_input = "Should I use tabs or a sidebar?"
mode = "Direct Mode"

formatted_message = f"Use {mode}. {user_input}"
```

This produces cleaner outputs than asking users to specify modes manually. The skill also auto-detects the appropriate mode from the request — see the Mode auto-detection section in the system prompt for the trigger patterns.

**Mode examples by task type:**

```python
# Analysis / trade-off
"Use Strategic Mode. I'm redesigning checkout for a B2B procurement tool. What are the key friction points I should prioritize?"

# Quick recommendation
"Use Direct Mode. Tabs or sidebar for an app with 8 main sections?"

# Assumption stress-test
"Use Provocative Mode. We've decided to use infinite scroll instead of pagination for our data table."

# Build execution
"Use Build Mode. Design a notification toast component: success, error, warning, and info variants, with auto-dismiss and an undo action."
```

---

## Token Considerations

Output length varies significantly by mode:

| Mode | Typical Range | Notes |
|---|---|---|
| Strategic Mode | 500–1,200 tokens | Complex trade-off analysis runs longer |
| Direct Mode | 150–400 tokens | Intentionally brief |
| Provocative Mode | 300–600 tokens | Diagnostic, not comprehensive |
| Build Mode | 1,500–4,000 tokens | Component specs, CSS, full flows run long |

Set `max_tokens` at a minimum of 4,000 to avoid truncation on Build Mode tasks. A complete component with all states, tokens, and CSS can exceed 2,500 tokens. For API usage where you want to cap costs on strategic queries, you can pass `max_tokens=1500` for non-build requests and `max_tokens=4000` for build requests.

For multi-turn sessions on a single complex topic, monitor context window usage if the conversation exceeds 15 turns.
