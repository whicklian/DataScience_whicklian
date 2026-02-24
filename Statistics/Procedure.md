---

## Step-by-Step: Two-Sample Hypothesis Testing in Python

---

### **Cell 1: Import Libraries**

```python
import numpy as np
import scipy.stats as stats
import math
```

**Explanation:**

* `numpy` → for numerical operations (mean, std, variance, arrays)
* `scipy.stats` → for statistical tests like *t-test*, *Levene’s test*, etc.
* `math` → for basic math like square roots and rounding

These are the essential libraries every one uses for hypothesis testing and probability work.

---

### **Cell 2: Define the Function Header and Description**

```python
def compare_two_groups(sample1, sample2, label1="Group 1", label2="Group 2", alpha=0.05, assume_equal_var=False):
    """
    Perform hypothesis testing (Z-test or T-test)
    between two independent samples.
    """
```

**Explanation:**

* We’re creating a **reusable function** so we can test *any two groups* (e.g., “Appliances vs Accessories”).
* `alpha=0.05` → our **significance level** (probability threshold for rejecting the null hypothesis).
* `assume_equal_var=False` → by default, we **don’t assume equal variances** (Welch’s test, which is safer).

---

### **Cell 3: Step 1 — Define the Hypotheses**

```python
print("="*70)
print(f"Hypothesis Test: Comparing {label1} vs {label2}")
print("="*70)

print("\nStep 1: Define Hypotheses")
print(f"Null Hypothesis (H₀): Mean of {label1} = Mean of {label2}")
print(f"Alternate Hypothesis (H₁): Mean of {label1} ≠ Mean of {label2}\n")
```

**Explanation:**
Every statistical test begins with a **hypothesis**:

* **H₀ (Null):** There’s no difference between group means.
* **H₁ (Alternative):** There *is* a difference.

The test will later give us a **p-value** to decide whether to reject or fail to reject H₀.

---

### **Cell 4: Step 2 — Descriptive Statistics**

```python
mean1, mean2 = np.mean(sample1), np.mean(sample2)
var1, var2 = np.var(sample1, ddof=1), np.var(sample2, ddof=1)
n1, n2 = len(sample1), len(sample2)
std1, std2 = np.std(sample1, ddof=1), np.std(sample2, ddof=1)

print("Step 2: Sample Summary")
print(f"{label1}: n={n1}, mean={mean1:.2f}, std={std1:.2f}")
print(f"{label2}: n={n2}, mean={mean2:.2f}, std={std2:.2f}\n")
```

**Explanation:**
We calculate **summary statistics** for both groups:

* `mean`: the average
* `std`: standard deviation (spread)
* `n`: number of observations
* `ddof=1`: ensures we use *sample variance*, not population variance (important for small datasets)

This gives us a snapshot before testing — do the means *look* different?

---

### **Cell 5: Step 3 — Check Variance Equality (Levene’s Test)**

```python
print("Step 3: Variance Check")
lev_stat, lev_p = stats.levene(sample1, sample2)
print(f"Levene’s test p-value: {lev_p:.4f} → {"Equal variances" if lev_p > alpha else "Unequal variances"}")

equal_var = assume_equal_var if assume_equal_var else (lev_p > alpha)
print(f"→ Proceeding with {"Student’s t-test (equal variances)" if equal_var else "Welch’s t-test (unequal variances)"}\n")
```

**Explanation:**

* **Levene’s Test** checks whether both samples have similar variances.
* If **p > 0.05**, variances are roughly equal → we can use the **Student’s t-test**.
* If **p < 0.05**, variances differ → we use the **Welch’s t-test** (doesn’t assume equal variance).

This step ensures our choice of test is statistically correct.

---

### **Cell 6: Step 4 — Perform the T-Test**

```python
print("Step 4: Performing T-Test")
t_stat, p_val = stats.ttest_ind(sample1, sample2, equal_var=equal_var)
print(f"t-statistic = {t_stat:.4f}")
print(f"p-value = {p_val:.4f}")
print(f"Decision (α={alpha}): {"Reject H₀" if p_val < alpha else "Fail to Reject H₀"}\n")
```

**Explanation:**

* `stats.ttest_ind()` performs an **independent samples t-test**.
* `t_stat`: measures how many standard errors apart the means are.
* `p_val`: probability of observing this difference *if* H₀ were true.

Decision rule:

* **If p < α (e.g. 0.05):** reject H₀ → significant difference.
* **If p ≥ α:** fail to reject H₀ → no significant difference.

---

### **Cell 7: Step 5 — Compute Confidence Interval (CI)**

```python
print("Step 5: Confidence Interval (95%)")
mean_diff = mean1 - mean2
se = math.sqrt(var1/n1 + var2/n2)
t_crit = stats.t.ppf(1 - alpha/2, df=min(n1, n2) - 1)
lower = mean_diff - t_crit * se
upper = mean_diff + t_crit * se
print(f"Mean difference = {mean_diff:.3f}")
print(f"95% CI: ({lower:.3f}, {upper:.3f})")
print(f"→ 0 {"is NOT" if lower>0 or upper<0 else "IS"} within the interval → {"Significant difference" if lower>0 or upper<0 else "No significant difference"}\n")
```

**Explanation:**

* The **confidence interval** estimates the *range* where the true mean difference lies.
* If **0 is not in the CI**, the difference is significant.
* Formula:
  [
  CI = \bar{X_1} - \bar{X_2} \pm t_{crit} \times SE
  ]
* `t_crit` = critical t-value for 95% confidence.

This tells you the *precision* of your difference estimate.

---

### **Cell 8: Step 6 — Effect Size (Cohen’s d)**

```python
print("Step 6: Effect Size (Cohen’s d)")
pooled_std = math.sqrt(((n1 - 1)*std1**2 + (n2 - 1)*std2**2) / (n1 + n2 - 2))
cohen_d = (mean1 - mean2) / pooled_std
effect_strength = ("Very small" if abs(cohen_d) < 0.2 else
                   "Small" if abs(cohen_d) < 0.5 else
                   "Medium" if abs(cohen_d) < 0.8 else "Large")
print(f"Cohen’s d = {cohen_d:.3f} → {effect_strength} effect\n")
```

**Explanation:**
Even if a difference is statistically significant, we want to know **how big** it is.
That’s what **Cohen’s d** tells us:

* ( d = \frac{\text{mean1 - mean2}}{\text{pooled std}} )
  Interpretation:
* **0.2 → small**, **0.5 → medium**, **0.8+ → large**

This helps answer: “Is the difference *practically meaningful*?”

---

### **Cell 9: Step 7 — Final Interpretation Summary**

```python
print("Step 7: Interpretation Summary")
print("-"*70)
if p_val < alpha:
    print(f"We reject the null hypothesis. There IS a statistically significant difference between {label1} and {label2}.")
else:
    print(f"We fail to reject the null hypothesis. There is NO statistically significant difference between {label1} and {label2}.")
print(f"Effect Size: {effect_strength} ({cohen_d:.3f})")
print(f"Confidence Interval: ({lower:.2f}, {upper:.2f})")
print("="*70)
```

**Explanation:**
This part gives a **clear summary** of what the results mean in plain English:

* Whether the means are *significantly different* (based on p-value)
* How *large* the difference is (effect size)
* The *confidence range* for that difference

This makes the output **understandable even for non-statisticians**.

---

### **Cell 10: Example Usage**

```python
appSalesData = data[data['Sub-Category'] == 'Appliances']['Sales'].values
accSalesData = data[data['Sub-Category'] == 'Accessories']['Sales'].values

compare_two_groups(appSalesData, accSalesData, label1="Appliances", label2="Accessories", alpha=0.05)
```

**Explanation:**
Here we compare:

* **Appliances** vs **Accessories** sales from your Superstore dataset
* α = 0.05 (5% significance level)
* Function prints full analysis: mean, t-test, p-value, CI, and effect size

---