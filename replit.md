# VastuVision AI

A luxury AI-powered Vastu architectural planning and interior generation platform where users upload plot images, get AI-driven Vastu analysis, generate floor plans, visualize interiors/exteriors in 3D, and chat with a conversational AI architect.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080, proxied at `/api`)
- `pnpm --filter @workspace/vastuvision run dev` — run the frontend (proxied at `/`)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string
- Required env: `AI_INTEGRATIONS_OPENAI_BASE_URL`, `AI_INTEGRATIONS_OPENAI_API_KEY` — Replit-managed OpenAI proxy

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite, Tailwind CSS, shadcn/ui, Framer Motion, Wouter routing
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- AI: OpenAI via Replit AI Integration (vision analysis, image generation, streaming chat)
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — OpenAPI spec (source of truth for all API contracts)
- `lib/db/src/schema/projects.ts` — projects table schema
- `lib/db/src/schema/templates.ts` — templates table schema
- `artifacts/api-server/src/routes/projects.ts` — main business logic (AI analysis, generation, scoring)
- `artifacts/api-server/src/routes/templates.ts` — template CRUD
- `artifacts/api-server/src/routes/upload.ts` — image upload (stored in `./uploads/`)
- `artifacts/api-server/src/routes/openai.ts` — SSE streaming chat + image generation
- `artifacts/vastuvision/src/pages/` — all frontend pages
- `artifacts/vastuvision/src/index.css` — global luxury dark theme (matte black + gold)

## Architecture decisions

- Contract-first API: OpenAPI spec defines all routes; Orval generates React Query hooks and Zod validators automatically.
- AI image generation uses `gpt-image-1` model via Replit-managed proxy (no external API key needed).
- AI analysis uses GPT vision: plot image uploaded, URL passed to model as image_url content block.
- Upload files served statically at `/api/uploads/:filename`; base64 data URIs accepted for AI image gen.
- Vastu score computed from stored `analysisResult` JSON on the project record.
- Cost estimate: plot area × floors × 1800 INR/sqft base rate.

## Product

- **Dashboard**: Overview of all projects with stats (total, avg Vastu score, completed)
- **Projects**: Create, manage, and view architectural projects; upload plot images
- **AI Analysis**: GPT-Vision analyzes uploaded drawings for dimensions, orientation, Vastu compliance
- **Floor Plan Generation**: AI generates multiple Vastu-compliant floor plan options
- **Interior Visualization**: AI generates high-quality 3D interior renders by room/style
- **Exterior Visualization**: AI generates 3D facade renders at different times of day
- **AI Chat**: Conversational AI architect (streaming) for Vastu guidance
- **Vastu Score**: Detailed scoring with zone-by-zone breakdown
- **Cost Estimate**: Per-project construction cost estimate in INR
- **Templates**: Pre-designed Vastu-optimized architectural starting points

## User preferences

- Luxury dark aesthetic: matte black backgrounds, gold (#C9A84C) as primary accent, glassmorphism panels
- No placeholder text or lorem ipsum — all content should be meaningful and domain-specific

## Gotchas

- Run `pnpm --filter @workspace/db run push` before starting the API server after schema changes
- Run `pnpm --filter @workspace/api-spec run codegen` after editing `openapi.yaml`
- API server must be restarted after any code changes (it compiles with esbuild on start)
- Upload directory (`artifacts/api-server/uploads/`) is created automatically on first use
- `gpt-image-1` is the correct model name for image generation via Replit AI Integration

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
