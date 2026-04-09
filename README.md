# Fanvest Wagering Exchange

> **Portfolio showcase** — Fanvest was a sports investment exchange I co-founded and served as CTO from April 2019 to December 2022. This repository is not a codebase; it is a record of what we built, how we built it, and what we learned along the way.

---

## What We Built

Fanvest's mission was to *revolutionize the way people invest in sports* — by combining the engagement of daily fantasy sports and sports gambling with the mechanics and transparency of financial markets and exchanges.

On a wagering exchange, every position has two sides: a **backer** who believes something will happen and a **layer** who is willing to pay out if it does. The exchange matches them, holds funds in escrow, and collects a small commission on winnings. No house edge. No artificial lines. Market-driven pricing.

We took that model and built structured portfolio competitions around it: users could trade NFL and college team positions, enter seasonal and weekly challenges, and compete for cash prizes in a fully exchange-backed marketplace. Real contracts. Live pricing. Settlement tied to actual game outcomes.

The result was something genuinely novel — part exchange, part fantasy platform, part sports data product — designed for the generation of sports fans who grew up with DraftKings but wanted real market dynamics, not lottery-style variance.

---

## Market Validation — Robinhood Built What We Built

In March 2025, Robinhood launched **event contracts**: a product that lets users trade real-time outcome contracts on sporting events, elections, and other occurrences.

It is, in almost every respect, what Fanvest was building in 2019.

By October 2025, Robinhood's CEO Vlad Tenev declared it the **company's fastest-growing business in its history**. That month alone, users traded **2.5 billion contracts** — more than all of Q3 combined — putting the product on a trajectory for **$300 million in annual revenue**.

When my co-founder John sent me screenshots of the Robinhood product after launch, I initially mistook them for screenshots of *our* app — the UI was that close to what we had designed and shipped years earlier. His message with them:

> *"So proud of the work we did! We were right, but just too early."*

We were right about the market. We were right about the product. We were early — which in startups is often indistinguishable from wrong, but isn't the same thing.

---

## The Product

### Core Experience

Users arrived at a market, browsed live order books for team positions, and could either accept an existing offer at the posted price or post their own at a custom price. Matched positions were held in escrow. Settlement was automatic upon game resolution.

Alongside individual markets, we ran structured portfolio competitions:

- **Pro-Football Postseason Portfolio Challenge** — users traded NFL team "stocks" throughout the postseason, competing for cash prizes
- **March Madness Portfolio Challenge** — tournament bracket trading with live market pricing
- **Weekly NFL Competitions** — regular-season team market contests with weekly settlement

Each competition had its own market, liquidity, and content layer — original analysis written by our contributors to help users find value and build strategy.

### Key Features

- **Live order book** — real-time matching engine with WebSocket-powered price feeds
- **Custom pricing** — users could post offers at any price, enabling true price discovery
- **Fractional matching** — positions could be partially matched, allowing liquidity in thin markets
- **Portfolio competitions** — seasonal and weekly structured contests with cash prizes
- **User wallets** — deposit, withdrawal, and escrow management with full transaction history
- **KYC / identity verification** — compliant onboarding with document verification and liveness checks
- **Content platform** — original market analysis and strategy content integrated into the product
- **Responsive web app** — fully mobile-responsive React application, no native app required
- **Push notifications** — match confirmations, settlement alerts, competition updates

---

## Architecture & Technology

### Stack

| Layer | Technology |
|---|---|
| Frontend | React (TypeScript), Redux |
| Backend | Node.js |
| Real-time | WebSockets |
| Database | Firebase Firestore |
| Auth | Firebase Authentication |
| Storage | Firebase Storage |
| Infrastructure | Google Cloud Platform |
| Design | Sketch (offshore design consultancy) |

### The Matching Engine

The most technically demanding part of the system was the order book and matching engine. Every market maintained a standing list of unmatched offers sorted by price. When a user submitted a position, the engine would:

1. Lock the stake from the user's wallet via a Firestore transaction
2. Scan the order book for compatible offers at an acceptable price
3. Match wholly or partially against available liquidity
4. Write matched and unmatched portions back atomically
5. Broadcast updated order book state to all connected clients via WebSocket

Firestore transactions gave us atomic writes without a traditional relational database — but the data modeling discipline required was real. Firestore's consistency model forced us to structure reads and writes carefully to avoid contention under load. We rewrote the data schema twice before landing on a structure that separated market-state reads from user-ledger reads, which resolved the hotspot problems we saw during beta.

### Real-Time Architecture

WebSockets provided bidirectional, persistent connections. Every open market had a room; connected clients subscribed on load and received incremental diffs — not full snapshots — on each state change. This kept bandwidth low and UI updates smooth with concurrent users on a single market.

Fan-out was the hard problem: when a large position settled, it triggered cascading order book updates for multiple users simultaneously. We handled this with a GCP Cloud Functions queue that buffered and batched emissions before broadcasting, decoupling settlement logic from the WebSocket layer.

### Responsive Web App

Rather than building separate native apps, we built a single responsive React application that worked across desktop and mobile browsers. This kept the team focused on one codebase and one deployment pipeline, while still delivering a polished mobile experience — the demo recordings show how well the UI adapted to smaller screens.

### Design to Code

All UI mockups were produced in Sketch by an offshore design consultancy. My responsibility was translating those mocks into functional, stateful interfaces — which meant solving a long tail of problems the designs didn't anticipate: loading states, empty states, partial matches, error paths, and the inherent complexity of live order book UIs that designers had intentionally simplified for visual clarity. The gap between a polished Sketch frame and a working exchange interface is large. Bridging it was a significant part of the engineering work.

---

## Outcomes

| Metric | Result |
|---|---|
| Total funding raised | $450,000 |
| Registered users | 11,000+ |
| Cost per acquisition | < $5 |
| Platform | Responsive web (desktop + mobile) |
| Active years | 2019 – 2022 |

Acquiring over 11,000 users at under $5 CPA without a major paid acquisition budget was one of the results I'm most proud of. Growth came primarily from community-driven channels, referral mechanics, and timing product launches around high-interest sporting events — NFL playoffs, March Madness — when organic reach was highest.

---

## Demos

The following recordings show the product at various stages of development.

---

### Web App — Full Walkthrough

End-to-end walkthrough of the web application: market browsing, live order book interaction, position entry, wallet management, and settlement flow.

[![Fanvest Web App](https://img.youtube.com/vi/IzTFHweG-bI/maxresdefault.jpg)](https://youtu.be/IzTFHweG-bI)

---

### Mobile Web — Shot 1

Home screen, navigation, and market listing on a small viewport.

[![Fanvest Mobile Shot 1](https://img.youtube.com/vi/MnhmtfxA-8o/maxresdefault.jpg)](https://youtu.be/MnhmtfxA-8o)

---

### Mobile Web — Shot 2

Bet placement flow on mobile: selecting a market, reading the order book, entering a stake, and confirming a position.

[![Fanvest Mobile Shot 2](https://img.youtube.com/vi/Z4Fs-Ni1z9M/maxresdefault.jpg)](https://youtu.be/Z4Fs-Ni1z9M)

---

### Mobile Web — Shot 3

Live market experience and real-time order book updates on mobile.

[![Fanvest Mobile Shot 3](https://img.youtube.com/vi/FGBVu9_sLGs/maxresdefault.jpg)](https://youtu.be/FGBVu9_sLGs)

---

### Interactive Prototypes

Two Principle prototypes are included in the [`demo/`](demo/) directory. These were used during investor presentations and user research sessions to communicate interaction intent before the full build. To view them, download [Principle Mirror](https://apps.apple.com/us/app/principle-mirror/id991911319) on your iPhone.

---

## What I'd Do Differently

**Model the data before writing the first query.** The Firestore schema rewrites were expensive. NoSQL requires designing around your read patterns — not your domain model. We learned this in production, which meant paying twice: once to build it wrong and once to fix it.

**Structured observability as a first-class requirement.** We had logging, but not instrumentation. Diagnosing production issues required manual log analysis when the right alerting thresholds would have surfaced problems much earlier.

**Shorten the KYC funnel faster.** We saw meaningful drop-off at the identity verification step and knew about it before we acted on it. Faster iteration on the onboarding flow would have improved conversion meaningfully.

**Push the regulatory path harder, earlier.** In hindsight, dedicating resources to a clearer licensing strategy sooner — rather than treating it as a later-stage problem — was the most consequential missed step.

---

## Why Fanvest Ended

Fanvest was dissolved in December 2022. The U.S. sports betting regulatory environment was moving more slowly than we had anticipated in the states where we had the clearest path to licensing. We were facing increasing competition from well-capitalized incumbents. And personal health circumstances affecting a founder made it the right moment to wind down responsibly rather than raise another round under conditions that wouldn't have been fair to anyone involved.

The technology worked. The product found an audience. The team built something genuinely new. Circumstances, not execution, ended it — and that distinction matters to me.

---

## Gratitude

To John, Jake, Kellen, Fernando, and everyone who gave their time and belief to Fanvest:

Building a startup is one of the harder things a person can choose to do. You give years of your life to something uncertain, make consequential decisions daily with incomplete information, and hold each other up through the stretches where nothing goes as planned. We did all of that, and we did it with integrity.

We raised capital from people who believed in us, shipped real product to real users, navigated a genuinely complex regulatory landscape without cutting corners, and — it turns out — called the market correctly. We just called it a few years before the infrastructure and regulation caught up.

John said it best:

> *"We were right, but just too early."*

Yes. We were.

Whatever each of us builds next carries something from this. That is not a small thing.

Thank you.

— Vinicius

---

## Contact

**Vinicius Morais** — Co-founder & CTO, Fanvest Wagering Exchange (2019–2022)

[LinkedIn](https://linkedin.com/in/viniciusmorais) · [GitHub](https://github.com/viniciusmorais)

---

*Fanvest Wagering Exchange, Inc. was incorporated in Delaware and dissolved in December 2022. This repository is maintained as a personal portfolio record.*
