# econ3916-lab18-model-evaluation
# Model Evaluation: The Curve, and What It Cannot Tell You

## Objective
I evaluated two classifiers on a rare outcome to see what ROC and precision-recall curves show, what a single summary number hides, and how stated costs turn an evaluation into a decision.

## Methodology
- Simulated 8,000 cases of a rare outcome with five features and split them into training and test rows, keeping the same share of positives in each; 11.8% of the test rows were positive.
- Fitted a logistic regression (scaled features in a pipeline) on the training rows and drew its ROC curve and precision-recall curve on the test rows.
- Fitted a gradient-boosted tree model as a second model and compared the two on ROC AUC, average precision and precision at four levels of recall.
- Ran a paired bootstrap over 500 resamples of the test rows, scoring both models on the same resample each time, to get a 95% interval for the gap between them.
- Built an expected-cost curve with a miss costing ten times a false alarm, chose the cheapest threshold using out-of-fold probabilities on the training rows, and priced that threshold once on the test rows.
- Repeated the threshold choice for both models at cost ratios from 2:1 to 50:1 and checked whether the cheaper model changes.
- Wrote a function, roc_point(), that flags every row whose predicted probability is at or above a threshold and returns the false positive rate and true positive rate at that threshold.

## Key Findings
- The same logistic probabilities looked much better on the ROC curve (AUC 0.847) than on the precision-recall curve (average precision 0.465). With few positives, a small false positive rate still produces many false alarms compared with true positives, and the ROC curve hides this while precision shows it.
- The boosted model had a ROC AUC of 0.843. The AUC difference on the full test set was +0.0038, and the paired bootstrap's 95% interval was [-0.0020, +0.0094]. Because the interval includes zero, one AUC being higher does not tell me which model is better.
- The ranking of the two models also depended on the recall level I looked at, which a single summary number hides.
- With a miss costing ten times a false alarm, the cheapest threshold on the training rows was 0.0905. On the test rows it cost 0.4692 per case, against 0.9437 at the default 0.5, roughly half the cost.
- When I varied the cost ratio, the chosen threshold fell as misses became more expensive. The cheaper model changed at one ratio, but the gaps were small and had no bootstrap interval, so I would not choose a model on them.
- At my chosen threshold, roc_point() gave a false positive rate of 0.296 and a true positive rate of 0.823.
- Choosing the cost ratio is not a statistical question. It has to come from the people responsible for the decision, and only once it is fixed does "which model is better" have a clear answer.
