---
title: "env.sync.local - Syncing API keys and secrets between devices in my home LAN"
datePublished: Tue Feb 10 2026 01:02:38 GMT+0000 (Coordinated Universal Time)
cuid: cmlfwcf1m000102jid1kr9s1m
slug: envsynclocal-syncing-api-keys-and-secrets-between-devices-in-my-home-lan
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1770685146265/133b6616-42dc-418e-ad70-c587ff2b40ff.png
tags: encryption, ssh, secrets, scp

---


My desk is a heterogeneous graveyard of differing architectures. I have a MacBook Pro for daily work, a Razer laptop that mostly serves as an expensive space heater for Windows testing, and a Beelink MiniPC that oscillates between Linux and Windows depending on how much I want to fight with local LLMs that day. It is a great playground for testing but an absolute nightmare for managing secrets.

The moment you generate a Google Maps API key on one machine, you have started a countdown. Eventually, you will need that key on the other two machines. Usually, this involves the shameful act of Slacking a secret to yourself or the tedious ritual of digging through a password manager. I just wanted the keys to exist everywhere, as if by magic.

So I build **[env.sync.local](https://github.com/championswimmer/env.sync.local)** - go check it out and install from my Github. 

### The Problem: The Copy-Paste Tax

We have been conditioned to accept the "Manual Copy-Paste" tax as a cost of doing business. When you work across three different operating systems, syncing a `.env` file usually involves a game of telephone with insecure intermediaries. 

I needed to scratch a few very specific itches. First, no master server. I didn't want to maintain a "secrets vault" or a central authority. If any two of my machines are online, they should be able to talk without a middleman. Second, zero configuration. If I buy a new machine, I should be able to run an install script and have it automatically discover the rest of the mesh. Finally, security at rest. Secrets should not sit in plaintext on my disk, even if the disk is encrypted.

### Architecture: Discovery and Transport

The first hurdle was discovery. Static IP addresses are a relic of the past in a home network context. I leaned on mDNS (Bonjour/Avahi). It is a beautiful protocol that allows machines to shout "I am here" into the local void. By registering an `_envsync._tcp` service, any node can find its peers without a config file. No more hardcoding IP addresses like it is 1998.

For the transport layer, I started with a simple HTTP server fallback, but SCP (over SSH) is the real winner here. It provides authenticated, encrypted pipes out of the box. If I can already SSH into my MiniPC, `env-sync` can just piggyback on that existing trust to move files. It is pragmatic, it uses existing infrastructure, and it works.

### The Crypto: AGE and Multi-Recipient Encryption

Initially, the project was just moving plaintext files. That felt wrong. If someone stole my Beelink, they would have the keys to my entire kingdom. I moved to AGE (Actually Good Encryption) for at-rest security. 

The challenge with a decentralized system is key management. If there is no central server to manage a shared secret, how does a new machine join the party? I implemented a multi-recipient model. Every machine generates its own X25519 key pair. When you add a new machine, it broadcasts its public key. The existing machines discover this new key and re-encrypt their secrets to include the new recipient.

I added a "remote trigger" feature to solve the bootstrap problem. A new machine can SSH into an existing peer and say: "Here is my public key, re-encrypt everything for me and let me sync." This makes it zero-config for the existing nodes and provides instant gratification for the new one.

### Evolution: From Bash to Go

The project started as a collection of Bash scripts. Bash is the ultimate glue. It allowed me to build the MVP in a single weekend. But Bash has very low ceilings. Once you start managing background daemons, parsing JSON from mDNS tools, and fighting with cross-platform pathing on WSL2, you are in for a world of pain. Shell quoting is a dark art that I would rather not practice daily.

I migrated the entire core to Go for a few specific reasons. First, the single binary. I wanted a statically linked artifact that I could drop onto any machine without worrying about dependencies. Second, native AGE. Using the `filippo.io/age` library meant I didn't need the `age` binary installed on the host. Finally, concurrency. Go's select loops and goroutines made managing the mDNS listener and the sync cron significantly more robust.

The result is a system that feels invisible. I add a key on one machine, and by the time I have walked over to my other desk and opened a terminal, the key has already propagated. It is eventually consistent, decentralized, and stays out of my way.

### The Nerd Stats

For those who want the technical specs:
- Discovery: mDNS via `_envsync._tcp` on port 5739.
- Transport: SCP/SSH (Default) or HTTP (Fallback).
- Encryption: AGE (X25519).
- Conflict Resolution: Per-key timestamps (latest write wins).
- Distribution: Peer-to-peer gossip-adjacent sync.

We spend so much time automating our CI/CD pipelines for production, yet we often leave our local development environments in the stone age. `env-sync` is my attempt to bring a little bit of that automation home. If it saves me from one more "where is that API key?" hunt, it has already paid for itself.
