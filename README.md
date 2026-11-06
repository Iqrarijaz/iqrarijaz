<h1 align="center">Iqrar Ijaz</h1>

<p align="center"><b>Backend Software Engineer</b></p>

<p align="center">
  Node.js · TypeScript · Distributed Systems · Fintech<br/>
  Islamabad, Pakistan — open to remote (UTC+5) and relocation
</p>

<p align="center">
  <a href="https://www.iqrarijaz.com">Portfolio</a> ·
  <a href="https://www.linkedin.com/in/iqrarijaz">LinkedIn</a> ·
  <a href="https://www.npmjs.com/package/@vantatrace/sdk">npm</a> ·
  <a href="mailto:iqrarijaz788@gmail.com">Email</a>
</p>

---

I build and operate backend services for payment-grade systems at **JazzCash**, and I
write the **[@vantatrace/sdk](https://www.npmjs.com/package/@vantatrace/sdk)** observability
SDK for Node.js in the open.

Most of my day is Node.js and TypeScript behind an API gateway — microservices, queues,
caching and the data modelling underneath them. Outside work I design and ship complete
products on my own: an observability platform and a community app that is live on the
Play Store. I also build mobile clients in React Native when a product needs one, but I
am a backend engineer first.

---

## Experience

**Software Engineer** — AKSA Business Technologies (JazzCash team) · *Apr 2025 – Present*
Node.js and TypeScript microservices for a national fintech platform. Express services behind
an API gateway handling high-volume financial workflows; asynchronous transaction processing
over Kafka with Redis caching; MongoDB aggregation pipelines and compound indexes for
reporting and ledger operations.
`Node.js` `TypeScript` `Express` `Kafka` `Redis` `MongoDB`

**Full Stack Engineer** — Vizz Web Solutions · *Jul 2023 – Mar 2025*
Payment gateway work, authentication and role-based access control, decoupling monolithic
checkout logic into standalone services, and analytics/invoicing modules with Redis caching.
Deployed and maintained on AWS EC2.
`Node.js` `MongoDB` `Redis` `Stripe` `AWS EC2` `React`

**MERN Stack Developer** — Digital Sensei Technologies · *Jun 2022 – Jul 2023*
Started as an intern and moved into backend work: REST API design and documentation,
request validation, Redis-backed rate limiting, and multi-document MongoDB transactions.
`Node.js` `Express` `MongoDB` `React` `Swagger`

> Work at my employers is private. The descriptions above stay at the level of
> responsibilities and technologies — no internal architecture, endpoints or data.

---

## VantaTrace — observability platform for Node.js

[vantatrace.com](https://www.vantatrace.com) · SDK: **[github.com/Iqrarijaz/vantatrace_sdk](https://github.com/Iqrarijaz/vantatrace_sdk)** · [npm](https://www.npmjs.com/package/@vantatrace/sdk)

[![npm](https://img.shields.io/npm/v/@vantatrace/sdk.svg)](https://www.npmjs.com/package/@vantatrace/sdk)
[![license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/Iqrarijaz/vantatrace_sdk)
[![node](https://img.shields.io/badge/node-%3E%3D16-339933.svg)](https://github.com/Iqrarijaz/vantatrace_sdk)

**The problem.** General-purpose APM agents instrument the request path directly, so every
request pays a latency tax for telemetry it may never need. And errors a service already
handles inside a `try/catch` never reach an error handler at all — they are invisible to
most tools even though they are often the ones that matter.

**The approach.** The SDK captures context synchronously in memory and hands dispatch off
the request path, so instrumentation does not sit between a request and its response.
Events are fingerprinted by name, message and stack shape so repeat failures group into one
issue instead of flooding a dashboard, and redaction of passwords, tokens, cookies and card
data runs by default before anything leaves the process.

**What I built:**

- **Capture layer** — Express middleware pair built on `AsyncLocalStorage` for request
  context, plus Fastify and NestJS adapters, Winston and Pino patches, console and outbound
  HTTP breadcrumbs, and global handlers for uncaught exceptions and unhandled rejections.
- **`try/catch` recovery** — a failed-request safety net, an opt-in V8-inspector watcher for
  development, and an optional Babel plugin that injects capture calls at build time, so
  handled errors are recoverable in production with no runtime cost.
- **Transport** — batched and gzipped delivery with retry, backpressure ceilings, connection
  pooling, and a kill-switch that stops sending if the project key is disabled.
- **Rate limiting** — global and per-fingerprint caps against event storms, with a visible
  summary of what was actually dropped rather than silent loss.
- **Distributed tracing** — W3C Trace Context `traceparent` extraction and propagation, so a
  trace survives a hop between services instead of restarting.
- **Packaging** — written in TypeScript with shipped declarations, a genuine dual CJS/ESM
  build verified at build time rather than interop shims, and 16 test files run on the
  Node.js built-in test runner.

Platform side (private repositories): ingestion and queueing on **Redis + BullMQ**, event
storage and querying in **ClickHouse**, and a **Next.js** dashboard for issues, traces and
per-route latency.

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

**Status — read this before judging it.** `@vantatrace/sdk` is at **v0.5.x** across 12
published versions and is MIT licensed. It is early-stage software: I have no third-party
adoption to point to, the repository has no CI pipeline yet, and the ingestion backend,
dashboard and mobile client are private rather than open source. The SDK itself is public
and readable end to end.

---

## Rehbar — hyperlocal community platform

[rehbarapp.com](https://www.rehbarapp.com) · [Play Store](https://play.google.com/store/apps/details?id=com.rehbar.community) · Mobile client: **[github.com/Iqrarijaz/mehnda-chinji-mobile-app](https://github.com/Iqrarijaz/mehnda-chinji-mobile-app)**

A community app for rural Punjab, built end to end and shipped to the Play Store. It
bundles the services a village actually asks for into one client: a local business
directory, peer-to-peer listings, blood-donation requests, chat, hyperlocal weather and
storm alerts, prayer times, and currency and metal rates.

**Engineering notes:**

- **Offline-first client** — React Native on Expo with TanStack Query persisted to
  AsyncStorage, so cached data survives a cold start on an unreliable rural connection.
- **Modular API layer** — the client is organised by domain (`business`, `posts`, `chat`,
  `bloodDonation`, `essentials`, `notifications`, `currency`, `metals`) against a Node.js
  backend I also wrote.
- **Delivery** — Firebase Cloud Messaging for push and alerts, Firebase Remote Config for
  server-controlled rollout, Zustand for client state, Expo SecureStore for credentials.

Android is live; iOS is not shipped yet. The mobile client and admin frontend are public;
the backend is private.

---

## Also built

| Project | What it is | Status |
| --- | --- | --- |
| Document scanner suite | On-device scanning and OCR, PDF compilation backend, admin portal, landing site | Private repositories |
| Dairy management platform | Backend, merchant app, customer app and web frontend | Private repositories |
| [Personal portfolio](https://www.iqrarijaz.com) | Next.js 14 site with long-form engineering case studies | Live, private repository |

---

## Technical expertise

**Daily, in production** — Node.js · TypeScript · Express · NestJS · MongoDB · PostgreSQL · Redis · REST API design

**Shipped with** — Kafka · BullMQ · ClickHouse · Docker · Nginx · AWS (EC2, S3) · GitHub Actions · React Native (Expo) · Next.js · React

**Working knowledge** — Prometheus · Grafana · Fastify · PostGIS · WebSockets · Stripe

**Practices** — microservice decomposition · async queues and background workers · caching
strategy · idempotency and retries · rate limiting · observability and structured logging ·
database indexing and aggregation pipelines

---

## Education

**BS Software Engineering** — Bahria University, Islamabad · *2018 – 2022*

---

## Contact

- Portfolio — [www.iqrarijaz.com](https://www.iqrarijaz.com)
- LinkedIn — [linkedin.com/in/iqrarijaz](https://www.linkedin.com/in/iqrarijaz)
- Email — [iqrarijaz788@gmail.com](mailto:iqrarijaz788@gmail.com)

Open to backend and platform roles at product companies — remote, or relocation.
