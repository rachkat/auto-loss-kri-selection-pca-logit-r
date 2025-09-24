# Auto Insurance Loss — KRI Selection with PCA & Logistic Regression (R)

![R](https://img.shields.io/badge/R-4.2.1-276DC3?logo=r&logoColor=white) ![Status](https://img.shields.io/badge/status-finished-brightgreen) ![License](https://img.shields.io/badge/license-MIT-black) ![Focus](https://img.shields.io/badge/focus-risk--analytics%20%7C%20KRI%20selection-blue)

---

## Executive Summary
This project identifies **Key Risk Indicators (KRIs)** for auto-insurance loss using the Insurance Institute for Highway Safety (IIHS) dataset. I engineered an interpretable **“Buy”** label (1 = low average loss) and evaluated which loss categories best predict this outcome.  

The workflow combines:
- **PCA** to reduce dimensionality and reveal dominant risk factors,  
- **Linear regression** to quantify directional relationships with the Buy signal, and  
- **Logistic regression** to model the probability of Buy.  

### Highlights
- **PCA**: First two components explain **~88.9%** of variance (PC1 ≈ 70.6%).  
- **Linear model** (Buy ~ 6 loss categories): R² ≈ **0.545**, moderate explanatory power.  
- **Logistic models**: All six categories are highly significant under quasibinomial GLM. A simple model using **only `avgloss`** is also strongly predictive.  
- **Recommended KRIs**: Collision, Bodily Injury, Property Damage, and composite **Average Loss (`avgloss`)**.  

📄 **Full write-up PDF** → [Determination-of-Key-Risk-Indicators.pdf](./Determination-of-Key-Risk-Indicators.pdf)

---

## Table of Contents
- [Project Context](#project-context)  
- [Data & Labeling](#data--labeling)  
- [Methods](#methods)  
  - [Principal Components Analysis (PCA)](#principal-components-analysis-pca)  
  - [Linear Regression](#linear-regression)  
  - [Logistic Regression](#logistic-regression)  
- [Results & Interpretation](#results--interpretation)  
- [KRIs Recommended](#kris-recommended)  
- [Reproducibility](#reproducibility)  
- [Technologies](#technologies)  
- [Key Skills Demonstrated](#key-skills-demonstrated)  
- [Limitations & Next Steps](#limitations--next-steps)  
- [License](#license)  
- [Tags](#tags)  

---

## Project Context
**Goal**: Identify which insurance loss categories (collision, bodily injury, property damage, comprehensive, personal injury, medical payment) are most indicative of low average loss for a vehicle model, and formalize them as **KRIs**.  

**Business Value**:  
- Prioritizes low-risk vehicles (“Buy”) and flags high-risk ones early.  
- Produces interpretable KRIs that can be monitored, reported, and governed.  

---

## Data & Labeling
- **Source**: IIHS dataset of insurance losses by vehicle model.  
- **Engineered Features**:  
  - `avgloss` = row-wise mean of six loss categories (with NA handling).  
  - `Buy` = binary target → 1 if `avgloss < 100`, else 0.  

---

## Methods  

### Principal Components Analysis (PCA)
- **Purpose**: compress correlated metrics into orthogonal factors.  
- **Implementation**: `princomp(~., iihs_data[ ,3:8])`.  
- **Use**: loadings show which categories dominate risk.  

### Linear Regression
- **Model 1**: Buy ~ Collision + PropertyDamage + Comprehensive + PersonalInjury + MedicalPayment + BodilyInjury  
- **Model 2**: Adds `avgloss`.  
- **Metrics**: coefficients, p-values, R².  

### Logistic Regression
- **GLM (quasibinomial)** to model P(Buy=1).  
- **Models**:  
  - Full GLM with 6 predictors.  
  - Full GLM + `avgloss`.  
  - Simple GLM: Buy ~ `avgloss`.  

---

## Results & Interpretation  

### PCA  
- PC1 ≈ **70.6%**, PC2 ≈ **18.3%** → ~88.9% cumulative.  
- Reveals strong latent factor across categories (e.g., collision frequency/severity).  

### Linear Regression  
- R² ≈ **0.545**; several predictors weakly significant.  
- Adding `avgloss` did not improve R² (collinearity).  

### Logistic Regression  
- Full GLM: all six predictors **highly significant**.  
- Signs aligned with risk intuition: higher loss → lower Buy odds.  
- `avgloss`-only model: very strong predictor → efficient single KRI.  

---

## KRIs Recommended
- **Collision Loss** — strongest, consistent signal.  
- **Bodily Injury Loss** — high cost severity.  
- **Property Damage Loss** — meaningful variance contributor.  
- **Average Loss (`avgloss`)** — efficient screening KRI.  

*Evidence*: PCA loadings, logistic regression coefficients, and interpretability for reporting.  

---

# Reproducibility  

**Environment**: R 4.2.1 (Windows x64), RStudio  
**Packages**: `stats`  

```r
# Load data
iihs <- read.csv("DAT_610_IIHS_Data.csv", header = TRUE)

# Feature engineering
iihs$avgloss <- rowMeans(iihs[, c("Collision","Property.damage",
                                  "Comprehensive","Personal.injury",
                                  "Medical.payment","Bodily.injury")], na.rm = TRUE)
iihs$Buy <- as.numeric(iihs$avgloss < 100)

# PCA
pca <- princomp(~., iihs[,3:8], na.action = na.omit)
summary(pca); screeplot(pca)

# Linear regression
lm1 <- lm(Buy ~ Collision + Property.damage + Comprehensive +
                Personal.injury + Medical.payment + Bodily.injury, data = iihs)

# Logistic regression
glm_full <- glm(Buy ~ Collision + Property.damage + Comprehensive +
                      Personal.injury + Medical.payment + Bodily.injury,
                family = "quasibinomial", data = iihs)
glm_simple <- glm(Buy ~ avgloss, family = "quasibinomial", data = iihs)

summary(lm1); summary(glm_full); summary(glm_simple)

```
---

## Technologies
- **R 4.2.1**, **RStudio**  
- **Methods**: PCA (`princomp`), Linear Regression (`lm`), Logistic Regression (`glm`)  
- **Practices**: feature engineering, missing-data handling, model interpretability  

---

## Key Skills Demonstrated
- Risk modeling & KRI design aligned to underwriting needs  
- Dimensionality reduction to reveal latent risk drivers  
- Binary outcome modeling with interpretable coefficients  
- Feature engineering (`avgloss`) for deployable indicators  
- Clear, professional documentation for reproducibility  

---

## Limitations & Next Steps
- **Labeling**: Explore thresholds beyond `avgloss < 100` (ROC-based)  
- **Validation**: Add cross-validation and calibration checks  
- **Governance**: Formalize thresholds & monitoring rules  

---

## License
Released under the **MIT License**. See [LICENSE](./LICENSE).  

---

## Tags
`r, risk-analytics, insurance, kpi-kri, pca, logistic-regression, linear-regression, dimensionality-reduction, model-interpretability, underwriting, actuarial, data-science, statistics, por
