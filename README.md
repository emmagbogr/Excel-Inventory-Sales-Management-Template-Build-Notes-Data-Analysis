Excel Inventory & Sales Management Template — Build Notes & Data Analysis

A single-file Excel inventory, sales, and receivables system built for VitalWell Nutrition Ltd — a fictional Ghana-based supplement retailer running a central warehouse and three shops (all names and figures are sample data). This writeup covers how the workbook is architected, how a realistic sample dataset was generated to stress-test it, and what the numbers say.

Currency: GHS (Ghana Cedi)  ·  Costing basis: Weighted Average Cost (WAC)  ·  Demo period: Q2 2026 (01-Apr – 30-Jun)

1. What this workbook does
The template runs a small multi-location retail operation end to end:
•	Maintains master records for items, suppliers, customers and locations, and captures goods receipts, sales invoices, stock adjustments and customer payments.
•	Values inventory on a weighted-average basis, computing COGS and gross profit per line, and tracks receivables with automatic aging buckets.
•	Rolls everything up into a Dashboard, an inventory KPI page and an ABC analysis.
It is deliberately formula-driven: you type raw transactions, and every derived value — names, landed cost, WAC, COGS, gross profit, balances, aging — is calculated by the workbook, so the logic stays transparent and auditable.

2. Architecture
The workbook is organized into four layers across 22 worksheets.

<img width="1378" height="688" alt="image" src="https://github.com/user-attachments/assets/4ed062c7-8a74-40a9-9a3f-e484e407865e" />

2.1 Data flow

Master data --+
              +--> Purchases Log --> Stock Ledger --> Stock on Hand --+
Price History-+                                                       |
                                                                      +--> KPIs / Dashboard / ABC
WAC --> Sales Log (COGS, GP) -----------------------------------------+
        |
        +--> Receivables <-- Payment Receipts

3. Design principles
Weighted-average costing. Each sale’s unit cost is the running weighted average of all receipts for that item up to the sale date — computed with SUMIFS(total cost)/SUMIFS(qty) rather than stored, so it always reflects the true blended cost.
Effective-dated price history. Cost and selling prices are stored as dated rows; the current price is the most recent row on or before the transaction date. Full audit trail, and historical invoices reprice correctly.
Tiered pricing. Every item carries Retail / Wholesale / Promo prices. The sale line picks by tier, applies any line discount, and a price-check column flags breaches of the 10% minimum-margin floor.
Formula-driven derived columns. Users fill only raw input columns; names, landed cost, WAC, COGS, gross profit and aging are formulas — click any number and read its logic.
 
<img width="1398" height="528" alt="image" src="https://github.com/user-attachments/assets/6c588d9f-d140-4bb7-9353-721e59fad100" />

4. The sample dataset
A coherent Q2 2026 dataset was generated and posted into the transaction sheets — kept on a standalone Sample Data sheet and mirrored into the live logs. Shape: 13 items priced (SUP-001 … SUP-013), 96 goods-receipt notes, 478 sales invoice lines, 15 stock adjustments, 38 payment receipts and 101 credit invoices.

4.1 How it was made coherent
•	No negative stock. Purchases and sales were simulated day-by-day against a running balance per (item, location) — a sale is only written if that shop currently has stock. Opening receipts land on 01-Apr, with mid-quarter replenishments.
•	Realistic behaviour, costs and prices. Sales are weighted by item popularity; walk-ins buy retail, the wholesale account buys in bulk, ~12% of lines are discounted and some sell at Promo. Landed cost splits into supplier price + duty + freight + clearing + other, with retail ≈ 1.9× cost and wholesale ≈ 1.5×. Shops close Sundays, batches carry mfg/expiry dates, and credit purchases and sales generate receivables and later receipts.

5. Results & analysis
All figures are Q2 2026 (01-Apr – 30-Jun), in GHS, as computed by the workbook.
<img width="1216" height="696" alt="image" src="https://github.com/user-attachments/assets/affaaeed-e54f-439f-985d-e3f3081781ce" />

5.1 Headline P&L
Sales revenue 265,972 · COGS (weighted-avg) 158,077 · gross profit 107,895 · gross margin 40.6% · 2,538 units sold · average value per line 556 · purchases received 806,591 at cost across 96 GRNs. A 40.6% blended margin sits squarely in the healthy 30–50% band for supplement retail.

5.2 Top sellers (by revenue)
<img width="1386" height="358" alt="image" src="https://github.com/user-attachments/assets/43c30a92-4c31-4f64-8b25-f78a305ee815" />
Mila is the volume outlier: 410 units — the highest of any item — but only 9,125 in revenue at the best margin (46.4%). A cheap, fast-moving sachet: a classic traffic-builder.

5.3 ABC classification
 <img width="1312" height="438" alt="image" src="https://github.com/user-attachments/assets/ea4125a9-2dd4-409e-b51a-61879c67bc83" />
ABC analysis (revenue-based) — top 12 of 13 lines shown
Revenue is concentrated as Pareto predicts: Class A — 9 items, 79% of revenue (the focus lines); Class B — 2 items (Neuroboost, Purezyme), 12%; Class C — 2 items (Organic Sulfur, Mila), 9%, worth reviewing for promotion or rationalization.

5.4 Channel, tier & location mix
Tier mix: Retail 56% · Wholesale 39% · Promo 5%. Payment: Cash 74% · Credit 26%. Locations: Shop 2 (94.8k) > Shop 3 (91.4k) > Shop 1 (79.7k). Top customer: MedPlus Wholesale at 103,521 — the single largest account at ~39% of revenue, effectively the entire wholesale tier, and the source of most credit exposure, a concentration worth monitoring.

5.5 Receivables & aging
<img width="1396" height="554" alt="image" src="https://github.com/user-attachments/assets/5a70879e-3a6c-4045-a22a-143d60014221" />
Of the 101 credit invoices, 25 are paid or settled and 76 remain outstanding — 18 current, 19 at 1–30 days, 21 at 31–60 and 18 at 61–90 — so the full aging logic is exercised. The outstanding balance is GHS 48,335 against GHS 20,894 collected in the period; with ~GHS 48k open on ~GHS 69k invoiced on credit, collection discipline is the main working-capital lever.

5.6 Inventory KPIs (and an important caveat)
 <img width="1406" height="534" alt="image" src="https://github.com/user-attachments/assets/f8b81202-458b-4d55-bea1-50d58ec40c86" />
Stock value 645,243 across 10,363 units · gross margin 40.6% · GMROI 0.17 · sell-through 19.7% · dead stock 0 · inventory turnover 0.24× · DSI ~1,490 days.
Caveat: the simulation deliberately over-stocked the opening position so no shop ever ran negative, leaving ~GHS 645k of inventory against one quarter of COGS (158k). That mechanically depresses turnover and inflates DSI — a property of the sample data, not a formula error; ~1 month of opening cover would give a realistic 4–5× annualized turnover.

6. How the workbook was built (summary)
   Laid out the four layers (master → pricing → transactions → outputs) as separate sheets.
   Converted transaction ranges to Excel Tables for structured references and autofill.
   Built the WAC and effective-dated pricing formulas; wired COGS/GP into the Sales Log.
   Added the Stock Ledger / Stock on Hand roll-ups and the Receivables aging engine.
   Built the Dashboard, KPI, and ABC pages on top of the ledgers.
   Generated a coherent Q2 2026 dataset and posted it, verifying zero formula errors and that outputs tie out end-to-end (revenue 265,972 → margin 40.6% across Sales Log, KPIs, and Dashboard).

Built and analyzed with Claude in Excel.
