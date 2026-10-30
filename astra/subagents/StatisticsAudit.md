I audited C5, C8 and C9 only, using code/_common.py, the report and the CSV. I ran nothing: every number below is hand arithmetic on cached CSV values. This checks that the arithmetic and the code reading are right; it is not a physical check of the error model.

## C5 (report lines 13–14): correct, one sub-claim not checked, one omission
- **Upper-limit bins:** `ceil = r["I"] if r["ul"] else r["I"] + cl * r["s"]` (_common.py:59). The two upper-limit rows are CSV:81 (Fermi 580–820 GeV) and CSV:94 (EGRET 20–50 GeV). Both have sigma_indep = 0 and every shared-error column = 0, so the ceiling equals I, as claimed.
- **Mixed confidence levels:** the CSV does not record what confidence level these upper limits were quoted at, and `cl` never applies to them. So `fmax(cl=…)` combines bins at different confidence levels. This does not matter at 10^16 g, because neither instrument binds there (EGRET f_max 1.11e4, Fermi 2.36e30).
- **Unused columns:** `norm`, `clo` and `chi` are parsed (lines 26–27) but never read in `fmax` (lines 51–64). The claim holds.
- **Not checked:** "K_att leaves f_max unchanged" needs a run. My review did not reproduce it.
- **Omission:** `operator()` leaves out the upper-limit bins (`not r["ul"]`, lines 82 and 90), but `fmax` includes them. This is a second difference between the two functions that C8 does not mention.

Replacement for the C5 upper-limit sentence:
> "Upper-limit bins (EGRET 20–50 GeV, Fermi 580–820 GeV; CSV sigma_indep = 0): ceiling = I at the quoted limit's unrecorded confidence level, unaffected by `cl`; fmax() includes them while operator() excludes them (_common.py:90). Neither bin binds at 1e16 g."

## C8 (lines 20–22): arithmetic right; "inconsistency" overstated; f_max is a bound, not an estimate
- **The ceiling checks out.** COMPTEL 6–9 MeV (CSV:52):
  - I = 4.217e-5 and σ_indep = 1.3028e-5.
  - The shared term is max(0, 0, 0.15·I) = 6.326e-6 (line 91).
  - σ_tot = hypot(1.3028e-5, 6.326e-6) = 1.448e-5.
  - I + 2σ_tot = 7.114e-5, and 3.04e-4 × 7.114/6.823 = 3.17e-4.
- **The direction is missed.** hypot(s, x) ≥ s in every bin, so applying the operator's error model to all bins can only raise each bin's ceiling. The binding bin can change but can never go above its own rescaled value. The full rerun therefore lands in [3.04e-4, 3.17e-4]. Writing "f_max ~ 3.17e-4" presents the upper end of that interval as the estimate.
- **"Inconsistency" is too strong.** The two functions have different jobs. `fmax` follows an external convention (docstring line 2 cites egal_mono_constraints.py). `operator` builds a whitened sensitivity matrix. What is actually wrong is narrower: `fmax` leaves out a shared systematic that is real and large (15 % for COMPTEL, 8 % for HEAO). That makes each bin's ceiling too tight, i.e. anti-conservative.

Replacement:
> "Error-model difference inside _common.py: fmax() uses sigma_indep only, omitting the shared normalization (COMPTEL 15 %, HEAO 8 %), which makes each bin's ceiling anti-conservative; operator() uses hypot(sigma_indep, max(corr_lo, corr_hi, norm_frac·I)) and drops upper-limit bins. Adding the shared term in quadrature raises every bin's ceiling, so the rerun f_max lies in [3.04e-4, 3.17e-4]; 3.17e-4 is the binding bin rescaled alone (ceiling 7.11e-5), an upper end, not a rerun."

## C9 (lines 23–25): the direction of each effect is missing, and 97.7 % is too high
- **Per-bin coverage is a floor, not ~97.7 %.** With no background subtracted, the true sky μ is at least f·p. So P(f_true ≤ f_b) ≥ P(I + 2σ ≥ μ) = 97.7 %, assuming the errors are Gaussian and σ_indep is the whole error.
- **With the normalization included, that floor drops.** In COMPTEL 6–9 MeV, 2σ_indep is only 2.606/1.448 = 1.80 σ_tot above I. One-sided coverage at 1.80σ is 96.4 %.
- **Taking the minimum over bins lowers coverage.** {f_true ≤ min_b f_b} is the intersection of the per-bin events, so its probability is at most any single bin's. The worst-case Bonferroni bound, 1 − 93 × 0.0228, is below zero, so it guarantees nothing. Only background-free slack keeps bins far from saturation from counting against coverage. The report's "no global CL" is correct but does not say which way the bias goes.
- **Correlations work in the opposite direction.** A common normalization correlates the COMPTEL bins positively. That shrinks the union of failures compared with independent bins. Ignoring correlations therefore overstates the look-elsewhere penalty, while leaving out the normalization variance understates each bin's error.
- **93 is not a count of independent bins.** HEAO has overlapping energy rows that look like two interleaved datasets. For example, CSV:23 covers 1.537e-5–1.665e-5 GeV and CSV:24 covers 1.569e-5–1.807e-5 GeV, with alternating σ of 5443 and 1724.

Replacement:
> "Statistical meaning: per-bin data + 2 sigma_indep ceiling with no background subtraction; with Gaussian errors each bin covers ≥97.7 % one-sided, but omitting COMPTEL's 15 % normalization puts the binding ceiling only 1.80 sigma_tot above I (≥96.4 %). The min over 93 rows, including overlapping HEAO rows, can only lower joint coverage below the per-bin level; positive shared-normalization correlation partly offsets this, and background-free slack protects unsaturated bins. Bonferroni gives no non-trivial global CL, so f_max is a heuristic ceiling, not a stated-CL limit; halo-profile uncertainty is excluded."

**Severity:**

| Claim | Severity | Issue |
|---|---|---|
| C8 | P2 | 3.17e-4 is presented as the estimate when it is the upper end of the interval |
| C9 | P2 | 97.7 % is stated where it is only a floor, and the min/correlation directions are missing |
| C5 | P3 | The operator's exclusion of upper-limit bins is not mentioned |