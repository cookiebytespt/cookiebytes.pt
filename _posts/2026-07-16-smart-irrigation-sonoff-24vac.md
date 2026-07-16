---
layout: post
title: "Smart garden irrigation with a Sonoff 4CH Pro and Rain Bird valves"
description: How we built a four-zone smart irrigation controller from a Sonoff 4CH Pro R3, a 24 VAC transformer and Rain Bird electrovalves — full wiring guide included, so you can replicate it.
category: tech
emoji: 💧
read_time: 8 min read
lede: Commercial irrigation controllers are either dumb timers or expensive cloud boxes. We wanted four watering zones we could control from Home Assistant, so we built our own from a Sonoff 4CH Pro R3, a 24 VAC transformer and Rain Bird valves — for well under 100 €. Here's the full recipe.
author_emoji: 💧
author_note: Tested all summer in a real Portuguese garden. Cookie supervised the trenching. Rocky attempted to supervise the wiring and was escorted out.
---

## 💡 The idea

An automatic irrigation system is really just three things: electrovalves that open water lines, something that switches them on and off, and a schedule. The valves are a solved problem — Rain Bird has been making 24 VAC solenoid valves for decades. The "something that switches them" is where commercial controllers get you: clunky interfaces, proprietary apps, no integration with the rest of the smart home.

A Sonoff 4CH Pro R3 solves that neatly. It's a four-channel Wi-Fi relay with **dry contacts** — each relay is a plain isolated switch, electrically independent from whatever powers the board. That means it can switch a completely separate low-voltage valve circuit, which is exactly what irrigation needs. Four relays, four watering zones.

<div class="divider">💧 💧 💧</div>

## 🎛️ Why this beats an off-the-shelf controller

A conventional irrigation controller does one thing: run zones on a timer. This build is a set of open building blocks, and that makes it far more customizable:

**Control from anywhere, your way.** Out of the box the Sonoff works through the **eWeLink app** — schedules, timers, manual control from your phone. It also comes with **433 MHz RF support**, so a cheap remote can trigger zones from the garden without touching a phone, and the physical buttons on the unit itself still work when the Wi-Fi doesn't.

**Plugs into Home Assistant.** Each zone becomes a switch entity (the eWeLink integration works out of the box; the board is also flashable with ESPHome or Tasmota for fully local control). From there, irrigation stops being a timer and becomes automations: water at dawn when evaporation is lowest, skip the run when the forecast says rain, water longer during a heat wave based on an outdoor temperature sensor, and get a notification if a zone ran longer than expected.

**Grows with you.** Need a fifth zone? Add another Sonoff. Want a soil moisture sensor to decide whether to water at all? It's one more entity in the same automation. No conventional controller lets you rewrite its logic — this one is *only* logic.

<div class="divider">💧 💧 💧</div>

## 🛒 Shopping list

Everything used in this build:

<div class="shop-grid">
  <div class="shop-item">
    <span class="shop-emoji">🎚️</span>
    <strong>Sonoff 4CH Pro R3</strong>
    <p>4-channel Wi-Fi + RF relay with dry contacts — the brain of the system.</p>
    <a href="https://amazon.es/-/pt/dp/B08DKW3JV5" target="_blank" rel="noopener">View on Amazon →</a>
  </div>
  <div class="shop-item">
    <span class="shop-emoji">⚡</span>
    <strong>24 VAC transformer</strong>
    <p>Steps 230 V mains down to the 24 VAC the valve solenoids expect.</p>
    <a href="https://amazon.es/-/pt/dp/B0C19ND3TY" target="_blank" rel="noopener">View on Amazon →</a>
  </div>
  <div class="shop-item">
    <span class="shop-emoji">📦</span>
    <strong>Waterproof enclosure (IP65)</strong>
    <p>Houses the Sonoff and transformer outdoors, rain or shine.</p>
    <a href="https://amazon.es/-/pt/dp/B0DJ2H6RK2" target="_blank" rel="noopener">View on Amazon →</a>
  </div>
  <div class="shop-item">
    <span class="shop-emoji">💧</span>
    <strong>Rain Bird electrovalves ×4</strong>
    <p>One per zone — the industry workhorse for opening and closing water lines.</p>
    <a href="https://www.leroymerlin.pt/produtos/electrovalvula-rainbird-91457189.html" target="_blank" rel="noopener">View at Leroy Merlin →</a>
  </div>
</div>

<div class="shop-extras">🧰 <strong>Boring-but-essential extras:</strong> an underground valve box, a manual ball valve for the water supply, gel-filled waterproof wire connectors, and two-core outdoor cable to run between the enclosure and the valves.</div>

<div class="divider">💧 💧 💧</div>

## ⚡ The electrical logic

Rain Bird solenoids are 24 VAC, so the transformer steps 230 V mains down to the 24 VAC the valves expect — the standard arrangement for irrigation. The Sonoff never powers the valves itself; its dry-contact relays just sit in the middle of the 24 VAC circuit like light switches. One nice property of AC: there's no polarity to get wrong on the valve wiring — the two solenoid wires are interchangeable.

<div class="callout">💡 Run one zone at a time (the Sonoff has an interlock mode for exactly this). Your water pressure stays usable, and the transformer only ever drives one solenoid's worth of current.</div>

<div class="divider">💧 💧 💧</div>

## 🔌 The wiring

Here's the schema, exactly as installed:

<figure>
  <img src="/assets/blog/irrigation/schema.png" alt="Wiring schema — Sonoff 4CH Pro R3 switching 24 VAC to four valve zones">
  <figcaption>The wiring schema — one transformer, four dry-contact relays, four zones.</figcaption>
</figure>

```
230 V mains ──┬── Sonoff 4CH Pro R3  [Input 100–240V: N, L]
              │
              └── 24 VAC transformer  [Primary: N, L]
                        │
                  Secondary A ──── COM R1 ── COM R2 ── COM R3 ── COM R4
                                    (daisy-chained commons)

   R1 NO ── zone 1 solenoid wire ─┐
   R2 NO ── zone 2 solenoid wire ─┤
   R3 NO ── zone 3 solenoid wire ─┼── all solenoid return wires ── Secondary B
   R4 NO ── zone 4 solenoid wire ─┘
```

Step by step:

1. **Mains in.** Bring 230 V into the waterproof enclosure and split it two ways: to the Sonoff's `Input 100–240V` N/L terminals (the board powers itself from mains directly) and to the transformer's primary.
2. **One secondary leg to the relays.** Take one wire of the transformer's 24 VAC secondary to the `COM` terminal of relay 1, then daisy-chain it across `COM` of relays 2, 3 and 4 with short jumpers.
3. **One wire per zone.** From each relay's `NO` (normally open) terminal, run one conductor out to the valve box — one per zone.
4. **Common return.** In the valve box, join the second wire of every solenoid together and run a single return conductor back to the transformer's other secondary leg.
5. **Plumbing side.** The valves sit in an underground valve box: manual ball valve first (so you can cut water for maintenance), then the Rain Bird valves inline. Use gel-filled waterproof connectors on every splice — this box floods, that's its job. A bed of gravel or pebbles at the bottom keeps fittings out of the mud and lets it drain.

<figure>
  <img src="/assets/blog/irrigation/enclosure-closed.jpeg" alt="The finished enclosure mounted on the wall">
  <figcaption>The finished enclosure — IP65, wall-mounted, Wi-Fi LED glowing.</figcaption>
</figure>

<figure>
  <img src="/assets/blog/irrigation/enclosure-inside.jpeg" alt="Inside the enclosure — 24 VAC transformer on top, Sonoff 4CH Pro R3 below">
  <figcaption>Inside: 24 VAC transformer on top, Sonoff 4CH Pro R3 below.</figcaption>
</figure>

<figure>
  <img src="/assets/blog/irrigation/valve-box.jpeg" alt="The valve box — Rain Bird valves, manual shutoff and waterproof splices">
  <figcaption>The valve box — Rain Bird valves, manual shutoff, waterproof splices, and drainage pebbles.</figcaption>
</figure>

<div class="callout">⚠️ This build involves 230 V mains wiring. Kill the breaker before touching anything, wire it behind an RCD, and if any of that sentence felt unfamiliar — have an electrician do the mains side. The 24 V half is safe to tinker with; the 230 V half is not the place to learn.</div>

<div class="divider">💧 💧 💧</div>

## 📱 Configuration

Pair the Sonoff with the eWeLink app first, then two settings matter:

**Interlock mode.** Enable it so only one relay can be on at a time — one zone watering at any moment, full pressure, no overloaded transformer.

**Home Assistant.** Bring the board into Home Assistant and build your schedules there instead of in the app. Ours waters at dawn, skips rainy days automatically, and reports every run in the nightly digest — try asking a store-bought controller to do that.

<div class="divider">💧 💧 💧</div>

## 🌱 What we'd tell you before you start

The whole system came in well under 100 € and has run all summer without a hiccup. If we were doing it again: buy the waterproof connectors before you think you need them, label both ends of every zone wire the moment you pull it, and put the enclosure somewhere you can actually reach — future-you will be standing there with a multimeter eventually.

Want a smart irrigation setup — or a whole smart home — without doing the trenching yourself? [We do installs](/#contact), schema and all.
