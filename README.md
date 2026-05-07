# U.S. Flight Delays and Cancellations (2010–2019)
## by Thomas

## Dataset

The analysis uses the **Reporting Carrier On-Time Performance** dataset published by the U.S. Bureau of Transportation Statistics (BTS). The file `airline_2m.csv` is a random sample of **2,000,000 domestic U.S. flights between 1987 and 2020** with 109 columns covering scheduled and actual times, delay reasons, cancellations, diversions, distance and origin/destination airports.

* Source: BTS *Reporting Carrier On-Time Performance* database
* Cancellation-code mapping (A/B/C/D) taken from BTS *Number 14 – On-Time Reporting*, section IX
* Working scope: restricted to the complete decade **2010–2019** (~644,000 flights, 22 reporting carriers)

### Wrangling steps performed before the exploration

1. Inspected the file header (109 columns) and a 10,000-row preview to identify near-empty columns (the `Div1…Div5` diverted-flight detail block is >95 % empty).
2. Loaded only **27 relevant columns** with `usecols=` to keep memory usage manageable.
3. Created a separate cleaned dataframe `df_clean` (best-practice: do not mutate the raw load) with:
   * `FlightDate` parsed to `datetime`
   * Binary flags (`Cancelled`, `Diverted`, `DepDel15`, `ArrDel15`) cast to `int` after filling NaN with 0
   * `DepHour` derived from the integer-encoded `CRSDepTime` (e.g. `1430` → `14`)
   * `MonthName` and `DayOfWeekName` as **ordered categoricals** for ordinal-correct plotting
   * `CancelReason` derived from `CancellationCode` using the official BTS code list
   * Restricted to 2010–2019 to drop the partial 1987 and the COVID-truncated 2020

## Summary of Findings

The exploration produced 18 visualisations across univariate (6), bivariate (7) and multivariate (5) views. The most important findings:

1. **Most flights are on time.** Only ~18 % of flights arrive ≥ 15 min late (the official BTS *delayed* threshold); the median flight even arrives 5 min early because carriers pad their schedules.
2. **The arrival-delay distribution is strongly right-skewed**, not log-normal: the mode sits at ~4–5 minutes with a secondary plateau at 15–30 min and a long tail that decays steadily up to several hours.
3. **Cancellations are rare** (~1.5 %). Among them, **weather** dominates (~45 %), followed by carrier-controlled and NAS reasons; security cancellations are negligible.
4. **Time of day is the single strongest predictor of delay.** The delay rate climbs almost monotonically from below 10 % for early-morning departures to more than 25 % for evening departures (delay-propagation effect).
5. **Seasonality is bimodal.** A summer peak in **June–July** (~22–23 %) and a winter peak in **December** (~21 %); September is the quietest month (~15 %).
6. **Carriers differ substantially.** Range is roughly 9 % (HA, a clear outlier) up to 24 % (B6). Low-cost / leisure carriers cluster at the top of the delay-share ranking; legacy carriers and AS / DL at the bottom.
7. **Distance barely affects the median delay** (always in the −4 to −6 min range across all distance groups), because long flights have padded schedules.
8. **Departure delay translates almost one-to-one into arrival delay** (Pearson r ≈ 0.95), with carriers absorbing only a few minutes en-route.
9. **Time-of-day and season amplify each other** (Plot 13 heat-map): summer or December evenings hit > 30 % delay rates, while early mornings stay below 10 % even in the worst months.
10. **Delay-cause composition** (Plot 14) shows that *late aircraft* and *carrier-controlled* causes account for ~65–75 % of all delay minutes. Weather dominates *cancellations* but not *delay minutes*.
11. **Yearly trends** (Plot 15) move synchronously across the top-6 carriers — system-wide factors (weather years, congestion) dominate over carrier-specific operational quality. 2014 was a particularly bad year, 2012 and 2016 notably good.
12. **Carrier ranking is a tail-risk story** (Plot 16). The boxplots show that the median experience is essentially identical across the top-6 carriers; the real difference shows up only in the **upper-tail quantiles** (e.g. P90: 25 min for DL vs. 51 min for EV).

## Key Insights for Presentation

The Part II slide deck distills the exploration into four polished slides + take-aways:

1. **Most flights are actually on time** — delay-distribution histogram with the BTS 15-min threshold and the 5-min-early median highlighted.
2. **The single best predictor is the time of day** — line chart of delay share by scheduled departure hour, with explicit best/worst markers.
3. **Time of day and season amplify each other** — month × hour heat-map (under-sampled night cells masked).
4. **Choosing an airline = choosing your tail risk** — paired box-plot + tail-quantile bar chart for the top-6 carriers, making explicit that medians are identical and tails are not.

The take-aways slide condenses these into one practical sentence:

> *Fly in the morning, avoid summer and December evenings, and care more about your carrier's tail behaviour than its average performance.*

### Notable design changes from exploration → presentation

* The exploratory **violin plot** (Part I, Plot 16) was replaced by a **box-plot + tail-quantile bar chart** because the long-tailed distribution made the violin shapes visually indistinguishable.
* The exploratory **month × hour heat-map** kept the BTS night-time cells (00–04 h) masked because their tiny sample size produced misleading dark-red artefacts.
* All Part II plots use a consistent visual language (semantic colours: red = bad, green = good; "k" formatter on count axes; bold titles).

## Files

| Path | Description |
|---|---|
| `Project Files/Part_I_exploration_flight_delays.ipynb` | Part I exploration notebook (18 plots incl. bivariate boxplot & multivariate facet plot, full QVO structure) |
| `Project Files/Part_II_slide_deck_flight_delays.ipynb` | Part II explanatory slide deck (Jupyter source) |
| `Project Files/Part_II_slide_deck_flight_delays.slides.html` | Rendered slide deck (RISE/reveal.js HTML) |
| `README.md` | This file |
| `Data/airline_2m.csv` | Raw dataset (BTS, 2 M flights, 1987–2020). **Not tracked in git** (~880 MB, exceeds GitHub's 100 MB file-size limit). Download instructions below. |
| `background/eda-phases.md` | Generic background note on the four EDA phases (workflow draft for a future blog post; not part of the submission) |
| `requirements.txt` | Python dependencies for the virtual environment |
| `.gitignore` | Files excluded from version control (venv, dataset, course material, …) |

> *Note:* The original Udacity course material (`Instructions/`, `Example Project/`) is kept locally only and excluded from the public repository.

## Software / Packages

Python 3.13 in a local `.venv`, dependencies listed in `requirements.txt`:

* **numpy** — numerical arrays
* **pandas** — data wrangling
* **matplotlib** — base plotting
* **seaborn** — statistical visualisations
* **jupyter / notebook** — notebook environment and `nbconvert` for the slide-deck render

### Getting the raw dataset

The 2-million-row sample is too large for GitHub (≈ 880 MB) and is excluded via `.gitignore`.
Download `airline_2m.csv` from the official BTS *On-Time* archive (or the Kaggle mirror commonly used by the course) and place it under `Data/airline_2m.csv` before running the notebooks.

### Reproducing the analysis

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook "Project Files/Part_I_exploration_flight_delays.ipynb"
```

### Re-rendering the slide deck

```bash
source .venv/bin/activate
jupyter nbconvert "Project Files/Part_II_slide_deck_flight_delays.ipynb" \
    --to slides --no-input --no-prompt --execute
```

This produces `Part_II_slide_deck_flight_delays.slides.html` next to the notebook.
