Team Project 2: The Loan Officer Challenge
================
Econ 122

### The Loan Officer Challenge

**Project description:**

You are the analytics team at **Bank of Overfit**, a consumer lender. Next year,
**10,000 people** will apply for a 3-year personal loan. For each applicant
your team decides:

1. **Approve or deny?**
2. **If approved, what interest rate do we offer?**

Offer too high a rate and the applicant takes a loan from another bank.
Offer too low and you leave money on the table. Approve someone who
defaults and you lose the whole loan.

You have last year's data to learn from. Your score is the **actual profit**
your decisions earn on the 10,000 new applicants. We will reveal the real
outcomes in class on **Monday, 11/30**.

**Teams:** 2-4 students. You choose your own team.

------------------------------------------------------------------------

### What You'll Need

Days 9-18 cover everything required. Some of those methods come after the
project is posted; start with what we have covered, and add the rest as
we cover them.

------------------------------------------------------------------------

### Dataset Summary

Both files are in `data/`. **Every team gets the same data**, so every
team's profit is directly comparable.

- **`loan_training_data.csv`**: **5,000 applicants from last year.** Last
  year the bank ran an experiment: each applicant was offered a
  **randomly chosen** interest rate. You see the rate
  each person was offered, whether they accepted, and, for those who
  accepted, whether they defaulted.
- **`loan_test_data.csv`**: **10,000 new applicants.** Same applicant
  information, no outcomes. These are the people you make decisions for.

**Applicant information (both files):**

| Variable | Meaning |
|---|---|
| `loan_id` | Applicant ID |
| `age` | Age in years |
| `annual_income` | Gross annual income, dollars |
| `employment_length` | Years at current job (`< 1 year` ... `10+ years`) |
| `home_ownership` | `RENT`, `MORTGAGE`, or `OWN` |
| `household_size` | Number of people in the household |
| `monthly_housing_cost` | Monthly rent or mortgage payment, dollars |
| `monthly_debt_payments` | Monthly payments on existing debts (car, student, credit card), dollars; excludes housing and this loan |
| `credit_score` | Credit score (540-850) |
| `num_credit_lines` | Number of open credit accounts |
| `delinquencies_2yrs` | Late payments (30+ days) in the last 2 years |
| `credit_utilization` | Share of available credit in use, percent |
| `inquiries_6months` | Credit applications in the last 6 months |
| `loan_purpose` | What the loan is for |
| `loan_amount` | Amount requested, dollars |

**Outcomes (training file only):**

| Variable | Meaning |
|---|---|
| `offered_rate` | Interest rate the bank offered (randomly chosen) |
| `offer_accepted` | 1 if the applicant took the loan, 0 if they went elsewhere |
| `default` | 1 if the loan defaulted, 0 if it was repaid. **`NA` if the offer was not accepted**: a loan that was never made can't default. |

------------------------------------------------------------------------

### How Profit Works

All loans are 3-year loans. For each applicant, with loan amount $L$ and
offered rate $r$:

| What happens | Profit |
|---|---|
| You **deny** | 0 |
| You approve, the applicant **rejects** your rate | 0 |
| You approve, they accept, and **repay** | $3 \times r \times L - 200$ |
| You approve, they accept, and **default** | $-(L + 700)$ |

All amounts are in dollars. The 200 is the cost of processing a loan; a
default also costs 500 in collections, and you lose the principal.

Example: a 10,000-dollar loan at 12% earns
$3 \times 0.12 \times 10{,}000 - 200 = 3{,}400$ dollars if repaid, and
loses $10{,}000 + 700 = 10{,}700$ dollars if it defaults.

**Expected profit of a loan.** You won't know in advance who defaults, but
you can estimate the probability. If a loan is made and its chance of
default is $p$, its **expected profit** (the average profit over many loans
like it) is

$$
(1 - p) \times (3rL - 200) \;-\; p \times (L + 700)
$$

Example: the 10,000-dollar loan at 12% with a 10% chance of default has
expected profit $0.9 \times 3{,}400 - 0.1 \times 10{,}700 = 1{,}990$ dollars.
With a 30% chance of default it is $0.7 \times 3{,}400 - 0.3 \times 10{,}700
= -830$ dollars: on average, that loan loses money.

**Acceptance.** Each applicant accepts only if your rate is low enough for
them, and different applicants have different limits.

How you turn these into an approve/deny decision and a rate for each
applicant is up to your team.

------------------------------------------------------------------------

### Getting Started

Create `TP2.Rmd` at the top level of your repo, next to this README. Load
the data with:

``` r
library(readr)

train <- read_csv("data/loan_training_data.csv")
test  <- read_csv("data/loan_test_data.csv")
```

If you get a "does not exist" error, run `getwd()` and check you are in the
repo root.

------------------------------------------------------------------------

### Team Deliverables

1.  **`predictions.csv`** (top level of your repo, next to this README):
    one row for each of the 10,000 test applicants, with exactly these
    three columns:

    ```
    loan_id,decision,interest_rate
    100001,approve,0.135
    100002,deny,0
    100003,approve,0.21
    ...
    ```

    `decision` is `approve` or `deny`; `interest_rate` is a decimal
    (0.135 = 13.5%), 0 if denied.

2.  **`TP2.Rmd`**: all your code, in a document that knits without error.

3.  **`TP2.md`**: the knitted report (use `output: github_document` in
    `TP2.Rmd`), written as a memo to the bank's
    leadership (no visible code). It must include:

    - **Predicted profit.** Your estimate of the total profit your
      `predictions.csv` will earn on the 10,000 applicants, and how you
      got it. Put this number in the first paragraph.
    - **Exploration.** What you learned about the data, and how it shaped
      your models.
    - **Models compared.** At least **four** different methods from the
      course for predicting default, compared on out-of-sample
      performance: **k-fold cross-validation** (the same folds for every
      method), or **out-of-bag (OOB) error** for forests. Report each
      one's performance and how much it varies, and say which you chose
      and why.
    - **Your pricing and approval rule:** how you chose whom to approve
      and what rate to offer, the evidence that it beats a simpler rule,
      and at least one figure showing how your offered rates or approvals
      vary across applicants.
    - **At least three figures** in total. Every figure needs a caption
      and must be referenced in the text where it supports your argument.

4.  **A ~5 minute team defense in class on Monday, 11/30.** A short
    walkthrough, then unscripted questions about your choices: why a
    particular applicant was denied, what happens if you change a rate,
    why you trust your out-of-sample estimates. **Any team member may be
    asked to answer any question, and attendance is required.**

------------------------------------------------------------------------

### Grading

- **Profit (30%):** your actual profit on the 10,000 test applicants.
  The top team gets 30 points and a simple instructor benchmark gets
  18; every other team is scored on the same straight line through
  those two points (minimum 0). An invalid `predictions.csv` (wrong
  rows or wrong format) gets 0.
- **Prediction honesty (10%):** 10 points if your predicted profit is
  within **1.5 million dollars** of your actual profit, falling linearly
  to 0 at **6 million dollars** off (0 if the memo gives no number). An
  honest out-of-sample estimate beats an optimistic one.
- **Models and validation (20%):** methods correctly fit and compared
  out of sample (k-fold CV or OOB); a reasoned choice.
- **Exploration (10%):** what you learned about the data, and how it
  shaped your models.
- **Figures and written memo (20%):** clear, captioned figures; a memo
  leadership could act on.
- **Oral defense (10%):** every team member can explain the full
  analysis, not just their own piece.

**Extra credit:** the top-profit team gets +5 points.

------------------------------------------------------------------------

### Due Dates

- **`predictions.csv`, `TP2.Rmd`, `TP2.md`:** Friday, 11/20, end of day.
- **Team defenses and outcome reveal:** in class, Monday, 11/30.

The defense covers what you actually submitted, not later work.

Good luck. The bank's money is in your hands.
