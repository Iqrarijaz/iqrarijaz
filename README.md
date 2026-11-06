<div align="center">

# Iqrar Ijaz

**Backend Software Engineer** — Node.js · TypeScript · Distributed Systems · Fintech

[![npm](https://img.shields.io/npm/v/@vantatrace/sdk?style=flat-square&logo=npm&label=%40vantatrace%2Fsdk&color=CB3837)](https://www.npmjs.com/package/@vantatrace/sdk)
![Node](https://img.shields.io/badge/Node.js-16%2B-339933?style=flat-square&logo=node.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Islamabad](https://img.shields.io/badge/Islamabad,%20PK-remote%20UTC%2B5-444?style=flat-square)

[**Portfolio**](https://www.iqrarijaz.com) · [**LinkedIn**](https://www.linkedin.com/in/iqrarijaz) · [**npm**](https://www.npmjs.com/package/@vantatrace/sdk) · [**Email**](mailto:iqrarijaz788@gmail.com)

</div>

---

|  |  |
| :-- | :-- |
| **Now** | Backend engineer at **JazzCash** — Node.js/TypeScript microservices on a national payments platform |
| **Building** | **VantaTrace** — observability platform for Node.js; SDK public on npm |
| **Shipped** | **Rehbar** (live on Play Store) · **ScanOrbit** (on-device PDF scanner & encrypted vault) |
| **Depth in** | Microservices · async queues · caching · data modelling · observability |
| **Open to** | Backend / platform roles — remote (UTC+5) or relocation |

---

## Products

<table>
<tr>
<td width="33%" valign="top" align="center">

<img src="https://www.iqrarijaz.com/images/projects/vantrace/icon.png" width="54" alt="VantaTrace"/>

### VantaTrace

Observability platform for Node.js — error tracking, tracing, telemetry

[![npm](https://img.shields.io/npm/v/@vantatrace/sdk?style=flat-square&label=npm&color=CB3837)](https://www.npmjs.com/package/@vantatrace/sdk)

`TypeScript` `ClickHouse` `BullMQ` `Next.js`

[Site](https://www.vantatrace.com) · [SDK source](https://github.com/Iqrarijaz/vantatrace_sdk) · [npm](https://www.npmjs.com/package/@vantatrace/sdk)

</td>
<td width="33%" valign="top" align="center">

<img src="https://www.iqrarijaz.com/images/projects/orbit-scan/icon.png" width="54" alt="ScanOrbit"/>

### ScanOrbit

On-device PDF scanner with ML-Kit OCR and a biometric encrypted vault

![private](https://img.shields.io/badge/repos-private-6e7681?style=flat-square)

`React Native` `Skia` `ML-Kit` `SQLite`

Private repositories — walkthrough on request

</td>
<td width="33%" valign="top" align="center">

<img src="https://www.iqrarijaz.com/images/projects/rehbar/icon.png" width="54" alt="Rehbar"/>

### Rehbar

Hyperlocal community platform — directory, trading, alerts, utilities

[![Play Store](https://img.shields.io/badge/Play_Store-live-3DDC84?style=flat-square&logo=googleplay&logoColor=white)](https://play.google.com/store/apps/details?id=com.rehbar.community)

`Expo` `Node.js` `MongoDB` `Firebase`

[Site](https://www.rehbarapp.com) · [App source](https://github.com/Iqrarijaz/mehnda-chinji-mobile-app)

</td>
</tr>
</table>

<details>
<summary><b>VantaTrace — how it works</b> &nbsp;·&nbsp; <sub>SDK architecture, transport, tracing, honest status</sub></summary>

<br/>

**The problem.** APM agents instrument the request path, so every request pays a latency tax for telemetry it may never need. Worse, errors a service already handles inside `try/catch` never reach an error handler — invisible to most tools, and often the ones that matter.

**The approach.** Capture context synchronously in memory, hand dispatch off the request path. Fingerprint events by name, message and stack shape so repeat failures group into one issue. Redact secrets before anything leaves the process.

| Layer | What I built | Stack |
| :-- | :-- | :-- |
| **Capture** | Express middleware pair on `AsyncLocalStorage`; Fastify and NestJS adapters; Winston/Pino patches; console + outbound-HTTP breadcrumbs; global handlers for uncaught exceptions and rejections | `TypeScript` |
| **`try/catch` recovery** | Failed-request safety net, opt-in V8-inspector watcher for dev, and a Babel plugin injecting capture at build time — handled errors recoverable in production at zero runtime cost | `Babel` |
| **Transport** | Batched, gzipped delivery with retry, backpressure ceilings, connection pooling, and a kill-switch when the project key is disabled | `Node.js` |
| **Rate limiting** | Global and per-fingerprint caps against event storms, with a visible summary of what was dropped rather than silent loss | — |
| **Tracing** | W3C Trace Context `traceparent` extraction and propagation, so a trace survives a hop between services | — |
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
<summary><b>ScanOrbit — how it works</b> &nbsp;·&nbsp; <sub>on-device CV pipeline, encrypted vault, zero-knowledge sync</sub></summary>

<br/>

**The problem.** Commercial scanners are ad-laden, send private paperwork to third-party servers, and charge subscriptions for basic PDF export. The fix is to keep the whole pipeline on the device.

| Layer | What I built | Stack |
| :-- | :-- | :-- |
| **Capture** | Native document-scanner module doing perspective warping and polygon corner detection on the live camera feed | `React Native` `Reanimated` |
| **Enhancement** | GPU-accelerated Skia shaders for real-time Magic Color, high-contrast B&W and shadow removal | `Shopify Skia` `Nitro Modules` |
| **OCR** | Google ML-Kit text recognition running entirely on-device — no upload, works offline | `ML-Kit` |
| **PDF engine** | Streaming multi-page PDF generation, Docx conversion and lossless image compression, client-side | `@cantoo/pdf-lib` `JSZip` |
| **Vault** | Sensitive documents behind biometric auth, encrypted with hardware-backed key storage | `Expo SecureStore` `@noble/ciphers` |
| **Index** | Local document catalogue with tag filtering, page-count indexing and full-text search | `Expo SQLite` `MMKV` |
| **Sync** | Zero-knowledge E2EE backup using client-derived keys and presigned object storage — multi-device sync without exposing documents | `AWS S3 / GCS` |
| **Release** | EAS cloud builds and store submission, Crashlytics for crash and performance diagnostics | `Expo EAS` `Fastlane` |

> **Status.** Four private repositories — mobile app, backend, admin portal and landing site. Nothing public to link; happy to walk through the architecture on request.

</details>

<details>
<summary><b>Rehbar — how it works</b> &nbsp;·&nbsp; <sub>offline-first client, domain API layer, live on Play Store</sub></summary>

<br/>

**The problem.** A village needs a directory, a marketplace, blood-donation calls, weather warnings and prayer times — and gets none of them in one place, on connections that drop constantly.

| Layer | What I built | Stack |
| :-- | :-- | :-- |
| **Client** | Offline-first app: TanStack Query persisted to AsyncStorage, so cached data survives a cold start on an unreliable rural connection | `React Native` `Expo` `Zustand` |
| **Domains** | Client organised by domain — `business`, `posts`, `chat`, `bloodDonation`, `essentials`, `notifications`, `currency`, `metals` | `expo-router` |
| **Backend** | v1 shipped on Firebase, then rebuilt on a Node.js/TypeScript API with MongoDB, Redis and BullMQ; FCM retained for push | `Node.js` `MongoDB` `Redis` `BullMQ` |
| **Delivery** | Firebase Cloud Messaging for alerts, Remote Config for server-controlled rollout, SecureStore for credentials | `Firebase` |

> **Status.** Android live on the Play Store; iOS not shipped yet. The mobile client and admin frontend are public, the backend is private.

</details>

---

## Experience

| Role | Where | Period | Focus |
| :-- | :-- | :-- | :-- |
| **Software Engineer** | AKSA Business Technologies *(JazzCash team)* | Apr 2025 – Present | Node.js/TypeScript microservices behind an API gateway handling millions of daily financial transactions; Kafka queues and Redis caching raising throughput 3× at 99.99% availability; MongoDB aggregation pipelines for reporting and ledger operations |
| **Full Stack Engineer** | Vizz Web Solutions | Jul 2023 – Mar 2025 | Payment gateway work, auth and RBAC, decoupling monolithic checkout into standalone services, analytics and invoicing modules with Redis caching, deployed on AWS EC2 |
| **Node.js Developer** | Digital Sensei Technologies | Jul 2022 – Jun 2023 | REST API design and documentation, request validation, Redis-backed sliding-window rate limiting, multi-document MongoDB transactions |

<sub>Employer work is private. Descriptions stay at the level of responsibilities and technologies — no internal architecture, endpoints or data.</sub>

---

## Stack

| | |
| :-- | :-- |
| **Daily, in production** | `Node.js` `TypeScript` `Express` `NestJS` `MongoDB` `PostgreSQL` `Redis` `REST` |
| **Shipped with** | `Kafka` `BullMQ` `ClickHouse` `Docker` `Nginx` `AWS (EC2, S3)` `GitHub Actions` `Next.js` `React Native / Expo` |
| **Working knowledge** | `Prometheus` `Grafana` `Fastify` `PostGIS` `WebSockets` `Stripe` |
| **Practices** | Microservice decomposition · async queues and workers · caching strategy · idempotency and retries · rate limiting · structured logging and observability · indexing and aggregation pipelines |

---

## Also built

| Project | What it is | Status |
| :-- | :-- | :-- |
| **Dastak** | Multi-role doorstep delivery platform — FSM order lifecycle, proximity dispatch, pessimistic row locking against double-assignment | Private |
| **Dairy management suite** | Backend, merchant app, customer app, web frontend | Private |
| **[Portfolio](https://www.iqrarijaz.com)** | Next.js 14 site with long-form engineering case studies | Live |

---

<div align="center">

**BS Software Engineering** — Bahria University, Islamabad · 2018 – 2022

[Portfolio](https://www.iqrarijaz.com) · [LinkedIn](https://www.linkedin.com/in/iqrarijaz) · [iqrarijaz788@gmail.com](mailto:iqrarijaz788@gmail.com)

<sub>Open to backend and platform roles at product companies — remote, or relocation.</sub>

</div>
