[![zread](https://img.shields.io/badge/Ask_Zread-_.svg?style=flat&color=00b0aa&labelColor=000000&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB3aWR0aD0iMTYiIGhlaWdodD0iMTYiIHZpZXdCb3g9IjAgMCAxNiAxNiIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KPHBhdGggZD0iTTQuOTYxNTYgMS42MDAxSDIuMjQxNTZDMS44ODgxIDEuNjAwMSAxLjYwMTU2IDEuODg2NjQgMS42MDE1NiAyLjI0MDFWNC45NjAxQzEuNjAxNTYgNS4zMTM1NiAxLjg4ODEgNS42MDAxIDIuMjQxNTYgNS42MDAxSDQuOTYxNTZDNS4zMTUwMiA1LjYwMDEgNS42MDE1NiA1LjMxMzU2IDUuNjAxNTYgNC45NjAxVjIuMjQwMUM1LjYwMTU2IDEuODg2NjQgNS4zMTUwMiAxLjYwMDEgNC45NjE1NiAxLjYwMDFaIiBmaWxsPSIjZmZmIi8%2BCjxwYXRoIGQ9Ik00Ljk2MTU2IDEwLjM5OTlIMi4yNDE1NkMxLjg4ODEgMTAuMzk5OSAxLjYwMTU2IDEwLjY4NjQgMS42MDE1NiAxMS4wMzk5VjEzLjc1OTlDMS42MDE1NiAxNC4xMTM0IDEuODg4MSAxNC4zOTk5IDIuMjQxNTYgMTQuMzk5OUg0Ljk2MTU2QzUuMzE1MDIgMTQuMzk5OSA1LjYwMTU2IDE0LjExMzQgNS42MDE1NiAxMy43NTk5VjExLjAzOTlDNS42MDE1NiAxMC42ODY0IDUuMzE1MDIgMTAuMzk5OSA0Ljk2MTU2IDEwLjM5OTlaIiBmaWxsPSIjZmZmIi8%2BCjxwYXRoIGQ9Ik0xMy43NTg0IDEuNjAwMUgxMS4wMzg0QzEwLjY4NSAxLjYwMDEgMTAuMzk4NCAxLjg4NjY0IDEwLjM5ODQgMi4yNDAxVjQuOTYwMUMxMC4zOTg0IDUuMzEzNTYgMTAuNjg1IDUuNjAwMSAxMS4wMzg0IDUuNjAwMUgxMy43NTg0QzE0LjExMTkgNS42MDAxIDE0LjM5ODQgNS4zMTM1NiAxNC4zOTg0IDQuOTYwMVYyLjI0MDFDMTQuMzk4NCAxLjg4NjY0IDE0LjExMTkgMS42MDAxIDEzLjc1ODQgMS42MDAxWiIgZmlsbD0iI2ZmZiIvPgo8cGF0aCBkPSJNNCAxMkwxMiA0TDQgMTJaIiBmaWxsPSIjZmZmIi8%2BCjxwYXRoIGQ9Ik00IDEyTDEyIDQiIHN0cm9rZT0iI2ZmZiIgc3Ryb2tlLXdpZHRoPSIxLjUiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIvPgo8L3N2Zz4K&logoColor=ffffff)](https://zread.ai/kathoffman/steroids-trial-emulation)
<h1>Steroids Target Trial Emulation Tutorial</h1>

This repository was created to help other analysts run analyses similar to [*Comparison of a Target Trial Emulation Framework to Cox Regression to Estimate the Effect of Corticosteroids on COVID-19 Mortality*](https://www.medrxiv.org/content/10.1101/2022.05.27.22275037v3) (Hoffman et al. 2022, *JAMA Network Open*).

This research was presented at the American Causal Inference Conference on May 24, 2022. The slide deck is available [[here]](presentations/hoffman_acic_slides.pdf).

<h2>Code Contents</h2>

- [`analysis.R`](code/analysis.R): a script to run a similar analysis (pared to improve computational time) with simulated data of n=500 patients

- [`report_results.R`](code/report_results.R): a script to clean the output of `analysis.R`

- [`trt_timeline_viz.R`](code/report_results.R): a script to create a patient treatment timeline similar to Supplemental Figure 1

- [`forest_plot_viz.R`](code/report_results.R): a script to create the forest plot of model first results (Figure 3)

<h2>Demo Data</h2>

The primary analysis is run using the open source `R` package [`lmtp`](https://github.com/nt-williams/lmtp). Please note we use the `sl3`-compatible branch to improve computational speed. We provide demo data in the `data` folder in combination with this visual representation of the required data format:

![](/img/analytical_file.png)

The required data structure for a longitudinal time-to-event analysis is wide (one row per subject), with one column per time point per variable (treatment, censoring indicator, outcome indicator, time-varying covariate). The exception to this is baseline variables, which by definition do not have multiple time points.

A few notes to help with pre-processing:

- Subjects should by default have a "censoring" indicator of `1` to indicate they are *observed at the next time point*. If lost to follow up, this indicator becomes `0`. The censoring indicator should be `1` if the subject experiences the event at the next time point, and `NA` for the following time points.

- If a patient experiences the event, their outcome variables should be `1` for all time points until the end of the study.

- If a patient has a censoring indicator of `0` (meaning they are lost to follow-up starting at the next time point), all columns corresponding to those future time points should have values of `NA`.

<h2>Analysis Specifications</h2>

<h3>Super learner libraries</h3>

The code to make super learner libraries (via `sl3`) used in the paper's analysis is in `analysis.R`, however, all but LASSO and mean are commented out to improve computational time. Learners were the same for intervention and outcome mechanisms. We specified 10 folds for superlearner cross-validation. This is set to a value of `.SL_folds=5` in our demo analysis code for computational time purposes.

<h3>Time-dependent confounding assumption</h3>

We used a Markov assumption of 2, meaning a patient's time-dependent confounders for the previous two time periods (48 hour windows) were sufficient to capture confounding for the next time point's mechanism. This was a decision stemming from clinical knowledge (laboratory results are ordered in  24 or 48 hour intervals). This is set to a value of `k=1` in our demo analysis code for computational time purposes.

<h3>Cross-fitting</h3>

We employed 10-fold cross-fitting on our SDR estimator. This is set to a value of `folds=5` in our demo analysis code for computational time purposes.

# Figures

## Figure 1: Hypothetical intervention

I've made this figure publicly available on a Google Slide deck [[here]](https://docs.google.com/presentation/d/18TpwcHzPrygb_4Wvm8saZwvJXE8ws4PqzQcSfQJg4Ak/edit#slide=id.g11b42e0cbf6_0_87). Anyone is free to edit as they see fit for their own papers and educational materials. To edit this read-only slide, click File --> Save a copy and edit off your duplicated copy.

<center><img src="/img/figure-1.jpg">.</center>

## Figure 2: Directed acyclic graph (DAG)

I've made this figure publicly available on a Google Slide deck [[here]](https://docs.google.com/presentation/d/18TpwcHzPrygb_4Wvm8saZwvJXE8ws4PqzQcSfQJg4Ak/edit#slide=id.g11b42e0cbf6_0_87). Anyone is free to edit as they see fit for their own papers and educational materials. To edit this read-only slide, click File --> Save a copy and edit off your duplicated copy.

<center><img src="/img/figure-2.jpg">.</center>

## Figure 3

Code to recreate this figure is in [`forest_plot_viz.R`](code/forest_plot_viz.R).

<center><img src="/img/figure-3.jpg".</center>

## e-Figure 1: Treatment timelines

A figure in the Supplemental Materials shows a random sample of 50 patients' treatment timelines. A blog post to aid other analysts in creating their own treatment timelines can be found [here](https://www.khstats.com/blog/trt-timelines/multiple-vars/).

<center><img src="/img/timeline.png">.</center>

## e-Figure 2: Data analytic file

This figure (shown above) under Demo Data is publicly available on a Google Slide deck [[here]](https://docs.google.com/presentation/d/18TpwcHzPrygb_4Wvm8saZwvJXE8ws4PqzQcSfQJg4Ak/edit#slide=id.g11b42e0cbf6_0_87). Anyone is free to edit as they see fit for their own papers and educational materials. To edit this read-only slide, click File --> Save a copy and edit off your duplicated copy.

## References

