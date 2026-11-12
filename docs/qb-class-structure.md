# QuickBooks class structure: State : Class group : Action Center

| Level | Holds | Set from |
|---|---|---|
| 1 · State | USPS 2-letter code (QBO short name, `data/states.csv`) | The Action Center's state for an event; the donor's state for website, monthly, matching, occasion, mailer and tele-calling gifts; OC or Foreign outside the US (Michelle to decide) |
| 2 · Class group | One of 15 fixed groups ([`data/qb_class_groups.csv`](../data/qb_class_groups.csv)) | The campaign's Category · Sub-category (Campaign Master field **Class group**) |
| 3 · Action Center | Short name from Locations ([`data/qb_class_level3.csv`](../data/qb_class_level3.csv)) | The campaign's location; left out when it has none |

Examples: `TX:Dandiya & Garba:Houston` · `CA:Music & Dance:San Diego` · `NJ:Walk, Run & Ride:New York-New Jersey` · `TX:General Donations` (website gift, Texas donor).

Build rules:

1. The CRM builds the class name from the campaign (state + class group + Action Center) and looks up the QBO class **Id**; it never creates classes.
2. A class that does not exist in QBO sends the gift to the exceptions queue for the accountant to create.
3. The event name is the CRM campaign, never a class.
4. Migrated gifts keep their old class; old classes are made inactive after the QBO migration.
5. About 440 classes cover all history (55 state, 200 state + group, 185 three-level), so QBO Advanced is needed (QBO Plus allows 40 classes and locations combined). Decision: QBO Advanced through TechSoup at $170 a year; CRY America is an eligible 501(c)(3).
6. Class settings in QBO: track classes **one to each row in transaction**, because a daily deposit carries gifts for several campaigns. Locations stay off.
7. Budgets: the FY 2027 budget is loaded in QBO by account and class, so Budget vs Actuals can be pulled by state, class group and Action Center (question 32).

Class decisions are taken jointly by Michelle (KLR) and the CRY America Executive Director.

The full mapping of the 550 old classes is kept internally, not in this repository, because some old class names contain people's names. Open points: questions 6, 7 and 26–33 in [accountant-questions.md](accountant-questions.md).
