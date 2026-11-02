---
layout: post
title: A Weekend of Taking a Network Apart
date: 2026-11-02 12:00:00
description: Notes from a weekend of curiosity about proxies, virtual network cards, and how a tunnel can hide inside an ordinary-looking HTTPS connection.
tags: networking linux proxies learning
categories: learning-notes
giscus_comments: false
---

I use the internet all day and could not have told you what happens between pressing Enter and a page loading. I knew the words — routing, proxy, handshake, firewall — but not what any of them actually *do*. I couldn't explain how a tunnel could sit inside a connection that looks like a normal visit to a big website.

So one weekend I decided to find out by building one. I rented a small VPS, used my own Linux laptop, and put a tunnel together one layer at a time, with debug logs turned all the way up so I could watch each layer do its job. I didn't write the protocols; I used Xray-core and tun2socks. The point was never the finished tunnel. It was understanding every piece underneath it, and finding out what breaks when you get a piece wrong.

Here's what I learned, mostly in the order I learned it.

## A SOCKS proxy is a doorway you have to choose to use

A SOCKS proxy sits on a local port. An app connects to it, says "please connect me to this address and port," and the proxy relays the bytes in both directions. It doesn't care what's inside them.

That cleared up something that had always bugged me: why my browser would follow a proxy setting while other programs ignored it completely. Proxy support isn't a property of the network. Each application decides whether to implement it. Browsers do. Plenty of other programs simply never walk through the door.

A small detail that turned out to matter: `socks5://` and `socks5h://` differ by one letter, and that letter decides whether DNS lookups happen inside or outside the tunnel. I had a proxy variable set to a bare `socks://` for a while, and a program on my machine misread it and sent a raw TLS handshake at a port that expected something else. The logs for that were a wall of garbled bytes until I understood what they were.

## If apps won't use the door, change the building

The fix for apps that ignore proxies is a **TUN device**, which is a network card that exists only in software. Instead of putting packets on a wire, it hands them to a program. Add two routes that together cover the whole IPv4 address space, and every packet on the machine ends up there, whether the app knows about a proxy or not. A tool called `tun2socks` then turns those raw packets into SOCKS requests.

The part that taught me the most was a trap in that design. If *everything* goes into the tunnel, the tunnel's own packets to the server go into the tunnel too, and it loops on itself until the network dies. Picture a courier service where the rule says every vehicle in town must use the courier's tunnel, including the courier's own van. The fix is one extra route that sends traffic for the server's IP out the normal way. Thinking it through that way made routing tables finally make sense to me.

## Two layers with two different jobs

This was the part I was most curious about.

**VLESS** is a deliberately tiny protocol. Each connection starts with a header carrying a UUID (the password) and the destination you want, and then data flows. It does no encryption of its own.

**Reality** is what makes the secure channel, and it does something clever with an ordinary TLS 1.3 handshake: it hides an authentication token in the field that normally holds random bytes. A client that knows the secret gets the tunnel. Anyone else gets quietly forwarded to a real website, so the server looks like an ordinary one. My mental picture is an unmarked armored van (Reality) carrying a delivery slip (VLESS).

I could check this against my own logs instead of just reading about it. The session-ID bytes the client printed decoded into the client version, a timestamp that matched the log line, and my short ID, and the server printed the same three values back. Meanwhile, random scanners from around the internet kept knocking on the server's port and being turned away as `invalid connection`. Every public server gets this constantly, and I had never seen it before because I'd never looked at a log.

## Most of the learning came from things going wrong

Honestly, this is where I learned the most, and where I was wrong the most.

- **Failure modes carry information.** A *timeout* means packets vanish on the way. An `EOF` means the connection opened and was then closed. A certificate error naming a domain that isn't mine meant something *else* was answering. Each rules out a different part of the stack.
- **A cascade of ordinary mistakes.** A placeholder left in the address field, a private IP where a public one belonged, a download for the wrong CPU architecture, command-line flags with one dash instead of two.
- **The longest bug.** The keys and short IDs were all correct, and the handshake still failed. The server's log showed it failing *after* authentication succeeded. I chased the wrong suspects first: a firewall, a post-quantum key exchange, the client fingerprint. What fixed it was changing the site the server imitates from one with a huge certificate to one with a tiny one. I suspect the large certificate was the cause, but I never proved the mechanism, so I've left it as a suspicion in my notes.
- **A test that lied to me.** One "proof" that the tunnel worked was invalid, because a proxy variable was still set in my shell and the request never touched the tunnel. The clean version of the test was checking that the destination appeared in the tunnel tool's own log.

## The wall I didn't see coming

Once ordinary web traffic worked, anything UDP-based (voice chat, game launchers, peer-to-peer) wouldn't connect, even though DNS worked. I confirmed the laptop side was fine, then captured packets on the server itself: the request left the machine and no reply ever came back. Nothing on the server was blocking it.

The answer, which I only found by reading the provider's documentation, was that free-trial accounts are locked to a short list of ports at the *provider's* network, outside the server entirely, no matter what firewall rules you add. I had spent a while blaming my own rules and theorising about the other side.

The lesson was less about networking than about method: **find where a packet actually disappears before you decide why.**

## What I'm taking away

- How SOCKS works, and why UDP and proxy-ignoring apps need a different approach
- How TUN devices, routing tables and `tun2socks` capture all traffic, and how routing loops happen
- How a TLS 1.3 handshake can carry an authentication token, and what a server does with a visitor who lacks it
- Why a protocol like VLESS can stay simple when the layer beneath it handles the encryption
- How to read debug logs from both ends of a connection and line them up
- How to disprove a hypothesis quickly, and to write down which parts I verified and which I only believe

Networking used to feel like magic that happened somewhere behind the screen. Now I can follow one `curl` request through a routing table, a translator, a proxy, a disguised handshake and a server, with a log line at every step. It also changed how I debug: several of my first explanations were wrong, and I now prefer a test that can tell two ideas apart over a theory that merely sounds right.

A note for anyone who wants to repeat this: I ran everything on a server I rented and devices I own, purely to learn. Check the rules of the networks you use and your hosting provider's terms first.

*Tools used: Xray-core, tun2socks, Linux (`ip route`, systemd, `tcpdump`, `journalctl`), a rented VPS, and a few small Python and Bash scripts.*

