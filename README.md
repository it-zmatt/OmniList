# OmniList MVP (Phase A)

A lean automation engine that lets property managers create a listing once, generate captions with AI, and publish to Facebook and Instagram from a single dashboard.

## Architecture at a Glance
- **Frontend (Vue 3)**: Dashboard and listing form talking to Supabase and the Python API.
- **Supabase**: Auth, Postgres, and storage for listing data and images.
- **Python API Server**: Listing/posting endpoints, AI text generation, and platform integrations.
- **n8n Webhooks**: Orchestrates posting jobs and logs outcomes.
- **Meta APIs**: Facebook Pages and Instagram Business publishing.

## Repository Structure
```
frontend/              # Vue SPA for auth, listing form, and dashboard
backend/api/           # FastAPI/Flask service for posting logic and AI helpers
automation/n8n/        # n8n workflows and webhooks for distribution tasks
automation/playwright/ # (Reserved) browser automation jobs for future platforms
infra/supabase/        # Supabase config, policies, and migrations
infra/ci_cd/           # Deployment scripts and pipelines
docs/                  # High-level specs and diagrams
```

## MVP Scope (Phase A)
- Email/Google auth via Supabase
- Create listings with images stored in Supabase
- AI-generated captions/descriptions
- "Post Everywhere" to Facebook Pages and Instagram via Meta Graph API
- Track post IDs, statuses, and timestamps with simple logs
- Minimal dashboard to review listings and posting results

## Getting Started (Suggested Sequence)
1. Bootstrap Supabase: set up Auth, Postgres tables, and storage buckets.
2. Scaffold the Vue frontend in `frontend/` with routes for auth and listings.
3. Stand up the Python API in `backend/api/` with endpoints for listings and posting.
4. Configure n8n workflows in `automation/n8n/` to trigger Meta API calls and log results.
5. Wire everything together with environment variables for Supabase and Meta credentials.

## Next Steps
- Add deployment scripts under `infra/ci_cd/` for preview environments.
- Expand automation in `automation/playwright/` when new platforms arrive.
