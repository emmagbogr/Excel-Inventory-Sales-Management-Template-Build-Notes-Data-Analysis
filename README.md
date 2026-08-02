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
Layer	Sheets (22 total)
Navigation & docs	Index (hyperlinked TOC) · User Guide · Settings (currency, costing method, near-expiry and slow-mover thresholds)
Master data	Locations · Items Master · Suppliers · Customers — codes, categories, reorder levels, lead times, default price tier, credit limits and terms
Pricing engine	Price History – Cost (effective-dated landed-cost build-up) · Purchase Costing · Price History – Selling (per tier) · Selling Prices (+ minimum-margin guardrail)
Transactions (input)	Purchases Log (GRNs with batch and expiry) · Sales Log (customer, item, qty, tier, discount) · Stock Adjustments (damage, expiry, count, theft) · Payment Receipts (cash/MoMo/bank/cheque)
Outputs (calculated)	Stock Ledger · Stock on Hand · Receivables (with aging) · Customer Statement · Dashboard · KPIs (turnover, DSI, GMROI, sell-through, dead stock) · ABC Analysis
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
 
Sales Log — formula-driven COGS & gross profit
Structured tables. Transaction sheets are Excel Tables (tblPurchases, tblSales, …), so formulas use structured references and new rows inherit calculations automatically.
Colour convention. Blue = hard-coded inputs, black = formulas, green = cross-sheet links, yellow = assumptions to review — the standard financial-modelling palette.
4. The sample dataset
A coherent Q2 2026 dataset was generated and posted into the transaction sheets — kept on a standalone Sample Data sheet and mirrored into the live logs. Shape: 13 items priced (SUP-001 … SUP-013), 96 goods-receipt notes, 478 sales invoice lines, 15 stock adjustments, 38 payment receipts and 101 credit invoices.
4.1 How it was made coherent
•	No negative stock. Purchases and sales were simulated day-by-day against a running balance per (item, location) — a sale is only written if that shop currently has stock. Opening receipts land on 01-Apr, with mid-quarter replenishments.
•	Realistic behaviour, costs and prices. Sales are weighted by item popularity; walk-ins buy retail, the wholesale account buys in bulk, ~12% of lines are discounted and some sell at Promo. Landed cost splits into supplier price + duty + freight + clearing + other, with retail ≈ 1.9× cost and wholesale ≈ 1.5×. Shops close Sundays, batches carry mfg/expiry dates, and credit purchases and sales generate receivables and later receipts.
5. Results & analysis
All figures are Q2 2026 (01-Apr – 30-Jun), in GHS, as computed by the workbook.
 
Dashboard — executive summary
5.1 Headline P&L
Sales revenue 265,972 · COGS (weighted-avg) 158,077 · gross profit 107,895 · gross margin 40.6% · 2,538 units sold · average value per line 556 · purchases received 806,591 at cost across 96 GRNs. A 40.6% blended margin sits squarely in the healthy 30–50% band for supplement retail.
5.2 Top sellers (by revenue)
#	Item	Revenue	Units	GP%
1	Cleanse	31,045	322	41.9%
2	Probiotic	28,306	210	41.5%
3	Daily Build	26,978	185	40.6%
4	Aqua Lean	26,439	221	39.6%
5	SleepTrim	20,923	124	42.9%

Mila is the volume outlier: 410 units — the highest of any item — but only 9,125 in revenue at the best margin (46.4%). A cheap, fast-moving sachet: a classic traffic-builder.
5.3 ABC classification
 
ABC analysis (revenue-based) — top 12 of 13 lines shown
Revenue is concentrated as Pareto predicts: Class A — 9 items, 79% of revenue (the focus lines); Class B — 2 items (Neuroboost, Purezyme), 12%; Class C — 2 items (Organic Sulfur, Mila), 9%, worth reviewing for promotion or rationalization.
5.4 Channel, tier & location mix
Tier mix: Retail 56% · Wholesale 39% · Promo 5%. Payment: Cash 74% · Credit 26%. Locations: Shop 2 (94.8k) > Shop 3 (91.4k) > Shop 1 (79.7k). Top customer: MedPlus Wholesale at 103,521 — the single largest account at ~39% of revenue, effectively the entire wholesale tier, and the source of most credit exposure, a concentration worth monitoring.
5.5 Receivables & aging
 
Receivables with automatic aging buckets
Of the 101 credit invoices, 25 are paid or settled and 76 remain outstanding — 18 current, 19 at 1–30 days, 21 at 31–60 and 18 at 61–90 — so the full aging logic is exercised. The outstanding balance is GHS 48,335 against GHS 20,894 collected in the period; with ~GHS 48k open on ~GHS 69k invoiced on credit, collection discipline is the main working-capital lever.
5.6 Inventory KPIs (and an important caveat)
 
Inventory KPI page
Stock value 645,243 across 10,363 units · gross margin 40.6% · GMROI 0.17 · sell-through 19.7% · dead stock 0 · inventory turnover 0.24× · DSI ~1,490 days.
Caveat: the simulation deliberately over-stocked the opening position so no shop ever ran negative, leaving ~GHS 645k of inventory against one quarter of COGS (158k). That mechanically depresses turnover and inflates DSI — a property of the sample data, not a formula error; ~1 month of opening cover would give a realistic 4–5× annualized turnover.
6. Data-quality findings (useful for the writeup / QA)
1.	Controlled vocabularies must match across sheets — found and fixed. Receipts were logged as Mobile Money / Bank Transfer while the Dashboard buckets Cash / MoMo / Bank / Cheque: totals were right, the per-method split was not. Aligning the labels made the widget reconcile exactly to GHS 20,894. Lesson: drive Payment Method from a single Settings picklist.
2.	Reference-date sensitivity. The Dashboard computes “as of” today (14-Jul-2026) while the demo period is Q2, so MTD, near-expiry and reorder alerts read 0 — correct, but a reminder that time-relative KPIs depend on the reference date.
7. Limitations, next steps & build order
Next steps: populate Items Master reorder levels and lead times to activate reorder alerts; standardize payment-method and status picklists via Settings plus data validation; add inter-location transfers as paired adjustments; and right-size opening inventory for realistic turnover/DSI in future demos.
Build order: four layers as separate sheets → transaction ranges converted to Excel Tables → WAC and effective-dated pricing formulas wired into the Sales Log → Stock Ledger / Stock on Hand roll-ups and the Receivables aging engine → Dashboard, KPI and ABC pages on top → Q2 2026 dataset generated and posted, verifying zero formula errors and end-to-end tie-out (revenue 265,972 → margin 40.6%). Built and analyzed with Claude in Excel.

