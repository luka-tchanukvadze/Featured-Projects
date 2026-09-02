<div align="center">

# The Developer Vault

</div>

<br/>

## At a Glance

> **\* = Pinned on my [main profile](https://github.com/luka-tchanukvadze).**

> Scroll down for detailed breakdowns of every project.

| | Project | What It Is | Stack | Links |
| :---: | :--- | :--- | :--- | :--- |
| **\*** | **[Oathgate](https://github.com/luka-tchanukvadze/Oathgate)** | Crypto payment gateway - double-entry ledger, signed webhooks, real Bitcoin settlement across three services | `NestJS` `TS` `Prisma` `PostgreSQL` `Redis` `BullMQ` `Bitcoin` `Next.js` | [Repository](https://github.com/luka-tchanukvadze/Oathgate) |
| **\*** | **[Coppermind](https://github.com/luka-tchanukvadze/Coppermind)** | Self-hosted full-stack social reading platform - 12 Prisma models, real-time chat with presence, recommendations, auto-deploying to a Pi | `TS` `Express` `Prisma` `PostgreSQL` `Redis` `Socket.io` `Docker` | [**Demo**](https://coppermind.tchanu.com) |
| **\*** | **[Natours PostgreSQL](https://github.com/luka-tchanukvadze/Natours-PostgreSQL)** | Tour booking API rebuilt from MongoDB to raw SQL - no ORM, Jest tested | `TS` `Express` `PostgreSQL` `Raw SQL` `Jest` | [**Demo**](https://natours-eight-psi.vercel.app/) / [Original](https://github.com/luka-tchanukvadze/Natours) |
| **\*** | **[CHANU-WARS](https://github.com/luka-tchanukvadze/CHANU-WARS)** | Star Wars platform - lore wiki, shop, and 3D ship battle game | `Next.js` `TS` `Three.js` `Framer Motion` `MongoDB` | [API](https://github.com/luka-tchanukvadze/CHANU-WARS-BACK) / [Demo](https://chanu-wars.vercel.app/) |
| | **[The Wild Oasis](https://github.com/luka-tchanukvadze/The-Wild-Oasis)** | Hotel management - admin dashboard + customer site, hybrid SSR/SSG | `Next.js` `React` `Supabase` `React Query` | [Demo](https://the-wild-oasis-two-ivory.vercel.app/) |
| | **[Spanreed](https://github.com/luka-tchanukvadze/Spanreed)** | Real-time chat app - Socket.io, full-stack TypeScript monorepo | `TS` `React` `Socket.io` `Express` `PostgreSQL` `Prisma` | [Demo](https://spanreed.onrender.com/) |
| | **[cool-notion](https://github.com/luka-tchanukvadze/cool-notion)** | Notion workspace clone - live Markdown editor, split-pane UI | `React` `react-mde` `react-markdown` | [Demo](https://main--tangerine-dodol-1afb3d.netlify.app/) |
| | **[GroundX](https://github.com/luka-tchanukvadze/GroundX)** | Taxi & car rental app - MapBox maps, external API integration | `Next.js` `TS` `Tailwind` `MapBox` `RapidAPI` | [Demo](https://ground-x-sigma.vercel.app/) |


<br/>

---

<div align="center">


**Frontend** &nbsp; `React` `Next.js` `TypeScript` `JavaScript` `Redux` `RTK` `Tailwind CSS` `SCSS`

**Backend** &nbsp; `Node.js` `NestJS` `Express` `PostgreSQL` `MongoDB` `Prisma` `Mongoose` `Redis` `BullMQ` `REST APIs` `Microservices`

**Auth & Testing** &nbsp; `JWT` `bcrypt` `argon2` `RBAC` `Jest` `Supertest`

**DevOps** &nbsp; `Docker` `GitHub Actions` `PM2` `CI/CD` `Linux`

</div>

---

<br/>

## Detailed Breakdowns

### $\color{#36BCF7}{\textsf{Oathgate}}$

**Crypto payment gateway, "Stripe for crypto"** - [Repository](https://github.com/luka-tchanukvadze/Oathgate)

A payment gateway that accepts Bitcoin and settles merchant balances. It was built as a single service first, then split into three microservices sharing one library:

- **API** - payment creation, live exchange rates, merchant dashboard, API key and session auth
- **Worker** - watches the blockchain, settles payments, delivers webhooks, retries failures
- **Notifications** - a separate service with its own database, fed by Redis pub/sub. It has no access to the payment tables at all.

**Money is stored as integers, never floats.** Fiat uses minor units, so 10.50 GEL is `1050`. Crypto uses base units, so satoshis rather than decimals. Both are `Decimal(38, 0)` in Postgres and `BigInt` in TypeScript. Values are converted at the edges of the system and formatted only for display.

**The ledger is double entry and append-only.** Every movement writes two rows that sum to zero, under a `SELECT ... FOR UPDATE` row lock. Rows are never updated or deleted. To undo a settlement the system writes a reversing pair, and a unique constraint stops the same entry being reversed twice. Balances are cached and can always be rebuilt by summing the entries. A concurrency test runs fifty settlements against one payment at once and asserts that exactly one pair was written.

**Bitcoin settlement is real, not simulated.** Every payment gets its own address, derived from an extended public key. The private key never reaches the server. A worker polls the blockchain and settles the payment once enough blocks confirm it. It handles overpayment, underpayment, fee-bumped transactions that replace each other, and chain reorganisations that undo a payment already marked as paid.

**Webhook delivery survives outages.** Deliveries are written to Postgres in the same transaction that settles the payment, then queued in Redis. If Redis is down, delivery is delayed rather than lost. Each request is signed with HMAC-SHA256, and the timestamp is part of the signature, so an old request cannot be replayed. Failed deliveries retry on a backoff schedule and end in a dead-letter log the merchant can replay from.

**Payment creation is idempotent.** Every request needs an idempotency key and the request body is hashed. A retry returns the original response. The same key sent with a different body is rejected. A separate reconciliation job sums the ledger, compares it against the blockchain, and alerts on any difference.

The merchant dashboard is Next.js: payments with status badges, balances, the webhook log with replay, API key management, and an AI panel that summarises recent activity. The system is deployed on a self-hosted Raspberry Pi through GitHub Actions.

`NestJS` `TypeScript` `Prisma` `PostgreSQL` `Redis` `BullMQ` `Bitcoin` `Next.js` `Docker` `GitHub Actions` `Jest`

---

### $\color{#36BCF7}{\textsf{Coppermind}}$

**Self-hosted full-stack social reading platform** - [Live Demo](https://coppermind.tchanu.com) / [Repository](https://github.com/luka-tchanukvadze/Coppermind)

A full-stack social book-tracking and discussion platform, live and self-hosted on a Raspberry Pi at home. The Prisma schema models 12 relational entities across five domains:

- **Reading** - User, Book, UserBook, and CustomData: progress tracking (WANT_TO_READ / READING / READ) and private or shared notes kept per book
- **Social** - Friend request system with a full PENDING, ACCEPTED, REJECTED lifecycle and a real-time, socket-driven request badge
- **Messaging** - Conversations with real-time delivery, multi-tab presence, typing and read state, and keyset-paginated history via Socket.io
- **Community** - Threaded discussions with comments and likes (a unique constraint prevents double-likes)
- **Feed** - An Activity model that powers a friends' activity feed and a three-tier recommendation engine: friends' books first, then your top genres, then what's popular

Authentication uses JWT with bcrypt password hashing and email-based password reset through nodemailer. Redis handles caching and rate-limiting on the auth endpoints. Role-based access control separates user, author, and admin permissions. The entire backend is TypeScript with Express, and the controller layer follows a handler factory pattern for DRY route logic.

The frontend is Next.js 15 (App Router), Tailwind v4, shadcn-style primitives, TanStack Query for server state, and react-hook-form + Zod for forms.

Deployed end-to-end on a self-hosted Raspberry Pi: GitHub Actions builds ARM64 Docker images on every push to `master`, publishes them to GHCR, and Watchtower on the Pi auto-pulls and restarts. Prisma migrations run automatically on container startup, so schema changes ship with the same `git push` as code. The frontend runs on Vercel; the backend is reachable through a secure tunnel with no open ports.

`TypeScript` `Express` `Prisma` `PostgreSQL` `Redis` `Socket.io` `Next.js` `TanStack Query` `Zod` `Docker` `GitHub Actions` `Raspberry Pi`

---

### $\color{#36BCF7}{\textsf{Natours - PostgreSQL Rebuild}}$

**Tour booking API** - [PostgreSQL Version](https://github.com/luka-tchanukvadze/Natours-PostgreSQL) / [Original MongoDB Version](https://github.com/luka-tchanukvadze/Natours) / [Live Demo](https://natours-eight-psi.vercel.app/)

A complete architectural rebuild of a tour booking API - migrated from MongoDB/Mongoose to raw PostgreSQL using the `pg` driver directly. No ORM. This means manual schema design with primary/foreign keys, complex multi-table JOINs across tours, users, and reviews, and full control over connection pooling and query execution plans.

The testing setup is custom-built: Jest + Supertest with raw SQL scripts (TRUNCATE / RESTART IDENTITY) to reset the PostgreSQL state between test suites, ensuring isolated, repeatable results without ORM magic.

The original MongoDB version (139 commits) is linked above. Together, the two repositories demonstrate the ability to work across both NoSQL and relational paradigms - and the architectural reasoning behind choosing one over the other.

`TypeScript` `Node.js` `Express` `PostgreSQL` `Raw SQL` `Jest` `Supertest` `JWT` `RBAC`

---

### $\color{#36BCF7}{\textsf{CHANU-WARS}}$

**Star Wars universe platform** - [Frontend](https://github.com/luka-tchanukvadze/CHANU-WARS) / [Backend API](https://github.com/luka-tchanukvadze/CHANU-WARS-BACK) / [Live Demo](https://chanu-wars.vercel.app/)

Three modules in one platform: an interactive **lore wiki** where users contribute and explore deep Star Wars canon, a fully functional **online shop**, and a **3D starship battle game** built with Three.js inside React. The frontend and backend live in separate repositories with independent Vercel deployment pipelines.

The frontend uses Next.js with TypeScript, Tailwind CSS, and Framer Motion for animations. The backend runs Node.js with Express and MongoDB for data persistence. The Three.js game was originally a learning exercise for integrating 3D rendering into React - a from-scratch rebuild is planned.

`Next.js` `TypeScript` `Tailwind CSS` `Framer Motion` `Three.js` `Node.js` `Express` `MongoDB`

---

### $\color{#36BCF7}{\textsf{The Wild Oasis}}$

**Hotel management system** - [Repository](https://github.com/luka-tchanukvadze/The-Wild-Oasis) / [Live Demo](https://the-wild-oasis-two-ivory.vercel.app/)

Two separate frontends sharing one Supabase backend. The **admin dashboard** is a React SPA handling cabin CRUD, booking management, guest records, and analytics - built with React Query for server state, React Hook Form for forms, React Router for navigation, and Styled Components for styling. The **customer website** is a Next.js app using hybrid rendering: static generation for cabin listings (fast, cacheable) and server-side rendering for personalized booking data (always fresh). Authentication is handled by NextAuth on the customer side and Supabase Auth on the admin side.

`Next.js` `React` `Supabase` `NextAuth` `React Query` `React Hook Form` `Styled Components` `Tailwind CSS`

---

### $\color{#36BCF7}{\textsf{Spanreed}}$

**Real-time chat application** - [Repository](https://github.com/luka-tchanukvadze/Spanreed) / [Live Demo](https://spanreed.onrender.com/)

A full-stack monorepo with WebSocket-driven messaging via Socket.io. The TypeScript Express backend uses PostgreSQL through Prisma ORM for persistent storage - users, conversations, and message history. The React + Tailwind frontend handles auth flows and live message rendering. Named after the Stormlight Archive's long-distance communication devices.

`TypeScript` `React` `Socket.io` `Express` `PostgreSQL` `Prisma` `Tailwind CSS`

---

### $\color{#36BCF7}{\textsf{cool-notion}}$

**Notion workspace clone** - [Repository](https://github.com/luka-tchanukvadze/cool-notion) / [Live Demo](https://main--tangerine-dodol-1afb3d.netlify.app/)

A Notion-inspired workspace with a live Markdown editor (react-mde), real-time preview rendering (react-markdown + showdown), split-pane layout mirroring Notion's sidebar-plus-content architecture, page creation and management with UUID-based document IDs, and React Router navigation between pages.

`React` `React Router` `react-mde` `react-markdown` `showdown` `Bootstrap`

---

### $\color{#36BCF7}{\textsf{GroundX}}$

**Taxi & car rental application** - [Repository](https://github.com/luka-tchanukvadze/GroundX) / [Live Demo](https://ground-x-sigma.vercel.app/)

A Next.js application integrating three external services: MapBox for interactive mapping, React-map-gl for the map UI layer, and RapidAPI (Cars by API-Ninjas) for vehicle data. Focused on handling multiple third-party APIs, geolocation, and building responsive map-based interfaces with TypeScript and Tailwind CSS.

`Next.js` `TypeScript` `Tailwind CSS` `MapBox` `React-map-gl` `RapidAPI`

<br/>

---

<div align="center">

<a href="https://github.com/luka-tchanukvadze">Main Profile</a> &nbsp;/&nbsp; <a href="https://www.linkedin.com/in/luka-tchanukvadze"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=flat-square&logo=linkedin&logoColor=white" alt="LinkedIn" valign="middle"></a>

</div>
