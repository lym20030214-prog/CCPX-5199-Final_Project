# Gender as a Moderator of the Association between Depression Vulnerability and Math GPA during Adolescence

---

## 1. Aim of the Analysis

This project conducts a longitudinal, secondary-data analysis to test how depressive symptoms in early adolescence relate to math academic achievement one year later, and whether gender moderates that relationship. Specifically, the notebook addresses four research questions using Wave I (1994–95) and Wave II (1996) of the **National Longitudinal Study of Adolescent to Adult Health (Add Health)**:

1. **Gender differences in depression** — Do girls report higher Wave I depression severity than boys?
2. **Depression → Math GPA** — Does Wave I depression negatively predict Wave II math GPA, after controlling for baseline GPA, SES, and self-efficacy?
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

> **Note on data access:** These files are restricted-distribution but freely available from ICPSR. They are **not** included in this repository. Download them from <https://www.icpsr.umich.edu/web/ICPSR/studies/21600> and place them at the paths listed in the next section.

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

Tested with Python 3.10. All packages are pinned-loose; any modern version should work.

```text
pyreadstat>=1.2     # read Stata .dta files
pandas>=1.5
numpy>=1.23
scipy>=1.10         # ttest_ind, t.interval
statsmodels>=0.14   # OLS via formula API (smf.ols)
matplotlib>=3.7
```

`requirements.txt`:

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

All effects below are at α = .05, two-tailed. Full statistics print to stdout when you run the corresponding cells.

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

**Q3 — Moderation by gender (Cell 7).** The Depression × Gender interaction was statistically significant. Girls' slope was substantially steeper than boys', meaning depression eroded math performance faster for girls.

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

### Headline takeaway

Girls in this sample outperform boys in math on average, **and** they are more vulnerable to the academic cost of depression. Beneath the achievement gap that favors girls sits a steeper psychological cost: when depression severity is high enough (above ≈ 1.74 on the 0–3 scale), the female advantage disappears in this small subgroup, though that reversal is not statistically reliable. The pattern motivates further research into gender-specific protective and vulnerability factors that the depression-only model does not capture.

---


```


