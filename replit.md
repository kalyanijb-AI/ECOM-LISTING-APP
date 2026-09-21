# Katalyst by Clariva

## Overview
Katalyst (by Clariva) is an AI-powered product listing generator SaaS that analyzes uploaded product images and generates comprehensive, publish-ready product details using OpenAI vision capabilities. Multi-tenant — each user has their own isolated workspace. Products can be published to Shopify and discovered through AI-powered conversational shopping. Contact: hello@clariva-dev.com

## SaaS Architecture
- **Multi-tenant**: Each user has isolated products and Shopify connection
- **Auth**: Passport.js local strategy with bcrypt, express-session
- **Plans**: Free (5 products), Starter $29/mo (100), Pro $59/mo (500), Agency $99/mo (unlimited)
- **Admin user**: username "talahi", email admin@talahicollections.com, plan=agency (password from ADMIN_PASSWORD env var or "Talahi2024!")
- **Route structure**: `/` = landing page, `/login` = login, `/signup` = signup, `/app` = protected dashboard, `/app/analytics` = analytics

## Features
- Upload product images (drag & drop or file browse)
- AI analyzes images and generates: title, description, packaging info, handling instructions, return policy, meta title, meta description, and tags
- SEO & AI discoverability optimized content generation (Google, ChatGPT, Meta/Facebook/Instagram)
- Shopify character limit enforcement (title: 255, description: 5000, meta title: 70, meta description: 320, tags: 40 chars each)
- Character count indicators in product detail view with color-coded warnings
- One-click "Publish to Shopify" - sends product directly to Shopify store as draft
- "Republish" button to update existing Shopify products with latest data
- **Bulk select & batch publish** - select multiple products and publish them all to Shopify at once
- **Bulk select & batch import** - select multiple Shopify products and optimize them all with AI at once (max 10)
- Tracks published status with shopifyProductId to prevent duplicates
- Sync status indicators showing which Shopify products are synced/out of sync
- View all generated products in a grid layout
- Detailed product view with tabbed sections (Details, Policies, SEO, Tags) - all fields editable before publishing
- Copy individual fields to clipboard
- Delete products
- **Share & Export**: Generate product card images (1080x1080) and slideshow videos (1080x1920) for social media
- **WhatsApp sharing**: One-click share with pre-formatted product details and store link
- **Instagram sharing**: Copy optimized caption with hashtags, download image/video for posting
- AI Shopping Assistant chat widget for conversational product discovery
- Embeddable chat widget for Shopify stores (single script tag integration)
- Product search API for AI assistants and ChatGPT Actions
- Schema.org JSON-LD structured data for products
- Public product feeds (JSON Feed 1.1 + RSS)
- Sitemap.xml, robots.txt with AI crawler directives
- ChatGPT Actions compatible OpenAPI specification
- AI plugin manifest (.well-known/ai-plugin.json)
- **Free tier enforcement**: product limit check on generate; upgrade dialog with pricing tiers
- **Usage indicator**: progress bar showing X/5 products used (free tier only)

## Brand
- Store brand: **Talahi Collections** (talahicollections.com)
- AI prompts are configured to always use "Talahi Collections" as the brand name
- Third-party/supplier brand names (e.g., "Shenori") are never used in generated content

## Architecture
- **Frontend**: React + Vite + TailwindCSS + shadcn/ui components
- **Backend**: Express.js with multer for file uploads
- **Database**: PostgreSQL with Drizzle ORM
- **AI**: OpenAI GPT-5.2 vision via Replit AI Integrations (no API key needed)
- **Auth**: passport-local + express-session + bcryptjs

## Theme / Visual Design
- **Dark mode globally**: `<html class="dark">` in index.html forces dark mode on all shadcn components sitewide
- **bg-dot-grid**: Dark navy (#0f0c29) with white dot overlay — used on all app page wrappers (home, analytics, login, signup, not-found, loading state)
- **Login/Signup**: Full-page dark gradient (same as landing) with semi-transparent input fields (`bg-white/5 border-white/15`)
- **AppFooter**: Shared footer component at `client/src/components/app-footer.tsx` — included in all app pages (home, analytics)
- **Font**: Plus Jakarta Sans globally via `--font-sans` CSS variable

## Project Structure
- `client/src/pages/landing.tsx` - Public landing/marketing page
- `client/src/pages/login.tsx` - Login page
- `client/src/pages/signup.tsx` - Signup page
- `client/src/pages/home.tsx` - Protected dashboard (list, create, detail views) + ChatWidget
- `client/src/pages/analytics.tsx` - Protected analytics page
- `client/src/hooks/use-auth.ts` - useAuth + useLogout hooks
- `client/src/App.tsx` - Root app with routing + ProtectedRoute
- `server/routes.ts` - API endpoints (auth, products, Shopify, feeds, etc.)
- `server/auth.ts` - Passport.js setup, isAuthenticated middleware
- `server/migrate-admin.ts` - Admin user migration (runs on startup)
- `server/storage.ts` - Database storage interface with user-scoped queries
- `server/db.ts` - Drizzle database connection
- `server/seed.ts` - Seed data for initial products
- `server/ssr-pages.ts` - SSR product pages with Schema.org
- `shared/schema.ts` - Drizzle schema and Zod types

## API Endpoints

### Auth
- `POST /api/auth/register` - Create account (returns user, logs in session)
- `POST /api/auth/login` - Login with username/password
- `POST /api/auth/logout` - Logout / destroy session
- `GET /api/auth/me` - Get current user (401 if not authenticated)

### Products (all require auth, scoped to user)
- `GET /api/products` - List user's products
- `GET /api/products/search?q=query` - Search user's products
- `GET /api/products/:id` - Get single product (must belong to user)
- `GET /api/products/:id/schema` - Schema.org JSON-LD for a product
- `PATCH /api/products/:id` - Update product fields
- `DELETE /api/products/:id` - Delete product
- `POST /api/products/generate` - Upload image and generate product (enforces free tier limit)
- `POST /api/products/:id/publish-shopify` - Publish product to Shopify as draft
- `POST /api/products/batch-publish-shopify` - Batch publish multiple products
- `POST /api/shopify/batch-import` - Batch import & optimize multiple Shopify products (max 10)

### Shopify (require auth, per-user settings)
- `GET /api/shopify-limits` - Returns Shopify character limits
- `GET /api/shopify/status` - Check Shopify connection for user
- `GET /api/shopify/auth` - Start OAuth flow
- `GET /api/shopify/callback` - OAuth callback
- `DELETE /api/shopify/disconnect` - Disconnect Shopify for user
- `GET /api/shopify/products` - Fetch products from connected Shopify store
- `POST /api/shopify/import/:shopifyId` - Import a Shopify product and regenerate with AI

## Public/Crawler Endpoints (all link to Shopify store at talahicollections.com)
- `GET /chatgpt-feed.json` - ChatGPT-compatible product feed
- `GET /chatgpt-feed.csv` - CSV feed matching OpenAI merchant spec
- `GET /feed.json` - JSON Feed 1.1 product feed
- `GET /feed.xml` - RSS feed
- `GET /sitemap.xml` - XML sitemap
- `GET /robots.txt` - Crawler directives
- `GET /.well-known/ai-plugin.json` - ChatGPT Actions plugin manifest
- `GET /.well-known/openapi.yaml` - OpenAPI 3.0 spec
- `GET /llms.txt` - LLM crawler discovery file
- `GET /llms-full.txt` - Complete product details
- `GET /api/shopify-llms-page` - Generates HTML for Shopify page
- `GET /product/:id` - Server-rendered product page
- `GET /products` - Server-rendered product listing

## Shopify Store Integration
- Store domain: talahicollections.com (Shopify custom domain)
- All AI discovery files link to Shopify product pages
- Shopify handle map is cached for 10 minutes
- Shopify product data (prices, inventory) cached with 10-minute TTL
- Each user has their own Shopify connection (stored in shopifySettings with userId)

## Running
- `npm run dev` starts both Express backend and Vite frontend on port 5000
- `npm run db:push` syncs database schema

## Environment Variables
- `SESSION_SECRET` - Session secret (required)
- `ADMIN_PASSWORD` - Admin user password (default: "Talahi2024!")
- `SHOPIFY_ACCESS_TOKEN`, `SHOPIFY_CLIENT_ID`, `SHOPIFY_CLIENT_SECRET`, `SHOPIFY_STORE_URL` - Shopify integration
