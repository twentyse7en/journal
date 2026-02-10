# SKAdNetwork (SKAN) – Beginner Guide for App Marketing

This document explains **Apple’s SKAdNetwork (SKAN)** in very simple terms, intended for **new app marketers or interns** who are just getting started with mobile growth and attribution.

---

## Why SKAdNetwork Exists

Earlier, app marketers could:
- Track which user clicked an ad
- See which user installed the app
- Measure exactly what that user did (trial, payment, renewal)

Apple changed this to protect user privacy.

Now:
- You usually **cannot track individual users**
- Personal identifiers (like IDFA) are restricted
- Apple decides what data advertisers receive

To allow **some measurement without breaking privacy**, Apple introduced **SKAdNetwork (SKAN)**.

---

## What is SKAdNetwork (SKAN)?

**SKAdNetwork is Apple’s privacy-safe way to measure ad performance.**

Instead of user-level tracking, Apple sends:
- Anonymous data
- Aggregated reports
- Delayed postbacks

Think of it as Apple saying:

> “Your ads worked, but we won’t tell you who the users are.”

---

## What SKAN Can and Cannot Do

### SKAN Can Tell You:
- Which ad campaign led to installs
- Whether users performed important actions after installing
- Performance at a **high level**

### SKAN Cannot Tell You:
- Who the user is
- Exact user journey over weeks or months
- Detailed revenue per individual user

---

## Conversion Value (Core Concept)

Because data is limited, Apple gives advertisers **one number** to summarize user behavior.

This is called a **conversion value**.

### Example Conversion Mapping

| Conversion Value | Meaning |
|------------------|--------|
| 0 | Installed the app |
| 1 | Opened the app |
| 2 | Started a free trial |
| 3 | Paid for a subscription |

Instead of:
> “User X paid ₹999 on day 3”

You receive:
> “Some users reached conversion value 3”

---

## Why SKAN Is Challenging for Subscription Apps

Subscription apps care about:
- Trials
- Payments
- Renewals
- Lifetime value

SKAN limitations:
- Short tracking window after install
- Delayed reporting
- No user-level revenue tracking

As a result:
- Exact ROAS is hard to calculate
- Marketers rely on **early signals**
- Insights are **directional, not precise**

---

## How Marketers Use SKAN in Practice

Typical workflow:
1. Identify key early actions (install, trial, subscribe)
2. Map them to conversion values
3. Run ad campaigns
4. Analyze SKAN reports to compare:
   - Campaign quality
   - Channels driving better users
   - Which ads lead to more trials or subscriptions

The goal is **optimization**, not perfect accuracy.

---

## One-Line Takeaway

> **SKAdNetwork lets you measure ad performance in a privacy-safe, high-level way — without tracking individual users.**

---

## Who This Is For

- App marketing interns
- Junior growth marketers
- Product managers new to mobile attribution
- Anyone new to Apple’s privacy ecosystem


## Reference
- https://www.revenuecat.com/blog/growth/skadnetwork-guide-subscription-apps/