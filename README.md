# Insurance Policy Intelligence Engine

**Upload an insurance policy (PDF or text) and ask questions about it in plain English, from a React dashboard on Cloudflare Pages backed by a Python Cloudflare Worker at the edge.**

![License](https://img.shields.io/badge/license-MIT-yellow.svg)

Live: [empire-rag-dashboard.pages.dev](https://empire-rag-dashboard.pages.dev) · API health: [`/health`](https://empire-rag-backend.soorya220104.workers.dev/health)

## What it does

1. **Client-side document parsing.** PDFs are parsed in the browser with PDF.js, so the raw file never leaves the user's machine; only the extracted text is sent.
2. **Edge ingestion.** `POST /api/upload` stores the document text in the Worker.
3. **Grounded answers.** `POST /api/chat` splits each document into paragraphs, retrieves the ones that match the question's terms, and returns them with the **source filename**. Answers are always excerpts of the actual policy, never generated text, so the system can't hallucinate coverage that isn't in the document.

## Architecture

```
React + TypeScript dashboard (Cloudflare Pages)
  │  PDF.js text extraction in the browser
  │
  ├── POST /api/upload  {filename, text}  ─┐
  ├── POST /api/chat    {query}           ─┤──► Python Cloudflare Worker (entry.py)
  └── GET  /health                        ─┘      paragraph split → keyword retrieval → cited excerpts
```

| Path | Role |
|---|---|
| `entry.py` | Worker: routing, CORS, upload, retrieval, cited answers |
| `wrangler.toml` | Worker config (Python runtime; Workers AI and Vectorize bindings declared) |
| `dashboard/` | React + TypeScript + Vite + Tailwind UI: upload, PDF parsing, chat, source display |

## Current scope and roadmap

Retrieval is keyword-based over an in-memory store, so documents last only for the lifetime of a Worker instance. The Workers AI and Vectorize bindings are already declared in `wrangler.toml` for the next step:

- [ ] Chunk and embed documents with Workers AI (`@cf/baai/bge-base-en-v1.5`) into Vectorize
- [ ] Top-k semantic retrieval in place of keyword matching
- [ ] Llama 3 on Workers AI to synthesize an answer from retrieved chunks, with citations
- [ ] Persist documents in R2/KV instead of instance memory
- [ ] Compare plans across several uploaded policies

## Run it

**Frontend** (talks to the deployed Worker):

```bash
cd dashboard
npm install
npm run dev          # http://localhost:5173
```

**Backend:**

```bash
npm install -g wrangler && wrangler login
npx wrangler deploy                                           # from the repo root
curl https://empire-rag-backend.soorya220104.workers.dev/health
```

**Deploy the dashboard:**

```bash
cd dashboard && npm run build
npx wrangler pages deploy dist --project-name empire-rag-dashboard
```

If the Worker URL changes, update `BACKEND_URL` in `dashboard/src/App.tsx`. More commands are in [`COMMANDS.md`](COMMANDS.md) and [`DEPLOYMENT_GUIDE.md`](DEPLOYMENT_GUIDE.md).

## Tech stack

Cloudflare Workers (Python runtime) · Cloudflare Pages · React · TypeScript · Vite · Tailwind CSS · PDF.js

## License

MIT — see [LICENSE](LICENSE).
