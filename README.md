# Parkinson's Disease Detection from Voice Measurements

A data mining project in R that compares parametric and non-parametric classifiers for detecting Parkinson's disease from biomedical voice recordings.

**Author:** Dinesh Kumar Gummadavelli

📄 **[Read the full report (live page)](https://dinesh3250.github.io/parkinsons-disease-detection-r/)** · 📊 [Slides (original submission)](Final_Parkinsons_Project_Analysis.pptx) · 📝 [Project proposal](Proposal%20for%20Analysis%20of%20Parkinson's%20Dataset.docx)

## Overview

Parkinson's disease affects motor control, including the muscles that produce speech. Subtle changes in pitch, amplitude and noise in a person's voice can therefore act as an early signal. This project asks:

> Which model is most effective at detecting Parkinson's disease from voice features, and how well does it generalize?

Models are evaluated on accuracy, precision and recall. Recall gets extra weight because a false negative (a missed patient) is the costly error in a medical setting.

## Dataset

[UCI Machine Learning Repository – Parkinsons Data Set](https://archive.ics.uci.edu/dataset/174/parkinsons) (Oxford Parkinson's Disease Detection Dataset, created by Max Little, University of Oxford, with the National Centre for Voice and Speech, Denver).

- 195 voice recordings, about 6 per person. The UCI documentation says 31 people (23 with Parkinson's); the recording names in the file contain 32 distinct subject IDs (24 with Parkinson's, 8 healthy), and the analysis uses the IDs in the file
- 22 numeric voice features (jitter, shimmer, noise-to-harmonics ratio, nonlinear dynamics measures, etc.)
- Target: `status` (1 = Parkinson's, 0 = healthy)

## Methods

| Category | Techniques |
|---|---|
| Parametric | Logistic regression, LDA, QDA, Naive Bayes, Lasso, Ridge |
| Non-parametric | KNN, Decision Tree, Random Forest, Gradient Boosting, SVM |
| Dimension reduction | PCA + logistic regression |
| Validation | Subject-level train/test split, LOOCV, 10-fold cross-validation, 30 repeated subject-level splits |

The analysis also covers multicollinearity (correlation heatmap, VIF) and the trade-off between interpretability and predictive performance.

### Evaluation: hold out people, not recordings

Each person contributes about 6 similar-sounding recordings. Splitting at random by recording puts the same person in both the training and test sets, which inflates scores (under that split the decision tree reached 94.5% accuracy). The analysis therefore holds out **whole people**, stratified by diagnosis: 23 people (140 recordings) for training and 9 people (55 recordings: 12 healthy, 43 Parkinson's) for testing. Because a 9-person test set is noisy, the main comparison is repeated over 30 random subject-level splits.

A trivial baseline that labels every recording as Parkinson's scores **78.2% accuracy, 78.2% precision and 100% recall** on this test set, so those are the numbers a model has to beat. Several models that show 100% recall are just predicting "Parkinson's" for everyone.

## Results

**Repeated subject-level splits** (30 random splits, mean ± standard deviation, %):

| Model | Accuracy | Precision | Recall |
|---|---:|---:|---:|
| PCA + Logistic regression | 84.0 ± 8.8 | 89.5 ± 6.7 | 90.7 ± 8.5 |
| Random Forest | 82.6 ± 6.9 | 86.8 ± 7.2 | 92.7 ± 6.5 |
| QDA | 82.3 ± 5.0 | 83.0 ± 4.4 | 97.6 ± 2.8 |
| LDA | 81.2 ± 8.4 | 89.2 ± 6.8 | 87.1 ± 9.5 |
| Decision Tree | 78.7 ± 7.0 | 85.8 ± 6.5 | 87.9 ± 7.0 |
| *Baseline: always "Parkinson's"* | *78.2 ± 0.3* | *78.2 ± 0.3* | *100.0 ± 0.0* |
| Logistic regression | 71.5 ± 10.2 | 84.7 ± 7.5 | 78.4 ± 13.5 |
| Naive Bayes | 71.3 ± 10.5 | 93.1 ± 7.0 | 68.6 ± 12.0 |

**Single split** (seed 32, 9 held-out people), all models, %:

| Model | Accuracy | Precision | Recall |
|---|---:|---:|---:|
| Logistic regression | 32.7 | 60.0 | 41.9 |
| LDA | 70.9 | 78.7 | 86.0 |
| QDA | 78.2 | 78.2 | 100.0 |
| Naive Bayes | 87.3 | 90.9 | 93.0 |
| Lasso | 69.1 | 76.0 | 88.4 |
| Ridge | 78.2 | 78.2 | 100.0 |
| KNN | 78.2 | 78.2 | 100.0 |
| Decision Tree | 74.5 | 78.4 | 93.0 |
| Random Forest | 78.2 | 78.2 | 100.0 |
| Boosting | 78.2 | 78.2 | 100.0 |
| SVM (tuned, radial) | 76.4 | 77.8 | 97.7 |
| PCA + Logistic regression | 81.8 | 81.1 | 100.0 |

**Key findings**

- Once whole people are held out, performance drops sharply. Most models on the single split either match the "everyone has Parkinson's" baseline or fall below it.
- A single 9-person split is unreliable for ranking models: Naive Bayes is best on it but among the worst over 30 splits.
- Over repeated splits, PCA + logistic regression has the highest mean accuracy (about 84%), but random forest, QDA and LDA are within one standard deviation of it, and all are only a few points above the baseline. The voice features carry some signal that generalises to new people, but with 32 people the top models can't be reliably separated.
- Plain logistic regression overfits badly on the 22 collinear predictors; regularisation (Lasso, Ridge) or PCA fixes most of that.
- Cross-validation via `caret` leaves models without tuning parameters (logistic, QDA, PCA + logistic) unchanged.

See [`Project.html`](Project.html) for the full write-up, including limitations and future directions.

> **Note:** `Final_Parkinsons_Project_Analysis.pptx` and the proposal document reflect the original course submission, which used a random recording-level split. The results in `Project.Rmd` / `Project.html` are the corrected ones. The original report is preserved in the git history (tag `v1-original-submission`).

## Repository contents

| File | Description |
|---|---|
| `Project.Rmd` | R Markdown source for the full analysis |
| `Project.html` | Rendered report (served via GitHub Pages) |
| `index.html` | Redirects the GitHub Pages root to `Project.html` |
| `parkinsons.csv` | Dataset |
| `parkinsons_names.csv` | UCI dataset documentation |
| `Parkinson_s_Dataset_Feature_Mapping.csv` | Feature descriptions grouped by category and the vocal muscles involved |
| `correlation_plot.png` | Correlation heatmap of the features |
| `Final_Parkinsons_Project_Analysis.pptx` | Presentation slides (original submission) |
| `Proposal for Analysis of Parkinson's Dataset.docx` | Project proposal (original submission) |

## Reproducing the analysis

1. Install [R](https://www.r-project.org/) and [RStudio](https://posit.co/download/rstudio-desktop/) (optional, but convenient).
2. Install the required packages:

   ```r
   install.packages(c(
     "glue", "dplyr", "tidyr", "boot", "corrplot", "caret", "car", "MASS",
     "e1071", "glmnet", "tree", "randomForest", "gbm", "ggplot2", "reshape2",
     "knitr", "rmarkdown"
   ))
   ```

3. Open `Project.Rmd` from this folder (it reads `parkinsons.csv` via a relative path) and knit it (about 1 to 2 minutes), or run:

   ```r
   rmarkdown::render("Project.Rmd")
   ```

## Acknowledgements

Dataset: Little, M. A., McSharry, P. E., Roberts, S. J., Costello, D. A. E., & Moroz, I. M. (2007). *Exploiting Nonlinear Recurrence and Fractal Scaling Properties for Voice Disorder Detection.* BioMedical Engineering OnLine, 6:23.
