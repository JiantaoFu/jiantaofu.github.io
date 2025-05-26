# 🛠️ 1. Core Product Modules

## 📥 A. Data Ingestion Layer

This layer collects, cleans, and unifies all deal data.

Inputs Accepted:

- Accounting software (QuickBooks, Xero, NetSuite)
- Bank & credit card statements (via Plaid or file upload)
- POS exports (e.g., Square, Toast, Clover)
- Payroll providers (Gusto, ADP, Paychex)
- Tax returns / W-2s / 1099s (PDF extraction + OCR)
- Broker CIMs (to extract seller-provided claims)


Features:

- Smart mapping of chart of accounts to standard LMM schema
- OCR + LLM-based auto-classification of scanned PDFs
- Historical trend normalization (standardizes fiscal year vs calendar year)
- Reconciliation engine: Ensures consistency between P&L, bank deposits, and cash on hand

## 🧠 B. LLM-Powered Analysis Engine

The heart of the platform—your AI due diligence analyst.

Capabilities:

1. Financial Normalization & Adjustments

- Owner compensation benchmarking (flag excessive salaries)
- Detection of owner perks/addbacks (cars, travel, family on payroll)
- One-time / non-recurring event detection (lawsuits, one-off projects)
- Revenue recognition alignment (cash vs accrual)

2. Variance & Trend Analysis

- Monthly / YoY revenue and margin swings with LLM-generated commentary
- Seasonality detection
- Expense trend outliers (e.g., sudden jump in subcontractor costs)

3. Cash Flow & Working Capital

- 13-week cash flow snapshot
- NWC normalization for deal structuring
- Inventory/purchasing cycle risk flags

4. Customer & Vendor Concentration

- Top customer/vendor reliance by %
- LLM summarizes risk from churned customers
- Lifetime value and cohort revenue retention if CRM data is provided

5. Labor & Operational Analysis

- Payroll breakdown by department
- Staff-to-revenue ratio benchmarking (e.g., techs per $1M HVAC revenue)
- Compensation benchmarking vs BLS or industry averages


## 📊 C. Output: AI-Generated Due Diligence Report

Deliverables:

- PDF + Interactive Web Report
- Standard sections:
   - Executive Summary
   - Financial Overview (Revenue, COGS, SG&A, EBITDA)
   - Key Adjustments & Addbacks
   - Working Capital & Cash Flow Snapshot
   - Customer/Vendor Analysis
   - Risk Flags Summary
   - Red Flag Watchlist
- Dynamic footnotes: Every metric includes LLM-generated rationale
- Toggle view: “Broker View” vs “Buyer View” vs “Bank View” to tailor emphasis

## 📈 D. Benchmarks & Valuation Module (Optional Add-On)

- Compares business KPIs to industry averages (NAICS + zip code)
- Suggests valuation range (3x–5x EBITDA, etc.) based on risk-adjusted metrics
- Creates a pre-filled investment memo with comps + deal notes
- Flagging system:
  - 🔴 Red: Customer concentration, cash-based revenue, no controls
  - 🟠 Yellow: Addbacks >15% of EBITDA, weak gross margins
  - 🟢 Green: Clean books, minimal owner involvement, recurring revenue

# 🔐 2. Security, Accuracy & Explainability

## A. Explainability Layer

- All LLM-generated insights are linked to source docs
- Allows users to click on a number or sentence and see:
  - What doc it came from
  - The prompt that generated it
  - LLM confidence score (0–100%)

## B. Reviewer Console

- Human analysts or advisors can review and override any section
- Edits logged for audit trail (critical for trust with banks or LPs)

# 🤖 3. Integrations

- Accounting: QuickBooks, Xero, NetSuite
- Banking: Plaid, MX
- POS: Square, Clover, Shopify
- Payroll: Gusto, ADP, Paychex
- CRM (optional): HubSpot, Salesforce
- Brokerage platforms: Axial, Acquire.com (for future deal flow ingestion)

# 🎯 4. Target Use Cases

Use Case | Persona | Outcome
-- | -- | --
Pre-LOI red flag review | Searcher | Spend <$1K to screen out bad deals
Post-LOI due diligence | PE Associate | Replace $30K QoE with faster insights
Broker-side pre-listing | M&A Advisor | Improve CIM quality & credibility
Financing partner prep | SBA lender or equity co-investor | Faster underwriting with standardized KPIs



# 📌 5. Example Scenarios

HVAC Company ($3M EBITDA, cash-based)
 → System detects 30% cash revenue unreported on books, large owner addbacks, top 2 clients = 50% revenue

Multi-location Med Spa ($1.2M EBITDA)
 → Flag: Owner has 2 Porsches on P&L, rent above market rate on affiliate-owned RE

Franchise Restaurant ($4.5M Rev)
 → System extracts POS data, flags declining AUV and labor cost creep vs category median

# Reasons for QofE 

The primary reason for conducting a QofE is its direct impact on the purchase price. It evaluates 3 critical factors, helping buyers assess if the business price is reasonable.

- Enterprise Value 
Determined by taking a multiple of EBITDA or similar financial measures, and analyzing the financials to determine if that financial measures is accurately reported as well as any key factors that impact the multiple 

- Net Debt 
Assessment of debt like items is done to highlight any potential reductions to Enterprise Value and future cash flows

- Net Working Capital
If less than a normal level of working capital is delivered at the closing of a transaction the buyer may be required to contribute additional capital in order to fund operations.

A comprehensive analysis of the business is needed to ensure adequate working capital is delivered at closing.
	
Advises self funded searchers, traditional searchers, private equity groups, family offices, and strategic acquirers in evaluating acquisitions with transaction values up to $50 million. 


## Industries

HVAC
Government Contracting
Professional Services
Retail
Landscaping
E-Commerce
Healthcare
Manufacturing
Plumbing
Trucking
Restaurants
Real Estate/Construction
Marketing Agencies
Electrical Contracting


## Is QofE different than an audit? 

An audit is different from a QoE in a variety of ways. For one an audit is a regulated procedure that has specific guidelines for how it needs to be completed. A QoE is a consulting engagement that can technically be done by anyone although they are typically done by CPAs.
An audit is much more balance sheet focused while a QoE is more focused on the income statement. The audit is looking to confirm that the financials comply with GAAP while a QoE helps a buyer understand the financial performance of the company that can be projected into the future as well as the key operating metrics.
On deals where audited financials are available the audit is complementary to the QoE.

[Understanding QofE](https://corporatefinanceinstitute.com/resources/valuation/quality-of-earnings-report/)



