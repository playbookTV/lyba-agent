# Vite + React

Source: https://lyba.io/docs/rx/vite

Render `<LybaReview />` once inside the app shell:

```tsx
// src/App.tsx
import { LybaReview } from "@lyba/react";

export default function App() {
  return (
    <>
      {/* …your app… */}
      <LybaReview enabled={import.meta.env.VITE_VERCEL_ENV !== "production"} />
    </>
  );
}
```

Vite only exposes variables prefixed with `VITE_` to browser code. If the host's deploy variable has no `VITE_` prefix (`VERCEL_ENV`, Netlify's `CONTEXT`, …), either expose it under a `VITE_` name in the build settings or use a dedicated flag:

```tsx
<LybaReview enabled={import.meta.env.VITE_LYBA_PREVIEW === "true"} />
```

Set `VITE_LYBA_PREVIEW=true` in the host's preview builds only, and leave it unset in production.

More: https://lyba.io/docs/rx/enabled-gate
