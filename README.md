# Amazon Sale Report — Data Cleaning & EDA

A small, beginner-friendly project that cleans a raw Amazon India sales export and explores it with simple charts — order status, sales by category, sales
over time, sales by state, order sizes, and outlier handling.

## Project structure

```
.
├── data/
│   └── Amazon Sale Report.csv        # raw dataset (~129,000 orders)
├── notebooks/
│   └── amazon_sales_analysis.ipynb   # the full analysis, step by step, with plain-language notes
├── figures/
│   └── 01_...png … 12_...png         # every chart from the notebook, saved as PNG
├── requirements.txt
└── README.md
```

## About the dataset

`Amazon Sale Report.csv` is an export of Amazon.in orders (April–June 2022).
Each row is one order line and includes things like order status, fulfilment
type, product category/size, sales amount, quantity, and the shipping
city/state/postal code/country.

Key columns:

| Column | What it means |
|---|---|
| `Order ID` | Unique ID for the order |
| `Date` | Order date |
| `Status` | Order status (Shipped, Cancelled, Pending, …) |
| `Fulfilment` | Who fulfilled the order — `Amazon` or `Merchant` |
| `Category` | Product category (kurta, Set, Western Dress, …) |
| `Size` | Product size |
| `Qty` | Quantity ordered |
| `Amount` | Sale amount (INR) |
| `ship-city` / `ship-state` / `ship-postal-code` / `ship-country` | Shipping address details |
| `B2B` | Whether the order is a business-to-business order |

## What the notebook does

1. **Load & inspect** the raw CSV — shape, columns, data types, missing values.
2. **Clean missing values**, column by column, using simple and explainable rules:
   - `courier status` → filled based on order `status` (cancelled orders get `cancelled`, others get `Shipped`)
   - `currency` → filled with the most common currency
   - `amount` → filled with the average amount for that product category
   - `ship-city` / `ship-state` / `ship-postal-code` / `ship-country` → filled with the most common value (city/state/country), postal code filled per-city
   - `promotion-ids`, `fulfilled-by`, `unnamed: 22` → filled with `"Unknown"`
3. **Drop unused columns** (`unnamed: 22`, `promotion-ids`).
4. **Explore the data** with charts:
   - Distribution of order status
   - Order status by fulfilment type (+ success/cancel rates for Merchant orders)
   - Total sales by category
   - Orders by month and daily sales timeline
   - Top 10 states by total sales
   - Sizes sold per category
5. **Find and treat outliers** in `qty` and `amount` using the IQR (boxplot) rule, with before/after charts.
6. **Summarize** with a short, plain-language conclusions section at the end.

Every chart in the notebook is also saved to the `figures/` folder as a PNG,
in the same order they appear in the notebook.

## How to run it

```bash
pip install -r requirements.txt
jupyter notebook notebooks/amazon_sales_analysis.ipynb
```

Then just run all cells from top to bottom (`Cell → Run All` or `Kernel → Restart & Run All`).
The notebook already includes the outputs/charts from a previous run, so you
can also just open and read it without re-running anything.

## Main takeaways

- Most orders end up **Shipped**; a smaller share are **Cancelled**, and only a
  tiny fraction get stuck as Pending / Lost / RTO.
- **Merchant**-fulfilled orders have a noticeably higher cancellation rate than
  Amazon-fulfilled orders (~17% vs Amazon's much lower rate).
- **Set**, **kurta**, and **Western Dress** are the categories that bring in
  the most total sales.
- Orders are heavily concentrated in **April–June 2022**.
- **Maharashtra** and **Karnataka** are the top two states by total sales.
- `qty` and `amount` both had extreme outliers, which were capped using the
  IQR rule so they don't distort the averages and charts.

## Requirements

See `requirements.txt`. Tested with Python 3.11.
