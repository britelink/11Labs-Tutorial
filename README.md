<div align="center">

<img src="public/logo%20a4.png" alt="BriteEducation — School of Technology" width="420" />

<br />
<br />

<h1>11Labs Tutorial</h1>

<p><strong>BriteEducation School of Technology</strong></p>

<p>Academic course repository for building an AI text-to-speech platform — an open-source ElevenLabs alternative.</p>

<p>
  <a href="https://school.britelink.io"><strong>school.britelink.io</strong></a>
</p>

<p>
  Learn text-to-speech, voice cloning, authentication, billing, and deployment with Next.js, React, and Chatterbox TTS.
</p>

</div>

<br />

## About this course

This repository supports the **BriteEducation** curriculum at [school.britelink.io](https://school.britelink.io). It walks through building a production-ready TTS application: from dashboard UI and backend infrastructure to voice management, usage-based billing, and GPU deployment.

Full course materials, environment setup, and chapter guides are available on [school.britelink.io](https://school.britelink.io).

## Course chapters

Each chapter has a matching branch so you can check out the code at any point. Follow the chapter walkthroughs on [school.britelink.io](https://school.britelink.io).

| Branch | Chapter |
|--------|---------|
| `main` | Final project (all chapters combined) |
| `02-dashboard` | Dashboard layout and navigation |
| `03-text-to-speech-ui` | Text-to-speech UI |
| `04-backend-infrastructure` | Backend infrastructure (tRPC, R2, Prisma) |
| `05-voice-selection` | Voice selection and library |
| `06-tts-generation-audio-player` | TTS generation and audio player |
| `07-tts-history-polish` | TTS history and polish |
| `bonus-sentry-error-monitoring` | Bonus: Sentry error monitoring |
| `08-voice-management` | Voice management and cloning |
| `09-billing` | Billing and usage metering |

```bash
git checkout 04-backend-infrastructure  # example: jump to Chapter 4
```

## Features

- **Text-to-Speech** — Generate speech from text with adjustable creativity, variety, expression, and flow parameters
- **Zero-Shot Voice Cloning** — Upload or record a voice sample (10s minimum) and clone it instantly — no fine-tuning required
- **20 Built-in Voices** — Pre-seeded system voices across 12 categories and 5 locales
- **Waveform Audio Player** — WaveSurfer.js visualization with seek, play/pause, and download
- **Multi-Tenant** — Team-based access via Clerk Organizations with full data isolation
- **Usage-Based Billing** — Pay-as-you-go character metering with configurable pricing via Polar products and meters
- **Generation History** — Browse and replay past generations with preserved voice metadata
- **Fully Responsive** — Mobile-first with bottom drawers, compact controls, and adaptive layouts

## Getting Started

See [school.britelink.io](https://school.britelink.io) for the full setup guide, prerequisites, and service configuration.

### Prerequisites

- Node.js **20.9** or later
- PostgreSQL database (Prisma)
- Clerk account (with Organizations enabled)
- Cloudflare R2 bucket
- Modal account (for GPU-hosted TTS)
- Polar account (for billing)

### 1. Clone and install

```bash
git clone https://github.com/britelink/11Labs-Tutorial.git
cd 11Labs-Tutorial
npm install
```

### 2. Configure environment

```bash
cp .env.example .env
```

Fill in the blank values in `.env`. Refer to [school.britelink.io](https://school.britelink.io) for environment variable details and service setup.

### 3. Set up Polar billing

Create two **meters** under **Meters** in your Polar dashboard (steps on [school.britelink.io](https://school.britelink.io)):

1. **Voice Creation** meter
   - Filter: Name equals `voice_creation`
   - Aggregation: **Count**

2. **Text-to-Speech Characters** meter
   - Filter: Name equals `tts_generation`
   - Aggregation: **Sum** over `characters`

Then create a new **product** with **Recurring subscription** pricing. Under **Price Type**, add two metered prices:

1. Click **Add metered price** and select the **Text-to-Speech Characters** meter
   - Set the **Amount per unit** (price per character, e.g. `$0.003`)
   - Optionally set a **Cap amount** (e.g. `$100`)

2. Click **Add metered price** again and select the **Voice Creation** meter
   - Set the **Amount per unit** (price per voice generation, e.g. `$0.25`)
   - Optionally set a **Cap amount** (e.g. `$100`)

With only metered prices, the subscription starts at **$0/month** and scales with usage.

Copy the product ID into `POLAR_PRODUCT_ID`. The meter filter names and aggregation property must match the `POLAR_METER_*` env variables.

### 4. Set up the database

```bash
npx prisma migrate deploy
```

### 5. Deploy the TTS engine

Update `chatterbox_tts.py` with your R2 credentials, create Modal secrets, then deploy. See [school.britelink.io](https://school.britelink.io) for Modal and R2 configuration.

```bash
modal deploy chatterbox_tts.py
```

Use the resulting Modal URL as `CHATTERBOX_API_URL` in your `.env`, then regenerate the API client:

```bash
npm run sync-api
```

### 6. Seed voices

```bash
npx prisma db seed
```

### 7. Run

```bash
npm run dev
```

Open the app locally after `npm run dev`. For deployment and hosting, see [school.britelink.io](https://school.britelink.io).

## Project Structure

```
src/
├── app/                        # Next.js App Router
│   ├── (dashboard)/            # Protected routes (home, TTS, voices)
│   ├── api/                    # Audio proxy routes + tRPC handler
│   └── sign-in/ / sign-up/     # Clerk auth pages
├── components/                 # Shared UI (shadcn/ui + custom)
├── features/
│   ├── dashboard/              # Home page, quick actions
│   ├── text-to-speech/         # TTS form, audio player, settings, history
│   ├── voices/                 # Voice library, creation, recording
│   └── billing/                # Usage display, checkout
├── lib/                        # db, r2, polar, env, chatterbox client
└── trpc/                       # tRPC routers and client
```

## Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start dev server |
| `npm run build` | Production build |
| `npm run start` | Start production server |
| `npm run lint` | Lint with ESLint |
| `npm run sync-api` | Regenerate Chatterbox API types from OpenAPI spec |

---

<p align="center">
  <a href="https://school.britelink.io">school.britelink.io</a> · BriteEducation School of Technology
</p>
