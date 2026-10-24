# appdata

Remote, app-agnostic data files for my Android apps. Apps fetch these JSON files over HTTPS at runtime, so content can change without shipping an app update. Served by GitHub Pages.

## Layout

```
apps/
  <app-name>/                 one folder per app, isolated from the others
    promo_banners.json        feed for the "more apps" banner strip
    banners/                  banner images referenced by the feed
schemas/
  promo_banners.schema.json   JSON Schema, checked by CI on every push
```

Published URL pattern:

```
https://<user>.github.io/appdata/apps/<app-name>/promo_banners.json
```

## Conventions

- **One folder per app.** An app only reads its own folder, so a mistake in one app's data cannot affect another. Adding an app means adding a folder.
- **Each feed carries `schemaVersion`.** Apps ignore versions they do not know. Breaking changes get a new version number and a new file name or schema, never an in-place change, because old app versions stay installed for years.
- **Additive changes only** within a version: new optional fields are fine, removing or renaming fields is not.
- **HTTPS only.** Android blocks cleartext traffic.
- **Public repo.** Everything here is world-readable. Never store secrets, keys, or personal data.
- **No required file means no feature.** Apps must treat a missing, empty, or invalid file as "show nothing" and keep working offline.
- **Images**: PNG or WebP, with 1x/2x/3x variants. Keep each file small (banners at 3x are about 900x300 px). Use immutable-style file names (`quranic-soul-v2-600.png`) when replacing artwork, since GitHub Pages caches aggressively.

## Adding a new feed type

1. Add `schemas/<feed>.schema.json`.
2. Add `apps/<app>/<feed>.json`.
3. Extend the CI step in `.github/workflows/validate.yml`.
4. Document the fields next to the app code that consumes it.

## Hosting

Settings > Pages > Deploy from branch `main`, folder `/ (root)`. The `.nojekyll` file disables Jekyll processing so files are served as-is.

## Security

Only the owner can write to this repository; everyone else has read-only access. Checklist for the GitHub settings (these cannot be set from files):

- Account: 2FA enabled, preferably with a passkey.
- Settings > Collaborators and teams: empty.
- Settings > Rules > Rulesets: protect `main` (block force pushes and deletion, require the `validate` check, optionally require signed commits). Do not require PR approvals while working solo.
- Settings > Actions > General: workflow permissions "Read repository contents"; fork PR workflows "Require approval for all outside collaborators".
- Settings > Code security: enable Dependabot alerts, secret scanning and push protection.
- Settings > Pages: deploy from `main` / root; enforce HTTPS.

See `SECURITY.md` for reporting.

## Apps

| Folder | App | Feeds |
|---|---|---|
| `apps/sempoa` | Sempoa (`com.rickseven.sempoa`) | `promo_banners.json` |
