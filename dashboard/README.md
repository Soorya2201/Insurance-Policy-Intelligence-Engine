# Dashboard

React + TypeScript + Vite + Tailwind front end for the Insurance Policy Intelligence Engine. See the [project README](../README.md) for the architecture.

- Upload PDF or text files. PDFs are parsed in the browser with PDF.js (`pdfjs-dist`), so only the extracted text is sent to the Worker.
- Chat with the uploaded documents; each answer lists its source files.
- Checks backend health on load (`GET /health`).

```bash
npm install
npm run dev       # http://localhost:5173
npm run build     # static build in dist/, deployed to Cloudflare Pages
```

The backend URL is set by `BACKEND_URL` in `src/App.tsx`.
