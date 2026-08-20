# thescavengers.software

Static site for The Scavengers Software LLC — company landing page, per-app
privacy policies, and app-store verification files.

## Structure

```
index.html                        Landing page
privacy/index.html                Company/site privacy page
apps/<app-slug>/                  One folder per app
  index.html                        App landing page (optional)
  privacy/index.html                App privacy policy (link this in Play/App Store)
templates/privacy-policy.html     Copy this for each new app's policy
.well-known/
  assetlinks.json                 Android App Links + Play verification (replace placeholders)
  apple-app-site-association      iOS Universal Links (replace TEAMID/bundle id)
_headers                          Cloudflare Pages/Netlify headers (AASA content-type)
CNAME                             Custom domain for GitHub Pages
.nojekyll                         Stops GitHub Pages' Jekyll from hiding dotfiles/_files
```

## Adding a new app

1. `mkdir -p apps/<app-slug>/privacy`
2. Copy `templates/privacy-policy.html` to `apps/<app-slug>/privacy/index.html`
   and fill in every `{{PLACEHOLDER}}` (keep it consistent with your Play Data
   Safety form / Apple App Privacy answers).
3. Use `https://thescavengers.software/apps/<app-slug>/privacy/` as the privacy
   policy URL in the store listing.
4. For Android App Links, append the app's entry (package name + SHA-256 of the
   Play App Signing cert, from Play Console → Setup → App signing) to
   `.well-known/assetlinks.json`.

## Deploy

### Cloudflare Pages (recommended)

```sh
git init && git add -A && git commit -m "Initial site"
# Push to a GitHub repo, then in the Cloudflare dashboard:
# Workers & Pages → Create → Pages → Connect to Git → pick the repo.
# Build command: (none). Output directory: /
# Then Custom domains → add thescavengers.software
```

Or deploy directly without GitHub: `npx wrangler pages deploy .`

### GitHub Pages

Push to a repo, then Settings → Pages → Deploy from branch (`main`, `/`).
The `CNAME` file sets the custom domain; add the DNS records GitHub shows you.
Caveat: GitHub Pages serves `apple-app-site-association` with the wrong
Content-Type, which can break iOS Universal Links — Play/assetlinks is fine.

## Local preview

```sh
python3 -m http.server 8080
# open http://localhost:8080
```
