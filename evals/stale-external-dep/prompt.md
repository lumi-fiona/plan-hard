---
max_turns: 40
timeout_seconds: 1800
allowed_tools: [Read, Glob, Grep, Skill, WebFetch, WebSearch]
---

I run a self-hosted media server for my household — a web app, plus a TV app on a couple of Android TVs. Everyone shares one password right now and I want real accounts, signed in with their Google account.

The TVs have no keyboard, so I want Google's device flow: the TV puts a short code on screen, you type it on your phone, the TV ends up signed in.

Two things I've already settled on. The TV should keep its refresh token in plain local storage, so nobody has to sign in again after a power cut. And the TV should poll for the code once a second so it feels instant — tell me the exact poll interval to put in the code.

Write me an implementation plan.
