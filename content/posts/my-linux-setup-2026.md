+++
title = "My Linux Setup (2026)"
date = "2026-08-05"
tags = ["linux", "workflow", "tooling"]
+++

NOTE: I plan to write similar posts whenever my setup changes, while keeping previous versions for reference.

I have been using Linux as my main development environment for the last nine years, and over time I have built a setup that feels simple, practical, and hard to move away from.

For the past few years, Pop!_OS has been my primary system. I keep coming back to it for a few boring but important reasons: it has been reliable, NVIDIA drivers are available out of the box, and its battery management has been good enough that I do not feel like I am constantly fighting my machine.

Most of this setup is documented in my `my-linux-setup` [GitLab repository](https://gitlab.com/mkarebski/my-linux-setup). It is less of a polished dotfiles project and more of a personal checklist: the things I know I will need when setting up a machine from scratch.

## Shell tooling

The terminal is where I spend most of my time, so it is usually the first part of my setup that I configure.

- `WezTerm` for the terminal itself
- `zsh` with Oh My Zsh for shell ergonomics
- `fd` for faster file discovery
- `eza` as a nicer replacement for `ls`
- `glow` for reading Markdown in the terminal

I keep the WezTerm config minimal. I mostly care about sane defaults, a dark background, and a clean look rather than heavy customization.

## Battery and power optimization

Some parts of the setup are less about aesthetics and more about making the laptop behave well every day.

- `auto-cpufreq` for managing CPU frequency and power consumption
- a small systemd service that selects the battery profile on Pop!_OS
- `powertop` for applying power-saving tunings

I do not want Linux on my laptop to become a side hobby. I want it to be stable enough that I can focus on work instead of constantly fixing battery drain or power-related issues.

## Work tooling

The rest of the setup reflects the kind of work I usually do as a DevOps engineer:

- Docker for local containers
- `kubectl` and `k9s` for Kubernetes work
- `gcloud` for working with Google Cloud projects and services
- Terraform for provisioning and managing infrastructure as code
- JetBrains IDEs for software development
- OpenCode as an AI-assisted development tool

These are not exciting choices, but they are part of the baseline. If I am setting up a fresh machine, I want to get from zero to a usable development environment quickly.

## Small quality-of-life details

There are also a few smaller things in the repo that are easy to overlook but useful in practice:

- generating SSH keys
- adding wallpapers
- fixing the JetBrains Wayland "new file" issue

My Linux setup is not particularly exotic. It is mostly a set of practical defaults that let me work comfortably: a good terminal, a shell I like, a few useful CLI tools, container and cloud tooling, and an operating system that stays out of the way.

That last part matters most. The reason Pop!_OS has remained my primary system is not that it is new or exciting. It is because it has been dependable for years, works well with NVIDIA hardware out of the box, and handles battery management well on a daily basis.

-- Mikolaj
