# yamp deployment repo

<!-- health-badge:start -->
![yamp health](https://yamp.samuelbankhead.workers.dev/health.svg)
<!-- health-badge:end -->


This is the **control plane and plugin marketplace** for a self-hosted [yamp](https://github.com/caseyWebb/yet-another-meal-planner) instance — created from the [yet-another-meal-planner-deployment-template](https://github.com/caseyWebb/yet-another-meal-planner-deployment-template). It holds your `wrangler.jsonc`, the deploy workflow, and the **published plugin bundle** — and nothing else: **all data lives in Cloudflare**, not in this repo — your authored corpus (recipes + guidance markdown) in an **R2 bucket**, all operational and per-member state plus the derived recipe index in **D1**, and ephemeral infra in **KV**, all read and written by the operator's yamp Worker. The one Actions secret it carries (`CLOUDFLARE_API_TOKEN`) stays **encrypted** — so this repo is **public** (which is what lets members add your marketplace without a GitHub account), and member management is the Worker's Cloudflare Access-gated **`/admin`** panel, so no invite code is ever printed into a CI log.

You do **not** fork the code repo. This repo is your control plane: the deploy workflow runs here, as a thin caller of a *reusable* workflow in the public code repo — so the code repo holds no secrets and you take updates by ref. Full operator setup: [docs/SELF_HOSTING.md](https://github.com/caseyWebb/yet-another-meal-planner/blob/main/docs/SELF_HOSTING.md).

## Layout

This repo holds your Worker config, the thin deploy workflow, and the plugin marketplace your deploy publishes. Your authored markdown (recipes + guidance) lives in **Cloudflare R2**; everything operational lives in **Cloudflare D1**.

```
wrangler.jsonc                   # YOUR Worker config (operator-owned keys; merged onto the upstream source at deploy)
.github/workflows/deploy.yml     # thin caller of the code repo's reusable deploy workflow
.claude-plugin/marketplace.json  # your plugin marketplace manifest (-> ./plugin/yamp)
plugin/yamp/                     # the GENERATED plugin bundle — published by the deploy, your URL baked in (do not hand-edit)
docs/SCRAPER.md                  # operator guide for the optional off-cloud walled-source scraper
```

`plugin/yamp/` is created by your **first deploy** (it builds the bundle with your connector URL and commits it here). Until then it doesn't exist — onboard members after your first deploy.
