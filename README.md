# Gender as a Moderator of the Association between Depression Vulnerability and Math GPA during Adolescence

---

## 0. Why project
I chose this topic because I am personally interested in how mental health affects academic outcomes in adolescents, particularly whether that relationship looks different for boys and girls. When selecting a dataset, Add Health was a strong fit. The dataset includes measures of depressive symptoms, academic performance, SES, and self-efficacy all in one longitudinal design. The main limitation of this choice is that the data are from 1994–1996, which is now about 30 years old. Adolescent mental health and academic contexts have changed considerably since then, so the findings may not fully reflect today's students. 

## 1. Aim of the Analysis

This project conducts a longitudinal, secondary-data analysis to test how depressive symptoms in early adolescence relate to math academic achievement one year later, and whether gender moderates that relationship. Specifically, the notebook addresses four research questions using Wave I (1994–95) and Wave II (1996) of the **National Longitudinal Study of Adolescent to Adult Health (Add Health)**:

1. **Gender differences in depression** — Do girls report higher Wave I depression severity than boys?
2. **Depression predicts Math GPA** — Does Wave I depression negatively predict Wave II math GPA, after controlling for baseline GPA, SES, and self-efficacy?
3. **Moderation by gender** — Does gender change the strength of the depression → math GPA relationship?
4. **Gender gap in math GPA** — How do girls' and boys' mean math GPAs compare at Wave I and Wave II, overall and within depression-severity subgroups?

The analytic sample consists of **N = 3,549 adolescents** (1,663 boys; 1,886 girls) in Grades 7–11 at Wave I, after listwise deletion on key variables and exclusion of 12th graders (who had graduated by Wave II).

---

## 2. Data

The notebook reads two Stata `.dta` files from the ICPSR Add Health Public-Use release (Study 21600):

| File | Wave | Description |
|---|---|---|
| `21600-0001-Data.dta` | Wave I (1994–95) | In-home interview: depression items (H1FS1–H1FS19), baseline math grade (H1ED12), gender (BIO_SEX), mother's education (H1RM1), self-efficacy items (H1SE1–H1SE4), grade level (H1GI20) |
| `21600-0005-Data.dta` | Wave II (1996) | In-home interview: outcome math grade (H2ED8) |

> Note on data access: These are ICPSR Add Health public-use files, freely available after creating an ICPSR MyData account and agreeing to the Terms of Use. They are not included in this repository — download from https://www.icpsr.umich.edu/web/ICPSR/studies/21600

### Constructed variables

| Variable | Source | Coding |
|---|---|---|
| `dep_w1` | Mean of 19 Feeling-Scale items (items 4, 8, 11, 15 reverse-coded; "don't know"/"refused" treated as missing); requires ≥ 14 valid items | Continuous, 0–3 (higher = more depressive symptoms). Cronbach's α ≈ 0.86 |
| `gpa_w1` | `5 − H1ED12` (recodes letter-grade response so higher = better) | Ordinal, 1–4 |
| `gpa_w2` | `5 − H2ED8` | Ordinal, 1–4 |
| `gender` | `BIO_SEX` recoded | 0 = Male, 1 = Female |
| `ses_w1` | Mother's education `H1RM1` (capped at ≤ 9) | Ordinal, 1–9 |
| `se_w1` | Mean of 4 self-efficacy items (item 4 reverse-coded so lower = greater self-efficacy) | Continuous, 1–5. Cronbach's α ≈ 0.58 (interpret with caution) |

---

## 3. How to Run

The notebook was authored in **Google Colab** (paths default to `/content/...`). 

### Google Colab 

```text
1. Upload 21600-0001-Data.dta and 21600-0005-Data.dta to the Colab session
   (drag into the file panel, or use files.upload()). They will land at /content/.
2. Open CCPX_5199_Final.ipynb in Colab.
3. Runtime → Run all.
```

The first cell installs `pyreadstat` (`!pip install pyreadstat`); the rest of the notebook runs end-to-end and prints all tables plus three figures.


### Notebook structure

The notebook is laid out as eight numbered analysis cells plus three figure cells. Each cell is self-contained but assumes earlier cells have been run:

```text
Cell 1   Setup, imports, helper functions (drop_bad, cronbach)
Cell 2   Load .dta files, build all derived variables, merge waves, drop 12th grade,
         apply listwise deletion → produces df_a (the analytic frame, N = 3,549)
Cell 3   Cronbach's α for Depression and Self-Efficacy scales
Cell 4   Descriptive statistics (M, SD) for all key variables
Cell 5   Independent t-test: gender differences in Wave I depression
Cell 6   Simple OLS (gpa_w2 ~ dep_w1) and multiple OLS controlling for SES,
         self-efficacy, baseline GPA
Cell 7   Moderation model with mean-centered depression and Depression × Gender
         interaction; computes simple slopes and the line-intersection point
Cell 8   t-tests on Wave I and Wave II math GPA; subgroup t-tests above/below
         the depression threshold derived in Cell 7
Figs 1–3 Mean depression by gender; simple-slopes plot; mean math GPA by gender
         across waves
```

---

## 4. Dependencies

Tested with Python 3.10. All packages are pinned loosely; any modern version should work.

```
pyreadstat
pandas
numpy
scipy
statsmodels
matplotlib
```

Install everything with:

```bash
pip install pyreadstat pandas numpy scipy statsmodels matplotlib
```

---

## 5. Summary of Results

All effects below are at α = .05, two-tailed. 

**Q1 — Gender differences in depression (Cell 5).** Girls reported significantly higher Wave I depression severity than boys.

```text
Boys:  N = 1,663   M = 0.4823   SD = 0.3314
Girls: N = 1,886   M = 0.5760   SD = 0.3978
t(3,547) = 7.5649   SE = 0.0124   p < .001
```

**Q2 — Depression → Math GPA (Cell 6).** Wave I depression negatively predicted Wave II math GPA, both unadjusted and after controlling for SES, self-efficacy, and baseline math GPA.

```text
Simple regression:    β = −0.3890   t = −8.4636   p < .001
Multiple regression:  β = −0.1903   t = −4.3181   p < .001
   (controls: ses_w1, se_w1, gpa_w1)
```

**Q3 — Moderation by gender (Cell 7).** The Depression × Gender interaction reached statistical significance, though the p-value is borderline (p ≈ .049) and should be interpreted as marginal evidence rather than a robust effect. Girls' slope was substantially steeper than boys', meaning depression eroded math performance faster for girls.

```text
Interaction (Depression_c × Gender):  β = −0.1750   t = −1.97   p ≈ .049
Simple slopes (at covariate means):
   Boys:  −0.100      Girls: −0.275
Lines cross at:  Depression ≈ 1.74,  Predicted Math GPA ≈ 2.50
```

**Q4 — Gender gap in math GPA (Cell 8).** Girls had significantly higher mean math GPA than boys at both waves. Within the small subgroup with depression scores at or above the 1.74 threshold, the direction reversed but was not statistically significant.

```text
Wave I  (baseline):  Girls M = 3.0764   Boys M = 2.7180   t = 11.53   p < .001
Wave II (outcome):   Girls M = 2.8181   Boys M = 2.6236   t =  5.66   p < .001

Subgroup, dep < 1.74 (N ≈ 3,521): Girls > Boys, p < .001
Subgroup, dep ≥ 1.74 (N = 28):    Girls (M = 2.19) < Boys (M = 2.86), p = .194 (n.s.)
```

### Takeaway

Girls in this sample score higher in math than boys on average. This female advantage is consistent across most of the sample. However, depression does not affect both genders equally. Girls show a steeper drop in math performance as depression severity increases. This means depression is more academically costly for girls than for boys.
When depression is mild to moderate, girls still outperform boys. But when depression is severe (above approximately 1.74 on the 0–3 scale) the female advantage disappears. In this high-severity subgroup, boys and girls perform similarly. This reversal is based on a small number of participants, so it is not statistically reliable and should be interpreted with caution.
The pattern raises an important question that why are girls more vulnerable to the academic effects of depression? One possibility is that boys and girls have different psychological risk and protective factors that influence how depression translates into academic outcomes. A model that only looks at depression severity cannot answer this question. Future research should examine gender-specific factors that may explain why the same level of depression has a larger academic impact on girls than on boys.

## 6. Citation
 
> Harris, K. M., Halpern, C. T., Whitsel, E. A., Hussey, J. M., Killeya-Jones, L. A., Tabor, J., & Dean, S. C. (2019). Cohort profile: The National Longitudinal Study of Adolescent to Adult Health (Add Health). *International Journal of Epidemiology*, 48(5), 1415–1425. https://doi.org/10.1093/ije/dyz115
 








