# Credit-Card-Fraud-Risk-Analysis
Credit card fraud risk analysis using Power BI, DAX  Measures fraud rate and value, ranks fraud types, states and months, and tests whether risk labels work. Result: 28.6% fraud, Card Not Present leads, and the risk labels fail to flag fraud, so scoring rules need recalibration.






---

## 🎯 Business Problem

Banks lose money and customer trust when fraudulent card transactions slip through. The fraud team needs to know **how much fraud is happening, which fraud types and segments matter most, and whether the current risk classification is doing its job.**

## ⚡ Headline Results

| 28.6% | INR 33.1 L | 50.9% | 9.8% |
|:---:|:---:|:---:|:---:|
| fraud rate: 286 of 1,000 transactions | fraud value, 27.0% of INR 1.22 crore transacted | of fraud value comes from Card Not Present and Identity Theft | of confirmed fraud was rated Critical; 39.5% was rated Low |

> **💡 Key insight:** Fraud is widespread and spread fairly evenly across states, cards, banks and merchants, but the **risk labels and fraud scores do not separate fraud from genuine activity**: Critical-rated transactions are fraudulent 26.2% of the time, *less* often than Low-rated ones (28.2%). Recalibrating the scoring rules is the biggest opportunity.

## 🧮 DAX Measures in the Dashboard

The model is a single table with **six custom measures** (results verified against the raw CSV):

| Measure | DAX | Result |
|---|---|---|
| **Total Transaction Amount** | `SUM('Credit Card Fraud Risk Analysis'[Transaction Amount (INR)])` | INR 12,226,843 |
| **Fradulent Transaction** | `CALCULATE(COUNTROWS('Credit Card Fraud Risk Analysis'), 'Credit Card Fraud Risk Analysis'[IsFraud] = 1)` | 286 |
| **Fraud Rate %** | `DIVIDE([Fradulent Transaction], COUNTROWS('Credit Card Fraud Risk Analysis'), 0) * 100` | 28.6 |
| **Critical Risk Transaction** | `DIVIDE(CALCULATE(COUNTROWS('Credit Card Fraud Risk Analysis'), 'Credit Card Fraud Risk Analysis'[Fraud Risk] = "Critical"), COUNTROWS('Credit Card Fraud Risk Analysis'), 0) * 100` | 10.7 |
| **Fraudulent Transaction Amount** | `CALCULATE([Total Transaction Amount], 'Credit Card Fraud Risk Analysis'[IsFraud] = 1)` | INR 3,306,813 |
| **Top Fraud Type** | see below | Card Not Present |

```dax
Top Fraud Type =
VAR Toptype =
    TOPN(1,
        SUMMARIZE('Credit Card Fraud Risk Analysis',
                  'Credit Card Fraud Risk Analysis'[Fraud Type],
                  "Fraudulent Count",
                  CALCULATE(COUNTROWS('Credit Card Fraud Risk Analysis'),
                            'Credit Card Fraud Risk Analysis'[IsFraud] = 1)),
        [Fraudulent Count], DESC)
RETURN
    MAXX(Toptype, 'Credit Card Fraud Risk Analysis'[Fraud Type])
```

Measures are layered (Fraud Rate % reuses Fradulent Transaction; Fraudulent Transaction Amount reuses Total Transaction Amount), so one definition feeds every visual, and *Top Fraud Type* updates dynamically with the slicers.

## 📊 Power BI Dashboard

One-page interactive report (13 elements): a multi-row card showing all five KPI measures, a bar chart (amount by fraud type and category), a donut (amount by risk label), a column chart (fraud by state), a line chart (fraud by month) and slicers for fraud type, state and merchant.

<!-- Add a screenshot: save it as images/dashboard.png, then uncomment the next line -->
<!-- <p align="center"><img src="images/dashboard.png" width="700" alt="Power BI dashboard"></p> -->

## 📈 Key Findings

### 1. Nearly three in ten transactions are fraud
286 of 1,000 transactions (95% CI 25.9% to 31.5%), worth INR 33.07 lakh. *Note: a 28.6% share is far above real-world portfolios, so this looks like a curated sample; the rates describe this dataset.*


### 2. Card Not Present and Identity Theft lead


### 3. The risk labels do not separate fraud from genuine transactions
If the labels worked, fraud rates would climb from Low to Critical. They don't (chi-square p = 0.73), and fraud score averages 53.9 for fraud vs 52.9 for genuine (p = 0.55). **Only 28 of 286 confirmed frauds were rated Critical; 113 were rated Low.**


### 4. Where and when

By merchant city, Ahmedabad (36.7%), Mumbai, Chennai and Kochi run well above Delhi (18.9%), the only statistically significant gap found (p = 0.039, borderline across nine comparisons).


The fraud rate rose from **25.7% to 31.7%** between the first and second half of the window (p = 0.042). Hatched bars are partial months.

### 5. Which differences are real?
Chi-square tests across nine dimensions show that **only merchant location** is statistically significant. Gaps by state, card, bank, merchant, category and amount are within sampling noise, so treat them as signals to monitor.



## ✅ Recommendations

| # | Action | Evidence |
|---|---|---|
| 1 | **Recalibrate the risk-scoring rules** against confirmed fraud | Critical 26.2% vs Low 28.2%; only 9.8% of fraud rated Critical |
| 2 | **Strengthen Card Not Present and identity-verification controls** | 50.9% of fraud value; 136 of 286 fraud transactions |
| 3 | **Review merchants in Ahmedabad, Mumbai, Chennai and Kochi** | Rates 33.7-36.7% vs Delhi 18.9% (p = 0.039) |
| 4 | **Set a monthly alert on Fraud Rate %** | 25.7% to 31.7% (p = 0.042) |

## 🔍 Limitations

- 1,000-row sample with an unusually high fraud share; Fraud Type is populated even for genuine transactions, so type analysis uses `IsFraud = 1` rows only.
- Most segment differences are within sampling noise, and the first and last months are partial (16-31 Dec 2023, 1-15 Dec 2024).

## 🚀 Next Steps

- [ ] Add DAX measures: `Fraud Value % = DIVIDE([Fraudulent Transaction Amount], [Total Transaction Amount], 0)`, Average Fraud Amount, and a "fraud rated Low" share to monitor missed fraud
- [ ] Add a fraud-rate-by-risk-label visual and a Year-Month trend axis to the dashboard
- [ ] Rename `Fradulent Transaction` to `Fraudulent Transactions` and `Critical Risk Transaction` to `Critical Risk %`


```

## ▶️ How to Reproduce

```bash
pip install pandas numpy seaborn matplotlib scipy jupyter
jupyter notebook Credit_Card_Fraud_Risk_Analysis.ipynb
```
Open the `.pbix` in Power BI Desktop and point the data source at the CSV if prompted.

## 👤 About Me

**Vicky Jeeru**: Data Analyst & Power BI Developer, Hyderabad, India

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/vicky-jeeru-257a18281/)
[![Portfolio](https://img.shields.io/badge/Portfolio-13A89E?style=flat&logo=googlechrome&logoColor=white)](https://vickyjeeru.my.canva.site/)
[![Email](https://img.shields.io/badge/Email-D14836?style=flat&logo=gmail&logoColor=white)](mailto:jeeruvicky51@gmail.com)

*Open to Data Analyst and BI roles. If this project was useful, a ⭐ is appreciated.*
