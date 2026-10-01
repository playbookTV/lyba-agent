# Reading a Lyba comment's location

`list_comments` returns each comment's location in a few overlapping forms. Use them together.

## `page`

The full page URL on the preview (web) or the app route (native, e.g. `/settings/profile`). Strip the preview host and query to get the route.

## `breakpoint` and `viewportWidth`

Derived from the reviewer's viewport when they pinned:

| breakpoint | width |
|---|---|
| `mobile` | under 768px |
| `tablet` | 768px to 1023px |
| `desktop` | 1024px and up |

A layout bug reported on `mobile` usually lives in the base styles or a `max-width: 767px` rule; on `desktop` in `min-width: 1024px` (Tailwind `lg:`) or wider.

## `element`

- `selector` — a CSS selector for the pinned element at the time of the comment. Treat it as a strong hint, not ground truth: generated class names change between builds.
- `label` — a short human label (e.g. "Button", "Heading").
- `anchor` — Lyba's durable fingerprint:
  - `stable` — a stable attribute when present: `data-testid="…"`, `id="…"`, or for native apps `testID="…"` / `nativeID="…"`. The best search key.
  - `role` + `name` — the accessible role and name, e.g. `button` / `Start trial`. Search the source for the visible text.
  - `path` — a structural path through landmarks and headings.
  - `component` — a component name when the build exposed one.

## `position`

`xPct` / `yPct` (and `wPct` / `hPct` for area pins) are percentages of the page (web) or screen (native). Useful when the selector no longer resolves: they say roughly where on the page to look.

## `raisedOnCommit` / `resolvedOnCommit`

The commit of the preview build the comment was pinned on, and the build it was resolved on. If `raisedOnCommit` is behind `HEAD`, check `git log <sha>..HEAD` for the relevant files before changing anything.

## `carriedFromCommentId`

Set when the comment was carried over from an earlier round because it was still open. The client is still waiting on it.
