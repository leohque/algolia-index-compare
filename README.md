# Index Compare

A one-page tool for comparing two Algolia indices side by side. It's useful for checking a customer's dev index against a staging copy, or "before vs after" a config change.

It shows you, with differences highlighted:

- **Results:** which records rank differently, move up or down, or only show up on one side, plus each record's ranking info.
- **Settings:** every index setting that's different.
- **Synonyms:** synonyms that were added, removed or changed.
- **Rules:** rules that were added, removed or changed.
- **Batch:** a quick health check over a whole list of queries at once.

Nothing to install: it's a single `index.html` file.

---

## 1. Get an API key

Both indices must be in the **same Algolia application**.

You need a key with these two permissions (ACLs):

| ACL | Why |
|---|---|
| `search` | To run the queries and get ranking info |
| `settings` | To read the settings, synonyms and rules |

> ⚠️ A normal **search-only key is not enough**. Without the `settings` ACL, the Settings, Synonyms and Rules tabs can't load.

**Easiest safe option:** in the Algolia dashboard, open your application's **API Keys** page and create a **new API key** with:
- ACLs: `search` and `settings`
- Indices: just the two indices you want to compare

An Admin key also works, but a restricted key like this is safer.

The key only lives in the page while it's open. It's **never saved**, so you'll type it again each time you open the page.

## 2. Open the app

You should be able to run this tool by simply opening **index.html**


OR
You can also run it via

```bash
python3 -m http.server 8130
```

Then open **http://localhost:8130** in your browser.

(Stop the server later with `Ctrl + C`.)

## 3. Connect

1. Fill in the **Connection** box:
   - **App ID**
   - **API key** (the one from step 1)
   - **A Index:** the first index, e.g. `dev_products`
   - **B Index:** the second index, e.g. `dev_products_v2`
2. Click **Connect & load settings**.

The settings, synonyms and rules for both indices load right away, and an empty-query comparison runs automatically.

A red message at the top usually means the key is missing the `settings` ACL, or doesn't have access to one of the indices.

## 4. Compare

Type a query and press **Enter** (or click **Compare**). Then use the tabs.

The red number on each tab is how many differences it found. Green `0` means identical.

### Results tab
- The two result lists are shown side by side, **A** on the left and **B** on the right.
- Each result has a badge:

  | Badge | Meaning |
  |---|---|
  | `=` | Same position in both |
  | `▲2` (green) | Ranked 2 places **higher** here than on the other side |
  | `▼3` (amber) | Ranked 3 places **lower** here |
  | `only A` / `only B` (red) | Doesn't appear in the other result list at all |

- **Hover any result** to highlight the same record on both sides.
- The small chips under each result are its **ranking info**: typos, proximity, words, and so on.
  - **Amber chips** are values that differ from the same record on the other side. Hover one to see the other side's value.
  - **Dashed chips** are the record's `customRanking` values.
  - Tick **Only differing ranking fields** to hide the noise.
- The tiles at the top summarize everything: overlap %, same #1 result, how many results moved, and so on.

### Settings tab
- Only the settings that differ are listed. Untick **Differences only** to see them all.
- **not set / default** means that index doesn't set this option explicitly, so Algolia's default applies.
- For lists like `searchableAttributes` or `customRanking`:
  - **green** items are missing on the other side
  - **amber** items are at a different position

### Synonyms and Rules tabs
- Synonyms and rules are matched by their objectID.
- For anything that changed, you see a one-line summary plus **only the fields that differ**. Click **full JSON** to see the whole thing.
- **Same content, other objectID** means it's the same synonym or rule saved under a different ID. That's usually harmless.
- For synonyms, word order doesn't count as a difference.

### Batch tab
1. Paste a list of queries, one per line. Real top searches from Analytics work great.
2. Click **Run batch**.
3. You get a table sorted **most different first**. Click any row to open that query in the Results tab.
4. **Copy CSV** copies the table so you can paste it into a spreadsheet or a customer email.

## 5. Optional: shared params and display

Click **Shared search params & display**. Hover the **ⓘ** icon for a reminder of what each option does.

These options apply to **both** indices so the comparison stays fair:

- **filters:** e.g. `brand:"Acme" AND price < 50`
- **hitsPerPage:** how many results to compare. The default is 20.
- **Extra params (JSON):** any other search parameter, e.g. `{ "ruleContexts": ["mobile"] }`
- **Title / Image / Extra attributes:** which record fields to show on each result card. These only change the display.

Every query automatically sends `getRankingInfo: true`, plus `analytics: false` so your testing doesn't show up in the customer's Analytics.

---

## Good to know

- Your App ID, index names and options are remembered in your browser for next time. The **API key is not**.
- The tool **only reads data**. It never changes the indices.
- Rules and synonyms are fetched in full, but each table shows at most 500 rows at a time. Use the filter box to narrow down big lists.
