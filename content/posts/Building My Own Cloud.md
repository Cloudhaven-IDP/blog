---
title: "Building My Own Cloud — Lessons From $280 and a Pile of Raspberry Pis"
date: 2025-11-15
draft: false
tags: ["cloudhaven", "kubernetes", "talos", "homelab", "raspberrypi"]
summary: "How I built a fault-tolerant Kubernetes cluster at home using Raspberry Pis, Talos Linux, and a lot of stubborn curiosity — after learning the hard way how expensive EKS can get."
---

## Why I Ditched the Cloud (For My Own)

My first attempt at running Cloudhaven was on **Amazon EKS**.  
It was smooth, fast, and exactly like the setup I use at work — until the bill came in.  
**$280.** For one month.

That was my “never again” moment.

I realized all I really needed were **machines that can run Linux**,  
**clustered together on the same network**, and **expose an API** I could talk to.  
Surely there had to be a cheaper (and more personal) way to do that.

---

## The Birth of a Tiny Cloud ☁️

I already had a **Raspberry Pi 4 (8GB RAM)** lying around from an old project —  
originally bought to host a Pi-hole to block ads across my network.  

That little Pi became my first **worker node**.

I then bought an **Orange Pi**, which turned out to be a mistake —  
limited community support, frustrating driver issues, and inconsistent docs.  
So I returned it, picked up another Raspberry Pi, and paired it with an **NVMe SSD kit (512GB)**.  
That SSD-backed Pi became my **control plane node**.

At the time of writing, I’m running **three nodes**:

1. Raspberry Pi (Master node, NVMe-backed)  
2. Raspberry Pi (Worker node)  
3. Mac mini VM (Worker node, via [UTM](https://mac.getutm.app))

All wired into the same switch — a tiny, humming, personal cloud in my living room.

---

## The Stack: Talos Linux + Kubernetes

Instead of running full Linux distributions, I went with **[Talos Linux](https://www.talos.dev)** — a lightweight, immutable OS built specifically for Kubernetes.  

Talos takes over the kernel, **stripping away everything unnecessary**, and exposes a secure API that you interact with using `talosctl`.  

That means:
- No SSH into nodes (everything is API-driven)  
- Immutable filesystem (great for security)  
- Minimal attack surface  
- Consistent upgrades and reboots  

It’s frustrating at first (especially when debugging network configs), but the payoff in reliability and transparency is huge.

---

## Keeping It Cheap — and Reliable

So far, the setup has been **super efficient and surprisingly stable**.  
Each node idles around **2–4 watts**, and everything boots in under 30 seconds.

To improve **fault tolerance**, here’s what I’ve added (or plan to add):

- **Daily Cluster Backups:**  
  Using Kubernetes’ batch API, I run a CronJob that exports etcd snapshots daily and uploads them to **S3**.  
  (Still working on OIDC authentication for the service account — static keys would be easier, but I’m aiming for a *production-grade* setup.)

- **Uninterruptible Power Supply (UPS):**  
  Small UPS units can keep the control plane alive through short outages.  
  Considering one for the switch + Pi master node combo.

- **Network Monitoring & Observability:**  
  I recently discovered **[HyperDX](https://hyperdx.io)** — an open-source, Datadog-adjacent observability stack built on **ClickHouse** and **OpenTelemetry**.  
  I love that it relies on open standards (OTel especially), which means I can always pivot to another backend if I outgrow it.  
  For now, I’m excited to integrate it into my cluster and see how well it handles logs, metrics, and traces in a lightweight homelab environment.

- **Future: Cluster Autoscaling with Pi Additions.**  
  The goal is to eventually expand to 4–5 nodes and introduce a small load balancer.

---

## What I’ve Learned So Far

- **Cloud is convenient — but it’s not the only way.**  
  You can learn 90% of the same lessons locally for 1% of the cost.  
- **Failure is cheap here.**  
  If my cluster goes down, I just reboot and keep going.  
- **Talos Linux will test your patience.**  
  But it rewards you with a hardened, secure, API-first environment.  
- **Building things from scratch builds empathy.**  
  I now understand Kubernetes control planes, networking, and storage in ways that EKS abstracts away.

---

## Closing Thoughts

This project started with frustration — and turned into fascination.  
I now have my own mini data center, completely under my control, running on open-source software and cheap hardware.

It’s not perfect, but it’s **mine**, and every crash, fix, and tweak teaches me something new.

Next, I’ll be exploring **GitOps for my homelab** — using ArgoCD to manage app deployments and experimenting with lightweight observability stacks like HyperDX.

Stay tuned — Cloudhaven’s local edition is just getting started.

---

🧠 *Curious about Talos or my cluster layout? I’ve open-sourced part of my setup here:*  
[https://github.com//cloudhaven/infrastructure](https://github.com/Cloudhaven-IDP/infrastructure/)
