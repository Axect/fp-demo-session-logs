## Evidence review: `20261031_pbh_photon_ceiling_gbp/report.md`

I edited nothing. All reruns ran on a copy in `/tmp/ev_review`.

### Critical: the report is built on superseded COMPTEL data
**Where:** TL;DR, §3.3, §4.1, §4.3, §5, §6.1, §6.2 Q1, App. B, Figs 1–4, the manifest captions and all CSVs.

**Evidence:**
- `data/isotropic_backgrounds.csv` changed at 03:42. That is after `work/S_matrix_K*.npz` (03:31) and after `ceiling_1e16.py` (03:19).
- `work/error_provenance.md`, section "Applied 2026-10-31 (user decision)", records why: all 9 COMPTEL rows were replaced with Kappadath thesis Table VII.2 values. The effective-area systematic was moved into `norm_frac` = 0.15.
- COMPTEL 6–9 MeV now reads I = 2.88e-5 and σ = 1.82e-5, so the ceiling is 6.52e-5. The report still uses 4.22e-5, 1.30e-5 and 6.82e-5.
- Running `ceiling_1e16.py` now gives **f_max = 2.903e-4**, not 3.04e-4. COMPTEL 6–9 MeV still binds.
- `emf_gbp.py` and `export_report_data.py` still output 3.0377e-4 because `build_S()` loads the cached `C` array without checking whether the input data changed. I diffed the cached `C` against the current `rows()`: 9 of 93 ceilings differ, and they are exactly the COMPTEL rows.
- **The shipped CSVs mix old and new data.** The `data` rows carry the new I and σ, but the `ceiling` and `bin_model` columns use the old values. Figs 1 and 4 therefore draw new error bars against old ceiling dashes. For example, the 0.8–1.2 MeV dash is at 9.89e-3, while the new point plus 2σ is 1.25e-2.
- The report's text contradicts the provenance file:
  - §3.3 table says "Kappadath 1998 thesis (table not retrievable)" and "unverified; …exact quadrature sums".
  - §6.1 says σ "could not be traced to the Kappadath (1998) table".
  - §6.2 Q1 says "The thesis server was unreachable".
  - App. B says "the Kappadath thesis was unreachable".
  - The provenance file says the thesis was retrieved and read, that the old CSV matched 0 of 9 thesis rows, and that it was then replaced.
- `emf_gbp.py` compares against the hardcoded literal `"(fmax(): 3.038e-04)"`, so this check cannot detect the drift.

**Fix:**
- Delete `work/S_matrix_*.npz`. Make the cache key include a sha256 of both inputs, or always rebuild `C` from `rows()`.
- Remove the hardcoded literal from `emf_gbp.py`.
- Rerun `emf_gbp.py`, then `export_report_data.py`, then the four plot scripts.
- Rewrite every COMPTEL-dependent number and the provenance prose. The corrected values from my fresh rerun:

| Quantity | Report | Fresh (current data, no cache) |
|---|---|---|
| mono f_max | 3.04e-4 | **2.903e-4** |
| COMPTEL 6–9 MeV: I, σ, ceiling | 4.22e-5, 1.30e-5, 6.82e-5 | 2.88e-5, 1.82e-5, 6.52e-5 |
| Galactic / extragalactic signal at f_max | 5.53e-5 / 1.29e-5 | 5.28e-5 / 1.24e-5 (split still 81 % / 19 %) |
| 15 % normalisation added in quadrature | 3.17e-4 | 2.948e-4 (+1.6 %) |
| narrow GB2, a = 1000 | 3.040e-4 | 2.905e-4 (still 0.09 % off mono) |
| (5,1,1) | 1.87e-4 | 1.947e-4 |
| (10,1,1) | 2.80e-4 | 2.679e-4 |
| largest tightening, mono / (2,0.5,2) | 1.6e3 | **1.5e3** |
| mono / (3,1,1) and mono / (1,1,1) | ≈10 and ≈10³ | 9.4 and 911 |
| (2,2,0.5) relative to mono | "slightly weaker" | 1.61× weaker (drop "slightly") |
| COMPTEL per-instrument f for (1,1,1) / (2,2,0.5) | 4.17e-6 / 5.89e-4 | 3.23e-6 / 6.12e-4 |

These do not change: the Fermi-LAT and EGRET-bound rows (1,1,1), (2,1,1), (3,1,1), (2,0.5,2), (2,2,0.5); their 90 % signal intervals; the binding bins; and the K_att and gap sensitivities.

**§5 also needs rewriting.** Its stat-only paragraph is moot now: thesis stat = 1.32e-5 gives a stat-only ceiling of 5.52e-5. The new σ already excludes the effective-area systematic, and `norm_frac` is now meaningful.

### High
1. **Equation in §3.1 contradicts the text after it.** The displayed I_eg includes $e^{-\tau(E,z)}$ and defines K. The next paragraph says K excludes attenuation and `K_att` includes it. Fix: drop $e^{-\tau}$ from the definition of K and define K_att separately.
2. **Figure 2 does not show "all 93 bins".** The claim appears in §4.2 ("Figure 2 shows the full set of 93 bin-wise ceilings"), the alt text and the manifest. But `xlim=(2e-3, 1e3)` hides 24 bins (5 EGRET, 19 Fermi-LAT above 1 GeV) in every panel. In panel (a), a further 12 bins sit above 1e9 and 25 have f_b = ∞. The caption mentions only "Fermi-LAT bins lie above 1e9". Fix: extend xlim to 1e6 MeV, or state "69 of 93 bins shown; >1 GeV bins have f_b ≥ 2.0e-6 (1,1,1) and 3.1e-4 (3,1,1)". Mark the infinite bins.
3. **Gap sensitivity is understated.** The report says "at most 8.7 %" (Fig 3 caption, §6.1, App. A, manifest). The table's own (2,0.5,2) row gives 2.107/1.937 = 8.8 %. The Fig 3(b) scan CSV peaks at **8.9 % at a = 1.34**. Fix: say "≤ 8.9 %".
4. **Fig 3(b) cannot show the effect its caption reports.** An 8.9 % change is invisible on a four-decade log axis, so the dashed curve sits on top of the solid one. Fix: add a ratio sub-panel, f_gap-removed / f_max.

### Medium: orphaned claims (no script prints them)
I checked each by hand and all are correct, but the report has no evidence trail for them:
- 0.019 % analytic vs trapezoid bin average (I get −0.0189 %)
- 3.17e-4 with the 15 % normalisation added (I get 3.167e-4 on the old data)
- 3.1 % and 0.23 % mass in the 90 % intervals (I get 3.14 % and 0.225 %)
- present mass across the gap running from 0 to 1.7e14 g (I get 1.735e14)
- E²Q peak at 6.8 MeV (I get 6.81)
- omitted mass fraction 1.5e-3 (`emf_gbp` prints only `1e-03`; I get 1.50e-3)
- the 4.2 runner-up claims and the 18 % and 48–52 % Galactic shares (derivable from the CSVs only)

Fix: print each from `emf_gbp.py` or `ceiling_1e16.py`.

Two further claims say more than the evidence supports:
- **"a ≳ 4.5"** (§4.3). In the scan, COMPTEL binds from a ≈ 3.8–4.0 and the factor-2 point is at a ≈ 4.2 (old data) or ≈ 4.0 (new data). Use "a ≳ 4".
- **"HEAO-1's 0.0153 is below every all-sky or high-latitude average"** (§6.1). Only two NFW parameter sets were tested. Say "both NFW variants tested".

### Medium: small number mismatches
- §6.1 NFW |b| > 20° range: the report says 0.0267–0.0272; `check_units.py` prints 2.661e-2 and 2.711e-2, so 0.0266–0.0271.
- §3.3: HEAO "only sees the redshifted low-energy tail of the extragalactic emission". Its tightest bin (0.23–0.30 MeV) is 70 % extragalactic and **30 % Galactic**.
- §4.1: "a missing 4π would change the answer by a factor of 12". 4π = 12.6, so write "≈ 12.6".
- App. A: "each step under 15 s". `check_units.py` took 25.5 s.

### Medium: Fig 4 smooth curves don't match its bin steps
`export_report_data.case()` builds the GB2 `eg`/`ga` curves by trapezoid on the coarse 1000-point grid. P_b uses the 8×-refined log-interpolated S. For (1,1,1) and (2,0.5,2), the bin average of the plotted smooth curve exceeds the plotted binding-bin step by 2.6 %; for the other cases the gap is 0.1–0.3 %. Fix: compute the curves on the same refined grid.

### Physics and dimension check of §3 equations
**Correct:**
- I_eg, including the per-steradian bookkeeping: $n_0 \int dz/H\, dN/dE'dt$ follows from $dt = dz/[(1+z)H]$ and $dE'/dE = 1+z$.
- K in cm MeV⁻¹ s⁻¹ sr⁻¹.
- I_gal, with 𝒟 in g cm⁻²; it matches NFW ⟨∫ρ dl⟩ without a 1/4π.
- I₁ˣ, the bin average, C_b, f_b and P_b.
- ψ_ln normalisation (u = x^a gives a Beta integral), tails x^{ap} and x^{−aq}, mode M_c(p/q)^{1/a}, median for p = q.
- Width π/(a√3), giving 0.79, 0.26 and 0.079 decades.
- Mass fraction below M_*: 4.95 %, 1.41e-4, 1.47e-13.
- Generalized gamma limit.
- I_eg[ψ] and I_gal[ψ], with dn/dM = fρψ/M.
- T_H = 1.06 MeV at 1e16 g.
- ρ_DM = 2.254e-30 g cm⁻³.

**Defects:**
- **Emission formula:** $g_i/2\pi$ needs $1/(2\pi\hbar)$ to give MeV⁻¹ s⁻¹. T_H is written in SI form with k_B but used as an energy in $e^{E/T_H}$. The sum over "dof" with a Bose factor applies only to the photon. Fix: $(g_\gamma/2\pi\hbar)\,\Gamma_\gamma/(e^{E/k_BT_H}-1)$ and $k_BT_H \approx 1.06$ MeV.
- **Symbol collisions:** τ means both optical depth (§3.1) and lifetime (§3.2). ρ_DM means both the cosmic mean and the halo profile ρ_DM(r). Rename to τ_life and ρ_halo(r).
- **Redshift explanation:** "dilutes as (1+z)³, energy interval stretches by (1+z)" does not say these cancel against the comoving density and the dt/dz Jacobian. Add one clause.
- **ρ_DM definition:** "0.120 ρ_crit h⁻²" is ambiguous. Write $\Omega_c h^2 \times \rho_{\rm crit}/h^2 = 0.120 \times 1.878\times10^{-29}$.
- **z_max** is never defined.

### Captions and visual encoding
- **Fig 1 caption:**
  - It lacks the 81/19 split, which the manifest caption has.
  - It never says the error bars are 1σ while the dashes are 2σ.
  - It does not mention that xlim = 1e3 MeV hides all bins above 1 GeV and both upper-limit rows, even though the script draws upper limits.
  - Fig 1 is never cited by number in the body.
- **Fig 4 caption:** panel (c), (3,1,1) binding at EGRET 30–50 MeV, is not described.
- **Fig 3(a):** the y-label is "ψ(ln M_i)" while the text uses ψ_ln. The legend covers the lower-centre region where the tails run.
- **No figure for the mechanism claim** (§4.3 table, last column). A cumulative binding-signal vs M_i plot, for (1,1,1) and (2,2,0.5), would make it a figure-backed claim.

### Reproducibility and style
**What passes:**
- The manifest has `source_script` and `source_context` for all 4 plots, and `dpi: 300`.
- All four scripts import `_plot_style`, call `apply_style()` and `save_figure()`.
- PNGs are 300 dpi (checked via PIL) and PDFs exist.
- Each script reads only its CSV. All four regenerated cleanly from fresh CSVs in /tmp.

**What fails:**
- The manifest records no hash of the input data, which is why the data drift went undetected. Add sha256 of both data files.
- App. A's reproduction recipe silently reproduces the stale numbers unless the cache is cleared first.
- 13 per-script `fontsize=` overrides (4.2–5.5 pt in text and legends) defeat the style's `legend.fontsize=7`. Remove them, or raise them to at least 6 pt.
- `binwise_fb.py` imports `FIGSIZE_DOUBLE` but uses a literal (7.2, 2.5); `emf_spectra.py` also uses a literal.

### Math rendering
- No non-ASCII characters in `report.md`, and no single-line `$$…$$`; all display blocks are multi-line. Pass.
- The `<!-- EVIDENCE BLOCK: ev-1/ev-2 -->` markers are leftovers, and there is none for §4.3. Remove them.

### Numbers verified, and how
- **By running the scripts:** grid index 333; lifetime 1.253e14 yr; m_now/M_i = 0.99996; per-instrument f (HEAO 0.318, EGRET 1.11e4, Fermi-LAT 2.36e30); 81/19 split; K_att Δ < 0.35 %; MacGibbon 6.408e-8 vs 6.612e-8 (ratio 1.032, all-sky 0.082); 4πK_rebuilt/K = 0.982–1.003; refine 8 vs 32 (3.18687e-7 vs 3.18812e-7); M_* = 5.21e14 between nodes 5.149e14 and 5.257e14; the whole GB2 table for the non-COMPTEL rows.
- **From the CSVs:** runner-ups (9 bins, EGRET 100–150 at +3.1 %; 8 bins, EGRET 70–100 at +4.2 %); HEAO ≥ 3 decades above the minimum (1048×); Galactic share 18 % at 30 MeV and 48–52 % at 100–300 MeV; hard peak at 108 MeV; scan transitions and gap maximum.
- **Analytically:** all GB2 width, tail and fraction numbers; 1.5e-3 omitted mass; 3.14 % and 0.225 %; 2.4e3 ratio; ρ_DM; T_H; √(0.778² + 1.045²) = 1.30; 97.725 % coverage; 0.019 %; the 3.17e-4 quadrature value.

## Decisions
1. Which COMPTEL data should the report use: rebuild everything on the current thesis Table VII.2 rows (mono f_max = 2.903e-4) / restore the old CSV rows and keep 3.04e-4 (this contradicts the recorded decision in `work/error_provenance.md`)