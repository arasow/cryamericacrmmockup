# CRY America Donor CRM — clickable mockup

A clickable prototype of the CRY America Donor CRM: 12 desktop screens, 4 tele-calling screens, 7 volunteer-management screens, 3 payment and QuickBooks screens and 7 workflows. All names, amounts and contact details are sample figures for layout only.

Start page: `index.html`

## Publish on GitHub Pages

1. Create a new repository on GitHub and upload everything in this folder to the root of the repository (keep the `thumbs` folder and the `.nojekyll` file).
2. In the repository, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to *Deploy from a branch*, choose the `main` branch and the `/ (root)` folder, then **Save**.
4. After a minute or two the site is live at `https://<your-account>.github.io/<repository-name>/`.

## Notes

- The screens need to be opened from a web address (GitHub Pages or any web server). Double-clicking the files on your computer shows some lists empty.
- The pages load React from cdn.jsdelivr.net and fonts from Google Fonts, so viewers need an internet connection.
- `support.js` is the small runtime that makes the screens interactive. Every screen file must stay in the same folder as `support.js` and `index.html`.
- A GitHub Pages site from a public repository is visible to anyone with the link. Use a private repository with an organisation plan that supports private Pages if the mockup should stay internal.
