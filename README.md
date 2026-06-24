# Insurance Policy Intelligence Engine

> RAG-powered agentic AI that reads your insurance documents and answers questions about them in plain English.

![License](https://img.shields.io/badge/license-MIT-yellow.svg)

## What is this?

Upload an insurance policy document (PDF or text), then ask it questions — "what's my deductible?", "summarize this," "does this cover water damage?" — and get answers grounded in the actual document instead of generic advice. The backend runs as a Cloudflare Worker, retrieving relevant chunks of the uploaded document and feeding them to an LLM (RAG) so answers stay tied to what's actually in the policy.

This repo has two parts:

- **Backend** — Cloudflare Worker (Python), handles document upload, retrieval, and chat
- **Frontend** — React + TypeScript + Vite dashboard (`dashboard/`) for uploading files and chatting

Live deployment:

- Frontend: `https://empire-rag-dashboard.pages.dev`
- Backend: `https://empire-rag-backend.soorya220104.workers.dev`

---

## Project Structure

- `entry.py` — Cloudflare Worker backend (Python Workers)
- `wrangler.toml` — Cloudflare Worker config (bindings, name, etc.)
- `dashboard/` — React + Vite UI for uploading files and chatting
- `data.txt` — Sample data (not required)

---

## Prerequisites

- Node.js (LTS) and npm installed
- Cloudflare account
- Wrangler CLI:

```bash
npm install -g wrangler
```

Log in once:

```bash
npx wrangler login
```

---

## Run Locally (Backend in Cloud, Frontend on Your Machine)

The backend is already deployed and running in the cloud at
`https://empire-rag-backend.soorya220104.workers.dev`. The frontend just needs to run locally and talk to that URL.

```bash
git clone https://github.com/Soorya2201/Insurance-Policy-Intelligence-Engine.git
cd Insurance-Policy-Intelligence-Engine/dashboard
npm install
npm run dev
```

Then open the printed URL (usually `http://localhost:5173`). You can now:

1. Upload a PDF or text file using the upload buttons
2. Ask questions in the chat box (e.g. "summarize this" / "give me a glimpse of the document")

The frontend calls these cloud endpoints:

- `POST https://empire-rag-backend.soorya220104.workers.dev/api/upload`
- `POST https://empire-rag-backend.soorya220104.workers.dev/api/chat`

---

## Deploy / Update the Backend (Cloudflare Worker)

From the repo root:

```bash
npx wrangler deploy
```

Check that it's online:

```bash
curl https://empire-rag-backend.soorya220104.workers.dev/health
```

Expected response:

```json
{"status": "ok", "message": "Backend is online"}
```

---

## Deploy the Frontend to Cloudflare Pages

```bash
cd dashboard
npm run build
npx wrangler pages deploy dist --project-name empire-rag-dashboard
```

This builds the static Vite app and deploys the `dist/` folder. Wrangler will print a URL like `https://empire-rag-dashboard.pages.dev`, which uses the same backend URL above.

> If you ever change the Worker URL, update it in `dashboard/src/App.tsx` where `BACKEND_URL` is defined.

---

## Quick Command Cheat Sheet

**Backend (Worker), from repo root:**

```bash
npx wrangler deploy
```

**Frontend, from `dashboard/`:**

```bash
npm install        # once
npm run dev         # local dev server
npm run build && npx wrangler pages deploy dist --project-name empire-rag-dashboard   # build + deploy
```

---

## License

MIT — see [LICENSE](LICENSE) for details.
