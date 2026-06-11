# ConfigGard™

**Preflight validation and safe correction tooling for Microsoft Dynamics 365 Business Central + Dynamic Ship.**

ConfigGard is a suite of standalone browser-based tools that detect configuration misalignments in BC before they cause operational failures. No installation, no backend, no data leaves your machine.

---

## ShipGard™ — Dynamic Ship Carrier Alignment

ShipGard is the first module in the ConfigGard suite. It cross-references your carrier setup across all four Dynamic Ship tables and surfaces every conflict — carrier mismatches, missing templates, payment classification errors — before they hit the dock.

### What it checks

| Check | Tables Involved |
|---|---|
| Carrier mismatch: Customer card vs Ship-to Address | Table 18 · Table 222 |
| Carrier mismatch: Customer card vs Customer Options | Table 18 · Table 23044614 |
| Shipment Method mismatch: Customer card vs Ship-to | Table 18 · Table 222 |
| Missing catch-all Customer Options row | Table 23044614 |
| Missing Package Options template reference | Table 23044608 |
| Payment Type vs Shipment Method conflict | Table 23044608 |
| LTL accounts incorrectly set as Receiver | Table 23044608 |
| Account number length vs carrier (UPS=6, FedEx=9) | Table 23044608 |
| Default Label UOM ≠ BOX | Table 18 |

### How to use it

1. In Business Central, open your Dynamic Ship config package and export to Excel. The workbook must contain all four required tables as separate tabs.
2. Open `ShipGard.html` in any modern browser.
3. Drop the exported workbook onto the upload zone.
4. Review the analysis — issues are classified as Safe Fix, Key-Field Review, or Manual Only.
5. Click **Export Safe Correction Package** to download two files:
   - `ShipGard_Preflight_Report_[date].xlsx` — full audit report with all findings, action types, risk notes, and manual-review items
   - `ShipGard_Safe_Corrections_[date].xlsx` — BC-ready correction package containing only safe auto-fix rows

### Required BC config package tables

| Table ID | Table Name |
|---|---|
| 18 | Customer |
| 222 | Ship-to Address |
| 23044608 | Dynamic Ship Package Options |
| 23044614 | Dynamic Ship Customer Options |

### Issue classification

| Classification | Meaning | Included in Corrections File? |
|---|---|---|
| **SAFE FIX** | Non-key field update. Safe to import directly. | ✅ Yes |
| **VALIDATE** | Key-field change on Customer Options, or ambiguous payment logic. BC may insert a new row rather than update the existing one. Verify before applying. | ❌ No |
| **MANUAL** | No auto-fix available. Requires direct BC intervention. | ❌ No |

### Audit trail workflow

Before importing the correction package:

1. Create a folder named `ShipGard_[today's date]`
2. Save your original BC config export in that folder
3. Download both ShipGard output files into the same folder

This gives you a complete before/after record for every recommended change.

### Key-field change warning

Dynamic Ship Customer Options uses a composite primary key:
`Customer No. + Ship-To Code + Shipping Agent Code + Shipping Agent Service Code`

When ShipGard proposes changing the Shipping Agent Code on an existing row, BC may treat the corrected row as a **new record** rather than updating the existing one. The old row may remain. ShipGard flags these as VALIDATE and excludes them from the safe correction file. Review the Key-Field Review tab in the Preflight Report before acting on these.

---

## Preflight Report structure

| Sheet | Contents |
|---|---|
| Summary | Issue counts by category and by table. Findings vs export rows. |
| All Findings | Complete audit list with action type, old/new values, and risk notes. |
| Safe Fixes | Findings that went into the correction package. |
| Key-Field Review | Rows requiring validation before import. |
| Manual Only | Issues that require direct BC intervention. |
| Run Notes | Source file, timestamp, record counts, tool version. |

---

## Roadmap

ShipGard is the first module in the ConfigGard suite. Planned modules:

| Module | Problem it solves |
|---|---|
| **ShipGard** | Dynamic Ship carrier alignment *(current)* |
| **PlanSync** | Inventory planning parameter validation — ADU, Min/Max, MOQ, lead times |
| **PriceSync** | Customer pricing alignment across price groups and tiers |
| **ItemSync** | Item master data completeness and posting group validation |
| **VendorSync** | Vendor and supplier alignment across purchase setup |

---

## Technical notes

- Runs entirely in the browser. No server, no API calls, no data transmission.
- Reads BC config package exports directly — no reformatting required.
- Output files preserve the original BC XML map structure for direct import compatibility.
- Built with vanilla JavaScript + JSZip + SheetJS.

---

## Attribution

Built by [Ron Jones](https://www.linkedin.com/in/ronjones4133/) · Supply Chain & ERP Systems

© 2026 ConfigGard™ · All rights reserved · Source code proprietary
Not affiliated with Microsoft Corporation or Integrated Warehouse Solutions.
