# Probability, Statistics and Concept Learning Lab
 
A Google Colab notebook with five exercises: simulating dice sums, estimating a binomial success rate, computing entropy, and two concept-learning algorithms (Find-S and Candidate Elimination).
 
<a href="https://colab.research.google.com/github/YOUR_USERNAME/YOUR_REPO/blob/main/YOUR_NOTEBOOK.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>
 
## Contents
 
| # | Task | Libraries |
|---|------|-----------|
| 1 | Dice-sum simulation: histograms, mean and variance | NumPy, Matplotlib |
| 2 | Binomial distribution: estimating a success rate from 97/100 positive reviews | NumPy, Matplotlib |
| 3 | Entropy of every column in `advertising.csv` | Pandas, NumPy |
| 4 | Maximal specific hypothesis (Find-S) for `enjoy_sport.csv` | Pandas |
| 5 | Version space using candidate elimination on `enjoy_sport.csv` | Pandas, itertools |
 
## Requirements
 
- Python 3.8 or later (Colab already has everything below)
- `numpy`, `pandas`, `matplotlib`
To run it locally instead of in Colab:
 
```bash
pip install numpy pandas matplotlib jupyter
```
 
## Data files
 
Upload these to the Colab session (or the `/content/` folder) before running questions 3 to 5:
 
- `advertising.csv` with columns TV, Radio, Newspaper and Sales
- `enjoy_sport.csv` with six attributes and a final `Yes`/`No` target column
## How to run
 
1. Click the **Open in Colab** badge above.
2. Upload `advertising.csv` and `enjoy_sport.csv` using the Files panel on the left.
3. Choose **Runtime → Run all**.
---
 
## Question 1: Dice-sum simulation
 
**Task.** Roll a die 1 to 50 times (X1, X2, ..., X50) and take the sum. Repeat the experiment 100, 500, 1000, 5000, 10000, 50000 and 100000 times. Plot and save the histogram of each case, and find the mean and variance.
 
**Method.**
- For each number of dice `n` from 1 to 50 and each number of trials, `np.random.randint(1, 7, size=(trials, n))` simulates the rolls and the rows are summed.
- The mean and variance of the sums are calculated with plain loops (no `np.mean` or `np.var`). The variance is the population variance (divided by the number of trials).
- One figure per dice count is saved, with one histogram per trial count stacked in it. Histograms are normalised (`density=True`) so the shapes can be compared across trial counts.
**Output.** Images are saved as `dice_histograms/rolls_<n>_dice.png`, and the mean and variance of every case are printed.
 
**What to expect.** A single die roll is uniform on 1 to 6. As more dice are added, the histogram of the sum becomes a bell curve (Central Limit Theorem). For `n` dice, the theory says:
 
| Quantity | Formula | Example (n = 10) |
|----------|---------|------------------|
| Mean | 3.5 × n | 35.0 |
| Variance | (35 / 12) × n | about 29.17 |
 
The simulated values should get closer to these as the number of trials grows.
 
## Question 2: Binomial distribution and the success rate
 
**Task.** A product got 97 positive reviews out of 100. Build binomial distributions with different success rates and decide which rate best explains the result, using histograms.
 
**Method.**
- Candidate success rates: 91%, 95%, 97% and 99%.
- For each rate, 10,000 experiments of 100 reviews are simulated and the number of positive reviews is recorded.
- The four histograms are shown in a 2×2 grid with a dashed red line at the observed value (97).
**Reading the plots.** The observed value should sit inside the bulk of the histogram for a good rate and far out in the tail for a poor one. The exact binomial probabilities give the same picture:
 
| Assumed rate | P(exactly 97 of 100) | P(97 or more) |
|--------------|----------------------|---------------|
| 91% | 0.0125 | 0.0173 |
| 95% | 0.1396 | 0.2578 |
| **97%** | **0.2275** | 0.6472 |
| 99% | 0.0610 | 0.9816 |
 
**Conclusion.** A rate of about **97%** is the best estimate. It makes the observed 97 the most likely single outcome, which matches the maximum-likelihood estimate 97/100 = 0.97. A rate of 91% makes 97 or more very unlikely (about 1.7%), so it is ruled out. 95% and 99% remain possible given only 100 reviews, but they explain the data less well.
 
## Question 3: Entropy of each column in `advertising.csv`
 
**Task.** Find the entropy of every column. Categorical columns can be used directly, and continuous columns have to be binned first.
 
**Method.**
- A column is treated as categorical if it is text or has fewer than 10 unique values. Otherwise it is split into 10 equal-width bins with `pd.cut`.
- Entropy is `H = -Σ p·log2(p)` over the category or bin frequencies, in bits.
**Result.**
 
| Column | Entropy (bits) |
|--------|----------------|
| TV | 3.2985 |
| Radio | 3.2896 |
| Newspaper | 2.7522 |
| Sales | 3.0530 |
 
With 10 bins the maximum possible entropy is log2(10) ≈ 3.32 bits. TV and Radio are close to that maximum, so their values are spread almost evenly across the bins. Newspaper is lower because its values are concentrated in fewer bins. The numbers depend on the number of bins, so they should only be compared with each other.
 
## Question 4: Maximal specific hypothesis (Find-S)
 
**Task.** Find the maximal specific hypothesis for the instances in `enjoy_sport.csv`.
 
**Method.** Find-S starts from the first positive (`Yes`) example and generalises. For each further positive example, any attribute that differs from the current hypothesis is replaced by `?`. Negative examples are ignored.
 
**Result.**
 
```
['Sunny', 'Warm', '?', '?', '?', '?']
```
 
The hypothesis says that the sport is enjoyed when the sky is Sunny and the air temperature is Warm, whatever the other attributes are.
 
## Question 5: Version space (Candidate Elimination, list-then-eliminate)
 
**Task.** Find the version space for the training examples in `enjoy_sport.csv`.
 
**Method.**
- Build the complete hypothesis space: every combination of attribute values or `?`, plus the all-reject hypothesis `('0', '0', '0', '0', '0', '0')`. This gives 973 hypotheses.
- Go through the examples one by one and keep a hypothesis only if it classifies the example correctly: it must cover positive examples and must not cover negative ones.
**Result.** The version space shrinks from 973 to 1 hypothesis:
 
```
('Sunny', 'Warm', '?', '?', '?', '?')
```
 
The specific boundary and the general boundary meet at this single hypothesis, so the training data identifies the target concept exactly. This is the same hypothesis Find-S found in Question 4.
 
---
 
## Project structure
 
```
.
├── README.md
├── YOUR_NOTEBOOK.ipynb
├── advertising.csv
├── enjoy_sport.csv
└── dice_histograms/
    ├── rolls_1_dice.png
    ├── rolls_2_dice.png
    └── ...                 (one image per dice count, up to 50)
```
 
