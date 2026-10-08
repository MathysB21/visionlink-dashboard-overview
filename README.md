# VisionLink Dashboard

The web app behind VisionLink's physical NFC and QR business cards: a public digital card for each person, and a dashboard where companies manage their team's cards and see how they perform.

## My role

One of two developers, alongside VisionLink's co-founder; we split the product between us. In the first build I worked on the public cards and sign-up, and built card statistics and teams inside companies. For the relaunch I own performance and security.

## Stack

Nuxt 3, Vue 3, TypeScript, Tailwind CSS, TanStack Vue Query, Supabase (Postgres, Auth, Storage), Apple Wallet passes, Resend, Vitest, Vercel.

## Architecture

One Nuxt 3 app serves both the public card pages and the signed-in dashboard, with route middleware for each role, including reseller partners and staff. The dashboard talks to Supabase directly through generated types and Vue Query. Anything that needs a secret, such as payment webhooks, wallet pass signing and invites, runs in Nitro server routes. Card QR codes come from VisionLink QR, a separate service, through its API.

## Hard problems

**Adding row-level security to a live database.** Printed cards point at this app, so a policy mistake takes real cards offline, and an earlier attempt to switch RLS on had locked customers out. I rejected turning it on everywhere at once. Instead I inventoried every query the app makes: who runs it, from which page, and what breaks if a policy is wrong. From that I wrote the migrations in waves and tested each one, with its rollback, against the real app in a local lab before applying it.

**A public card that did database work in the browser.** The card page ran several queries from the visitor's browser on every view. I rejected tuning those queries, because that would keep the card tables open to the browser, the access RLS needed to close. For the relaunch, the server render reads the card and a single beacon records the view, which fixes the speed and the security problem together.

## Status

Live since 2024. The relaunch is in development.

Source is private. Full write-up: https://mathysbasson.vercel.app/work/visionlink-webapp
