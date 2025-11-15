---
title: "Building Cloudhaven — Publishing the Easy Way ✍️"
date: 2025-10-21
draft: false
tags: ["cloudhaven", "hugo", "cloudflare", "terraform", "writing", "ai"]
summary: "How I built and deployed my personal site using Hugo, Cloudflare Pages, and Terraform — and how AI is helping me write better, faster, and more intentionally."
---

I’ve always liked writing things down — but only if the process doesn’t get in the way.  
So when it came time to build a home for these Cloudhaven posts, I wanted something **fast, portable, and low-maintenance**.  

That’s how I landed on **Hugo**.

---

## Why Hugo?

Hugo is an open-source static site generator that turns Markdown files into a full-fledged website — *instantly*.  
It’s written in Go, so it’s blazing fast and doesn’t require any runtime or database.  
All I have to do is write in Markdown, commit, and push — and my site updates itself automatically.

That simplicity keeps me focused on the writing, not on frameworks, dependencies, or patching servers.

---

## The Setup 🧱

I use the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme — clean, responsive, and minimal.  
It fits well with the tone of my Cloudhaven series and looks great on both mobile and desktop.

Here’s what my workflow looks like:

1. **Write in Markdown** using any editor (I’m currently using VS Code).  
2. **Commit to Git** — the repo lives on GitHub.  
3. **Cloudflare Pages** picks up the change and rebuilds the site automatically.  

That last part is the magic.  
Cloudflare’s continuous deployment is handled through a **connector** that links my GitHub repo to Cloudflare Pages.  
Once it’s set up, publishing a new post is as simple as:

```bash
git add .
git commit -m "new post: building cloudhaven website"
git push
```
That’s it — the new article is live in under a minute.

---

## Terraform + Cloudflare: The Domain Side 🌍

To keep things consistent with the rest of my infrastructure work, I manage my domain using **Terraform** and the **Cloudflare provider**.

I added an alias record that points to `afolabi.cloudhaven.work`, and Terraform handles the rest:

- DNS records  
- Page rules  
- SSL settings  

The Cloudflare provider is surprisingly easy to use and integrates perfectly with my infrastructure setup.

---

## How AI Fits Into My Writing ✨

Building Cloudhaven isn’t just about code and clusters — it’s also about **communicating what I’m learning clearly**.  
And lately, **AI has become a quiet writing partner** in that process.

I use it to:

- **Refine my thoughts** when I’m stuck or rambling.  
- **Restructure drafts** into a logical flow that reads better.  
- **Highlight gaps** — moments where I assume too much technical context.  
- **Polish** the tone to stay approachable without losing depth.  

It doesn’t write *for* me — it writes *with* me.  
I still write every sentence, but AI helps me think like an editor and shape my ideas into something readable.  

That’s been the biggest creative unlock so far:  
I can focus more on *what I want to say* and less on *how I say it.*

---

## Why It Matters

This setup feels like **engineering minimalism**.  
It’s infrastructure that doesn’t get in your way — it just works.  
And when I’m experimenting with something like Cloudhaven, that’s all I want.

My deployment pipeline is now as simple as:

> **Write → Commit → Publish**

No CI scripts, no servers to maintain, no billing surprises — just ideas flowing straight to the web.  
And with AI helping me shape those ideas, the process feels more natural than ever.

