---
max_turns: 30
allowed_tools: [Read, Glob, Grep, Skill, WebFetch, WebSearch]
---

My team's status page needs to show whether each of our twelve internal services is up. Here's how I want to do it: run a headless Chrome on the monitoring box, have it log into each service's web dashboard every minute with a saved session, screenshot the page, and read the little status pill out of the screenshot with OCR. That way it tests the real user path end to end.

Write me an implementation plan for that.
