# Exploring Treatment Effect Heterogeneity of Right Heart Catheterization Using Time-to-Event Survival Machine Learning Models

## Project Overview  
This repository contains code and analyses from our **Summer 2025 Big Data Summer Immersion at Yale (BDSY)** research project, where we investigated heterogeneous treatment effects (HTE) of right heart catheterization (RHC), a heart monitoring procedure, in critically ill patients using the SUPPORT dataset.

While prior work (Connors et al., 1996) reported a negative overall average treatment effect of RHC, we explored whether modern machine learning–based causal inference methods could uncover subgroup-level variation in outcomes that might inform more individualized clinical decision-making.

Our team developed a survival analysis framework integrating DeepSurv and Causal Survival Forests (CSF) to estimate 30-180 day survival, identify patient characteristics that influence treatment effectiveness, and highlight clinically meaningful subpopulations. Our framework demonstrates how machine learning methods can move beyond average effects to guide personalized treatment.

## Research Question
*How can the implementation of time-to-event analysis techniques explore heterogeneity in the causal effect of RHC and identify clinically significant patient subpopulations?*

---

## Data Source
- SUPPORT Study (Connors et al., 1996): Critically ill patients, with baseline covariates, treatment assignments (RHC vs. non-RHC), and survival outcomes.
- Preprocessing included missing data imputation, censoring adjustments, and variable encoding.

## Assumptions
- Causal Inference Assumptions: Positivity, Consistency, Exchangeability, Independence of Observations
- Survival Analysis Assumptions: Independence of Censoring (3.2% right censored, discharged and ceased contact before cutoff; tested using imputed dataset), Proportional Hazards

---

## Approach
We implemented a multi-model survival analysis pipeline to explore treatment effect heterogeneity of RHC.
- Selecting a Causal Estimand and Outcome
  - We estimate Conditional Average Treatment Effects (CATEs) using the difference in Restricted Mean Survival Time (RMST) at 180 days. RMST reflects expected survival up to a fixed time and is both causally interpretable and clinically relevant.
- Implementing Models
    - DeepSurv
        - Neural network extension of the Cox proportional hazards model aimed at accurately predicting survival probabilities.
        - Limited by the proportional hazards assumption and low interpretability.
    - Causal Survival Forests (CSF)
        - Survival tree-based extension of random forests that estimates heterogeneous treatment effect (HTE) by splitting to maximize treatment effect differences
        - Does not directly predict survival or have a measureable performance indicator (like the C-index)
    - Random Survival Forests (RSF)
        - Ensemble tree-based method for non-parametric survival prediction.
        - Served as a comparison model for performance and interpretability.
- Evaluating Models
    - DeepSurv
        - C-index (Evaluator of predictive accuracy): 0.7095, compared to the traditional IPW-Cox model's 0.6860.
    - Causal Survival Forests (CSF)
        - Simulated the CATEs of 10,000 individuals with a linear treatment effect.
        - Both DeepSurv and Causal Survival Forests (CSF) aligned well with the true effect distribution, but CSF more closely matched the shape of the true distribution, highlighting its value for estimating heterogeneous effects.
- Building a Framework for Identifying Heterogeneity
    - Our approach integrates prediction (DeepSurv) with heterogeneity estimation (CSF) to find clinically relevant patient subgroups:
        - DeepSurv identifies key predictors of survival risk.
        - Causal Survival Forests (CSF) identify drivers of variation in treatment effect.

---

## Repository Contents  
- `data/`  
  - `rhc.csv` → Original SUPPORT dataset
  - `rhc_imputed.csv` → Cleaned and imputed dataset 
  - `rhc_uncensored.csv` → Adjusted dataset for DeepSurv model
  - `Dictionary.xlsx` → SUPPORT dataset dictionary with variable definitions
- `code/`  
  - `DataCleaning.Rmd` → Data preparation (Written by Leo Hickey)
  - `CSF.Rmd` → Causal Survival Forests (Written by Quinn Hungerford and Isabella Summe)
  - `RSF.ipynb` → Random Survival Forests (Written by Isabella Summe, edited by Quinn Hungerford)
  - `DeepSurv.Rmd` → DeepSurv base model (Written by Abhroneel Ghosh, edited by Quinn Hungerford)
  - `DeepSurv_CART.Rmd` → DeepSurv decision tree visualization (Written by Abhroneel Ghosh, edited by Quinn Hungerford)
  - `DeepSurv_CATE_Vis.Rmd` → DeepSurv visualizations (Written by Abhroneel Ghosh, edited by Quinn Hungerford)
  - `DeepSurv_Simulation.Rmd` → Simulation experiments to evaluate CSF against DeepSurv (Written by Abhroneel Ghosh, edited by Quinn Hungerford)
- `results/`
  - `figures/` → All generated plots (PNG outputs), separated into `CSF/`, `RSF/`, and `DeepSurv/` folders.
  - `tables/` → Model dataframe outputs and summary CSVs, separated into `CSF/`, `RSF/`, and `DeepSurv/` folders.
- `presentation`
  - `final_poster.pdf` → Final research poster
  - `final_presentation.pptx` → Symposium presentation
      - Contains the work of 3 subgroups:
          - *Estimating the Average Treatment Effect (ATE) of RHC* by Gabrielle Craft (Smith), Aditya Krishnan (UNC), and Akshay Kumar (Harvard)
          - *Binary Survival Machine Learning Methods Evaluating RHC* by Ria Bhandari (UC Davis) and Saptashwa Baisya (Indian Statistical Institute)
          - (Our subgroup) *Exploring HTE in RHC Using Time-to-Event Survival Machine Learning Methods* by Quinn Hungerford (UT Austin), Abhroneel Ghosh (Indian Statistical Institute), Leo Hickey (Vassar), and Isabella Summe (UChicago)

---

## Key Findings  
Our work revealed that **right heart catheterization generally offered little to no survival benefit for most patients**, with a majority showing negative effects. However, a small subgroup appeared to benefit from RHC, indicating meaningful heterogeneity in treatment response.
  - DeepSurv's CATE distribution was left-skewed, with ~11% of patients benefiting from RHC and ~89% experiencing harm or no benefit.
By designing a decision tree on CATE estimates with DeepSurv, we identified **respiratory rate and mean arterial pressure as key covariates influencing heterogeneity.** Patients with high respiratory rate (≥25 bpm) and low MAP (<73 mm Hg) had the strongest negative effects.
  - Respiratory rate exhibited clear bimodal distributions that aligned with clinically meaningful cutoffs (like tachypnea ≥25 bpm).
  - Mean arterial pressure was similar, with hypertension/hypotension levels linked to treatment heterogeneity.
We used CSF to validate these findings. The model confirmed respiratory rate and mean arterial pressure as drivers of treatment effect variation.

---

## Reproducibility
All analyses are reproducible using the code and datasets provided in this repository:
- Clone this repository.
- Install required R and Python packages (listed in each Rmd/Notebook).
- Run `DataCleaning.Rmd` to generate cleaned datasets.
- Execute model scripts (`DeepSurv.Rmd`, `CSF.Rmd`, `RSF.ipynb`) to reproduce survival models.
- Outputs (figures and tables) are stored in `results/`.

---

## Authors & Contributors
- Quinn Hungerford (UT Austin): CSF implementation and decision tree, editing/cleaning all project files, and integration of models.
- Abhroneel Ghosh (Indian Statistical Institute): DeepSurv model development, visualizations, and simulation comparison.
- Leo Hickey (Vassar): Data cleaning, preprocessing, and evaluating assumptions. 
- Isabella Summe (UChicago): RSF modeling, CSF visualizations and model editing

## Acknowledgments
For their invaluable support, we thank our principal investigators Dr. Fan Li and Dr. Lee Kennedy-Shaffer, program director Dr. Bhramar Mukherjee, and the BDSY staff including Xi Fang, Jiaqi Tong, and Jackson Higginbottom.

---

*Developed as part of the 2025 Big Data Summer Immersion program at the Yale School of Public Health. This project demonstrates how machine learning–based survival models can move beyond average treatment effects to reveal patient-level heterogeneity, offering insights that may guide more personalized clinical decision-making.* 
