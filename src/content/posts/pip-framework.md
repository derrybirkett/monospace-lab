---
title: ".pip: Giving AI Agents a Genome"
description: "How I stopped re-scaffolding every project and built a framework that gives AI agents governance, memory, and reusable infrastructure."
date: 2026-02-28
---

I have a problem. Every time I start a new project, I do the same thing: scaffold the monorepo, wire up Docker, configure Postgres, set up auth, write a README (that nobody reads), then open an AI agent and spend ten minutes explaining the entire project from scratch. What it is. What's been decided. What it's allowed to touch.

Then I start the next project and do it all again. From zero.

I've been here before — the itch to extract a pattern. Remember when I couldn't stop building job boards? Same energy. Except this time the pattern is the scaffolding itself. So I built [.pip](https://derrybirkett.github.io/pip/).

---

## What It Actually Is

`.pip` stands for **Project Intelligence & Process**. It's a Git submodule you drop into any project. A knowledge layer.

Mission docs. Delivery methods. Agent roles. Infrastructure scaffolds. A universal entrypoint that works with ChatGPT, Claude, Cursor, Warp, n8n — basically whatever AI tool you’re vibing with this week. All versioned alongside your code.

The idea is deceptively simple: give every project (and every AI agent working on it) the same starting context. No more blank-slate conversations.

---

## The DNA Thing

Here’s the mental model that made it click for me. I think of `.pip` as a **genome**. Your project is the **organism**.

The genome is the `.pip` repo itself — immutable. Governance docs, agent definitions, delivery processes, fragment blueprints. You don’t modify it directly (seriously, don’t).

The organism consumes the genome as a submodule and *expresses* it. Your own mission. Your own activity log. Your own docs. Scaffolded from templates, then yours to own and evolve.

```
your-project/
├── .pip/              ← genome (submodule, read-only)
├── docs/              ← organism docs (yours to modify)
├── src/               ← your code
└── .piprc             ← runtime config
```

One command bootstraps the lot. An interactive wizard asks about your project and generates a tailored mission statement, README, activity log, and agentic playbook. It’s not boilerplate — it’s actually personalised, which I wasn’t expecting to work as well as it does.

---

## Seven Agents, Seven Lanes

This is the bit that raises eyebrows. Every `.pip` project gets a C-suite agent model:

- **CEO** — mission, strategy, cross-functional calls
- **CTO** — architecture, tooling, automation
- **CPO** — product roadmap and outcomes
- **CISO** — security, risk, compliance
- **CMO** — messaging, content, marketing
- **CRO** — revenue, pricing, growth
- **COO** — delivery ops, release hygiene, wrap-up

Each role has documented decision rights. The CTO doesn’t make mission calls. The CMO doesn’t approve security policies. Each agent reads its role doc and stays in its lane.

I know — sounds like corporate roleplay. It’s not. It’s a practical way to get scoped, consistent behaviour from LLMs across sessions. Without it, every conversation starts from zero and the agent improvises authority. That’s how you get an AI rewriting your database schema when you asked it to fix a typo.

---

## Fragments

Fragments are reusable project scaffolds. Run a shell script, files get copied in. From that point, they’re yours. No runtime dependency on `.pip`.

- **nx-dev-infra** — Nx monorepo + Docker + Postgres + n8n
- **astro-blog** — content marketing site with Astro
- **nx-product-surfaces** — landing page + dashboard + auth boundary (ShadCN UI, Playwright)
- **nx-webapp-starter** — lightweight web app scaffold

Apply once, own forever. I’m quite proud of these actually — the philosophy is no lock-in. The fragment gives you a starting point; you take it wherever you want.

---

## Safe Execution

v2 added runtime modes because (surprise) letting agents run wild isn’t always ideal:

- **PIP_MODE** — `observe` (read-only), `propose` (suggest), or `execute` (apply changes)
- **PIP_ACTION_MODE** — `live` (immediate), `confirm` (prompt first), or `dry-run` (preview only)

Defaults in `.piprc`, override with env vars. Means an agent can poke around safely before touching anything. Learned this one the hard way.

---

## The Agents Ship Their Own PRs

This is the part that genuinely surprised me. `.pip` has a GitHub Actions pipeline where autonomous agents:

1. Pick up unassigned roadmap issues every 6 hours
2. Generate implementations via GPT-4
3. Create PRs with code and documentation
4. Review through CTO (technical) and CISO (security) agents
5. Auto-merge when reviews pass

Costs about $2–5/month. It’s already shipped dozens of PRs to the pip repo itself. I still do a double-take when I open GitHub and there’s a new PR I didn’t write.

---

## What’s Next

The roadmap has seven phases over 13 weeks — ambitious, but I’ve scoped it against reality (mostly). `.pip` is evolving from a documentation framework into a full agentic development system:

- **Vector database memory** — persistent context across sessions (Chroma/Qdrant)
- **Formal patterns** — ReAct, Planning, Reflection workflows from actual research
- **Multi-agent coordination** — handoffs, escalation, conflict resolution
- **Quality metrics** — self-evaluation, performance reports, trend analysis
- **Template system** — new agent setup in under 30 minutes

The [full roadmap](https://github.com/derrybirkett/pip/issues) lives in GitHub Issues.

---

## Why Bother

Every AI-assisted dev session starts from zero. The agent doesn’t know your architecture. Doesn’t know your delivery process. Doesn’t know the CTO already rejected that database choice last week.

I’ve spent years thinking about how context shapes experience — in UX, in design systems, in product. Turns out the same principle applies to AI agents. Give them context and they perform. Leave them cold and they hallucinate.

So: give it a genome. Governance, patterns, memory. Let it express that in every project it touches.

Work very much in progress. The [.pip blog](https://derrybirkett.github.io/pip/) documents each step as it ships.

**→ [derrybirkett.github.io/pip](https://derrybirkett.github.io/pip/)**
