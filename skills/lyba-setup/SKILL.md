---
name: lyba-setup
description: Add Lyba client review to a React app — install @lyba/react, render <LybaReview /> gated to preview builds, and create a Lyba review session from CI after each preview deploy with @lyba/cli. Use when the user wants to add Lyba, set up client review or feedback on deploy previews, or wire Lyba into Next.js, Vite, Vercel, Netlify, Cloudflare Pages or GitHub Actions.
---

# Add Lyba to a React app

Lyba lets clients pin comments on a deploy preview and records their sign-off against the commit. A React app needs two pieces:

1. **`@lyba/react`** in the app — renders the review overlay, but only on preview builds and only when someone opens a Lyba review link.
2. **`@lyba/cli`** in CI — creates a review session for each preview deploy and prints the review link.

This skill doesn't need the Lyba MCP server. Make the code changes; the user does the account steps (creating the project, adding the CI secret) in their Lyba dashboard and CI settings.

## 1. Detect the setup

Read `package.json` and the repo layout:

- **Next.js App Router** — `next` dependency and an `app/layout.tsx` (or `src/app/layout.tsx`). Follow [references/nextjs.md](references/nextjs.md).
- **Vite + React** — `vite` and `react`, an app shell such as `src/App.tsx` or `src/main.tsx`. Follow [references/vite.md](references/vite.md).
- **Host** — `vercel.json` / Vercel env, `netlify.toml`, Cloudflare Pages, and any `.github/workflows/*.yml` that deploys previews.

Other React setups: render `<LybaReview />` once near the root with an `enabled` flag that is true only on preview builds.

## 2. Install and render the widget

Install with the project's package manager (`npm install @lyba/react`, `pnpm add @lyba/react`, …). React 18 or 19 must already be installed.

Render `<LybaReview />` **once**, high in the tree, with `enabled` true only on previews. Never pass `enabled={true}` unconditionally. Lyba also refuses to render when it detects production, and the overlay only mounts when the URL carries a review token, so ordinary visitors never see it.

## 3. Create review sessions from CI

Add a step that runs **after** the preview URL exists:

```bash
npx @lyba/cli session create
```

It auto-detects the preview URL, commit SHA, branch, provider and PR number on Vercel, Netlify, Cloudflare Pages and GitHub Actions. It authenticates with the agency **CI key** from the `LYBA_API_KEY` secret. See [references/ci.md](references/ci.md) for each host.

Never put the CI key in code, in `.env` files that get committed, or in any `NEXT_PUBLIC_` / `VITE_` variable. Tell the user to create the secret themselves:

> Copy your CI key from **Lyba → Settings → CI key** into your CI's secrets as `LYBA_API_KEY`.

## 4. Content Security Policy

If the app sets a Content-Security-Policy, add `https://lyba.io` to `connect-src` — the overlay validates review links against the Lyba API there. If the project offers recorded reviews, `connect-src` also needs `https://*.r2.cloudflarestorage.com https://qahsylnsskeuoqogddld.supabase.co`, and `Permissions-Policy` needs `display-capture=(self), microphone=(self), camera=(self)` on preview builds. Details: https://lyba.io/docs/rx/csp

## 5. Hand off

Summarise what changed and what the user still has to do:

1. Add the `LYBA_API_KEY` secret in CI.
2. Push a branch so a preview deploys; the CI step prints the review link (and in GitHub Actions sets the `review-url` output).
3. Open the link to check the overlay appears, then share it with the client.

For a first review without CI, the user can open **Lyba → Set up React** and create a review from any deployed HTTPS preview URL and its commit SHA.

Full guides: https://lyba.io/docs/rx
