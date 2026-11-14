# Credit Risk Analytics Dashboard (Power BI)

A 3-page Power BI report for monitoring a retail loan portfolio. It covers disbursement and exposure, default and NPA analysis, expected loss, and customer and branch performance. It uses a star-schema model, about 100 DAX measures and calculated columns, a custom dark theme and a designed canvas background.

## Business Questions Answered

- How much have we lent, how much is outstanding, and how is that changing year over year?
- Where are defaults concentrated: by credit score band, risk category, income band, loan type and occupation?
- What is the expected loss (PD x LGD x EAD), and how sensitive is it to the LGD assumption?
- Which branches and customer segments drive exposure, and which carry the most risk?
- Which accounts should go on a watchlist?

## Report Pages

| Page | Focus | Highlights |
|---|---|---|
| **1. Portfolio Overview** | Disbursement, exposure and portfolio health | KPI cards with sparklines, monthly disbursement vs default rate, risk-category mix, branch ranking, loan-type summary |
| **2. Risk & Default Analytics** | Credit risk and early warning | NPA ratio, expected loss with LGD what-if slicer, default rate by credit score band, risk x income heatmap, vintage analysis, at-risk accounts table |
| **3. Customer & Branch Insights** | Who we lend to and how branches perform | Branch scorecard with data bars, segment mix, default rate by age band, exposure by state, decomposition tree of default drivers |

## Data Model

Star schema with three tables and a date dimension:

- **Loan_Fact**: ApplicationID, ApplicationDate, CustomerID, LoanID, LoanAmount, EMI, OutstandingBalance, LoanStatus, DefaultFlag, Branch
- **CUST**: CustomerID, CustomerName, Gender, Age, Occupation, IncomeBand, City, State, CustomerSegment, CreditScore
- **PROD**: LoanID, LoanType, InterestRate, RiskCategory, TenureMonths
- **Dim_Date**: calculated calendar table, marked as the date table

Relationships (many-to-one, single direction):

```
CUST[CustomerID]   -> Loan_Fact[CustomerID]
PROD[LoanID]       -> Loan_Fact[LoanID]
Dim_Date[Date]     -> Loan_Fact[ApplicationDate]
```

## DAX Highlights

- **Credit risk:** Default Rate %, NPA Ratio %, Defaulted Exposure, High-Risk Exposure %, Watchlist Exposure
- **Expected loss:** PD x LGD x EAD, with a what-if LGD parameter and a version calibrated by credit score band
- **Weighted metrics:** exposure-weighted credit score and interest rate, risk-adjusted yield
- **Time intelligence:** YoY, YTD, MoM, rolling 3-month, change in percentage points
- **Ranking and concentration:** branch rank, exposure share, top-3 branch concentration, Pareto
- **Dynamic UI:** conditional colour measures, KPI delta labels, dynamic subtitles, a field parameter metric selector

### Calculated Columns

Credit Score Band, Age Band, Loan Size Band, Tenure Band, Rate Band, Months on Book, Vintage Cohort, Early Warning Score and Account Status (Defaulted / Watch / Performing).

### Sample Measures

```dax
Default Rate % =
DIVIDE ( [Defaulted Loans], [Total Applications] )

NPA Ratio % =
DIVIDE ( [Defaulted Exposure], [Total Outstanding] )

Expected Loss =
[NPA Ratio %] * [LGD Value] * [Total Outstanding]

Disbursed YoY % =
DIVIDE ( [Total Disbursed] - [Disbursed LY], [Disbursed LY] )
```

## Design

- Custom theme (`CreditRisk_Theme_v2.json`) with a risk-coded palette: green is good, amber is watch, red is bad
- 1280x720 canvas with a custom background and a consistent panel grid
- Synced slicers (Year, Branch, Loan Type), page navigation buttons, drill-through to risk analysis

## How to Use

1. Clone the repo and open `CreditRisk.pbix` in Power BI Desktop.
2. To rebuild from scratch: import the theme, set the canvas background, create `Dim_Date` and the calculated columns, then run the measure scripts in DAX query view.
3. Refresh the data if you replace the sample dataset.

## Repository Structure

```
.
├── CreditRisk.pbix
├── theme/
│   └── CreditRisk_Theme_v2.json
├── assets/
│   └── CreditRisk_Background.png
├── dax/
│   └── measures and calculated columns
├── screenshots/
│   ├── page1.png
│   ├── page2.png
│   └── page3.png
└── README.md
```

## Assumptions and Notes

- Amounts are in INR. Default status thresholds (5% and 8%), credit score bands, early-warning weights and the default LGD of 45% are illustrative and should be adjusted to your policy.
- The dataset has no snapshot date, so "vs LY" comparisons are by loan origination period, not balances at two reporting dates.
- Expected loss uses observed default as a PD proxy. A production model would use a calibrated PD by segment.
- The data is a sample dataset and not real customer data.

## Screenshots

![Portfolio Overview](screenshots/page1.jpg)
![Risk and Default Analytics](screenshots/page2.jpg)
![Customer and Branch Insights](screenshots/page3.jpg)

## Tools

Power BI Desktop, DAX, Power Query, data modelling

## Author

Sameer Abbas, Data Analyst / Power BI Developer, Dubai, UAE
