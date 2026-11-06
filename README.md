<div align="center">

# Iqrar Ijaz

**Backend Software Engineer** — Node.js · TypeScript · Distributed Systems · Fintech

**I don't stop at the service layer.** I take products from the problem that started them
through architecture, backend, data, client, release and the operations after launch.

[![npm](https://img.shields.io/npm/v/@vantatrace/sdk?style=flat-square&logo=npm&label=%40vantatrace%2Fsdk&color=CB3837)](https://www.npmjs.com/package/@vantatrace/sdk)
![Node](https://img.shields.io/badge/Node.js-16%2B-339933?style=flat-square&logo=node.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Islamabad](https://img.shields.io/badge/Islamabad,%20PK-remote%20UTC%2B5-444?style=flat-square)

[**Portfolio**](https://iqrarijaz.vercel.app) · [**LinkedIn**](https://www.linkedin.com/in/iqrarijaz) · [**npm**](https://www.npmjs.com/package/@vantatrace/sdk) · [**Email**](mailto:iqrarijaz788@gmail.com)

</div>

---

|  |  |
| :-- | :-- |
| **Day job** | Backend engineer at **JazzCash** — Node.js/TypeScript microservices on a national payments platform |
| **Outside it** | Four self-initiated products, each taken from idea to a running system: **VantaTrace**, **ScanOrbit**, **Rehbar**, **Dastak** |
| **Shipped** | Rehbar live on the Play Store · `@vantatrace/sdk` published on npm |
| **Layers I own** | API & services · data modelling · caching and queues · mobile clients · CI/CD and release · monitoring in production |
| **Open to** | Backend, platform and product-engineering roles — remote (UTC+5) or relocation |

---

## How I build

Everything below started as friction I hit myself, and I carried each one the whole way
rather than stopping where a backend ticket would end. That is the work I want to be hired
for — not a layer, a system that keeps working.

**I start from the problem, not the stack.** VantaTrace exists because APM agents tax the
request path and miss the errors a service already catches. ScanOrbit exists because
scanners ship private paperwork to third-party servers. Rehbar exists because a village has
no single place for a directory, a marketplace, blood-donation calls and storm warnings.
The architecture follows the problem; it isn't chosen first.

**I design for the second year, not the demo.** VantaTrace's transport has backpressure
ceilings, per-fingerprint rate limits and a kill-switch — an observability tool that falls
over during an event storm is worse than none. Rehbar's backend was *rebuilt* from Firebase
onto Node/MongoDB once the data model outgrew it, rather than patched around.

**I own the unglamorous parts.** Dual CJS/ESM builds verified at build time, typed public
APIs, tests, EAS release pipelines, Crashlytics, Docker, Nginx, PM2 — the parts nobody
demos, and the parts that decide whether the thing survives six months after launch.

**Self-initiated and self-taught.** No team briefed these, no one reviewed the designs. I
picked the problem, learned whatever it needed — computer vision on-device, columnar
storage, zero-knowledge encryption, store release pipelines — and shipped. What I take from
that into a team is the whole arc: scoping, trade-offs, and responsibility for the system
after it goes live.

---

## Products

<table>
<tr>
<td width="50%" valign="top">

<img src="assets/vantrace.png" width="44" align="left" hspace="12" alt=""/>

### VantaTrace

**Observability platform for Node.js**

[![npm](https://img.shields.io/npm/v/@vantatrace/sdk?style=flat-square&label=npm&color=CB3837)](https://www.npmjs.com/package/@vantatrace/sdk)
![MIT](https://img.shields.io/badge/MIT-blue?style=flat-square)

Error tracking, distributed tracing and telemetry that stays off the request path. SDK is public and MIT; the ingestion platform is private.

`TypeScript` `ClickHouse` `Redis` `BullMQ` `Next.js`

[Site](https://www.vantatrace.com) · [SDK source](https://github.com/Iqrarijaz/vantatrace_sdk) · [npm](https://www.npmjs.com/package/@vantatrace/sdk)

</td>
<td width="50%" valign="top">

<img src="assets/rehbar.png" width="44" align="left" hspace="12" alt=""/>

### Rehbar

**Hyperlocal community platform**

[![Play Store](https://img.shields.io/badge/Play_Store-live-3DDC84?style=flat-square&logo=googleplay&logoColor=white)](https://play.google.com/store/apps/details?id=com.rehbar.community)

Directory, peer-to-peer trading, blood-donation calls, storm alerts and prayer times — offline-first, for rural Punjab.

`Expo` `React Native` `Node.js` `MongoDB` `Firebase`

[Site](https://www.rehbarapp.com) · [App source](https://github.com/Iqrarijaz/mehnda-chinji-mobile-app)

</td>
</tr>
<tr>
<td width="50%" valign="top">

<img src="assets/orbit-scan.png" width="44" align="left" hspace="12" alt=""/>

### ScanOrbit

**On-device PDF scanner & encrypted vault**

![private](https://img.shields.io/badge/repos-private-6e7681?style=flat-square)

Document capture, ML-Kit OCR and PDF compilation that never leave the phone, behind a biometric vault with zero-knowledge sync.

`React Native` `Skia` `ML-Kit` `SQLite` `E2EE`

Private repositories — walkthrough on request

</td>
<td width="50%" valign="top">

<img src="assets/dastak.png" width="44" align="left" hspace="12" alt=""/>

### Dastak

**On-demand doorstep services**

![private](https://img.shields.io/badge/repos-private-6e7681?style=flat-square)

Two-sided marketplace where the hard part is dispatch: a strict order state machine and row-level locking so no job is assigned twice.

`TypeScript` `PostgreSQL` `React Native`

Private repositories — walkthrough on request

</td>
</tr>
</table>

<details>
<summary><b>VantaTrace — the reasoning and the architecture</b></summary>

<br/>

**Why.** General-purpose APM agents instrument the request path, so every request pays a
latency tax for telemetry it may never need. Worse, errors a service already handles inside
`try/catch` never reach an error handler — invisible to most tools, and usually the ones
that matter. I wanted the visibility without the tax, and without shipping raw payloads to
somebody else's SaaS.

**The decision that shaped everything else:** capture synchronously in memory, dispatch
asynchronously off the request path. Everything after that — batching, backpressure,
grouping, the kill-switch — follows from refusing to block a request for telemetry.

| Layer | What I built | Stack |
| :-- | :-- | :-- |
| **Capture** | Express middleware pair on `AsyncLocalStorage`; Fastify and NestJS adapters; Winston/Pino patches; console and outbound-HTTP breadcrumbs; global handlers for uncaught exceptions and rejections | `TypeScript` |
| **`try/catch` recovery** | Failed-request safety net, opt-in V8-inspector watcher for dev, and a Babel plugin injecting capture at build time — handled errors recoverable in production at zero runtime cost | `Babel` |
| **Transport** | Batched, gzipped delivery with retry, backpressure ceilings, connection pooling, and a kill-switch when the project key is disabled | `Node.js` |
| **Grouping** | Fingerprinting by name, message and stack shape, so a repeating failure is one issue instead of ten thousand rows | — |
| **Rate limiting** | Global and per-fingerprint caps against event storms, reporting what was dropped rather than losing it silently | — |
| **Tracing** | W3C Trace Context `traceparent` extraction and propagation, so a trace survives a hop between services | — |
| **Redaction** | Passwords, tokens, cookies, PINs and card data stripped from bodies, query strings and headers before anything leaves the process | — |
| **Packaging** | Genuine dual CJS/ESM build verified at build time (not interop shims), shipped `.d.ts`, 16 test files on the Node.js built-in runner | `tsup` |
| **Platform** *(private)* | Ingestion and queueing on Redis + BullMQ, event storage and querying in ClickHouse, Next.js dashboard for issues, traces and per-route latency | `ClickHouse` `BullMQ` `Next.js` |

```bash
npm install @vantatrace/sdk
```

```javascript
import { VantaTrace } from '@vantatrace/sdk';

const vantaTrace = new VantaTrace({ apiKey: process.env.VANTATRACE_KEY });
vantaTrace.initGlobalHandlers();

app.use(vantaTrace.requestHandler());  // top of the stack
// ... your routes
app.use(vantaTrace.errorHandler());    // bottom of the stack
```

> **Status, stated plainly.** `v0.5.x` across 12 published versions, MIT licensed. Early-stage: no third-party adoption to point to, no CI pipeline in the repository yet, and the ingestion backend, dashboard and mobile client are private rather than open source. The SDK itself is public and readable end to end.

</details>

<details>
<summary><b>Rehbar — the reasoning and the architecture</b></summary>

<br/>

**Why.** A village needs a business directory, a marketplace, blood-donation calls, weather
warnings and prayer times — and gets none of them in one place, on connections that drop
constantly. The constraint that drove the design was the network, not the feature list.

**The decision that shaped everything else:** treat offline as the normal case. Cached data
has to survive a cold start, so query state is persisted rather than held in memory.

| Layer | What I built | Stack |
| :-- | :-- | :-- |
| **Client** | Offline-first app with TanStack Query persisted to AsyncStorage, so content is there before the network is | `React Native` `Expo` `Zustand` |
| **Domains** | Organised by domain — `business`, `posts`, `chat`, `bloodDonation`, `essentials`, `notifications`, `currency`, `metals` | `expo-router` |
| **Backend** | v1 shipped on Firebase; rebuilt onto a Node.js/TypeScript API with MongoDB, Redis and BullMQ when the data model outgrew it, keeping FCM for push | `Node.js` `MongoDB` `Redis` `BullMQ` |
| **Admin** | Next.js portal over the same API and schema, so the two surfaces can't drift | `Next.js` |
| **Delivery** | FCM for alerts, Firebase Remote Config for server-controlled rollout, SecureStore for credentials | `Firebase` |

> **Status.** Android live on the Play Store; iOS not shipped yet. Mobile client and admin frontend public, backend private.

</details>

<details>
<summary><b>ScanOrbit — the reasoning and the architecture</b></summary>

<br/>

**Why.** Commercial scanners are ad-laden, send private paperwork to third-party servers,
and charge subscriptions for basic PDF export. Passports and contracts should not transit
somebody else's infrastructure to become a PDF.

**The decision that shaped everything else:** nothing leaves the device unencrypted. That
rules out server-side OCR and server-side PDF generation, so both had to run on the phone —
and sync had to work without the server ever seeing a document.

| Layer | What I built | Stack |
| :-- | :-- | :-- |
| **Capture** | Native document-scanner module doing perspective warping and polygon corner detection on the live camera feed | `React Native` `Reanimated` |
| **Enhancement** | GPU-accelerated Skia shaders for real-time Magic Color, high-contrast B&W and shadow removal | `Shopify Skia` `Nitro Modules` |
| **OCR** | Google ML-Kit text recognition running entirely on-device — no upload, works offline | `ML-Kit` |
| **PDF engine** | Streaming multi-page PDF generation, Docx conversion and lossless compression, client-side | `@cantoo/pdf-lib` `JSZip` |
| **Vault** | Documents behind biometric auth, encrypted with hardware-backed key storage | `Expo SecureStore` `@noble/ciphers` |
| **Index** | Local catalogue with tag filtering, page-count indexing and full-text search | `Expo SQLite` `MMKV` |
| **Sync** | Zero-knowledge E2EE backup with client-derived keys and presigned object storage — multi-device sync without exposing documents | `AWS S3 / GCS` |
| **Release** | EAS cloud builds and store submission, Crashlytics for crash and performance diagnostics | `Expo EAS` `Fastlane` |

> **Status.** Four private repositories — mobile app, backend, admin portal, landing site. Nothing public to link; happy to walk the architecture.

</details>

<details>
<summary><b>Dastak — the reasoning and the architecture</b></summary>

<br/>

**Why.** Local on-demand services fail on dispatch, not on the app: orders get double-assigned,
states go inconsistent, and disputes have no record to settle them.

**The decision that shaped everything else:** treat the order lifecycle as a state machine
with the database as the arbiter, so correctness doesn't depend on client behaviour.

| Layer | What I built | Stack |
| :-- | :-- | :-- |
| **Clients** | Two roles, two journeys — customer booking with live ETA, and agent fulfilment with swipe-to-accept and proof-of-delivery capture | `React Native` |
| **Dispatch** | Strict finite state machine over the order lifecycle with rollback handlers, plus proximity and rating-based matching | `TypeScript` |
| **Integrity** | `SELECT ... FOR UPDATE` row locks when agents claim jobs, so a job cannot be assigned twice under concurrency; immutable audit trail for billing disputes | `PostgreSQL` |
| **Production** | Helmet headers, input sanitisation, locked-down CORS, and a zero-downtime deployment pipeline | `Docker` `GitHub Actions` |

> **Status.** Private repositories.

</details>

---

## Experience

| Role | Where | Period |
| :-- | :-- | :-- |
| **Software Engineer** | AKSA Business Technologies *(JazzCash team)* | Apr 2025 – Present |
| **Full Stack Engineer** | Vizz Web Solutions | Jul 2023 – Mar 2025 |
| **Node.js Developer** | Digital Sensei Technologies | Jul 2022 – Jun 2023 |

<details>
<summary><b>What each role involved</b></summary>

<br/>

**AKSA Business Technologies (JazzCash team)** — *Backend, national payments platform*
- Node.js/TypeScript microservices behind an API gateway, handling millions of daily financial transactions
- Asynchronous transaction processing over Kafka with Redis caching, raising throughput 3× at 99.99% availability
- MongoDB aggregation pipelines and compound indexes for high-frequency reporting and ledger operations

**Vizz Web Solutions** — *Full stack, client platforms*
- Refactored monolithic storage-checkout logic into a standalone Node.js microservice with its own collections, stabilising checkout under peak traffic
- Built modular analytics and invoice microservices, cutting dashboard data-fetch latency by ~70% with distributed Redis caching
- Payment gateway integration, authentication hardening and role-based access control across multiple user roles
- Deployed and operated on AWS EC2

**Digital Sensei Technologies** — *Backend, from intern onward*
- Distributed sliding-window rate limiting in Express backed by Redis counters, protecting public endpoints from burst traffic
- Multi-document MongoDB ACID transaction pipelines keeping financial and inventory updates consistent
- REST API design and documentation with Swagger, request validation with Joi

<sub>Employer work is private. These stay at the level of responsibilities and technologies — no internal architecture, endpoints or data.</sub>

</details>

---

## Stack

| | |
| :-- | :-- |
| **Daily, in production** | `Node.js` `TypeScript` `Express` `NestJS` `MongoDB` `PostgreSQL` `Redis` `REST` |
| **Shipped with** | `Kafka` `BullMQ` `ClickHouse` `Docker` `Nginx` `AWS (EC2, S3)` `GitHub Actions` `Next.js` `React Native / Expo` `Firebase` |
| **Working knowledge** | `Prometheus` `Grafana` `Fastify` `PostGIS` `WebSockets` `Stripe` |
| **Architecture & practice** | Microservice decomposition · event-driven processing · async queues and workers · caching strategy · idempotency and retries · rate limiting and backpressure · offline-first clients · structured logging and observability · indexing and aggregation pipelines · zero-downtime deploys |

---

<div align="center">

**BS Software Engineering** — Bahria University, Islamabad · 2018 – 2022

[Portfolio](https://iqrarijaz.vercel.app) · [LinkedIn](https://www.linkedin.com/in/iqrarijaz) · [iqrarijaz788@gmail.com](mailto:iqrarijaz788@gmail.com)

<sub>Open to backend, platform and product-engineering roles — remote, or relocation.</sub>

</div>
