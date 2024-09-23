---
layout: post
title: Self-hosting RustDesk servers
subtitle: for a modern FOSS remote desktop application
author: Brandon Victor
readtime: true
date: 2024-09-23
tags: software-tools
---

So, TeamViewer or whatever other remote desktop software has annoyed you too much. Either you are frustrated with them as a company, or, like me, they decided you were using it for commercial purposes due to overuse and kicked you out. What are you to do? If you're anything like me, you want to see what FOSS options are available. And, ideally, you want to self-host.

One of the options that comes up is RustDesk. Seems good. Let's use that. How do we use it? [Here](https://rustdesk.com/docs/en/) are their docs. I found it to be a little difficult to follow. They spend a lot of time talking about hosting solutions and Pro version. But, it turns out the basic usage is quite straightforward once you know it. Hence this post.

## Basic idea

Each computer involved needs a RustDesk client application. These client applications will then communicate with each other through two separate RustDesk servers: the id server (called `hbbs`) and the relay server (called `hbbr`).

So, we need to install a few things. On each computer, the client. On one of the computers, those servers.

## Install

The installers for these are available as github releases: [Client](https://github.com/rustdesk/rustdesk/releases) and [Servers](https://github.com/rustdesk/rustdesk-server/releases). I used the debs:

```bash
# Client
wget https://github.com/rustdesk/rustdesk/releases/download/1.3.1/rustdesk-1.3.1-x86_64.deb
sudo apt install -y rustdesk-1.3.1-x86_64.deb

# Servers
wget https://github.com/rustdesk/rustdesk-server/releases/download/1.1.11-1/rustdesk-server-hbbs_1.1.11-1_amd64.deb
wget https://github.com/rustdesk/rustdesk-server/releases/download/1.1.11-1/rustdesk-server-hbbr_1.1.11-1_amd64.deb
sudo apt install -y rustdesk-server-hbbs_1.1.11-1_amd64.deb rustdesk-server-hbbr_1.1.11-1_amd64.deb
```

Note that once installed, both the client and servers will all be set to auto-start with the computer.

## Configure the client

In the client application, go "Settings"->"Network". You need to fill "ID server", "Relay server" and "Key".

![RustDesk configuration UI](/assets/img/2024-09-23-rustdesk-client-config.png)

You only need to provide a hostname for both "ID server" and "Relay server"; the port is optional. e.g. you can use "10.192.168.1" for both.

On Ubuntu, the key can be obtained with:

```bash
cat /var/lib/rustdesk-server/id_*.pub
```

Click "Apply". It should tell you that it was successful.

Repeat for both client applications.

## Connect

You should now be able to connect between the machines using the IDs on the landing page.

![RustDesk landing page](/assets/img/2024-09-23-rustdesk-landing.png)

That's it. Enjoy.
