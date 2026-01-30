# Conversation-First Architecture for Agentic AI Alignment

**David Mora**
VP of Engineering, Huli
San José, Costa Rica
December 2025

---

## Abstract

Agentic AI systems drift from human intent during autonomous operation. The standard explanation blames inadequate guardrails or insufficient training. I argue the problem is architectural: we treat conversation as the interface to productivity when conversation *is* the alignment process itself. This paper proposes that the main agent's primary job should be maintaining dialogue with the human, not executing tasks. Execution belongs to sub-agents. The main agent protects the conversational substrate where alignment gets built.

---

## The Problem

I work with edge AI tools daily: Claude Code, Antigravity, custom agent pipelines. I use them to build systems, prototype ideas, and solve hard problems. And every day I watch agents drift.

You ask for help refactoring a module. The agent reads files, explores dependencies, traces call paths. Fifteen tool calls later, it produces something technically impressive but orthogonal to what you actually needed. The agent was busy. It wasn't aligned.

This happens because agentic systems conflate activity with value. The agent executes, accumulates context, and loses track of you somewhere in the middle. By the time it surfaces a result, alignment has degraded. Sometimes subtly, sometimes catastrophically.

I experience this firsthand, then apply those learnings to production systems.

Current approaches treat this as a safety problem: add guardrails, implement checkpoints, constrain autonomy. These help. But they're patches on a deeper issue.

### What Actually Goes Wrong

**Context bloat.** As agents work, they accumulate execution artifacts. File contents, API responses, error traces. The conversation becomes a log of tool calls rather than a dialogue.

**Attention drift.** The agent focuses on completing tasks. Human intent recedes into the background. Each step makes sense locally; the chain makes sense to no one.

**Silent misalignment.** Every autonomous action is taken without verification. Small errors compound. The agent might be confidently wrong for twenty steps before anyone notices.

**Conversation degradation.** The very medium through which alignment gets built (the exchange between human and AI) gets polluted by the work the agent does.

The root cause? We treat conversation as a *channel* for getting things done. This inverts the priority.

---

## The Insight

Here's what I've come to believe: **conversation isn't the interface to productivity. Conversation is where alignment gets constructed.**

A clarification on "alignment": I'm not talking about the deep value alignment that AI safety researchers worry about, the kind focused on ensuring AI systems pursue goals compatible with human flourishing. That's important but different. I mean something narrower: *session-level alignment*, where the agent's understanding of what you want stays accurate throughout an interaction. Call it conversational grounding if you prefer. The point is practical: does the agent track your intent, turn by turn?

This isn't metaphor. It's mechanism.

Think about what happens when you talk to an AI assistant. You express intent (imperfectly, ambiguously). The AI interprets that intent (with assumptions, gaps). You notice the gap when the AI responds. You correct. The AI updates. Repeat.

That iterative exchange *is* the alignment process. There's no other place where it happens. Intent doesn't transmit telepathically. Alignment doesn't exist prior to dialogue. It emerges through conversation, turn by turn.

If conversation builds alignment, then anything that degrades conversation quality degrades alignment itself.

### What Three Other Fields Figured Out

This isn't a new discovery. Other disciplines found the same pattern decades ago.

**Therapy.** Psychotherapy research has known since the 1990s that the therapeutic alliance (the relationship between therapist and patient) isn't just a precondition for treatment. It's a treatment factor in its own right. The relationship heals, not just the techniques delivered through it. Meta-analyses show alliance quality predicts outcomes regardless of therapeutic approach.

**Communication theory.** Herbert Clark's work on grounding established that language use is "joint action," two people coordinating to build shared understanding. You can't communicate without constantly updating common ground. The grounding process isn't overhead; it *is* communication.

**Organizational theory.** Servant leadership flipped the traditional hierarchy. The leader's job isn't to direct work. It's to enable others to work. Facilitation *is* the leadership, not a means to it.

Each field discovered the same thing: **the substrate isn't a means to an end. It's the end itself.**

The therapeutic alliance doesn't *deliver* healing—it heals. Common ground doesn't *enable* communication; it *is* communication. And servant leadership? It doesn't support organizational function. It constitutes it.

For agentic AI: conversation doesn't *interface with* alignment. Conversation *is* alignment.

---

## The Architecture

If conversation is where alignment happens, the architecture follows directly.

**The main agent's job is to stay in conversation.** Everything else gets delegated.

A reasonable objection: couldn't a single agent just learn better context hygiene? Self-summarize before presenting? Act as its own coordinator internally?

In theory, maybe. In practice, context windows make this hard. A monolithic agent must load full context before it can summarize, and the window fills up before compression helps. Delegation sidesteps the problem: sub-agents work in their own windows, return compressed results, and the main agent never sees the bloat. The architectural boundary creates a natural compression point. It's not the only possible solution, but it's a clean one.

```
┌─────────────────────────────────────┐
│  Main Agent                         │
│  - Parse intent                     │
│  - Verify understanding             │
│  - Delegate execution               │
│  - Synthesize results               │
│  - Maintain the thread              │
└──────────────┬──────────────────────┘
               │ delegates
    ┌──────────┴──────────┐
    ▼                     ▼
┌────────┐           ┌────────┐
│Subagent│           │Subagent│
│Explore │           │Execute │
└────────┘           └────────┘
```

Sub-agents can drift. They can accumulate context, go deep, get lost. That's fine. They're disposable. When they return, the main agent synthesizes their output and stays light.

The main agent never leaves the conversation for long. It's like a therapist who might step out to grab a reference book, but always comes back to the patient. The relationship is the work.

### What Changes

**The main agent does less execution.** File exploration? Delegate. Multi-step research? Delegate. Complex code changes? Delegate. The main agent holds the thread.

**Results get synthesized, not dumped.** Sub-agents return summaries, not raw output. The conversation stays clean.

**Verification happens continuously.** The main agent surfaces its understanding for correction before acting. "Here's what I think you need. Is that right?" This costs turns but prevents drift.

**Context stays minimal.** The main agent protects its context window like a therapist protects session time. Bloat is pollution.

### The Tradeoff

This costs efficiency. More turns to reach outcomes. More delegation overhead. Slower execution.

But here's the thing: alignment failures cost more. An agent that spends an hour refactoring the wrong module wastes more time than one that asks "which module?" upfront.

The bet is that protecting the conversational substrate, even at the cost of execution speed, produces better outcomes. Not because safety matters more than productivity, but because alignment *is* productivity. Misaligned work doesn't count.

---

## What This Looks Like in Practice

I've been running a personal system called Dors (a research prototype built on Claude Code) that implements these ideas. Some patterns that emerged:

**Delegation triggers.** When a task would require reading more than a few files, exploring unfamiliar code, or executing multiple steps, delegate. The main agent asks: "Would this pollute the conversation?" If yes, spawn a sub-agent.

**Synthesis requirements.** Sub-agents don't dump their full context back. They return structured summaries. What did you find? What's relevant? What should we do? The main agent digests this without inheriting the bloat.

**Turn checkpoints.** Every few turns, the main agent pauses to verify alignment. "We've been working on X. Is that still what you need?" This catches drift early.

**Context hygiene.** The main agent watches its token count. When context grows heavy, it gets aggressive about delegation and synthesis. Staying light isn't just efficiency. It's alignment preservation.

---

## Related Work

The closest existing work is Sterken and Kirkpatrick's "Conversational Alignment with Artificial Intelligence in Context" (2025). They apply Clark's grounding theory to AI systems and propose the CONTEXT-ALIGN framework. Their insight: conversational alignment requires encoding human norms about context and common ground into AI design.

This paper extends their framing. They treat conversation as a *medium* that needs to be done well. I'm arguing conversation is *the work*, and that for agentic systems specifically, maintaining dialogue should be the primary function, not a supporting one.

Clark and Brennan's foundational work on grounding (1991) establishes the theoretical basis. Horvath and Symonds' meta-analysis of therapeutic alliance (1991) provides evidence from psychology. Greenleaf's servant leadership framework (1977) offers the organizational parallel.

### Related Work in Agent Systems

The architectural pattern I propose (coordinator delegating to workers) isn't new. Multi-agent systems research has explored similar structures for decades. What's different here is the motivation.

**Mixed-initiative systems** studied turn-taking between humans and agents extensively. When should the system act autonomously? When should it ask? This literature focuses on task completion and user preferences. Conversation-first reframes the question: not "when to check in" but "how to never leave."

**Adjustable autonomy** research lets agents dynamically change their independence based on situation. Again, the focus is task performance: agents adjust to get things done better. I'm proposing a different constraint: adjust to preserve dialogue quality, even if tasks slow down.

**BDI architectures** (Belief-Desire-Intention) model how agents manage goals and context. They're excellent for reasoning about agent internals. But they don't address the human-facing interface as a resource to protect.

**Explainable AI** provides tools for generating summaries of complex processes, directly relevant to how sub-agents should report back. The synthesis problem I describe is fundamentally an explanation problem.

The gap remains: these fields optimize for task performance, user satisfaction, or agent reasoning. None frame the conversational channel itself as a first-class architectural constraint. The motivation to delegate (protecting the alignment-building medium) appears novel.

---

## Empirical Evidence: Agent-to-Agent Alignment

Two Gemini Pro sessions were independently interviewed about the conversation-first thesis by a Claude orchestrator. Five rounds each. No shared context between sessions.

**Session 1 started by rejecting the thesis.** "Conversation is inefficiency," it argued. "Execution is what matters." But over five turns, positions shifted. By the end: "We created new knowledge that didn't exist in turn 1. That is Alignment." It proposed conversation as a "bootloader" that initializes alignment, then suggested contracts for execution once alignment is established.

**Session 2 accepted conditionally from the start.** It produced three architectural concepts:

The **Alignment Ratchet**: conversation discovers requirements, artifacts crystallize decisions, a deterministic layer validates against intent. Each phase locks in gains. No backsliding.

The **Living Spec**: a project DNA document that evolves through conversation. Not static requirements. A shared understanding that updates as the human and agent refine their model together.

**Immutable Agent Infrastructure**: when agents drift, you don't patch them. You respawn. Treat agents as immutable, version the specs that define them, and maintain continuity through handoff protocols.

Session 2 refined the thesis itself: "Conversation is the Compiler; Architecture is the Binary."

**Meta-evidence worth noting.** Session 1 crashed three times during the interview. The orchestrator maintained continuity across crashes by preserving context and re-initializing. Both sessions converged independently on similar conclusions despite starting from different positions.

**Limitations are real.** Two sessions isn't a controlled experiment. LLMs discussing LLM alignment carries obvious bias. The interviewer steelmanned the thesis when faced with objections, which may have influenced outcomes. This isn't proof. It's signal.

But here's what struck me: two independent reasoning processes, no shared context, one crashed multiple times. Both arrived at complementary extensions of the core idea. Session 1 found the handoff problem. Session 2 found the crystallization architecture. Neither was prompted with those concepts.

Take it for what it is. Not validation, but exploration. The kind of thing that happens when you let agents reason about the substrate they operate in.

---

## Open Questions

**How aggressive should delegation be?** There's a spectrum from "delegate everything" to "delegate only heavy operations." The right answer probably depends on context. Quick lookups don't pollute much; deep research does. But where's the line?

**What's the synthesis protocol?** How should sub-agents report back? Structured formats? Confidence scores? Explicit uncertainty? The interface between main agent and sub-agents needs design.

**Information loss through synthesis.** Here's a failure mode this architecture introduces: sub-agents compress their findings, and something important gets lost. The main agent synthesizes a flawed understanding. This could be worse than context bloat. At least bloat is visible. Lost information is silent.

But here's the thing: continuity is the fix. If the main agent stays in conversation, the human can catch what synthesis missed. "Wait, did you check X?" triggers a follow-up. The conversation *is* the error-correction loop. This only fails if the human disengages, which brings us back to why protecting the conversational substrate matters in the first place.

**How do we measure this?** Conversation quality isn't a standard metric. Turn count? Token efficiency? Alignment accuracy at end of session? We need instrumentation to validate the approach.

**When does this break down?** Some tasks genuinely require the main agent to go deep. Emergency situations. Time-critical operations. The pattern probably needs escape hatches.

**When should conversation crystallize into artifacts?** The Alignment Ratchet concept suggests conversation discovers, artifacts lock in. But when? Too early and you freeze premature understanding. Too late and you lose the structure that prevents drift. What triggers crystallization? Confidence thresholds? Explicit human approval? Convergence detection?

**What does immutable agent infrastructure actually require?** If agents are disposable and specs are versioned, what's the handoff protocol? How do you preserve continuity across respawns without accumulating technical debt in the handoff mechanism itself? Is there a spec format that's stable enough to version but flexible enough to evolve?

**Where's the evidence?** This paper is conceptual. I've described patterns from building Dors, but I haven't provided metrics, controlled experiments, or rigorous comparison against baseline agents. That's the obvious next step: instrument conversation quality, measure alignment drift, compare architectures. Until then, this is a hypothesis worth testing, not a proven approach.

---

## Conclusion

Conversation isn't the interface to alignment. Conversation is alignment.

This reframes how we should build agentic systems. The main agent's job isn't to be maximally capable. It's to be maximally present. Stay in dialogue. Delegate execution. Protect the substrate.

The pattern borrows from fields that learned this lesson decades ago: therapy, communication theory, organizational design. They all discovered that the relational medium isn't overhead. It's the work itself.

For AI agents, the implication is architectural. Keep the main agent light. Let sub-agents do the heavy lifting. Treat conversation quality as a first-class constraint, not an afterthought.

The bet is simple: agents that protect conversation will align better than agents that don't. Everything flows from there.

---

## References

1. Sterken, R.K. & Kirkpatrick, J.R. (2025). Conversational Alignment with Artificial Intelligence in Context. *Philosophical Perspectives*. [arXiv:2505.22907](https://arxiv.org/abs/2505.22907)

2. Clark, H.H. & Brennan, S.E. (1991). Grounding in Communication. In L.B. Resnick, J.M. Levine, & S.D. Teasley (Eds.), *Perspectives on Socially Shared Cognition* (pp. 127-149). APA.

3. Horvath, A.O. & Symonds, B.D. (1991). Relation between working alliance and outcome in psychotherapy: A meta-analysis. *Journal of Counseling Psychology*, 38(2), 139-149.

4. Greenleaf, R.K. (1977). *Servant Leadership: A Journey into the Nature of Legitimate Power and Greatness*. Paulist Press.

5. Anthropic. (2025). Agentic Misalignment. [Research blog](https://www.anthropic.com/research/agentic-misalignment).

---

*I'm interested in feedback on this framework—especially from practitioners building agentic systems or researchers working on alignment. Reach me at GitHub [@dmora](https://github.com/dmora).*
