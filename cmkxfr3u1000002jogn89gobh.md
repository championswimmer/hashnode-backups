---
title: "sideproject diaries: sharetime.zone - A Simple Timezone Sharing Tool"
datePublished: Wed Jan 28 2026 02:58:19 GMT+0000 (Coordinated Universal Time)
cuid: cmkxfr3u1000002jogn89gobh
slug: sideproject-diaries-sharetimezone-a-simple-timezone-sharing-tool
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1769569015718/4ecabc92-93ed-44b8-b1a4-0680ced9d70b.png
tags: vuejs, netlify, timezone

---

If you have friends or clients scattered across the globe, you know the drill. You agree to meet at "5 PM," but then follows the inevitable dance of clarification: "My 5 PM or your 5 PM?" "Wait, is that daylight savings?" "Let me just send you a calendar invite so we don't mess this up."

I built [sharetime.zone](https://sharetime.zone) because I found myself in this loop way too often. Whether it was catching up with friends or consulting with clients in different locations, I needed a way to just say, "Here is the time," without the mental math.

## The Idea: Simplicity First

The core idea is dead simple. I wanted to be able to share a link that instantly communicates a specific time in a specific timezone, converted to the viewer's local time.

Instead of opening a timezone converter app, selecting cities, and copying a long generated link, I wanted something I could type out manually if I had to.

So, `sharetime.zone` works like this:

**[sharetime.zone/IST/1700](https://sharetime.zone/IST/1700)**

That's it. You click that link, and it tells you exactly what 5:00 PM Indian Standard Time is in *your* local timezone. No friction, no confusion.

## For the Developers: It Works in cURL

Since I spend most of my time in the terminal, I didn't want to leave the command line just to check a time. I figured, why not make it work with `curl`?

I added support for cURL user agents, so you can literally fetch the time from your terminal:

```bash
curl sharetime.zone/IST/1700
```

And it returns a plain text response with the converted time. It's a small touch, but it makes the tool significantly more useful for my workflow.

## What I Learnt Building This

While the app looks simple on the surface, timezones are notoriously tricky. Here are a few things I picked up along the way:

### 1. Edge Functions are Powerful
To make the cURL feature work fast and detect the user's location instantly, I turned to **Netlify Edge Functions**. This was a great opportunity to dive into how they work. 

I learnt that Netlify uses **Deno** for their edge functions. This meant I couldn't just drop in my usual Node.js code; I had to adapt to the Deno runtime environment. It was a fun challenge to figure out how to read the client's geo-point (specifically their timezone) directly from the request context (`context.geo`) and respond immediately from the edge, closer to the user.

### 2. Timezones with Luxon
For the frontend, I leaned heavily on the **Luxon** library. Handling timezones "correctly" is a rabbit hole of edge cases—Daylight Saving Time (DST) shifts, ambiguous abbreviations (is "CST" Central Standard Time or China Standard Time?), and historical changes.

Luxon made this manageable, but it also taught me that you can't just assume a timezone offset is constant. You have to calculate it for the specific instant in time you're talking about.

## Wrapping Up

This was a fun weekend project that solved a real itch for me. It’s open source, so feel free to poke around the code or fork it if you want to add support for your favorite obscure timezone.

Give it a try next time you're setting up a meeting: **sharetime.zone**
