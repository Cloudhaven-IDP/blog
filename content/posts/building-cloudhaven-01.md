---
title: "Building Cloudhaven — The Vision🕵🏻‍♂️."
date: 2025-10-12
draft: true
tags: ["cloudhaven", "devops", "kubernetes", "setup"]
summary: "Kicking off the Cloudhaven project — why I’m building it, how I’m setting it up, and what went right (and wrong) this week."
---

Every project starts with a thought that refuses to leave your head.  
For me, this one started with a simple question:

> “What if I could build an AI agent that actually understands systems — enough to help during incidents or even when cleaning up tech debt?”

That’s the goal of this project.  
To create a fully integrated AI agent that knows my stack well enough to provide useful context when things go wrong — and to help make sense of forward progress technically.

---

## Why build this?

I can think of two clear use cases right now:

1. **During active incidents.🧨**  
   Imagine being in the middle of an outage and being able to ask,  
   “What changed recently?” or “Has this service failed in the same way before?”  
   The agent should already know.

2. **Cleaning up tech debt.💸**  
   Tech debt always sounds like a bad thing — but to me, it’s a sign that decisions were being made.  

   Those shortcuts tell a story. I want an agent that can surface those stories, so I can decide what’s worth untangling first.

---

## Who’s already doing this?

I’m sure I’m not the first to think this way.  

Datadog has **Bits AI**, and there are probably other tools in the same space trying to marry observability with context-aware AI.

The difference here is scale and philosophy.  

This project won’t need a massive SaaS platform or enterprise billing.  

It’s meant to be **small, local, and cheap to run** — an experiment in how far one person can go with the right setup and some curiosity.

---

## What makes this different?

It’s not the model.  

It’s not the stack.  

It’s the fact that it’ll be **dirt cheap**, **transparent**, and **personal**.  

Most importantly — it’s being built in public, by someone still learning the ins and outs of agentic AI.

Agentic systems are new to me, so this is as much about *learning how* to build one as it is about *building one that works*.  

There’s no grand pitch here — just curiosity, experiments, and reflection.

---

## Next Steps (Day 1–3 Roadmap)

Here’s roughly what comes next:

- **Day 1 – Architecture Sketch**  
  Map out what the agent will look like — from data sources to inference flow.  
  Probably start with LangChain or AgentCore as the backbone, I really love n8n and crewAI but this is not about being comfortable.

- **Day 2 – Local Context Layer**  
  Begin wiring environment i.e kubernetes cluster, networking, 

---

This is **Day 0 — Operations.**  
The start of something small, probably messy, but definitely worth building.