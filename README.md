<div align="center">

# The Developer Vault

A curated archive of full-stack applications, backend architecture, and real-time systems.

`TypeScript` `React` `Next.js` `Node.js` `PostgreSQL` `MongoDB` `Redis` `Prisma` `Socket.io`

</div>

<br/>

## At a Glance

| | Project | What It Is | Stack | Links |
| :---: | :--- | :--- | :--- | :--- |
| **\*** | **[Coppermind](https://github.com/luka-tchanukvadze/Coppermind)** | Social reading platform - 10 Prisma models, Redis, real-time messaging | `TS` `Express` `Prisma` `PostgreSQL` `Redis` `Socket.io` | - |
| **\*** | **[Natours PostgreSQL](https://github.com/luka-tchanukvadze/Natours-PostgreSQL)** | Tour booking API rebuilt from MongoDB to raw SQL - no ORM, Jest tested | `TS` `Express` `PostgreSQL` `Raw SQL` `Jest` | [Original](https://github.com/luka-tchanukvadze/Natours) |
| **\*** | **[CHANU-WARS](https://github.com/luka-tchanukvadze/CHANU-WARS)** | Star Wars platform - lore wiki, shop, and 3D ship battle game | `Next.js` `TS` `Three.js` `Framer Motion` `MongoDB` | [API](https://github.com/luka-tchanukvadze/CHANU-WARS-BACK) / [Demo](https://chanu-wars.vercel.app/) |
| | **[The Wild Oasis](https://github.com/luka-tchanukvadze/The-Wild-Oasis)** | Hotel management - admin dashboard + customer site, hybrid SSR/SSG | `Next.js` `React` `Supabase` `React Query` | [Demo](https://the-wild-oasis-two-ivory.vercel.app/) |
| | **[Spanreed](https://github.com/luka-tchanukvadze/Spanreed)** | Real-time chat app - Socket.io, full-stack TypeScript monorepo | `TS` `React` `Socket.io` `Express` `PostgreSQL` `Prisma` | [Demo](https://spanreed.onrender.com/) |
| | **[cool-notion](https://github.com/luka-tchanukvadze/cool-notion)** | Notion workspace clone - live Markdown editor, split-pane UI | `React` `react-mde` `react-markdown` | [Demo](https://main--tangerine-dodol-1afb3d.netlify.app/) |
| | **[GroundX](https://github.com/luka-tchanukvadze/GroundX)** | Taxi & car rental app - MapBox maps, external API integration | `Next.js` `TS` `Tailwind` `MapBox` `RapidAPI` | [Demo](https://ground-x-sigma.vercel.app/) |

> **\* = Pinned on my [main profile](https://github.com/luka-tchanukvadze).** Scroll down for detailed breakdowns of every project.

<br/>

---

<br/>

## Detailed Breakdowns

### $\color{#36BCF7}{\textsf{Coppermind}}$

**Social reading platform** - [Repository](https://github.com/luka-tchanukvadze/Coppermind)

A backend for a social book-tracking and discussion platform. The Prisma schema models 10 relational entities across four domains:

- **Reading** - User, UserBook, Book with progress tracking (WANT_TO_READ / READING / READ) and private notes per book
- **Social** - Friend request system with a full PENDING, ACCEPTED, REJECTED lifecycle
- **Messaging** - Conversations with participant management and real-time message delivery via Socket.io
- **Community** - Threaded discussions with comments and likes (unique constraint prevents double-likes)

Authentication uses JWT with bcrypt password hashing and email-based password reset through nodemailer. Redis handles caching. Role-based access control separates user, author, and admin permissions. The entire backend is TypeScript with Express, and the controller layer follows a handler factory pattern for DRY route logic.

`TypeScript` `Express` `Prisma` `PostgreSQL` `Redis` `Socket.io` `JWT` `bcrypt` `nodemailer`

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

**[Main Profile](https://github.com/luka-tchanukvadze)** &nbsp;&nbsp;/&nbsp;&nbsp; [![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/luka-tchanukvadze)

</div>
