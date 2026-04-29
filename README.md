# Report Themes

A Manager.io extension that installs and uninstalls report themes for a business.

## What it does

1. Lists the businesses in your Manager.io instance.
2. After you pick a business, shows every theme bundled with this extension and whether it is **Installed** or **Not installed** in that business.
3. Lets you toggle each theme with an **Install** / **Uninstall** button.

Installation reads the theme's HTML file from this extension and writes it to the business via `PUT /api4/report-theme`. Uninstallation calls `DELETE /api4/report-theme`.

## Bundled themes

| Theme        | UUID                                   |
|--------------|----------------------------------------|
| Accessible   | `283e93e6-c155-49cc-9a85-79b68b283ff5` |
| Brutalist    | `90acd871-8694-4dd2-8d69-e241d710f310` |
| Classic      | `ae50197a-6ac2-4671-99f7-49cd26a9ff4a` |
| Compact      | `7d4fad7a-d86c-4edc-95dd-653b7bac97fd` |
| Corporate    | `6a57bc87-f06a-4034-80e8-30ede45cb342` |
| Editorial    | `4ab04352-5859-4972-aabb-5ba31b787ce4` |
| Handwritten  | `a9443bd6-1a86-4042-a1ed-0f1120541180` |
| Modern       | `233c2fc5-6272-49fb-90c0-8232703873bb` |
| Newspaper    | `264c4598-4fd9-441a-9eff-0db08fc2c969` |
| Ornamental   | `1844f0e2-5949-403d-bc22-12f97ae629e0` |
| Typewriter   | `3af91bf0-4fb8-4954-95f0-8bbfb9071035` |
| Vibrant      | `d124427e-e6c8-42c0-bbf9-38e36f8fe27d` |

A theme's UUID is derived from its filename: `{slug}-{uuid32}.html`. Reinstalling a theme overwrites the existing one in the business with the same UUID, so updates are non-destructive to the user's selection.

## How a business knows it has a theme installed

The extension paginates `GET /api4/report-theme-batch?Business=<name>` and intersects the returned `key` values with the UUIDs of the bundled themes. Themes added directly in Manager.io (with different UUIDs) are ignored — only this extension's bundled themes are listed.

## Adding a new theme

1. Drop the new HTML file into this directory using the naming convention `{slug}-{uuid32}.html`. The 32-character hex segment must be a UUID with hyphens removed.
2. Add the filename to the `THEME_FILES` array in `index.html`.

The slug is auto-converted into the display name (e.g., `news-letter` → `News Letter`), and the UUID is reconstructed from the 32-char hex segment.

## Files

- `index.html` — the extension UI loaded by Manager.io in an iframe.
- `*-{uuid}.html` — bundled report theme HTML files, fetched at install time and posted as the `template` field.

## API endpoints used

- `GET /api4/businesses` — populate the business dropdown.
- `GET /api4/report-theme-batch?Business=<name>&Skip=<n>&PageSize=50` — list themes already in the business.
- `PUT /api4/report-theme` — install (or overwrite) a theme.
- `DELETE /api4/report-theme?Key=<uuid>&Business=<name>` — uninstall a theme.

All requests are made via `window.parent.postMessage({ type: 'api-request', ... })` and resolved by matching `requestId` on the `*-response` reply, per Manager.io's iframe extension protocol.
