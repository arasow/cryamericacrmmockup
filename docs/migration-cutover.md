# Data migration and go-live cut-over

The old DMS stays the system of record until **31 December 2026**. From **1 January 2027** every gift is entered or imported only in the new CRM. Gifts up to 31 December are migrated as history: original details kept, **never posted to QuickBooks**, never a receipt or thank-you again.

## Old campaigns and causes

| Data | FY 2023–FY 2026 (Live window) | FY 2022 and earlier |
|---|---|---|
| Campaigns | Mapped to the new Campaign Master with a mapping sheet staff review once (these gifts drive the email segments) | One Closed legacy campaign per category, year and Action Center where known, e.g. `Legacy Events FY2019 – SDA`; unclear → `Legacy – unknown FY____` |
| Causes | Mapped to the new cause list where the old purpose is clear | `General Donations (legacy)` unless clear |
| Original values | Kept on every gift in read-only fields: `legacy_campaign_code`, `legacy_campaign_name`, `legacy_cause` | Same |

Legacy campaigns carry only code, name, category, location (if known), FY, status Closed and a Legacy flag: no QB class, no source links, no tele-callers.

## Rules for every migrated gift

- Keep original gift date, amount, payment method and old receipt number; flag `Migrated = true`.
- Never posted to QuickBooks (QuickBooks Online history is migrated separately by KLR).
- No receipt, thank-you or email is sent again.
- Store gateway transaction IDs (Auth.net, Givebutter, Charitable, Event Espresso) as unique keys, so API pulls from 1 January cannot create the same gift twice.
- Migrate active Auth.net ARB donors as pledges with their ARB subscription ID before 1 January.

## Keep every donor

Migrate all donors (about 51,000) even if only 10 or 12 years of gifts are migrated in detail. Each donor carries first gift date, last gift date, lifetime total, and one summary row per FY for years whose gifts are not migrated, so Lapsed and Super-lapsed segments stay correct. Keep the full old data as a read-only archive export.

## Cut-over plan

| When | What happens |
|---|---|
| By 15 Nov 2026 | Mapping sheet for old campaigns and causes built and reviewed |
| Mid–late Nov | One-time clean-up (Smarty, Clearout, ZeroBounce); trial migration; counts and totals checked by FY and campaign |
| About 20 Dec | Main migration; old DMS stays live |
| 1–5 Jan 2027 | Final top-up load of everything entered in the old DMS up to 31 Dec; reconcile; old DMS read-only |
| From 1 Jan 2027 | All entry in the new CRM; API pulls start from the cut-over time |
| About 31 Jan 2027 | December 2026 locked; FY 2026 audit-locked after sign-off (WF-13) |

**Late-December gifts:** a check postmarked by 31 December is entered in the new CRM with its 2026 gift date (December stays open until about 31 January), posts to QuickBooks on the January deposit date, and the receipt shows the 2026 gift date.

**Sign-off:** donor counts, gift counts and dollar totals must tie to the old DMS for every FY and each mapped campaign.

Open points are in [accountant-questions.md](accountant-questions.md).
