---
layout: post
title: Five Home Assistant automations we install in every smart home
description: After dozens of installs, these are the automations that clients actually keep using six months later — plus the ones we quietly stopped recommending.
category: tech
emoji: 🏠
read_time: 7 min read
lede: After dozens of Home Assistant installs, a pattern emerged — clients love automations for two weeks, then quietly disable most of them. These five are the ones that survive. They're boring, and that's exactly the point.
author_emoji: 🏠
author_note: Field-tested in real homes, including our own — where the leak sensor's first catch was Billy's water bowl.
---

## 1. Lights that follow the sun, not a schedule

Fixed-time lighting schedules break twice a year and feel wrong most evenings. Instead, we trigger evening lights on `sun.sun` elevation — typically when the sun drops below 1.5° — and ramp brightness gradually. Nobody ever notices this automation, which is the highest compliment an automation can get.

## 2. The "everyone left" reset

One automation, triggered when the last person's presence drops: lights off, thermostat to eco, media players paused, and a notification only if a window was left open. We use person entities grouped into a `zone.home` count rather than device trackers directly — phones lie, and Wi-Fi presence flaps when someone's phone sleeps.

<div class="callout">💡 The key detail: a 10-minute delay with a cancel condition. Without it, taking out the trash turns your house off.</div>

## 3. Morning modes, not morning alarms

Instead of "at 7:00 do X", we set up an `input_select` for house mode (sleeping, waking, day, evening, away). Automations react to the mode change, and the mode itself can be flipped by time, first motion in the kitchen, or manually from a dashboard. Decoupling "what happens" from "when it happens" is the single biggest maintainability win in Home Assistant.

## 4. Water and leak alerts that escalate

Leak sensors under the sink, behind the washing machine, near the water heater. First alert: phone notification. No acknowledgment in 5 minutes: notification to everyone in the house plus lights flash red. If a smart valve is installed, shut the water after 10. The escalation ladder matters — a single silent notification at 3 a.m. protects nothing.

## 5. The "is everything okay?" nightly digest

At 22:00 the house sends one message: doors locked or not, windows open or not, batteries below 15%, and any entity that's been `unavailable` for over an hour. One message, once a day. This replaces the dozen nagging notifications that make people mute the app entirely.

<div class="divider">🍪 🍪 🍪</div>

## What we stopped recommending

Motion-triggered lights in living rooms (great in hallways, infuriating on movie night), voice announcements for routine events (novelty wears off in days), and any automation that requires the client to remember it exists. If it needs a manual, it's a feature, not an automation.

Thinking about a Home Assistant setup? [We do installs](/#contact) — sensors, dashboards, escalation ladders and all.
