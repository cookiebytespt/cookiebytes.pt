---
layout: post
title: What Rocky taught us about chaos engineering
description: Our Head of Chaos Engineering has deleted two Figma files and one production config. Here's how a cat made our deployment pipeline genuinely more resilient.
category: crew
emoji: 🐱
read_time: 5 min read
lede: Netflix has Chaos Monkey. We have a cat. Rocky's job title — Head of Chaos Engineering — started as a joke after he deleted a production config by sleeping on a keyboard. Then we realized he'd found a real gap, and the joke became a discipline.
author_emoji: 🐱
author_note: Reviewed by Rocky, who walked across the draft twice and deleted a paragraph. It was probably the weakest one.
---

## The incident

A Tuesday afternoon. Rocky settles onto a laptop, as cats do. Twelve minutes later, a config file in a production repo has been overwritten with what can only be described as `jjjjjjjjjjjjj4`, committed (autosave plus a very unfortunate keyboard shortcut), and deployed by our pipeline — which validated nothing.

The service went down. The rollback took 40 minutes because we'd never actually rehearsed one. Rocky slept through the entire recovery.

## What the cat exposed

Every part of that failure was ours, not his. The pipeline deployed config changes without schema validation. Nothing alerted us until users did. And our rollback was a wiki page last updated two quarters earlier. Rocky didn't break the system — he revealed it was already broken, cheaper than a real outage with a real client watching.

<div class="callout">🐾 Chaos engineering in one sentence: if a cat on a keyboard can take down production, production was the problem, not the cat.</div>

## What changed

Config files now pass schema validation before any deploy — a malformed value fails the pipeline, not the service. Deploys are staged with automatic rollback on failed health checks. And once a month we run a "Rocky drill": someone intentionally breaks a staging environment in an unannounced way, and we measure how long detection and recovery take. The first drill took us 35 minutes to even notice. We're now under four.

## The honest lesson

Most small teams skip resilience work because it never feels urgent — until the outage, when it's suddenly the only thing that matters. You don't need Netflix-scale tooling. You need validation gates, rehearsed rollbacks, and something unpredictable applying pressure. We happen to have that last part covered in-house, ginger, and fundamentally unrepentant.

<div class="divider">🍪 🍪 🍪</div>

Want your pipeline cat-proofed? [Talk to us](/#contact) — DevOps audits are one of our favorite jobs, and yes, Rocky consults.
