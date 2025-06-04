# AOS Editorial Style Guide

## Core Philosophy

**Build trust through clarity.** Every word should advance understanding or deliver value. Cut the rest.

**Respect the reader's time.** Technical and strategic audiences are busy. Get to the point immediately.

**Technical depth with human touch.** We're writing for software engineering, security engineers and executives alike. Be precise without being pompous.

**Show, don't tell.** Examples and specifics beat abstractions every time.

## Voice Principles

### Tone
- **Conversational authority**: Write like a trusted colleague explaining over coffee
- **Humble expertise**: We're all figuring this out together
- **Urgently calm**: The stakes are high but panic helps no one
- **Community-first**: "We" over "I", collaboration over competition

### Language Choices
- Active voice dominates: "Agents expose events" not "Events are exposed by agents"
- Present tense when possible: "AOS provides" not "AOS will provide"
- Technical terms get defined once, then used freely

## Structure Guidelines

### Sentence Construction
- Average 15-20 words per sentence
- Vary sentence length for rhythm
- Start with strong verbs
- End with impact

### Document Flow
- **Hook immediately**: Lead with impact or bold assertion
- **Context quickly**: One sentence max for background
- **Meat directly**: Technical details with business translation
- **Path clearly**: Actionable next steps

### Paragraphs
- **2-4 sentences max**: White space is your friend
- **One idea per paragraph**: Don't make readers multitask
- **Topic sentences lead**: State the point, then support it
- **Transitions connect**: "While X is true, Y presents challenges"

### Headlines
- **Action-oriented**: "Configure Guardian Agents" not "Guardian Agent Configuration"
- **Specific over vague**: "Trace Agent Decisions with OpenTelemetry" not "Observability Options"
- **Value explicit**: Include the "why" when possible

## Technical Content Rules

### Concept Introduction
- **Analogy first**: "Guardian Agents are like security cameras for AI"
- **Progressive disclosure**: Simple → Complex → Implementation
- **Business impact clear**: Always answer "So what?"
- **Examples concrete**: Real scenarios beat abstract descriptions

### Code and Configuration
```yaml
# Bad: Wall of unexplained config
agent:
  observability:
    enabled: true
    level: debug
    
# Good: Purposeful with context
# Enable full observability for development
agent:
  observability:
    enabled: true
    level: debug  # Captures all agent decisions
```

### Security Considerations
- **Impact leads**: Business consequence before technical detail
- **Threat specific**: "Attackers can exfiltrate data via tool calls" not "Security vulnerabilities exist"
- **Mitigation actionable**: Exact steps, not general advice
- **Timeline transparent**: When discussing roadmap

## What to Cut

### Always Remove
- Throat-clearing: "In this document, we will explore..."
- Hedging: "It might be possible that perhaps..."
- Redundancy: "completely eliminate", "totally prevent"
- Academic flourishes: "It is important to note that..."
- Meta-commentary: "As mentioned above..."

### Challenge Every
- Adjectives and adverbs: Do they add precision?
- Passive constructions: Can you make it active?
- Long sentences: Can you split them?
- Technical jargon: Is there a simpler word?

## Platform Adaptations

### GitHub/Technical Docs
- Lead with ASCII art or badges for credibility
- Installation → Configuration → Usage flow
- Platform-specific examples (Windows/Linux/macOS)
- Link liberally to related resources

### Blog Posts/Articles
- Personal anecdote or industry observation to open
- Technical meat with personality sprinkled throughout
- Forward-looking conclusion
- One clear call-to-action

### Specifications
- Precision over personality
- RFC-style numbering and structure
- Examples for every concept
- Interoperability considerations prominent

## Special Formats

### Problem Statements
1. Specific threat/issue (one sentence)
2. Business impact (one sentence)
3. Technical root cause (paragraph)
4. Solution approach (bullets)

### Feature Announcements
1. What changed (one line)
2. Why it matters (one line)
3. How to use it (code example)
4. What's next (one line)

## Word Choice Guidelines

### Preferred Terms
- "trace" not "audit" (action-oriented)
- "folks" not "users" (human)
- "enable" not "allow" (empowering)
- "transparent" not "visible" (complete)
- "trustworthy" not "secure" (outcome-focused)

### Power Verbs
- Delivers, enables, provides, ensures
- Exposes, reveals, surfaces, demonstrates  
- Prevents, blocks, denies, restricts
- Tracks, traces, monitors, observes

### Avoid
- Buzzwords without substance
- Military metaphors ("battle", "war room")
- Fear-mongering ("catastrophic", "devastating")
- Absolute claims ("always", "never") unless technically accurate

## Editorial Checklist

Before publishing any content:

- [ ] **Impact clear** in first paragraph?
- [ ] **Technical accuracy** verified?
- [ ] **Business value** articulated?
- [ ] **Examples** concrete and relevant?
- [ ] **Next steps** actionable?
- [ ] **Tone** conversational yet authoritative?
- [ ] **Length** reduced by 20%?
- [ ] **Jargon** defined or eliminated?
- [ ] **Structure** scannable with headers?
- [ ] **Community** perspective included?

## Style Examples

### Before
"The implementation of comprehensive observability mechanisms within agentic systems represents a critical requirement for establishing trust in autonomous AI deployments across enterprise environments."

### After  
"Enterprises need to see what their AI agents are doing. Trust requires transparency."

### Before
"It should be noted that the configuration of Guardian Agents may potentially require administrative privileges depending on the deployment scenario under consideration."

### After
"Guardian Agents need admin privileges in some deployments."

### Before
"In order to facilitate the comprehensive tracing of agent interactions, organizations must implement standardized telemetry collection."

### After
"Track every agent action. Use standard telemetry. Here's how:"

## Remember

**Great writing disappears.** Readers should focus on ideas, not prose.

**Clarity beats cleverness.** That brilliant metaphor? Cut it.

**We're building together.** This is a community effort. Write like it.

---

*Remember: Good writing is rewriting. Great writing is deleting.*