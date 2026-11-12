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
| `data/states.csv` | 57 | State short codes used as the QuickBooks class: 52 from Google's states.csv, USPS codes for VI, GU, MP, AS, and OC (Other Country) for any donor outside the United States, with the ZIP count per state |
| `data/fiduciaries.csv` | 36 | Fiduciary master seed: type, legal payer, portal login email, default payment type and QB bank, name on the bank credit, receipt rule |
| `data/company_routes.csv` | 125 | Company → fiduciary routes with payment type and QB bank from CRY's FIDUCIARY Details sheet, duplicates removed and names corrected (Oct 31, 2026); `flags` marks the rows still to fill |
| `data/offices.csv` | 51 | Locations (Action Centers + corporate office) from the old CRM with QB class prefix, AC bank and a `flags` column (placeholder addresses, overlaps, code clashes); Houston added as missing. Street addresses and phones left out |
| `data/qb_banks.csv` | 36 | QB bank accounts from the old CRM: key, old name, current QB name, what the CRM uses it for, flags |
| `data/qb_class_groups.csv` | 15 | QB class groups (level 2) and the campaign category · sub-category each comes from |
| `data/qb_class_level3.csv` | — | Action Centers used as level 3, with state and office code |
| `data/qb_payment_methods.csv` | 10 | QB payment methods with key and how the CRM uses each |

Check the country before the ZIP: anything other than US / USA / United States / blank gets state code OC, the ZIP check is skipped, and the postal code is kept as typed.

Store QuickBooks **keys** (ListID now, QBO Id after January 2027), not names: QB renames accounts and classes, and the old CRM still shows old names. The QB class list (550) is synced from QuickBooks, not seeded from a file.

Import the ZIP column as **text**, never as a number, or the leading zeros are lost again. The rules for cleaning and checking addresses are in workflow WF-11 and on the *Masters → Zip codes* screen.

State codes source: [Google DSPL states.csv](https://developers.google.com/public-data/docs/canonical/states_csv) (CC BY 4.0).

## Migration and open questions

- [docs/migration-cutover.md](docs/migration-cutover.md): how old campaigns, causes, donors and gifts move from the old DMS, and the cut-over plan for a 1 January 2027 go-live.
- [docs/qb-class-structure.md](docs/qb-class-structure.md): the 3-level QuickBooks class structure (State : Class group : Action Center) and how the CRM builds the class.
- [docs/accountant-questions.md](docs/accountant-questions.md): every open accounting question for Michelle (KLR) on one page.

## Campaign Master (WF-16)

- **Set-up:** code, name, unique short name (= `utm_campaign`), category, sub-category, location, status; QBO class (donor's state rule or a fixed class) and default cause; one link per source; tele-callers when it is a tele-calling campaign.
- **Matching order on import:** 1 Event Espresso code · 2 Charitable ID or parent ID · 3 Givebutter appeal ID · 4 Website Campaign Name + Mailer Code, or donation scheme · 5 Auth.net invoice description. Monthly ARB charges take the campaign from the pledge (ARB subscription ID).
- **No match:** the gift is saved and waits in the unmapped-campaign queue; when staff pick the campaign, the link is saved on it so the next gift matches automatically.
- **Receipt rule:** CRY receipt only when the payment type has Print receipt ticked and the payer is not a fiduciary; otherwise "issued by Givebutter / Benevity / …".
- **Child records (lookup to the campaign):** mail drops and returns (WF-14), EDM mailers (WF-15), call lists, pledges; totals roll up to the campaign summary.
- **Change or close:** edits are logged; gifts in a locked month cannot be moved to another campaign (WF-13); Inactive campaigns are hidden from manual entry and any gift that still matches is flagged for review.

## EDM sync (screen 14, WF-15)

- Constant Contact gets **Live** donors only (current FY + last 3 years); Lapsed and Super-lapsed go to Zoho Campaigns. A donor is in one platform at a time.
- Push: Monday and Thursday, 2:00 AM ET. Constant Contact `POST /v3/activities/contacts_json_import` (up to 40,000 contacts / 4 MB per call, 1–50 list_ids, custom fields as `cf:` properties); poll `GET /v3/activities/{id}`. Zoho Campaigns `addlistsubscribersinbulk`.
- Pull every night: unsubscribes (`GET /v3/contacts?status=unsubscribed&updated_after=`), bounces (`/v3/reports/email_reports/{id}/tracking/bounces`), campaign summaries; Zoho Campaigns `getlistsubscribers` by status. These set Unsubscribed / Hard bounce on the donor master.
- ZeroBounce every night on new or changed emails (`validatebatch`, or `sendfile`/`getfile` for big files) and live on the tele-calling screen before a revised email is saved.
- Each campaign in the Campaign Master has a unique **short name**; each EDM is a **mailer** under the campaign with its own **mailer code**. The CRM builds every donate link, and the website stores the four campaign fields:

  | Website field | UTM parameter | Value |
  |---|---|---|
  | Campaign Medium | `utm_medium` | `email` |
  | Campaign Source | `utm_source` | `CC` (Constant Contact) or `Zoho` (Zoho Campaigns) |
  | Campaign Name | `utm_campaign` | campaign short name, e.g. `seadandiya26` |
  | Mailer Code | `utm_content` (confirm with the web team) | mailer code, e.g. `EDM-2610-SEA-DANDIYA` |

  On import, Campaign Name finds the campaign (FK) and Mailer Code finds the mailer. The CRM keeps summaries only: sent, delivered, opened, clicked, bounced, unsubscribed, gifts, amount.
- Hard bounces with a phone not on DNC go to an `EMAIL-<month>` tele-calling list; staff choose the callers.
- **GOPS file from CRY India** (quarterly or half-yearly, donors whose country is USA): upload the Excel; each row is matched by CRY India donor ID → email → phone → name + ZIP (possible match, staff decide). Existing donors only get the GOPS flag and CRY India ID (nothing overwritten); **only missing donors are added**, source GOPS · CRY India, with ZeroBounce and Smarty checks. No gifts are created. Each upload is a GOPS file record linked to its rows.

## Check deposit (screen 11, WF-9)

- The accountant collects checks every Tuesday, scans them and uploads the PDF; the CRM reads each check and suggests donor, campaign, QB account and class.
- **The deposit never waits.** A check nobody can identify is marked *Needs mapping* and deposited on class `1-Corporate:Unidentified checks`, account Unsolicited Donations:Individual, with the payer as "received from". The QBO deposit (one line per check) always equals the deposit slip.
- The CRM emails the fundraisers a short form (campaign, donor, optional split). Reminders on day 3 and day 7; day 10 goes to the Operations Head.
- Staff confirm the answer. Month open: the CRM edits the QBO deposit line (same total, splits allowed). Month locked: a reclass journal entry in the open month.
- The receipt or thank-you goes out only after mapping (target 14 days). Month-end *Unidentified checks* report to the accountant and Michelle; after 90 days the gift stays a general gift.
- Fiduciary checks (CyberGrants, Benevity, DAFs) without a remittance are deposited the same way, not held. See Q33 in `docs/accountant-questions.md`.

## Brand and fonts

- Colours, logo and photos follow the **CRY America Communications Guidelines**: primary yellow `#FFD806` (at least 20% of each screen), secondary turquoise `#1CB7B8`, orange `#F16723`, purple `#6B2F67`, pink `#E4296B`. The logo on yellow is the yellow version with no white outline (`assets/cry-logo-yellow.png`); never stretch, tilt, invert or watermark it.
- Fonts follow the developer font spec: the system UI stack `system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`, text `#1a1a1a`, no Google Fonts or web fonts. KPI numbers 700–800, table headers and labels 500, body 400, and `font-variant-numeric: tabular-nums` on table data and KPIs.
- Filter and form boxes: white fill, 1.5px `#6C757D` border with a black dropdown arrow; darker border on hover; on focus a 2px ink outline with a yellow `#FFD806` ring; a filter that is not on its default value turns light yellow `#FFF6CC` with an ink border; placeholders `#6B6B6B`; labels weight 500.
- Status colours use brand tints: turquoise = good, orange = warning, pink = problem, purple = information. In the workflow boards: yellow = start, white = person, purple = automatic, pink = warning, turquoise = end.

## Notes

- The screens need to be opened from a web address (GitHub Pages or any web server). Double-clicking the files on your computer shows some lists empty.
- The pages load React from cdn.jsdelivr.net, so viewers need an internet connection. Fonts are the system fonts already on each computer.
- `support.js` is the small runtime that makes the screens interactive. Every screen file must stay in the same folder as `support.js` and `index.html`.
- A GitHub Pages site from a public repository is visible to anyone with the link. Use a private repository with an organisation plan that supports private Pages if the mockup should stay internal.
