---
date: "2025-03-20"
draft: false
title: "Automating Arch Linux with Ansible"
---

> _Why, yes; I do use Arch._

This is a quick writeup for [arch-btw](https://github.com/yamlinson/arch-btw),
an Ansible project I use to manage the configuration of my Arch Linux workstations.

## Introduction

A while back, I decided to take the plunge and install Arch Linux on my main PC.
After leaving Windows for GNU/Linux a couple years ago, I bounced around between a few different Linux distros.
Ultimately, my decision to move to Arch came down to two main reasons:

- Frustration with the bloat included in most distros
- Wanting to learn Linux a little closer to the metal

I don't mean for this to be a jab at any specific distribution, so I'm not going to mention any.
I don't think any major distribution is "bad" at all.
In fact, I'm pretty amazed at how many features their developers are able to pack into them.
I just reached a point where I wanted more control over my operating system
and the experience of customizing it to be exactly what I want (and nothing more.)

Another major factor was the Ansible skills I had been developing over the last few years.
I had largely stayed away from any major customizations to my environment up to this point
because I was bad at documenting them and hadn't put the effort into automating them.
Without either of those things in place,
the idea of transferring significant customizations to a new machine or replicating to my other machines
sounded like a nightmare.
With some decent Ansible chops built up, I felt more confident in my ability to maintain this project long-term.

As a side note, I think a lot of people recognize Ansible's value in managing fleets of servers,
but I think it is unfortunately overlooked as a tool for managing smaller environments,
or even single machines.
I think it has a lot of value as a solution to both of the hurdles I mentioned above.
It obviously addresses the automation, but is also pretty effective at self-documenting.
An Ansible playbook doesn't read quite as well as plain English,
but with a little time to familiarize with a project's structure, it shouldn't be too hard to follow.

## Inspiration

This project was heavily inspired by [Jeff Geerling](https://www.jeffgeerling.com/)'s
[Mac Development Playbook](https://github.com/geerlingguy/mac-dev-playbook).
In the future, I plan to start another playbook to manage my MacBooks.

## Digging In

This project isn't really intended to be a portable solution for anyone but me,
so I want to talk more about how it came to be and why I did what I did,
but I think there is value in a quick look at what it looks like in practice.

### How I Use The Project

Configuration begins from a fresh installation of Arch with a few key packages installed during provisioning.
The README in the source linked above gives a brief rundown of the initial bootstrap steps needed.

Post-install, I use the 'init.yml' playbook to perform bootstrapping actions needed for later tasks,
primarily configuring a non-root administrative user.
I use [yay](https://github.com/Jguer/yay) as an AUR helper and frontend for pacman,
and yay requires use of a non-root account for security.

From my new admin account, I run the 'main.yml' playbook, which pulls together all of my roles and tasks.
When this is done, my machine is mostly configured and ready for use.
There are a few manual actions I need to complete after this point, such as logging into my password manager.
I recently moved my dotfiles to a separate repository managed with Chezmoi and have not yet included their setup
in this project.
Using Chezmoi, my dotfiles are still automated, but it's an extra step and leaves room for improvement.
This is on my short list for future additions to the project.

### How The Project Has Grown

I started this project as I was transitioning to using Arch as I knew I would be doing
a lot of tinkering and rebuilding.
I wanted a quick way to always get back to a checkpoint, so to speak,
getting straight back to forward progress after a reset.

I had been using Linux for years, but mainly in the form of mainstream distro's like Ubuntu, Fedora, and PopOS,
at least on my workstations.
As such, I hadn't considered how much goes into those distributions and what I was taking for granted.

Looking back through the commit history (roughly a year long at the time of writing)
shows the organic growth of the project as I slowly pieced together my perfect system.
Over time, I started from a very simple configuration and slowly added in pieces as I found them.
My needs in a workstation honestly aren't very extreme, and I would still consider this a pretty simple project,
but it has grown with me as I have formed stronger opinions about how a Linux machine should run.

### Inventory Files as Keys

A core aspect of this project is the use of inventory files as a sort of key to the configuration of each machine.
Depending on the machine I'm setting up (test laptop, cheap travel laptop, main desktop, etc.),
I use a separate 'hosts.yml' file containing only the specific config for that machine.

If I want to install a new package, I just modify my local 'hosts.yml'.
These changes are still tracked, just in a separate, private repository.
That lets me keep me specific machine configuration private,
but also separates out noise from this project, whose focus is the framework, not the specific machines.

For each machine using the framework, I also pull down the private inventory repo and link the relevant
file into this project space as 'hosts.yml'.
The file is ignored by this project, and changes are made to the other file to be tracked.

### (Not) Automating Initial Install

All of the manual steps of the initial OS installation could, of course, be automated too.
I considered starting here, but decided to prioritize the Ansible piece instead.
It is still on my roadmap to do this at some point though.

I found a great existing project which already handles the entire install in a very robust way,
and I did consider using it, but my focus with this project was less on the end product
and more on the experience.
It was more important to me to work through solving this problems on my own, in my own ways,
than to having the absolute best product in existance.

This is largely in line with why I switched to Arch in the first place:
more fine-grained control, sure, but also forcing myself to learn by not using someone else's polished solution.

### Generalized Tasks, Specific Roles

The primary general tasks used in this playbook are centered around users and packages.
These are simple tasks that can be expected to be needed on any system.

For more specific needs, I broke tasks out into roles. This includes setting up Nvidia drivers,
something I don't need on every machine
(or actually any machine at time of writing...
I finally bought a Radeon GPU last year for better compatibility with Wayland),
Bluetooth, rebinding caps lock, etc.
Whether these roles are run on a machine is controlled by a boolean flag in the 'hosts.yml' file.

## Closing Thoughts

I hope this inspired you to rethink how you manage your own machines.
If you haven't already, I really think you should start a similar project.
It can be for whatever applies to you.
Maybe you want to keep using Fedora, but don't want to reset display preferences in a KDE GUI
any time you set up a new computer.
Maybe you've always wanted to change some hotkeys, but didn't want to have to think about maintaining them.
Start small! The project will grow with you.

I also hope this might change some perception around Ansible and open more people up to using it
at a smaller scale.
I have also worked in the middle ground on this, using principles similar to the ones in this project to manage single
or low double digits of hosts; small enough environments where everything **could** be maintained manually,
but where a little time investment writing Ansible playbooks has paid huge dividends in time saved down the road.
