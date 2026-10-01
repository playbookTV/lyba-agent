# Next.js App Router

Source: https://lyba.io/docs/rx/nextjs

`@lyba/react` ships a `"use client"` bundle, so it can be rendered directly from the root layout (a server component).

```tsx
// app/layout.tsx
import { LybaReview } from "@lyba/react";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        {children}
        <LybaReview enabled={process.env.NEXT_PUBLIC_VERCEL_ENV !== "production"} />
      </body>
    </html>
  );
}
```

## The `enabled` gate by host

`enabled` is evaluated at build time from a public env var:

| Host | Gate |
|---|---|
| Vercel | `process.env.NEXT_PUBLIC_VERCEL_ENV !== "production"` (Vercel exposes it automatically) |
| Netlify | set `NEXT_PUBLIC_LYBA_PREVIEW=true` in deploy-preview builds only, then `process.env.NEXT_PUBLIC_LYBA_PREVIEW === "true"` |
| Cloudflare Pages | same dedicated flag: `NEXT_PUBLIC_LYBA_PREVIEW=true` in the preview environment only |

A dedicated flag works on any host. Browser code can't read private CI variables, so the flag must be a `NEXT_PUBLIC_` build variable. More: https://lyba.io/docs/rx/enabled-gate

If the root layout already renders other client providers, place `<LybaReview />` as the last child of `<body>`; it renders nothing until a review link is opened.
