# CRY America Donor CRM — clickable mockup

A clickable prototype of the CRY America Donor CRM: desktop screens, tele-calling, volunteer management, payments and QuickBooks, integrations, masters and workflows. Donor names are shortened (first name + last initial); sample screens are marked as such.

Start page: `index.html`

## Publish on GitHub Pages

1. Create a new repository on GitHub and upload everything in this folder to the root of the repository (keep the `thumbs` folder and the `.nojekyll` file).
2. In the repository, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to *Deploy from a branch*, choose the `main` branch and the `/ (root)` folder, then **Save**.
4. After a minute or two the site is live at `https://<your-account>.github.io/<repository-name>/`.

## For the Zoho developers: data files

| File | Rows | What it is |
|---|---|---|
| `data/zip_master.csv` | 33,121 | Zip Codes master, ready to import: zip (5-digit text, leading zeros restored), city, county, state_code, state_name, time_zone (IANA), time_zone_name, latitude, longitude |
| `data/states.csv` | 56 | State short codes used as the QuickBooks class: 52 from Google's states.csv plus USPS codes for VI, GU, MP, AS, with the ZIP count per state |

Import the ZIP column as **text**, never as a number, or the leading zeros are lost again. The rules for cleaning and checking addresses are in workflow WF-11 and on the *Masters → Zip codes* screen.

State codes source: [Google DSPL states.csv](https://developers.google.com/public-data/docs/canonical/states_csv) (CC BY 4.0).

## Notes

- The screens need to be opened from a web address (GitHub Pages or any web server). Double-clicking the files on your computer shows some lists empty.
- The pages load React from cdn.jsdelivr.net and fonts from Google Fonts, so viewers need an internet connection.
- `support.js` is the small runtime that makes the screens interactive. Every screen file must stay in the same folder as `support.js` and `index.html`.
- A GitHub Pages site from a public repository is visible to anyone with the link. Use a private repository with an organisation plan that supports private Pages if the mockup should stay internal.
