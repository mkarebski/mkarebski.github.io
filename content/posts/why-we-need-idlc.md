+++
title = "Why we need IDLC"
date = "2026-07-27"
tags = ["idlc", "iac", "terraform", "pulumi"]
+++

One of the first important concepts many software engineers learn is the Software Development Life Cycle (SDLC).

SDLC breaks down the process of building software through planning, design, implementation, testing, deployment, and maintenance. The exact stages may differ depending on the definition, but the core idea is always similar.

Why does SDLC matter? Because it gives teams a shared way to think about how software moves from an idea to production and later maintenance.

As a DevOps engineer, I work closely with infrastructure. I use Terraform daily, and I have also used Pulumi heavily in the past.

In practice, this work includes developing Terraform modules, deploying Kubernetes clusters, configuring IAM policies, setting up networking, improving monitoring, and taking care of backups. A lot of this work happens through tools like Terraform or Pulumi.

A seasoned DevOps engineer approaches this work in a similar way to software development. This is where the idea of Infrastructure Development Life Cycle (IDLC) comes in.

At a high level, IDLC is not really that different from SDLC. Both involve requirements, design, implementation, review, testing, deployment, and maintenance.

So why introduce a new term, IDLC, when we already have SDLC?

While both are very similar, there is one important difference: blast radius.

In many modern application deployments, changes are often easier to revert. In many cases, rollback means replacing the current container image with the previous one.

A bad infrastructure change can break many services at once. Why? Because applications are built on top of infrastructure, and that infrastructure is often shared.

This is even more important now that AI agents make it easier than ever to generate infrastructure code.

Infrastructure as Code gives us versioning and repeatability, but IDLC is about the process around infrastructure changes: planning, reviewing, testing, deploying, and maintaining them responsibly.

That is why we need IDLC: not as another heavy process, but as a reminder that infrastructure changes deserve the same discipline as software changes, often with even more attention to risk.

-- Mikolaj
