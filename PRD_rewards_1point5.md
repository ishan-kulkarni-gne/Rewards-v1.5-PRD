# PRD: Rewards Page v1.5 — Discovery Carousel, Gold Redemption & Threshold Change

**Author:** Ishan Kulkarni | **Team:** Growth & Engagement
**Status:** Draft | **Last updated:** 2026-04-07
**Target launch:** May 1, 2026 (force-routing activation)
**Priority order:** Scratch Cards tile (P0) → Gold redemption (P1) → Threshold change + carousel (P1)

---

## Table of Contents

1. Context & Motivation
2. Goals & Success Metrics
3. User Segments
4. Rewards Page — Full Page Anatomy
5. Surface 1: Cashback Balance Widget
6. Surface 2: Card Nudge
7. Surface 3: Discovery Carousel
8. Surface 4: Redemption Bottomsheet
9. Surface 5: Scratch Cards Inner Page (deferred)
10. State Matrix — Complete Cross-Surface Reference
11. Business Model & Unit Economics
12. Risks & Mitigations
13. Support & CX
14. Legal & Compliance
15. Open Questions
16. Appendix: Glossary

---

## 1. Context & Motivation

### 1.1 What exists today

The Rewards page is the 2nd most visited surface in the app (40% DAU). It currently shows:

- A cashback balance widget (lifetime cashback, redeemed, on the way, redeemable)
- A "Redeem cashback" CTA (previously active only at ≥ ₹10 redeemable balance)
- A card nudge below the widget
- Cashback history (list of transactions with earned cashback)

When users tap "Redeem", they see a 2-option bottomsheet: Gift Voucher or Bank Account. The vast majority default to bank. Digital Gold, Scratch Cards/Coupons, and the discovery carousel do not exist today.

### 1.2 What's wrong

Three problems:

**Discovery failure.** Gift Vouchers exist as a redemption option but are buried behind the Redeem CTA. Users don't know they exist until they tap — if they ever tap. Digital Gold (via Safegold) is launching and faces the same discovery problem from day 1.

**Threshold friction.** Users with < ₹10 redeemable balance currently see a locked/greyed Redeem CTA. They cannot enter the flow at all. This blocks engagement for an estimated 30–40% of the redemption-eligible user base.

**Asset blindness.** Users who have bought Digital Gold or Gift Vouchers via super.money have zero visibility into those assets on the Rewards page. Their accumulated wealth on the platform is invisible, weakening the perceived value of the super.money ecosystem and reducing return visits.

### 1.3 Why now

- **Safegold Digital Gold** is launching — a new redemption surface that needs an entry point.
- **Rewards 2.0 / "dopamine loop" redesign** is planned. v1.5 is the stepping stone.
- **₹15Cr/month reward burn constraint** is fixed while volume is doubling. Every redemption routed to GV or Gold instead of bank is financially favorable — GV already controls ~30% of cashback costs and earns ~₹1Cr+/month ARR.
- **Post-payment coupons** (scratch card games) are being built separately. The discovery carousel creates the Rewards page entry point for that feature.

### 1.4 What this PRD covers

Four changes shipped together:

1. **Digital Gold as a 3rd redemption option** (via Safegold) in the redemption bottomsheet for active users.
2. **₹0.01 entry threshold** — any positive balance activates the Redeem CTA. Bank redemption still requires ₹10. Sub-₹10 users are structurally funnelled to GV or Gold.
3. **Discovery carousel** — a horizontal scroll row of asset tiles (Gold, Gift Cards, Scratch Cards, Referral) on the Rewards page.
4. **Scratch Cards carousel tile** — the entry point to the post-payment coupons/games feature (being built separately under coupons_idea.md).

---

## 2. Goals & Success Metrics

### 2.1 North stars (two, measured separately)

| # | North star | What it measures | Baseline | Target |
|---|-----------|-----------------|----------|--------|
| 1 | Revenue from Safegold gold purchases (₹ commission/month) | Gold redemption adoption | ₹0 (Gold not live) | TBD post Safegold commission rate confirmation |
| 2 | Repeat redemption rate (% of users who redeem in month N AND month N+1) | Discovery carousel's retention impact | TBD (measure pre-launch) | +15% relative lift (hypothesis) |

### 2.2 Input metrics

| Metric | Surface | Expected direction |
|--------|---------|-------------------|
| Redeem CTA tap rate | Cashback widget | ↑ (sub-₹10 users now enter flow) |
| Bottomsheet → Gold selection rate | Redemption bottomsheet | New metric (target: 15–25% of active user redemptions) |
| Bottomsheet → GV selection rate | Redemption bottomsheet | ↑ (more users entering flow who can't use bank) |
| Scratch Cards tile CTR | Carousel | New metric (high priority — most engaging tile) |
| Gold widget CTR → purchase conversion | Carousel | New metric |
| GV widget CTR → browse/purchase | Carousel | New metric |
| Flip tile engagement vs static | Carousel | A/B test candidate |

### 2.3 Guardrails

| Guardrail | What it protects | Alert threshold |
|-----------|-----------------|----------------|
| Total cashback disbursed (₹/month) | Reward burn staying under ₹15Cr/month | Any spike > 5% MoM not explained by volume growth |
| Overall redemption completion rate | Ensure 3-option bottomsheet doesn't cause abandonment vs 2-option baseline | > 5% relative drop in completion rate post-launch |
| D30 repeat redemption rate | Primary retention guardrail for carousel hypothesis | Any relative drop vs control |

### 2.4 Measurement note

Force-routing to GV/Gold activates May 1st. Pre-May 1st data = organic behaviour baseline. Post-May 1st = forced funnel. These two periods **must be tracked separately** to isolate the organic discovery effect of the carousel from the structural force effect.

---

## 3. User Segments

Every user on the Rewards page falls into one of the following segments. Each segment determines: (a) what they see on the page, (b) what bottomsheet variant they get, and (c) what carousel tiles are visible.

### 3.1 Segment definitions

| # | Segment | superBenefits status | Redeemable balance | Card status | Products owned |
|---|---------|---------------------|-------------------|-------------|----------------|
| S1 | Active, fresh user | Active | Any | No card | Never bought Gold/GV, never unlocked game/coupon |
| S2 | Active, product-engaged user | Active | Any | No card | Has bought Gold and/or GV and/or unlocked game/coupon |
| S3 | Active, card holder | Active | Any | Has card | Any |
| S4 | Expired, fresh user | Expired | Any | No card | Never bought Gold/GV, never unlocked game/coupon |
| S5 | Expired, product-engaged user | Expired | Any | No card | Has bought Gold and/or GV and/or unlocked game/coupon |
| S6 | Expired, card holder | Expired | Any | Has card | Any |

### 3.2 Segment → experience mapping

| Segment | Bottomsheet variant | Extra cashback copy | Bank row state | Carousel shown? | Card nudge |
|---------|--------------------|--------------------|---------------|----------------|------------|
| S1 (active, fresh) | Variant A (3-option) | Yes (1% Gold, 20% GV) | Depends on balance | No (< 2 tiles; only Referral) | "Get extra cashback of ₹6,000" |
| S2 (active, product-engaged) | Variant A (3-option) | Yes | Depends on balance | Yes (≥ 2 tiles) | "Get extra cashback of ₹6,000" |
| S3 (active, card holder) | Variant A (3-option) | Yes | Depends on balance | Depends on product ownership | "superCard cashbacks" |
| S4 (expired, fresh) | Variant B (2-option) | No | "Not eligible" (tappable → card nudge) | No (< 2 tiles) | "Get extra cashback of ₹6,000" |
| S5 (expired, product-engaged) | Variant B (2-option) | No | "Not eligible" (tappable → card nudge) | Yes if ≥ 2 tiles | "Get extra cashback of ₹6,000" |
| S6 (expired, card holder) | Variant B (2-option) | No | "Not eligible" (tappable → card nudge) | Depends | "superCard cashbacks" |

**Note on S4/S5/S6:** Expired users see "View gift card" button in the Rewards page header (top-right).

### 3.3 Balance sub-states (applies to all segments)

| Balance state | Redeem CTA | Bank row in bottomsheet |
|--------------|-----------|----------------------|
| ₹0.00 | Greyed out. Copy: *"at least ₹0.01 cashback needed to redeem"* | N/A (bottomsheet not triggered) |
| ₹0.01 – ₹9.99 | Active green. Shows: *"Redeem cashback ₹X.XX"* | Active users: "Not eligible" pill, non-tappable, tooltip: "You need a minimum balance of ₹10 to transfer." Expired users: "Not eligible" pill, tappable → inline card nudge. |
| ≥ ₹10.00 | Active green. Shows: *"Redeem cashback ₹X.XX"* | Active users: Selectable, no pill. Expired users: "Not eligible" (tappable → card nudge) regardless of balance. |

---

## 4. Rewards Page — Full Page Anatomy

Top-to-bottom layout of the Rewards page after v1.5:

```
┌──────────────────────────────────────┐
│  ← Rewards          [View gift card] │  ← Header. "View gift card" only for expired users. (?) help icon always shown.
│                          (?)         │
├──────────────────────────────────────┤
│                                      │
│     ₹ X.XX                          │
│     Lifetime cashback                │  ← Cashback Balance Widget (Surface 1)
│                                      │
│  Redeemed              ₹XX           │
│  On the way ⓘ         ₹XX           │  ← "On the way" row: hidden when ₹0
│  Redeemable            ₹XX           │
│                                      │
│  ┌──────────────────────────────┐    │
│  │  Redeem cashback ₹X.XX      │    │  ← Green CTA (or greyed at ₹0)
│  └──────────────────────────────┘    │
│                                      │
│  [Card nudge banner]                 │  ← Surface 2: Card Nudge
│                                      │
├──────────────────────────────────────┤
│  [Gold] [Gift Cards] [Scratch] [Ref] │  ← Surface 3: Discovery Carousel
│   ←→ horizontal scroll               │     (only shown when ≥ 2 tiles)
├──────────────────────────────────────┤
│                                      │
│  Cashback history                    │
│  ┌────────────────────────────┐      │
│  │ R  Ramu chaiwala     ₹1.8 │      │
│  │    1 Apr 26                │      │
│  ├────────────────────────────┤      │
│  │ S  Sonia Mahajan       ₹3 │      │
│  │    30 Mar 26               │      │
│  └────────────────────────────┘      │
│                                      │
├──────────────────────────────────────┤
│  UPI  Card  Cash  Rewards  History   │  ← Bottom nav (Rewards tab active)
└──────────────────────────────────────┘
```

---

## 5. Surface 1: Cashback Balance Widget

### 5.1 Layout

The widget is a blue/purple gradient card showing:

| Row | Content | Visibility rule |
|-----|---------|----------------|
| Hero number | "₹ X.XX" (lifetime cashback) | Always shown |
| Label | "Lifetime cashback" | Always shown |
| Row 1 | "Redeemed" + "₹XX" | Always shown |
| Row 2 | "On the way ⓘ" + "₹XX" | **Shown only when value > ₹0.** Row hidden entirely at ₹0. |
| Row 3 | "Redeemable" + "₹XX" | Always shown |

**"On the way"** = cashback earned from a transaction but not yet settled/credited into the redeemable balance. The ⓘ icon has an existing tooltip explaining the settlement delay. No changes to this tooltip in v1.5.

### 5.2 Redeem CTA

The CTA button sits below the widget rows, inside the widget card.

| Redeemable balance | CTA state | CTA copy | Background |
|-------------------|-----------|----------|------------|
| ₹0.00 | Greyed / disabled | *"at least ₹0.01 cashback needed to redeem"* | Light grey |
| ≥ ₹0.01 | Active | *"Redeem cashback ₹X.XX"* (exact amount always shown) | Green (#B5E550 or similar) |

**CTA must always display the exact redeemable amount** (e.g. "Redeem cashback ₹4.80"), not a generic "Redeem cashback". The amount serves as an anchor and pull mechanic.

### 5.3 Swipe variant (expired users)

For superBenefits expired users, the CTA may render as "Swipe to redeem" instead of a tap button. This is the existing expired-user behaviour being preserved in v1.5.

---

## 6. Surface 2: Card Nudge

### 6.1 Position

Directly below the cashback balance widget. Always visible to all users regardless of superBenefits status.

### 6.2 States

| User's card status | Nudge copy | Icon | Tap destination |
|-------------------|-----------|------|----------------|
| No card held | "Get extra cashback of ₹6,000" | Sparkle/gift icon | Card page (card allocator flow) |
| Card held (any — Axis, Utkarsh, or Kotak) | "superCard cashbacks" | Card icon | Card page (card cashback details) |

**No eligibility gating at the nudge level.** The nudge is shown to all users without a card. The card allocator flow (which determines secured vs unsecured card, Axis vs Utkarsh vs Kotak) handles eligibility downstream. If a user is ultimately ineligible, the card page handles that experience — not the Rewards page nudge.

**Expired users also see the card nudge.** Getting a superCard is the path for expired users to unlock bank redemption (as communicated in the tappable "Not eligible" state in the bottomsheet).

### 6.3 Card types (FYI, not in scope)

super.money offers two card types:
- **Secured card** (Utkarsh Bank) — backed by FD, 1% cashback
- **Unsecured card** (Axis Bank) — 3% cashback

Users are routed through an allocator decision flow, then allocated a card based on a rule engine. This is entirely outside the scope of this PRD.

---

## 7. Surface 3: Discovery Carousel

### 7.1 Overview

A horizontally scrollable row of tiles positioned between the card nudge and cashback history. Each tile represents a product the user has engaged with on super.money.

### 7.2 Rendering rules

| Rule | Detail |
|------|--------|
| Minimum tiles to render | 2 (carousel row hidden entirely if < 2 tiles qualify) |
| Tile ordering | Fixed left-to-right: Gold → Gift Cards → Scratch Cards → Referral |
| Missing tiles | Compress-left. No gaps. |
| Tile width | ~160px each |
| Scroll behaviour | Horizontal scroll with natural momentum. Next tile peeks into view to signal scrollability. |
| Refresh behaviour | State set on Rewards page load. Does NOT refresh mid-session. Refreshes when user navigates away and returns. |

**Since Referral is always shown, the carousel renders as soon as any one of Gold/GV/Scratch Cards qualifies.**

### 7.3 Tile: Gold

| Property | Detail |
|----------|--------|
| **Condition** | User has ever purchased Digital Gold on super.money. |
| **Persistence** | Tile persists even if current gold value = ₹0 (fully withdrawn/sold). Re-purchase nudge. |
| **Display** | "₹X" (current gold value in ₹) + "Your gold value" + 24K gold coin icon |
| **₹0 state** | Shows "₹0" + "Your gold value". Tile still visible. |
| **Tap** | Opens Digital Gold product page |
| **Badge** | None |

### 7.4 Tile: Gift Cards

| Property | Detail |
|----------|--------|
| **Condition** | User has ever purchased a Gift Voucher on super.money. |
| **Persistence** | Tile persists even if count = 0 (all GVs used/expired). Top-of-mind nudge. |
| **Display** | "N" (count of active gift cards) + "Your gift cards" + gift box icon |
| **0 count state** | Shows "0" + "Your gift cards". Tile still visible. |
| **Tap** | Opens GV wallet/browse page |
| **Badge** | None |

### 7.5 Tile: Scratch Cards (entry point to Coupons/Games)

This is the **highest priority tile** in the v1.5 release.

| Property | Detail |
|----------|--------|
| **Condition** | User has ever unlocked a game (scratch card) OR ever received a coupon. Either condition independently qualifies. |
| **Persistence** | Tile visible whenever user has ≥ 1 active (non-expired) game or coupon. Hidden when all items are expired. (But inner page persists with dimmed expired items.) |
| **Display** | Contextual — depends on what the user has. See §7.5.1–7.5.5 below. |
| **Tap** | Opens games/coupons inner page within Rewards |
| **Badge** | "NEW" badge on first appearance. Cleared after first tap into inner page. |

---

#### 7.5.1 Scratch Cards tile — single category: Games only (no coupons)

No flip. Static tile.

| State | Count shown | Primary copy | Sub-label (urgency) |
|-------|------------|-------------|---------------------|
| Unplayed games, none expiring | Total game count (e.g. "12") | "Scratch card's" | — |
| Unplayed games, 1 expiring within 24h | Total game count | "Scratch card's" | "1 expiring today" |
| Unplayed games, >1 expiring within 24h | Total game count | "Scratch card's" | "X expiring today" |
| Unplayed games, mixed (some expiring, some not) | Total game count | "Scratch card's" | "X expiring today" (X = only the expiring subset count) |

**Expiry threshold for games:** Within 24 hours of the 7-day expiry window (i.e. day 7 of the game's life).

---

#### 7.5.2 Scratch Cards tile — single category: Coupons only (no unplayed games)

No flip. Static tile.

| State | Count shown | Primary copy | Sub-label (urgency) |
|-------|------------|-------------|---------------------|
| Coupons, none expiring | Total coupon count (e.g. "3") | "Your coupons" | — |
| Coupons, 1 expiring within 7 days | Total coupon count | "Your coupons" | "1 expiring soon" |
| Coupons, >1 expiring within 7 days | Total coupon count | "Your coupons" | "X expiring soon" |
| Coupons, mixed (some expiring, some not) | Total coupon count | "Your coupons" | "X expiring soon" (X = only the expiring subset count) |

**Expiry threshold for coupons:** Within 7 days of the ~3-month expiry window.

---

#### 7.5.3 Scratch Cards tile — both categories: Flip mechanic

When user has both active unplayed games AND active coupons, the tile auto-flips between two faces on a slow loop (~2–3 seconds per face).

**Face A:** Shows game count + "Scratch card's" (+ urgency sub-label if any games are expiring)
**Face B:** Shows coupon count + "Your coupons" (+ urgency sub-label if any coupons are expiring)

| State | Face A | Face B | Urgency behaviour |
|-------|--------|--------|-------------------|
| Both, nothing expiring | "12 / Scratch card's" | "3 / Your coupons" | No badge on either face |
| Both, only games expiring | "12 / Scratch card's" + "X expiring today" | "3 / Your coupons" | Urgency only on Face A |
| Both, only coupons expiring | "12 / Scratch card's" | "3 / Your coupons" + "X expiring soon" | Urgency only on Face B |
| Both, games AND coupons expiring | "12 / Scratch card's" + "X expiring today" | "3 / Your coupons" + "Y expiring soon" | Urgency on both faces independently |

**Flip animation spec:**
- Loop speed: 1 flip every 2–3 seconds
- Animation: Card flip (180° Y-axis rotation) or crossfade (design to decide)
- Tap debounce: Flip pauses when user's touch begins anywhere on the tile. This prevents tapping on the wrong face during transition.
- Fallback: If animation fails to load, render a static tile showing the games face (Face A) as default.

---

#### 7.5.4 Scratch Cards tile — post-expiry states

| State | Carousel tile | Inner page |
|-------|--------------|------------|
| Had only games, all expired | **Tile hidden** from carousel | Inner page accessible via cashback history; expired games shown dimmed |
| Had only coupons, all expired | **Tile hidden** from carousel | Inner page accessible; expired coupons shown dimmed |
| Had both, all expired | **Tile hidden** from carousel | Inner page accessible; all items shown dimmed |
| Had both, games expired but coupons still active | **Flip stops.** Single-face tile showing coupons only. | Active coupons shown normally + dimmed expired games |
| Had both, coupons expired but games still active | **Flip stops.** Single-face tile showing games only. | Active games shown normally + dimmed expired coupons |

**Key principle:** Carousel tile only shows when ≥ 1 active item exists. Inner page persists regardless and shows expired items as context (dimmed, listed last within their group).

---

#### 7.5.5 Scratch Cards tile — transition states

These are states where the tile changes configuration between page loads:

| Trigger | Before next load | After next load |
|---------|-----------------|----------------|
| User plays all games, active coupons remain | Flip tile (games + coupons) | Single-face coupons tile |
| User redeems all coupons, active games remain | Flip tile (games + coupons) | Single-face games tile |
| User plays all games AND all coupons expire/redeemed | Flip tile or single-face | Tile hidden |
| User earns first-ever game (tile didn't exist before) | No tile in carousel | Tile appears with "NEW" badge |
| User taps tile for first time | "NEW" badge shown | "NEW" badge cleared |

**Important:** Tile state does NOT update in real-time. All transitions apply on next Rewards page load (user navigates away and returns).

---

### 7.6 Tile: Referral (superReferral)

| Property | Detail |
|----------|--------|
| **Condition** | Always shown. |
| **Display** | "super / Referral" + phone/device icon |
| **Tap** | Opens referral page |
| **States** | None. Fully static. |

---

## 8. Surface 4: Redemption Bottomsheet

### 8.1 Trigger

Tapping the active (green) "Redeem cashback ₹X.XX" CTA on the Rewards page opens the "Choose to redeem in" bottomsheet.

**Not triggered when:** CTA is greyed (₹0 redeemable).

### 8.2 Two distinct variants

The bottomsheet is NOT one-size-fits-all. There are **two fully separate designs** based on user tier.

---

### 8.3 Variant A — superBenefits active user

**Header:** "Choose to redeem in"

**Options (top to bottom):**

| # | Option | Icon | Sub-label | Selection state | Tappable? |
|---|--------|------|-----------|----------------|-----------|
| 1 | **24K Digital Gold** | Gold coin icon | "Earn 1% cashback" (orange text) | **Pre-selected by default** (green checkmark) | Yes |
| 2 | **Gift cards 100+ brands** | Gift box icon | "Earn 20% cashback" (bold text) | Unselected (empty circle) | Yes |
| 3 | **Bank account** [Bank name XX##] | Bank building icon + bank logo | See §8.3.1 below | See §8.3.1 below | See §8.3.1 below |

**CTA button:** "Confirm" (green, full-width)

**Default selection logic:** Gold is pre-selected by default for now. In future, ML will determine the optimal default per user.

#### 8.3.1 Bank row states (Variant A)

| Balance | Pill | Tappable? | Tooltip/inline message |
|---------|------|-----------|----------------------|
| ≥ ₹10 | No pill | Yes (selectable as redemption option) | None |
| ₹0.01 – ₹9.99 | "Not eligible" (grey pill) | **No** (non-tappable dead state) | Tooltip shown inline: *"You need a minimum balance of ₹10 to transfer."* |

---

### 8.4 Variant B — superBenefits expired user

**Header:** "Choose to redeem in"

**Options (top to bottom):**

| # | Option | Icon | Sub-label | Selection state | Tappable? |
|---|--------|------|-----------|----------------|-----------|
| 1 | **Gift card** | Gift box icon | "100+ brands" | **Pre-selected by default** (green checkmark) | Yes |
| 2 | **Bank account** [Bank name XX##] | Bank building icon + bank logo | See §8.4.1 below | See §8.4.1 below | **Yes** (tappable, but not selectable) |

**Gold is NOT shown** in this variant. Expired user Gold redemption is deferred.

**CTA button:** "Swipe to redeem" (green, full-width, swipe gesture)

**Additional UI:** "View gift card" button appears in the top-right of the Rewards page header when an expired user is viewing the page.

#### 8.4.1 Bank row states (Variant B)

For expired users, bank is ALWAYS "Not eligible" regardless of balance. But the row is tappable.

| Balance | Pill | Tappable? | On tap behaviour |
|---------|------|-----------|-----------------|
| Any (₹0.01+) | "Not eligible" (grey pill) | **Yes** | Expands an inline card nudge below the bank row: *"Get access to this feature with superCard"* + **"Apply for card"** deep link (blue, underlined). Tapping "Apply for card" navigates to card page. |

---

### 8.5 Post-bottomsheet flows

| Selected option | Next screen |
|----------------|-------------|
| **24K Digital Gold** | Gold purchase flow. Min purchase = max(redeemable cashback ÷ 0.03, ₹10). Cashback applied as discount. User pays remainder. |
| **Gift card** | GV catalogue (100+ brands). User selects brand. Cashback applied as discount on GV face value. Minimum GV face value constraints apply. User pays remainder. |
| **Bank account** | Direct transfer to linked bank account. Full redeemable amount transferred. |

#### 8.5.1 Gold minimum purchase formula

The minimum gold purchase value is dynamically calculated to ensure super.money recoups its 3% take rate from the transaction:

```
min_gold_purchase = max(redeemable_cashback / 0.03, ₹10)
```

| Redeemable balance | Formula result | Effective minimum | User top-up required |
|-------------------|---------------|-------------------|---------------------|
| ₹0.30 | ₹10.00 | ₹10.00 (hard floor) | ₹9.70 |
| ₹1.00 | ₹33.33 | ₹33.33 | ₹32.33 |
| ₹4.80 | ₹160.00 | ₹160.00 | ₹155.20 |
| ₹10.00 | ₹333.33 | ₹333.33 | ₹323.33 |
| ₹50.00 | ₹1,666.67 | ₹1,666.67 | ₹1,616.67 |

**Hard floor:** ₹10 absolute minimum regardless of formula result. This is super.money's business rule, not a Safegold platform constraint.

#### 8.5.2 Extra cashback on redemption

| Redemption path | superBenefits active user | superBenefits expired user |
|----------------|--------------------------|---------------------------|
| Gold | Earns 1% extra cashback on gold purchase value | No extra cashback |
| Gift Voucher | Earns 20% extra cashback on GV value | No extra cashback |
| Bank | No extra cashback | N/A (bank not available) |

The extra cashback rates are shown inline in the bottomsheet for active users (§8.3) and hidden for expired users (§8.4).

---

## 9. Surface 5: Scratch Cards Inner Page (deferred)

The inner page that opens when a user taps the Scratch Cards carousel tile is a **2-column grid** showing:

- **Unplayed games** listed first (expiring-first within group, then by recency)
- **Active coupons** listed second (expiring-first within group, then by recency)
- **Expired items** shown last within their respective group, visually dimmed/greyed

**All tap behaviours within this page (what happens when you tap an expired game, an active game, an active coupon) are deferred to the coupons_idea.md PRD.** This PRD only covers the carousel tile entry point and the rendering rules for the grid.

---

## 10. State Matrix — Complete Cross-Surface Reference

This is the definitive reference for what every user sees across all 4 surfaces, given their segment and balance.

### 10.1 Matrix: Segment × Balance → All surfaces

| Segment | Balance | Redeem CTA | Bottomsheet | Card nudge | Carousel | Header |
|---------|---------|-----------|-------------|------------|----------|--------|
| S1 (active, fresh) | ₹0 | Greyed: "at least ₹0.01…" | Not triggered | "₹6,000 cashback" | Hidden (< 2 tiles) | Standard |
| S1 (active, fresh) | ₹0.01–₹9.99 | Green: "Redeem ₹X.XX" | Variant A: Gold (default), GV, Bank (not eligible, non-tappable) | "₹6,000 cashback" | Hidden (< 2 tiles) | Standard |
| S1 (active, fresh) | ≥ ₹10 | Green: "Redeem ₹X.XX" | Variant A: Gold (default), GV, Bank (selectable) | "₹6,000 cashback" | Hidden (< 2 tiles) | Standard |
| S2 (active, engaged) | ₹0 | Greyed: "at least ₹0.01…" | Not triggered | "₹6,000 cashback" | Shown (≥ 2 tiles) | Standard |
| S2 (active, engaged) | ₹0.01–₹9.99 | Green: "Redeem ₹X.XX" | Variant A: Gold (default), GV, Bank (not eligible, non-tappable) | "₹6,000 cashback" | Shown (≥ 2 tiles) | Standard |
| S2 (active, engaged) | ≥ ₹10 | Green: "Redeem ₹X.XX" | Variant A: Gold (default), GV, Bank (selectable) | "₹6,000 cashback" | Shown (≥ 2 tiles) | Standard |
| S3 (active, card holder) | ₹0 | Greyed: "at least ₹0.01…" | Not triggered | "superCard cashbacks" | Depends on products | Standard |
| S3 (active, card holder) | ₹0.01–₹9.99 | Green: "Redeem ₹X.XX" | Variant A: Gold (default), GV, Bank (not eligible, non-tappable) | "superCard cashbacks" | Depends on products | Standard |
| S3 (active, card holder) | ≥ ₹10 | Green: "Redeem ₹X.XX" | Variant A: Gold (default), GV, Bank (selectable) | "superCard cashbacks" | Depends on products | Standard |
| S4 (expired, fresh) | ₹0 | Greyed: "at least ₹0.01…" | Not triggered | "₹6,000 cashback" | Hidden (< 2 tiles) | + "View gift card" |
| S4 (expired, fresh) | ₹0.01–₹9.99 | Green: "Redeem ₹X.XX" | Variant B: GV (default), Bank (not eligible, tappable → card nudge) | "₹6,000 cashback" | Hidden (< 2 tiles) | + "View gift card" |
| S4 (expired, fresh) | ≥ ₹10 | Green: "Redeem ₹X.XX" | Variant B: GV (default), Bank (not eligible, tappable → card nudge) | "₹6,000 cashback" | Hidden (< 2 tiles) | + "View gift card" |
| S5 (expired, engaged) | ₹0 | Greyed: "at least ₹0.01…" | Not triggered | "₹6,000 cashback" | Shown if ≥ 2 tiles | + "View gift card" |
| S5 (expired, engaged) | ₹0.01–₹9.99 | Green: "Redeem ₹X.XX" | Variant B: GV (default), Bank (not eligible, tappable → card nudge) | "₹6,000 cashback" | Shown if ≥ 2 tiles | + "View gift card" |
| S5 (expired, engaged) | ≥ ₹10 | Green: "Redeem ₹X.XX" | Variant B: GV (default), Bank (not eligible, tappable → card nudge) | "₹6,000 cashback" | Shown if ≥ 2 tiles | + "View gift card" |
| S6 (expired, card holder) | ₹0 | Greyed: "at least ₹0.01…" | Not triggered | "superCard cashbacks" | Depends | + "View gift card" |
| S6 (expired, card holder) | ₹0.01–₹9.99 | Green: "Redeem ₹X.XX" | Variant B: GV (default), Bank (not eligible, tappable → card nudge) | "superCard cashbacks" | Depends | + "View gift card" |
| S6 (expired, card holder) | ≥ ₹10 | Green: "Redeem ₹X.XX" | Variant B: GV (default), Bank (not eligible, tappable → card nudge) | "superCard cashbacks" | Depends | + "View gift card" |

### 10.2 Matrix: Carousel tile visibility

The carousel renders when ≥ 2 of the following are true:

| Tile | Condition | Persists when value = 0? |
|------|-----------|-------------------------|
| Gold | User has ever purchased Digital Gold | Yes (shows ₹0) |
| Gift Cards | User has ever purchased a Gift Voucher | Yes (shows 0) |
| Scratch Cards | User has ≥ 1 active (non-expired) game or coupon | No — hidden when all items expired |
| Referral | Always | N/A (always shown) |

**Minimum: Referral (always) + any 1 other = carousel renders.**

### 10.3 Matrix: Scratch Cards tile — complete state enumeration

Every possible state the Scratch Cards tile can be in:

| # | Games (active) | Games (expiring) | Coupons (active) | Coupons (expiring) | Tile mode | Display | Urgency |
|---|---------------|-----------------|-----------------|-------------------|-----------|---------|---------|
| 1 | >0 | 0 | 0 | 0 | Static (games) | "N / Scratch card's" | — |
| 2 | >0 | 1 | 0 | 0 | Static (games) | "N / Scratch card's" | "1 expiring today" |
| 3 | >0 | >1 | 0 | 0 | Static (games) | "N / Scratch card's" | "X expiring today" |
| 4 | 0 | 0 | >0 | 0 | Static (coupons) | "N / Your coupons" | — |
| 5 | 0 | 0 | >0 | 1 | Static (coupons) | "N / Your coupons" | "1 expiring soon" |
| 6 | 0 | 0 | >0 | >1 | Static (coupons) | "N / Your coupons" | "X expiring soon" |
| 7 | >0 | 0 | >0 | 0 | Flip | A: "N / Scratch card's" ↔ B: "M / Your coupons" | — |
| 8 | >0 | >0 | >0 | 0 | Flip | A: "N / Scratch card's" + "X expiring today" ↔ B: "M / Your coupons" | Urgency on A only |
| 9 | >0 | 0 | >0 | >0 | Flip | A: "N / Scratch card's" ↔ B: "M / Your coupons" + "Y expiring soon" | Urgency on B only |
| 10 | >0 | >0 | >0 | >0 | Flip | A: "N / Scratch card's" + "X expiring today" ↔ B: "M / Your coupons" + "Y expiring soon" | Urgency on both |
| 11 | 0 | 0 | 0 | 0 (all expired) | **Hidden** | Tile removed from carousel | — |
| 12 | >0 | any | 0 | 0 (coupons expired) | Static (games) | "N / Scratch card's" | Per games expiry state |
| 13 | 0 | 0 (games expired) | >0 | any | Static (coupons) | "N / Your coupons" | Per coupons expiry state |

**N** = total count for that category (including non-expiring). **X/Y** = count of expiring items only.

States #12 and #13 are transition states — they occur when one category has fully expired but the other is still active. The flip mechanic stops and the tile reverts to a single-face static display.

---

## 11. Business Model & Unit Economics

### 11.1 Revenue lines impacted

| Revenue line | Mechanism | Current state | v1.5 impact |
|-------------|-----------|--------------|-------------|
| GV / Gift Vouchers | ~30% cost savings vs bank redemption + GV partner revenue | ₹1Cr+/month ARR target | ↑ More users funnelled to GV via threshold change + carousel discovery |
| Gold / Digital Gold | Commission from Safegold (% of gold GMV + GST) | ₹0 (not live) | New revenue line. Commission rate TBD (blocked on commercials). |
| Card issuance | Card nudge → card application → MDR revenue + FD float interest | 12K net CIF/month (target: 50K) | Incremental: expired users see card nudge in bottomsheet ("Apply for card") |

### 11.2 The forced funnel (primary business lever)

The single most important business outcome of this release:

- **~30–40% of redemption-eligible users** have < ₹10 redeemable balance.
- **Before v1.5:** These users see a locked Redeem CTA. Zero engagement. Zero redemption.
- **After v1.5:** These users tap an active CTA, enter the bottomsheet, see Bank as "Not eligible", and are structurally forced to choose GV or Gold.
- **Financial impact:** Incremental GV/Gold redemption volume at zero additional reward cost. The cashback was already earned — the only question was whether it gets redeemed (and where). Now it does, and it goes to a cheaper path.

### 11.3 Gold unit economics (illustrative, pending commission rate)

```
Assumption: Safegold commission = X%

Monthly Gold redemption events (estimate): TBD
Average gold purchase value: TBD (depends on cashback distribution)
Monthly Gold GMV: TBD
Revenue to SM: Gold GMV × X%
```

This section will be populated once Safegold commercials close.

---

## 12. Risks & Mitigations

| # | Risk | Severity | Mitigation |
|---|------|----------|------------|
| 1 | **3-option bottomsheet causes decision fatigue** — active users abandon more frequently than with 2-option | Medium | Pre-select Gold by default. Monitor completion rate as guardrail. If >5% drop, simplify to 2-option with progressive disclosure. |
| 2 | **Gold redemption UX not smooth in v1** — min value constraints, top-up flow, partial payment UX | Medium | Ship Gold in bottomsheet only when Safegold integration is fully tested end-to-end. Do not ship bottomsheet update ahead of Gold flow. |
| 3 | **Sub-₹10 population smaller than expected** — forced funnel impacts fewer users | Low | Data pull needed before May 1st. If population is <500K, forced funnel is a footnote. If >2M, it's a major lever. |
| 4 | **Safegold commission rate is low (<1%)** — Gold revenue is negligible | Medium | Gold still has strategic value as a wealth-on-platform signal (retention). Revenue is not the only justification. |
| 5 | **Force-routing to GV/Gold (May 1st) with buggy UX** — users can't complete redemption | High | Forced routing must not go live until GV and Gold flows are stable. Feature-flag the force date independently of the page redesign. |
| 6 | **Flip animation on Scratch Cards tile causes tap misregistration** — user taps during flip, wrong action fires | Low | Tap debounce: pause flip animation on touch-start. Fallback: static games face if animation fails to load. |
| 7 | **Gold/GV data pipeline not real-time** — tiles show stale values | Low | "On the way" pattern already exists for cashback. Gold/GV values can be eventually consistent (refresh on page load, not real-time). |

---

## 13. Support & CX

### 13.1 New CX scenarios introduced by v1.5

| Scenario | User says | Resolution |
|----------|----------|------------|
| "Why can't I redeem to bank?" (sub-₹10, active) | "I have ₹4 but it says not eligible" | Tooltip explains ₹10 minimum. FAQ: "Bank redemption requires a minimum of ₹10. You can redeem into Gift Cards or Digital Gold with any amount." |
| "Why can't I redeem to bank?" (expired) | "I have ₹200 but bank says not eligible" | Tappable bank row already shows card nudge. FAQ: "Bank redemption is available to superCard holders. Apply for a superCard to unlock this feature." |
| "My gold purchase failed" | "I redeemed but nothing happened" | Atomic transaction handling required: if Safegold API fails after cashback debit, cashback must be rolled back. Escalation: L2 with transaction ID. |
| "My gift voucher didn't arrive" | "I redeemed into GV but can't find it" | Existing GV failure flow: refund to redeemable balance or retry delivery. Escalation to Gyftr/Qwikcilver if needed. |
| "Where is my gold?" | "I bought gold but it doesn't show" | Safegold integration delay. Check: is Gold tile showing in carousel? Is value updated? Escalation: L2 with purchase timestamp. |

### 13.2 CX bot updates needed

- FAQ: "How do I redeem cashback?" → Update to mention Gold as 3rd option.
- FAQ: "Why can't I transfer to bank?" → Two distinct answers: (a) balance < ₹10, (b) superBenefits expired.
- FAQ: "What is Digital Gold?" → New FAQ for Gold explanation, Safegold partnership, min purchase formula.

---

## 14. Legal & Compliance

**No open concerns.** All three redemption paths (Gold, GV, Bank) are existing product surfaces or built on existing partner agreements:

- **Gold:** Safegold handles all gold-related compliance (BIS hallmarking, storage, insurance). super.money acts as distributor.
- **Gift Vouchers:** Gyftr/Qwikcilver agreements already in place. Discount pass-through within T&C.
- **Bank redemption restriction for expired users:** Existing product policy. T&C already covers this.
- **Threshold change (₹0.01):** No legal implication. Users are entering an existing flow earlier — no new regulatory surface.

---

## 15. Open Questions

| # | Question | Owner | Blocking? | Due date |
|---|---------|-------|-----------|----------|
| 1 | How many users have 0 < redeemable balance < ₹10? | Data team | No (but critical for sizing) | Before May 1st |
| 2 | What is Safegold's commission rate? | BizDev | Yes (blocks Gold revenue modelling) | Pending commercials |
| 3 | Gold/GV widget trigger — "ever purchased" vs "active value > ₹0"? | Eng | No (recommend "ever purchased" — simpler, consistent with tile persistence at ₹0) | Before build |
| 4 | How is the ₹6,000 annual cashback figure calculated? | Cards team | No (informational) | — |

---

## 16. Appendix: Glossary

| Term | Definition |
|------|-----------|
| **superBenefits active** | User whose superBenefits (rewards tier) is currently active. Earns cashback on payments and extra cashback on GV/Gold redemptions. |
| **superBenefits expired** | User whose superBenefits tier has expired. Classified as lower-quality. Bank redemption blocked. No extra cashback on GV/Gold. Can reactivate by getting a superCard. |
| **Redeemable balance** | Cashback that has been earned and settled, available for the user to redeem into bank/GV/Gold. |
| **On the way** | Cashback earned from a transaction that has not yet settled into redeemable balance. |
| **GV / Gift Voucher** | Electronic gift voucher from a brand (e.g. Amazon, Flipkart, Myntra). Sourced from Gyftr/Qwikcilver. User pays face value minus cashback discount. |
| **Digital Gold** | 24K gold purchased via Safegold. Stored digitally. User can buy, hold, and sell. |
| **Scratch Cards / Games** | Post-payment gamified reveal mechanic. User plays a game after UPI payment to reveal cashback or a brand coupon. Detailed in coupons_idea.md. |
| **Flip mechanic** | The Scratch Cards carousel tile auto-flips between two faces (games count / coupons count) every 2–3 seconds when user has both. |
| **Forced funnel** | The structural routing of sub-₹10 users away from bank redemption (disabled) toward GV or Gold (enabled). |
| **Take rate** | super.money's 3% margin on gold purchases, recouped from the transaction. Determines minimum gold purchase value. |
| **Card allocator** | Rule engine that determines whether a user is routed to a secured card (Utkarsh) or unsecured card (Axis) during the card application flow. |
