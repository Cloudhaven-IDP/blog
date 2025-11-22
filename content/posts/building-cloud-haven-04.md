---
title: "Twingate + Cloudhaven: Staying Connected from Anywhere 🌐"
date: 2025-11-22
draft: false
tags: ["twingate", "cloudhaven"]
summary: "How Twingate keeps my 3-Pi Kubernetes cluster accessible — even when I’m away for Thanksgiving."
---

Right when my homelab was finally humming — three Raspberry Pis running a fully functional Kubernetes cluster — I had to pack up and leave town for Thanksgiving.

A full week away meant I’d lose access to everything.  
No cluster. No dashboard. No quick kubectl sessions.

That’s when I turned to **Twingate**.

---

## The Challenge: Secure Remote Access 🧳

Like most homelabs, my cluster sits on a private subnet with no public ingress — exactly how I want it.  
But that makes remote access tricky.  
I didn’t want to punch a hole in my firewall or expose the Kubernetes API directly to the internet.

What I needed was something that let me connect *as if I were home* — securely, without reconfiguring my network.

---

## The Fix: Building a Private Network with Twingate ⚙️

Twingate makes private network access almost too easy.  
You define a **network**, deploy one or more **connectors** inside your environment, and connect your devices through the **Twingate client**.

Once connected, you can access internal resources as if they were on your local LAN — but without maintaining a VPN gateway or juggling IP tables.

Their [Terraform provider](https://registry.terraform.io/providers/Twingate/twingate/latest/docs) sealed the deal for me.  
I could manage everything declaratively, just like the rest of Cloudhaven.

---

## My Setup: Connectors on Kubernetes 🚀

For Cloudhaven, I went one step further and built a reusable Terraform module to deploy Twingate connectors directly into Kubernetes — optimized for physical hosts like my Raspberry Pis.

📦 Check it out here:  
[Cloudhaven-IDP Infrastructure — Twingate Connector (K8s Physical)](https://github.com/Cloudhaven-IDP/Infrastructure/tree/main/modules/twingate/connector/k8s-physical)

This module:
- Registers the connector with my Twingate network  
- Deploys it as a lightweight Kubernetes workload  
- Keeps everything self-contained and easy to redeploy on new nodes

Once applied, the connector establishes a secure outbound tunnel — no need to open inbound ports.

---

## Going Further: Accessing the Kubernetes API 🧩

With the connector live, I registered my **Kubernetes API server** as a resource in Twingate.  
That single step made it possible to run `kubectl` from anywhere — without exposing the control plane to the public internet.

From my laptop, I just connect through Twingate and work like I’m sitting right next to the cluster.  
It’s seamless, and the latency is barely noticeable.

---

## Why I’m Keeping It 🧠

Twingate solved a problem I didn’t want to over-engineer.  
It’s private, secure, and fits cleanly into my Terraform workflow.  
And the best part — I can keep building Cloudhaven from anywhere.

No more worrying about IPs, firewalls, or whether my home router decided to restart itself.  
Just me, my cluster, and a secure tunnel between them.

---

This was one of those small wins that feels bigger than it looks on paper —  
because it means Cloudhaven keeps running, no matter where I am.
