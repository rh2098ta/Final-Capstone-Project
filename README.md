# F1 Podium Probability — Final Capstone

> **One-sentence summary:** Predict a driver’s **pre-race** chance of a **podium (top-3)** using historical F1 data (grid position and recent driver/team form). Results are shown in a single, readable Jupyter Notebook for graders and nontechnical readers.

---

## 📌 What’s in this project
- **Notebook (start here):** [`notebooks/Final_Capstone.ipynb`](notebooks/Final_Capstone.ipynb)  
  (Runs end-to-end: data prep → EDA → models → evaluation → results)
- **Figures:** generated when you run the notebook (saved in `figures/`)
- **Data:** not committed (Kaggle link below)

---

## 🧠 Problem (nontechnical)
Teams and sponsors need realistic expectations before lights-out. I estimate each driver’s **pre-race** probability of finishing on the **podium** using only information known **before** the race (e.g., starting grid, recent driver/team results, season year).

**Business value:** better race planning and clearer sponsor communication about likely outcomes.

---

## 📊 Data
- **Source:** Kaggle — *Formula 1 World Championship (1950–2024)*  
  (Tables used: `races`, `results`, `drivers`, `constructors`.)

---

## 🛠️ What I built 
**Data prep & features**
- Joins across race/driver/team IDs; sorted by **driver → year → round → raceId**.
- Pre-race features only:
  - `grid`, `year`
  - `driver_starts_so_far`, `driver_podiums_so_far`, `driver_podium_rate_so_far`
  - `team_starts_so_far`, `team_podiums_so_far`, `team_podium_rate_so_far`
  - “So-far” values computed with `shift(1)` + cumulative counts (prevents leakage).

**EDA visuals**
- Podium rate vs starting grid (`figures/podium_rate_vs_grid.png`)
- Grid histogram + podium-rate subplot (`figures/grid_hist_and_rate.png`)
- Podium rate by constructor (Top-10) (`figures/podium_by_constructor_top10.png`)

**Models & evaluation**
- Models: **Logistic Regression** (baseline) and **Decision Tree** 
- **GroupKFold (5-fold) by season** + **tiny GridSearch** for each model
- **Time-aware split:** train on earlier seasons; test on later seasons
- Metrics:
  - **ROC-AUC** (primary; handles class imbalance; measures ranking quality)
  - **Accuracy** (secondary; shown for completeness)
  - **Top-3 per-race hit-rate** (stakeholder metric: of the model’s top-3 picks per race, how many actually podiumed?)

---

## ✅ Results 


Best model Decision Tree
ROC-AUC: 0.926
Accuracy .897
Top-3 per-race hit-rate 68.2%

**Analysis:** AUC shows how well the model ranks true podium finishers above others (0.50 = guessing, 1.00 = perfect). Accuracy can look high with few podiums, so I also report a Top-3 per-race metric that mirrors the real decision: pick three likely podium contenders for each race.

---

## 🔍 Key findings (nontechnical)
- **Starting position matters:** podium probability declines steadily as grid position number increases.  
- **Momentum matters:** drivers/teams with stronger **pre-race** podium rates are more likely to podium again.  
- **Useful ranking:** the model’s **top-3** picks per race capture a meaningful share of actual podium finishers.

---

## 🧽 Data cleaning decisions
- Sorted rows by **driver → year → round → raceId** so “so-far” stats use **past only** (no leakage).
- Engineered only **pre-race** features listed above.
- Kept **grid = 0** rows for modeling (pit-lane/unknown starts); dropped only from the grid plot.
- Checked key features for missing values 
- Did not remove outliers; extremes reflect real racing.

---

## ▶️ How to run
**Google Colab (recommended)**
1. Open the notebook: [`notebooks/Final_Capstone.ipynb`](notebooks/Final_Capstone.ipynb)  
2. Upload or mount the Kaggle dataset as used in the notebook.  
3. Run top-to-bottom; figures will be saved into `figures/`.

