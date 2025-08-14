# BlueStars – New Product Campaign Builder (Notebook) 🚀🧩

Create **Amazon Ads** bulk sheets (SP/SB/SD) for **BlueStars** new products in minutes — without writing code.  
This README documents the notebook **`TẠO_CAMPAIGN_CHO_SẢN_PHẨM_MỚI_BLUESTARS.ipynb`** and how to run it end‑to‑end.

> There is a **Canamax** variant of this notebook. The logic is the same; only the **brand name** and **creative assets** (brand entity/logo, etc.) differ.

---

## ✨ Why a Notebook (not .py)?

- **Non‑coders friendly**: runs step‑by‑step with clear prompts and buttons for file upload/download.
- **Inline validation**: you can preview tables at each step before export.
- **Easy handoff**: a single file with instructions + code together; no CLI or environment flags to memorize.
- **Colab‑ready**: designed to work in Google Colab (uses `google.colab.files` for upload/download).

---

## 🧭 What the notebook does

1. **Collects inputs** via prompts  
   Market (**US/CA**), `product_name`, `SKU`, `ASIN`, **Portfolio ID**, `start_date (YYYYMMDD)`, **Target ASIN list**, **Image link**, **Video link**.
2. **Uploads keyword workbook** (Excel) and parses **four sheets**:
   - `lv1` – level‑1 keywords (columns: **Keyword**, **Group**)
   - `lv2` – level‑2 keywords (columns: **Keyword**, **Group**)
   - `negative` – negatives (column: **Keyword**)
   - `competitor_asin` – competitor ASINs (column: **ASIN**)
3. **Cleans & normalizes keywords**  
   Removes special chars, trims spaces, lowercases `Group`, drops duplicates.
4. **Builds negative sets** for SP, SB, and video campaigns.
5. **Creates campaign structures** with sensible defaults:
   - **Sponsored Products**: `Auto`, `Research`, `Performance`, `SP` (phrase/broad + negatives), `SP PT` (product targeting via competitor ASINs)
   - **Sponsored Brands**: `SB` (phrase), `Video Phrase`, `Video Broad`, `Video PT` (product targeting)
   - **Sponsored Display**: `SD PT` (product targeting via competitor ASINs)
6. **Applies brand assets per market** (BlueStars):  
   Uses **Brand Entity ID** and **Brand Logo Asset ID** (US/CA differ) and parses **Image/Video asset IDs** from your preview URLs.
7. **Exports one Excel** with 3 sheets and **downloads** it for Amazon Bulk upload:
   - `Sponsored Products Campaigns`
   - `SB Multi Ad Group Campaigns`
   - `Sponsored Display Campaigns`  
   File name: `"<product>_campaigns.xlsx"`

---

## 📦 Requirements

- **Google Colab** (recommended) or local Jupyter with Python 3.10+
- Libraries used: `pandas`, `re`, `google.colab` (for Colab upload/download)
- An **Amazon Ads** bulk‑upload account with the right **Portfolio ID**

> If you run locally instead of Colab, replace the `google.colab.files` upload/download helpers with local I/O.

---

## 🗂️ Prepare your keyword workbook (Excel)

Create a single Excel file with **four sheets** (exact names):

1. **`lv1`**  
   Columns: `Keyword`, `Group` (e.g., *phrase*, *broad*, *exact*, *spf* etc.).  
   > The notebook derives **Match Type** from `Group` and sets default **bids** for each campaign type.
2. **`lv2`**  
   Same columns as `lv1`. Optional; leave blank if not used.
3. **`negative`**  
   Column: `Keyword`. Will be set to **negative phrase** automatically.
4. **`competitor_asin`**  
   Column: `ASIN`. Used to build **product targeting** rows for **SP PT**, **Video PT**, and **SD PT**.

Tips:
- If a sheet is not applicable, **keep the sheet but leave it empty** (don’t delete it).
- Keep keywords short and meaningful; the notebook flags/filters overly long entries in some steps.

---

## 🔐 Brand setup & creative assets

This BlueStars notebook sets **Brand Entity ID** & **Brand Logo Asset ID** **per market**:
- **US** → sets BlueStars **Brand Entity** and **Logo** for US
- **CA** → sets BlueStars **Brand Entity** and **Logo** for CA

It also converts your **Image** and **Video** links (from Amazon Asset Library **preview URLs**) into the **asset IDs** required by bulk sheets.

> For **Canamax**, use the *Canamax* version of the notebook with its brand entity and asset IDs. The logic and steps remain identical.

---

## ⚙️ Defaults & logic (key choices)

- **Daily Budget**: `10` for each campaign (change inside notebook cells if needed).
- **Bids** (initial, editable in cells):
  - SP **Auto**: `Ad Group Default Bid = 0.5`
  - SP **Research**: `bid = 0.1` for *broad*; negatives from `negative` + `Group` rules
  - SP **Performance**: `exact` terms only, `bid = 0.5`
  - **SP** (phrase/broad) + negatives applied
  - **SP PT** (product targeting): `bid = 0.5`
  - **Video PT** (product targeting): `bid = 0.3`
  - **SD PT**: `Budget = 10`, `Cost Type = CPC`
- **Bidding strategy** (SP): *Dynamic bids – down only*
- **Brand name** in SB Video creatives is set to **"BlueStars"**; change in the cell for other brands.
- **Dates**: `Start Date` uses your input (`YYYYMMDD`); `End Date` left blank.
- **Asset parsing**: the notebook extracts `.../preview/<ASSET_ID>/version...` and converts to the required `asset_id:version` format.

---

## ▶️ How to run (Google Colab)

1. **Open the notebook** in Colab.
2. **Run the first cells** to load packages and show the **input prompts**:
   - Market (**US/CA**), Product Name, `SKU`, `ASIN`, `Portfolio ID`, `Start Date (YYYYMMDD)`, **Target ASIN list**, **Image link**, **Video link**.
3. **Upload the Excel** when prompted (one file with the four sheets above).
4. **Run through the campaign sections** (SP → SB → SD).  
   You’ll see DataFrames previewing the rows that will be exported.
5. **Export**: Run the final **Download** cell to get `"<product>_campaigns.xlsx"`.
6. **Upload to Amazon Ads Bulk**: go to Bulk Operations and upload the generated file.

---

## 📄 Output format (Excel)

The exported workbook contains the required columns for Amazon Bulk sheets. Highlights:

- **SP sheets** use columns like:  
  `Product, Entity, Operation, Campaign Id, Ad Group Id, Portfolio Id, Ad Id, Keyword Id, Product Targeting Id, Campaign Name, Ad Group Name, Start Date, Targeting Type, State, Daily Budget, sku, asin, Ad Group Default Bid, Bid, Keyword Text, Match Type, Bidding Strategy, Placement, Percentage, Product Targeting Expression`

- **SB sheets** use columns like:  
  `Product, Entity, Operation, Campaign ID, Portfolio ID, Ad Group ID, Ad ID, Keyword ID, Product Targeting ID, Campaign name, Ad group name, Ad name, Start date, State, Brand Entity ID, Budget type, Budget, Bid optimization, Product location, Bid, Placement, Percentage, Keyword text, Match type, Native language keyword, Landing page URL, Landing page type, Brand name, Brand logo asset ID, Consent to translate, Video asset IDs, Creative ASINs`

- **SD sheets** use columns like:  
  `Product, Entity, Operation, Campaign Id, Portfolio Id, Campaign Name, Start Date, Tactic, Budget Type, Budget, Cost Type, Ad Group Id, Ad Group Name, Bid Optimization, Product Ad, sku, Product targeting expression`

> Column cases and exact names follow Amazon’s bulk templates for SP/SB/SD. Review previews before export.

---

## 🧯 Troubleshooting

- **Import/Upload issues** → Ensure sheet names are exact: `lv1`, `lv2`, `negative`, `competitor_asin`.
- **No negatives** or **no lv2** → keep the sheet but leave rows empty; the notebook handles empties.
- **Asset links not recognized** → Ensure links are **Amazon Asset Library preview URLs**; the notebook extracts the ID after `/preview/`.
- **Portfolio ID vs. Name** → The notebook expects the **numeric/ID**, not the portfolio display name.
- **Bids/Budgets** → Adjust directly in the cells that build the campaign rows; re‑run cells after editing.
- **Locale** → Market selection (**US/CA**) also switches brand entity & logo IDs for BlueStars.

---

## 🛠️ Customization

- Change default **budgets/bids** by editing the dict rows in the cells that create each campaign frame.
- Add/remove **campaign variants**: e.g., duplicate a section to create *Exact Phrase* or *Category Targeting*.
- Switch **brand**: use the Canamax notebook variant (asset IDs & brand strings swapped).

---

## ✅ FAQ

**Q: Can I run this for Canamax?**  
A: Yes. Use the **Canamax** notebook (same code; brand entity/logo IDs updated).

**Q: Can I run locally?**  
A: Yes, in Jupyter. Replace `google.colab.files.upload()/download()` with local file I/O.

**Q: Where do I change bids/budgets?**  
A: In the cells that build the SP/SB/SD `dict` rows (search for `'Daily Budget'`, `'Bid'`, `'Ad Group Default Bid'`).

**Q: Does it support only US/CA?**  
A: The notebook prompts for **US** or **CA** and swaps the correct brand assets. Extendable to other markets by adding IDs.

---

## 📌 Notes

- Keep your **keyword workbook** tidy. Good grouping (`Group` column) helps the notebook map **Match Types** correctly.
- Check the **SB Video** sections for the **Brand name** string (set to *BlueStars*); change for other brands if you fork this file.
- Always upload the final Excel to **Amazon Ads Bulk** and review success logs.

---

## 🧪 Changelog / Roadmap (suggested)

- v1: BlueStars US/CA, SP/SB/SD with negatives and PT variants
- vNext: Parameterize bids/budgets via a “Config” cell; add category/product targeting templates; optional campaign end date.

