# Model selection

## The question

The model predicts whether a customer is likely to leave the service (churn). A false negative may cause the business to miss a customer who needs attention, while a false positive may lead to unnecessary retention offers or interventions.

## Candidates

| Candidate | Why I tried it | AUC | F1 | F1 per slice (lowest) | p95 latency (ms) | Size (KB) | MLflow run ID |
|---|---|---:|---:|---|---:|---:|---|
| Logistic Regression | A simple, fast baseline that is easy to interpret | 0.7982 | 0.4948 | 0.2500 (one-year and two-year) | 9.70 | 3.55 | `99cee5dad5e64b23a5b363cf91ab45a5` |
| Random Forest | A tree-based model that can learn nonlinear patterns | 0.7589 | 0.4536 | 0.2222 (one-year) | 34.03 | 3160.10 | `85c6f7de33f4424fb9f69b479b19c974` |
| Gradient Boosting | A boosting model that combines trees to improve predictions | 0.7476 | 0.4167 | 0.2222 (two-year) | 9.66 | 141.64 | `8d4b6a581d6b41b69323193d8f4c01d3` |

## The champion

I selected **Logistic Regression** as the champion. It achieved the highest overall F1 (0.4948), the highest AUC (0.7982), and the best weakest-slice F1 (0.2500). It was also very small (3.55 KB) and fast (9.70 ms p95).

I accepted a very small latency difference compared with Gradient Boosting (9.70 ms versus 9.66 ms) because Logistic Regression performed better on the overall F1 and weakest-slice F1. Random Forest was larger and exceeded the 25 ms latency budget.

## My promotion rules

- Latency budget (p95): **25 ms**, because predictions should remain fast enough for a responsive service. This budget also excludes the Random Forest candidate in these runs.
- Minimum gain to replace the champion (F1 points): **0.005**, because a new model should show a meaningful improvement rather than replacing the champion for a negligible gain.

## What I would check next

I would check the number of customers in each contract slice and monitor whether the data distribution changes over time. Small slices or data drift could make the measured F1 scores less reliable in production.