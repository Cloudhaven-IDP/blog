---
title: "Bringing IRSA to a Raspberry Pi Kubernetes Cluster"
date: 2025-12-08
draft: false
tags: ["cloudhaven", "kubernetes", "aws", "security"]
summary: "How I wired secure AWS access into my mostly-local Kubernetes platform by exposing a custom OIDC provider and enabling IRSA on bare-metal."
---


Cloudhaven runs almost entirely inside my home:  
k3s on a Raspberry Pi cluster, my own networking, local services, my own observability stack — everything.

But “local” doesn’t mean “isolated.”

Some parts of Cloudhaven still benefit from AWS offerings.  
The first real need came from **External Secrets Operator**, which pulls secrets from AWS.  
Then new use cases followed quickly:

- storing “disliked” conversations to **S3**  
- later using those annotated logs for **RAG** in my agent  
- future workloads that may hit AWS services as Cloudhaven evolves  

So even though Cloudhaven is intentionally designed to run on my hardware, I still needed a **secure way for Kubernetes workloads to call AWS APIs**.

That meant bringing IRSA to a **bare-metal k3s cluster** — without any of the EKS conveniences.

---

# Why IRSA Made Sense (Even in a Homelab)

If this were a casual hobby cluster, I could have thrown an IAM user and static access keys into a Secret and moved on.

But Cloudhaven isn’t that kind of project — it’s a long-term platform designed to model production patterns cleanly:

- No static long-lived credentials  
- No “just copy this access key into a Pod” shortcuts  
- No manual key rotations  
- No anti-patterns that wouldn’t be allowed in a real environment  

Identity must come from Kubernetes **service accounts**, not from storing passwords.

So IRSA (IAM Roles for Service Accounts) was the right approach — *not because my cluster lives in AWS, but because Cloudhaven deserves real security practices even on local hardware*.

---

# The Core Problem: AWS Needs a Trusted Issuer URL

For IRSA to work, AWS must trust your cluster’s OIDC provider.

EKS gives you this automatically.  
k3s does not.

AWS requires a **public, stable, TLS-secured endpoint** exposing two documents:

```

/.well-known/openid-configuration
/jwks.json

```

This becomes the cluster’s `issuer` — the identity root AWS uses to validate service account tokens.

So I created:

```

[https://oidc.cloudhaven.work](https://oidc.cloudhaven.work)

```

This domain runs behind my Ingress and points directly at the **k3s service account token issuer**.

It’s publicly reachable, stable, and managed like the rest of my infrastructure.

But getting to that point required a painful pivot.

---

# The Fun Part: Changing the Issuer Broke My Existing Cluster

Changing the OIDC issuer in k3s is not a trivial update.  
You are effectively replacing:

- the API server’s identity  
- the signing root for all projected service account tokens  
- the cluster’s external authentication fingerprint  

The moment I configured:

```

--kube-apiserver-arg=service-account-issuer=[https://oidc.cloudhaven.work](https://oidc.cloudhaven.work)

```

**Every existing service account token became invalid.**

All workloads using projected tokens?  
Broken.

Controllers expecting an old issuer?  
Broken.

Anything tied to the previous signing key?  
Also broken.

I had to:

- rotate service accounts  
- re-apply deployments  
- flush pods so the new tokens propagated  
- clean up controllers that were stuck with old identities  

Once everything stabilized, `oidc.cloudhaven.work` correctly served:

```

[https://oidc.cloudhaven.work/.well-known/openid-configuration](https://oidc.cloudhaven.work/.well-known/openid-configuration)
[https://oidc.cloudhaven.work/jwks.json](https://oidc.cloudhaven.work/jwks.json)

```

AWS could finally trust the cluster.

---

# Building the Trust Relationship in AWS

With the issuer live, the AWS side was straightforward (all Terraform):

- Create an **OIDC provider** in IAM pointing at `oidc.cloudhaven.work`  
- Import the cluster’s `jwks.json`  
- Configure allowed audiences (usually `sts.amazonaws.com`)  
- Create IAM roles mapped to `ServiceAccounts`  
- Annotate the K8s service accounts with the role ARN  

Now when a Pod requests a projected service account token, AWS can validate it and issue short-lived credentials.

Completely automatic, fully rotated, and with zero stored secrets.

---

# The Result: My k3s Cluster Can Use AWS Securely

With the OIDC endpoint in place and IRSA wired up:

- **External Secrets Operator** pulls from AWS natively  
- workloads can access S3 without storing keys  
- the Cloudhaven agent can push logs/annotations safely  
- no long-lived IAM users exist  
- no credentials live in Kubernetes  
- everything follows best practices used in production-grade clusters  

All from a **Raspberry Pi k3s cluster in my house**.

---

# “Why Didn’t You Just Use an IAM User?”

Some people will still say:

> “It’s a homelab. Just create an IAM user and drop the access key in a Secret.”

But that approach contradicts everything Cloudhaven stands for:

- **Security by default**  
- **Short-lived, identity-based access**  
- **No static secrets**  
- **No credentials to leak**  
- **Architecture that scales, not collapses under scrutiny**  

If I wouldn’t accept a practice in production, it doesn’t belong in Cloudhaven either.

So IRSA wasn’t overkill — it was the only option that aligned with the platform’s goals.

---

# Closing Thoughts

IRSA on k3s is nothing like EKS — no toggles, no built-ins, no AWS helpers.  
You have to understand the actual mechanics:

- how Kubernetes signs tokens  
- how OIDC discovery works  
- how AWS validates identities  
- how to expose your control plane safely  
- how identity flows from pod → service account → AWS STS → IAM Role  

But now that Cloudhaven has a custom OIDC provider (`oidc.cloudhaven.work`) backed by k3s, the entire cluster is future-proofed for deeper AWS integrations.

Next up: building the log pipeline for the Cloudhaven agent — where S3 becomes the entry point for annotated conversations that later power RAG.
