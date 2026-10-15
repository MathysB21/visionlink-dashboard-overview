# VisionLink Dashboard

The web app behind VisionLink's physical NFC and QR business cards: a public digital card for each person, and a dashboard where companies manage their team's cards and see how they perform.

## My role

One of two developers, with VisionLink's co-founder; we split the product. In the first build I worked on the public cards and sign-up, and built card statistics and teams inside companies. In the relaunch I did performance and security.

## Stack

Nuxt 3, Vue 3, TypeScript, Tailwind CSS, TanStack Vue Query, Supabase (Postgres, Auth, Storage), Resend, Sentry, Vitest, Playwright, Vercel.

## Architecture

One Nuxt 3 app serves the public card pages and the signed-in dashboard, with route middleware per role. The dashboard talks to Supabase directly through generated types and Vue Query. Anything that needs a secret, such as payments, wallet passes and invites, runs in Nitro server routes. Card QR codes come from the separate VisionLink QR service.

## Hard problems

**Adding row-level security to a live database.** Printed cards point at this app, so a policy mistake takes real cards offline, and an earlier attempt to switch RLS on had locked customers out. I rejected turning it on everywhere at once. Instead I inventoried every query the app makes: who runs it, from which page, and what breaks if a policy is wrong. I then wrote the migrations in waves, each tested with its rollback against the real app in a local lab. All the waves are now live, with RLS on every public table.

**A public card that did database work in the browser.** Every view ran a chain of queries, a location lookup and a direct stats insert from the visitor's browser. I rejected tuning those calls, because the browser would still write view rows straight into the database. Now the server render reads the card's buttons and team as the visitor, so row-level security still applies, and one beacon records the view, checked by primary key on the server.

## Status

Live since 2024. The relaunch shipped in October 2026.

Source is private. Full write-up: https://mathysbasson.vercel.app/work/visionlink-webapp
