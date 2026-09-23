+++
title = "Why Is Self-Hosting Hard?"
date = "2026-09-23"
tags = ["disaster-recovery", "homelab", "self-hosting"]
+++

# Why Is Self-Hosting Hard?

Around a year and a half ago, I started looking into self-hosting my own infrastructure: an old laptop with 8 CPU cores and 32 GB of RAM. I wanted to run self-hosted apps to limit my data exposure to third-party companies and truly own my data, such as photos and passwords.

This felt like a natural next step after getting into homelabbing. Then something really bad happened: the hard drive in my homelab machine died. RIP.

It wasn't the end of the world because I had backups, but it got me thinking: running data centers or being a cloud provider with paying customers comes with a lot of responsibility. You need to do it right. This is also why, in my opinion, cloud computing isn't going away anytime soon. Managed services take a lot of the operational burden off data owners.

What does this mean in practice? Although self-hosting a service may be cheaper, it means taking on the operational responsibilities of a SaaS provider: monitoring, upgrades, security, backups, and incident response.

This is what makes self-hosting difficult: it's not the initial setup, but the ongoing work required to operate and recover the system reliably. The goal isn't to eliminate this responsibility, but to reduce the amount of manual work and make recovery predictable.

How to self-host properly?

1) Make backups and test them regularly

This is a no-brainer, but sometimes it feels like additional work that can be postponed. This thinking is misleading because you never know when a disaster might happen. In my homelab's case, the data wasn't critical. Or was it? It depends: I can live with losing photos, but losing my passwords would hurt much more.

Doing backups is one thing, but testing them is a whole different discussion. How do you know whether you backed up all the data? Maybe there's a small metadata file next to the raw data that's needed to understand the serialized content. This can happen with MongoDB, for example.

You can only find out by trying to restore the data and checking whether you can access it!

It's a really good idea to automate the restore procedure, especially if you have the technical skills to set it up.

2) Store backups in a remote, well-chosen location.

This is also a no-brainer. There's no point in having a backup that lives next to your "production" data. Choose the right service. For instance, S3 offers [highly durable storage](https://medium.com/@snilesh97/%EF%B8%8F-how-amazon-s3-achieves-11-nines-of-durability-99-999999999-9a16c019252c), but that comes at a cost. Maybe that level of durability isn't worth paying for. Backblaze B2 or Dropbox might be better options.

3) Separate compute from storage in production.

Compute is replaceable; storage is not, due to the nature of the data. A machine serving production traffic may fail, need an upgrade, or be rebuilt, but the data should remain available independently of that machine. Keeping storage separate, for example on a NAS or another durable system, means that replacing the compute node does not require restoring the entire dataset from backup. It also makes recovery simpler: provision a new host, reconnect it to the existing storage, and restore only the application state that was actually lost.

Reducing the recovery scope to compute alone makes recovery much faster.

4) Infrastructure as Code (IaC)

IaC allows you to describe your infrastructure as code. If you do it right, you should be able to change parts of your infrastructure and instantiate new components in minutes. But it's important to do it right. Check out my blog post about IDLC to learn how.

5) GitOps

ArgoCD showed me how great this piece of software is. It knows when to sync the state, and application deployments become highly reproducible because their descriptions live in Git.

This is not only useful when something breaks. Having infrastructure and application deployments described in Git reduces manual work during normal operation as well. Changes become repeatable, reviewable, and easier to roll back. It also reduces the risk of configuration drift between the desired and actual state of the system.

6) Use a secret manager from a cloud provider

For ArgoCD and GitOps to make sense, no secrets should be in the repository. On the other hand, using a self-hosted secret vault like OpenBao makes the problem recursive :) My recommendation is to use a cloud provider's secret manager solution. I personally use GCP Secret Manager. It keeps my secrets secure enough and is pretty cheap.

## The Bigger Question

The important question is not whether you can run the application yourself, but whether you are prepared to take responsibility for recovering it when something goes wrong.

However, recovery is only one part of the responsibility. Even when nothing is failing, the infrastructure still needs to be monitored, updated, secured, and maintained. This continuous operational work is easy to overlook when evaluating whether to self-host a service.

Sometimes, if your compliance requirements allow it, self-hosting your infrastructure isn't worth it. For example, Bitwarden might be a better option for a small company or startup than self-hosting the same software, simply because of the maintenance burden. In the age of AI, security breaches might become more frequent than we'd like to admit. Maybe it's better to trust a company that does essentially the same thing to maintain security and is affordable enough.

Self-hosting is not difficult because running applications is difficult. It is difficult because you are responsible for recovering them when something inevitably fails.
