# CLAUDE.md

Static company site for The Scavenger's Software LLC, served by Cloudflare Pages at https://thescavengers.software. No build step — every page is a self-contained HTML file (inline CSS, system fonts, no external assets); keep new pages that way.

## Brand facts

- Display name: "The Scavenger's Software LLC" — with the apostrophe. The domain and GitHub org drop it (thescavengers.software, The-Scavengers-Software).
- Contact email: drew@thescavengers.software everywhere. Legal pages name the LLC as the Service Provider, never a person.

## Theme

`index.html` is the source of truth for the design tokens (dark `#12100e` / amber `#e8a33d`, with a `prefers-color-scheme: light` variant). Every page carries its own inline copy of the tokens, so a palette change means editing every page.

## Per-app pages

Each app gets a top-level folder — `campfire/` holds `privacy.html`, `terms.html`, and `icon.png` (a 192px `sips -z 192 192` resize of the app's launcher icon). Store listings link `https://thescavengers.software/<app>/privacy.html`. Start a new app by copying `campfire/` and swapping the name, icon, and content; `templates/privacy-policy.html` has the policy skeleton for apps that need one written from scratch.

Campfire's legal text has a second source of truth: `~/StudioProjects/Campfire/docs/{privacy_policy,terms_conditions}.md`. An edit to either side must be mirrored to the other.

## Store verification

- `.well-known/assetlinks.json` — one array entry per app. The fingerprint must be the cert devices actually see: the Play App Signing key for Play installs, the local release key for sideloads (list both when both apply).
- `.well-known/apple-app-site-association` — still placeholder TEAMID/bundle values. `_headers` forces its `application/json` content type; keep that rule when editing `_headers`.

## Deploy and verify

Push to `main` = live in ~60s (Cloudflare Pages, no CI). Verification gotchas learned the hard way:

- Local DNS may negative-cache the domain; curl with `--resolve thescavengers.software:443:104.21.34.82` or dig via `@1.1.1.1`.
- `.html` URLs 308-redirect to extensionless (`/campfire/privacy.html` → `/campfire/privacy`); use `curl -L`. Both forms are fine to hand out.
- Cloudflare Email Obfuscation rewrites addresses in served HTML into `__cf_email__` spans, so grepping a live page for an email finds nothing even when the page is correct. Verify email edits against the repo files; browsers render them fine.
- Asset-links statement check (Google's own resolver): `curl "https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://thescavengers.software&relation=delegate_permission/common.handle_all_urls"`.
