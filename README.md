# SuspenseImage

A React image component and hook that load an image before rendering it, so Suspense boundaries can show a fallback while it loads.

**Status:** Working demo. The component and hook are real and build as a library, but the repo has no tests, no demo page, and no evidence of a live consumer.

## The problem

A plain `img` element gives no way to hold a render until the file has loaded and decoded. This package wraps the native `img` element so that, with the `suspense` prop enabled, React waits for the image inside a `Suspense` boundary. Without it, the component reports its loading state instead.

## What it does

- `Img` renders a native `img` with the same props, plus a `src` string and a `suspense` boolean (default `false`).
- `useImage` takes a single URL or a list. It tries each source in order and returns the first one that loads.
- Images are preloaded with `new Image()` and decoded before they are reported as loaded. `crossOrigin` is optional.
- A module-level cache keyed by the source list means each image is loaded once per page.
- `src/index.tsx` exports `Img` and `useImage`.

## How it's built

- React 18 and TypeScript, built with Vite 5 in library mode.
- The Suspense path throws the pending promise from the hook, as React expects. The non-Suspense path returns `{ isLoading, error, src }` and forces a re-render through state.
- Sources are tried one after another with `promiseFind`, so a fallback URL is only requested when the earlier one fails.

## Running it locally

```bash
pnpm install   # or: npm install
pnpm build     # or: npm run build
pnpm dev       # or: npm run dev
```

`dev` runs `vite`, but the repo has no `index.html`, so there is no page to open. `build` produces the library bundle.

## What's left

- No tests cover the loading, fallback-list, or error paths.
- No demo page or example app is included.
- A failed load is cached as rejected and is never retried for the life of the page.
- The cache has no size limit and no way to clear or invalidate an entry.
