Skilling with Claude AI, I built an Excel Inventory & Sales Management Template and simulated it with realistic sample dataset to stress-test it to 
A single-file Excel inventory, sales, and receivables system built for VitalWell Nutrition Ltd — a fictional Ghana-based wellness/supplement retailer operating a central warehouse and three retail shops (all names and figures are sample data). This document explains how the workbook is architected, how a realistic sample dataset was generated to stress-test it, and what the numbers say once the data flows through the model.

Currency: GHS (Ghana Cedi)  ·  Costing basis: Weighted Average Cost (WAC)  ·  Demo period: Q2 2026 (01-Apr – 30-Jun)

1. What this workbook does
The template runs a small multi-location retail operation end to end:
•	Maintains master records for items, suppliers, customers, and locations
•	Captures goods receipts (purchases), sales invoices, stock adjustments, and customer payments
•	Values inventory on a weighted-average basis and computes COGS and gross profit per line
•	Tracks accounts receivable with automatic aging buckets
•	Rolls everything up into a Dashboard, an inventory KPI page, and an ABC analysis
It is deliberately formula-driven: you type raw transactions, and every derived value (names, landed cost, WAC, COGS, gross profit, balances, aging) is calculated by the workbook so the logic is transparent and auditable.

2. Architecture
The workbook is organized into four layers across 22 worksheets.

2.1 Navigation & documentation
Sheet	Purpose
Index	Hyperlinked table of contents
User Guide	How to operate the workbook
Settings	Global switches — currency, costing method, near-expiry / slow-mover thresholds

2.2 Master data (the reference layer)
Sheet	Key fields
Locations	Warehouse + shops, manager, status
Items Master	Item code, category, unit, reorder level/qty, lead time
Suppliers	Contact, currency, payment terms
Customers	Type, default price tier, credit limit & terms

2.3 Pricing engine
Sheet	Role
Price History – Cost	Effective-dated landed-cost build-up (supplier price + duty + freight + clearing + other)
Purchase Costing	Current standard cost per item
Price History – Selling	Effective-dated selling prices per tier (Retail / Wholesale / Promo)
Selling Prices	Current price list + minimum‑margin guardrail

2.4 Transactions (the input layer)
Sheet	What you log
Purchases Log	Goods received notes (GRNs): qty, supplier price, other costs, batch, expiry
Sales Log	Invoice lines: customer, item, qty, tier, discount
Stock Adjustments	Damage, expiry write-offs, count corrections, theft
Payment Receipts	Cash/MoMo/bank/cheque received against invoices

2.5 Outputs (the calculated layer)
Sheet	What it shows
Stock Ledger	Per-item movement: opening, purchased, sold, adjusted, closing, value
Stock on Hand	Current quantity and value by item
Receivables	Open invoices, balances, and aging
Customer Statement	Per-customer account statement
Dashboard	Executive summary — sales, payment mix, aging, alerts
KPIs	Turnover, DSI, GMROI, sell‑through, dead stock
ABC Analysis	Revenue-based A/B/C item classification

2.6 Data flow
Master data --+
              +--> Purchases Log --> Stock Ledger --> Stock on Hand --+
Price History-+                                                       |
                                                                      +--> KPIs / Dashboard / ABC
WAC --> Sales Log (COGS, GP) -----------------------------------------+
        |
        +--> Receivables <-- Payment Receipts

3. Design principles
Weighted-average costing. Each sale's unit cost is the running weighted average of all receipts for that item up to the sale date — computed with SUMIFS(total cost)/SUMIFS(qty) rather than stored, so it always reflects the true blended cost.
Effective-dated price history. Both cost and selling prices are stored as dated rows. Current price = the most recent row on/before the transaction date. This keeps a full audit trail and lets historical invoices reprice correctly.
Tiered pricing. Every item carries three prices (Retail / Wholesale / Promo). The sale line picks the price by the tier chosen, then applies any line discount, with a minimum-margin floor (10%) flagged by a price-check column.
Formula-driven derived columns. In every transaction table, the user fills only the raw input columns; names, landed cost, WAC, COGS, gross profit, and aging are Excel formulas. This makes the model self-documenting and auditable — click any number and read its logic.
 
Sales Log — formula-driven COGS & gross profit
Structured tables. Transaction sheets are Excel Tables (tblPurchases, tblSales, …) so formulas use structured references and new rows inherit calculations automatically.
Colour convention. Blue = hard-coded inputs, black = formulas, green = cross-sheet links, yellow = assumptions to review — the standard financial-modelling palette.

4. The sample dataset
To exercise the model realistically, a coherent Q2 2026 dataset was generated and posted into the transaction sheets. It is kept on a standalone Sample Data sheet and mirrored into the live logs.

4.1 Shape
Block	Rows
Items priced	13 supplements (SUP‑001 … SUP‑013)
Purchases (GRNs)	96
Sales invoice lines	478
Stock adjustments	15
Payment receipts	38
Credit invoices (Receivables)	101

4.2 How it was made coherent
•	No negative stock. Purchases and sales were simulated day-by-day with a running inventory balance per (item, location); a sale is only written if that shop currently has stock. Opening receipts land on 01-Apr, with mid-quarter replenishments.
•	Realistic behaviour. Sales are weighted by item popularity; walk-in customers buy retail, the wholesale account buys in bulk, ~12% of lines get a discount, some sell at Promo.
•	Believable costs & prices. Landed cost is split into supplier price + duty + freight + clearing + other; retail ≈ 1.9× cost, wholesale ≈ 1.5×, promo in between.
•	Operational realism. Shops are closed Sundays; batches carry mfg/expiry dates; some purchases and sales are on credit, generating receivables and later receipts.

5. Results & analysis
All figures are Q2 2026 (01-Apr – 30-Jun), in GHS, as computed by the workbook.
 
Dashboard — executive summary
 
5.1 Headline P&L
Metric	Value
Sales revenue	265,972
COGS (weighted-avg)	158,077
Gross profit	107,895
Gross margin	40.6%
Units sold	2,538
Avg. value per line	556
Purchases received (96 GRNs)	806,591 (cost)

A 40.6% blended margin is squarely in the healthy 30–50% band for supplement retail.

5.2 Top sellers (by revenue)
#	Item	Revenue	Units	GP%
1	Cleanse	31,045	322	41.9%
2	Probiotic	28,306	210	41.5%
3	Daily Build	26,978	185	40.6%
4	Aqua Lean	26,439	221	39.6%
5	SleepTrim	20,923	124	42.9%

Note Mila: 410 units — the highest volume of any item — but only 9,125 in revenue at the best margin (46.4%). It's a cheap, fast-moving sachet: a classic traffic-builder.

5.3 ABC classification
 
ABC analysis (revenue-based)
Revenue is concentrated, as expected under Pareto:
•	Class A — 9 items — 79% of revenue (the focus lines)
•	Class B — 2 items (Neuroboost, Purezyme) — 12%
•	Class C — 2 items (Organic Sulfur, Mila) — 9% — review for promotion or rationalization

5.4 Channel, tier & location mix
Dimension	Split
Tier	Retail 56%  ·  Wholesale 39%  ·  Promo 5%
Payment	Cash 74%  ·  Credit 26%
Location	Shop 2 (94.8k) > Shop 3 (91.4k) > Shop 1 (79.7k)
Top customer	MedPlus Wholesale — 103,521 (single largest account)

The wholesale account alone drives ~39% of revenue, which is also the source of most credit exposure — a concentration worth monitoring.

5.5 Receivables & aging
 
Receivables with automatic aging buckets
Bucket	Invoices
Paid / settled	25
Current	18
1–30 days	19
31–60 days	21
61–90 days	18
Total outstanding	GHS 48,335 (101 credit invoices)
Collected in period	GHS 20,894

Aging spreads sensibly across buckets, exercising the full aging logic. With ~GHS 48k outstanding against ~GHS 69k invoiced on credit, collection discipline is the main working-capital lever.

5.6 Inventory KPIs (and an important caveat)
 
Inventory KPI page
KPI	Value
Total stock value	645,243
Units in stock	10,363
Gross margin	40.6%
GMROI	0.17
Sell-through	19.7%
Dead stock	0
Inventory turnover	0.24×
DSI	~1,490 days

Caveat — read turnover/DSI with context. The simulation intentionally over-stocked the opening position to guarantee no shop ever ran negative during the sales simulation. That leaves ~GHS 645k of inventory against a single quarter of COGS (158k), which mechanically depresses turnover and inflates DSI. It is a property of the sample-data generation, not a formula error. Rebalancing opening purchases to ~1 month of cover would lift annualized turnover to a realistic 4–5×.

6. Data-quality findings (useful for the writeup / QA)
The demo surfaced two real modelling lessons:
1.	Controlled vocabularies must match across sheets — found and fixed. The Dashboard's collections widget buckets payments as Cash / MoMo / Bank / Cheque, while receipts had been entered as Mobile Money / Bank Transfer / Cash / Cheque. The total was always correct, but the per-method split under-reported MoMo and Bank. Aligning the receipt labels to the Dashboard's keys resolved it — the widget now reconciles exactly: Cash 6,311 + MoMo 3,583 + Bank 5,910 + Cheque 5,090 = GHS 20,894. Lesson: drive Payment Method from a single Settings picklist used by both entry and dashboard.
2.	Reference-date sensitivity. The dashboard computes "as of" today (14-Jul-2026), while the demo period is Q2. As a result Month-to-Date, near-expiry, and reorder alerts read 0 — correct, but a reminder that time-relative KPIs depend on the reference date and on Items Master reorder levels being populated.

7. Limitations & next steps
•	Populate Items Master reorder levels/lead times to activate reorder alerts.
•	Standardize payment-method and status picklists via Settings + data validation.
•	Add inter-location transfers as paired adjustments if warehouse-shop movement is needed.
•	Right-size opening inventory for realistic turnover/DSI in future demos.

8. How the workbook was built (summary)
1.	Laid out the four layers (master → pricing → transactions → outputs) as separate sheets.
2.	Converted transaction ranges to Excel Tables for structured references and autofill.
3.	Built the WAC and effective-dated pricing formulas; wired COGS/GP into the Sales Log.
4.	Added the Stock Ledger / Stock on Hand roll-ups and the Receivables aging engine.
5.	Built the Dashboard, KPI, and ABC pages on top of the ledgers.
6.	Generated a coherent Q2 2026 dataset and posted it, verifying zero formula errors and that outputs tie out end-to-end (revenue 265,972 → margin 40.6% across Sales Log, KPIs, and Dashboard).
Built and analyzed with Claude in Excel.
