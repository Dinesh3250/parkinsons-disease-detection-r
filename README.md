# Parkinson's Disease Detection from Voice Measurements

A data mining project in R that compares parametric and non-parametric classifiers for detecting Parkinson's disease from biomedical voice recordings.

**Author:** Dinesh Kumar Gummadavelli

📄 **[Read the full report (live page)](https://dinesh3250.github.io/parkinsons-disease-detection-r/)** · 📊 [Slides](Final_Parkinsons_Project_Analysis.pptx) · 📝 [Project proposal](Proposal%20for%20Analysis%20of%20Parkinson's%20Dataset.docx)

## Overview

Parkinson's disease affects motor control, including the muscles that produce speech. Subtle changes in pitch, amplitude and noise in a person's voice can therefore act as an early signal. This project asks:

> Which model is most effective at detecting Parkinson's disease from voice features, and how well does it generalize?

Models are evaluated on accuracy, precision and recall. Recall gets extra weight because a false negative (a missed patient) is the costly error in a medical setting.

## Dataset

[UCI Machine Learning Repository – Parkinsons Data Set](https://archive.ics.uci.edu/dataset/174/parkinsons) (Oxford Parkinson's Disease Detection Dataset, created by Max Little, University of Oxford, with the National Centre for Voice and Speech, Denver).

- 195 voice recordings from 31 people, 23 of whom have Parkinson's
- 22 numeric voice features (jitter, shimmer, noise-to-harmonics ratio, nonlinear dynamics measures, etc.)
- Target: `status` (1 = Parkinson's, 0 = healthy)

## Methods

| Category | Techniques |
|---|---|
| Parametric | Logistic regression, LDA, QDA, Naive Bayes, Lasso, Ridge |
| Non-parametric | KNN, Decision Tree, Random Forest, Gradient Boosting, SVM |
| Dimension reduction | PCA + logistic regression |
| Validation | Train/test split, LOOCV, 10-fold cross-validation |

The analysis also covers multicollinearity (correlation heatmap, VIF) and the trade-off between interpretability and predictive performance.

## Results

Single train/test split (140 train / 55 test, `set.seed(32)`):

| Model | Accuracy | Precision | Recall |
|---|---:|---:|---:|
| Logistic regression | 85.5 | 97.3 | 83.7 |
| LDA | 87.3 | 100.0 | 84.1 |
| QDA | 81.8 | 78.7 | 100.0 |
| Naive Bayes | 70.9 | 92.0 | 62.2 |
| Lasso | 81.8 | 97.3 | 80.0 |
| Ridge | 80.0 | 97.3 | 78.3 |
| KNN | 83.6 | 83.3 | 94.6 |
| Decision Tree | 94.5 | 94.7 | 97.3 |
| Random Forest | 87.3 | 85.7 | 97.3 |
| Boosting | 89.1 | 89.7 | 94.6 |
| SVM | 83.6 | 80.4 | 100.0 |

**Key findings**

- The decision tree looked best on the single split, but its accuracy and precision dropped noticeably under cross-validation, which points to overfitting.
- PCA + logistic regression was the most stable model after cross-validation, with 100% recall and steady accuracy.
- Parametric models (logistic, QDA) changed little under cross-validation; the non-parametric models varied more.

See [`Project.html`](Project.html) for the full write-up, including limitations and future directions.

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
| `Final_Parkinsons_Project_Analysis.pptx` | Presentation slides |
| `Proposal for Analysis of Parkinson's Dataset.docx` | Project proposal |

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

3. Open `Project.Rmd` from this folder (it reads `parkinsons.csv` via a relative path) and knit it, or run:

   ```r
   rmarkdown::render("Project.Rmd")
   ```

## Acknowledgements

Dataset: Little, M. A., McSharry, P. E., Roberts, S. J., Costello, D. A. E., & Moroz, I. M. (2007). *Exploiting Nonlinear Recurrence and Fractal Scaling Properties for Voice Disorder Detection.* BioMedical Engineering OnLine, 6:23.
