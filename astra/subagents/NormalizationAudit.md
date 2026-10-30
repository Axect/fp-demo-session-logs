**Verdict:** C6 and C7 both need rewording before they can be accepted. The arithmetic checks out, but neither claim proves what it says. I ran two short numpy checks on `forward_model.npz` (grid row k=333) and did not re-run `check_units.py`, so the K-ratio and NFW column numbers in the report are not reproduced.

**C6 — the main logical problem (P1, confidence 0.85)**

`report_mono_1e16.md:15-17` says the K rebuild "matches cached K up to a constant 1/(4 pi)", and concludes "Hence Q_now is per steradian".

- **The K ratio cannot decide the question.** Two explanations predict the same thing. In the first, Q_now is per steradian and K is correct. In the second, Q_now is total emission over 4π and K is missing a 1/(4π). Both give cached K = c∫n_com·Q(E(1+z))/H dz. The redshift formula in `check_units.py:27-32` is correct, so the ratio only checks that K and Q agree with each other.
- **They come from the same file.** K and Q_now are read from one parquet (`prepare_data.py:4,34`), so their agreement shows the pipeline is self-consistent, not that its units are right.
- **The report doesn't say which way the ratio goes.** It doesn't state whether K_rebuilt/K_cached is 1/(4π) or 4π, and that direction is what the inference depends on.
- **J doesn't decide it either.** Cached J ≈ ∫ρ dl with no 1/4π is also expected under both explanations, as long as the same convention was used throughout. If the second one is true, both terms are 4π too large. The ceiling would then be 4π × 3.04e-4 ≈ 3.8e-3, and the 19 %/81 % extragalactic/Galactic split would not change. Brief question 2 expects only the extragalactic share to move; that is not what happens.
- **The only thing that tells them apart is the absolute normalization**, i.e. the photon power share. I reproduced it: 4π∫E·Q dE / |dM/dt| = 0.0783 with M = 1e16, |dM/dt| = 8.45e-7 g/s, E in MeV and 1.78266e-27 g/MeV. Without the 4π it is 0.0062. No script contains this check; `check_units.py` only does the K ratio and the NFW column.
- **Which share is physical is not settled by the files.** Saying a photon share of ~8 % is right for T_H ≈ 1 MeV (photons, neutrinos and e± all emitted) is an [INFERENCE] from published Hawking-emission fractions. Nothing in the allowed sources supports it. The units of `dmdt_L` (g/s) are also assumed; `prepare_data.py:6` only says "|dM/dt|(M)".

Replacement sentences for C6:
"C6. Units: K rebuilt from Q_now assuming Q_now is total emission over 4π gives K_rebuilt/K_cached = [state value, 1/(4π) or 4π] at 0.01, 0.1, 1, 3, 7 and 20 MeV. This ratio is consistent with either Q_now per steradian with K correct, or Q_now over 4π with K missing 1/(4π), so on its own it does not fix the angular convention. The deciding check is the absolute normalization: 4π∫E·Q_now dE = 7.8 % of |dM/dt| (0.62 % without the 4π), assuming dmdt_L is in g/s. Taking an ~8 % photon power share at T_H ≈ 1.06 MeV as the physical value, from the external Hawking-emission literature (not verified here), Q_now is per steradian and J must be ⟨∫ρ dl⟩ without 1/(4π). If Q_now were instead total emission, f_max would rise by 4π to ≈ 3.8e-3."

**C7 — what the NFW comparison can and can't show (P2, confidence 0.8)**

- **Fine as a test of the 4π factor.** The cached J values (0.0263–0.0286) are about ∫ρ dl, not ∫ρ dl/(4π) ≈ 0.002, so the comparison does separate the two by ~12.6×.
- **Too loose to confirm the sky regions.** The masks come from `region_columns()` in `egal_mono_constraints.py` (`prepare_data.py:8,23,37`), which is not among the allowed sources. `_common.py:2` points to a different path for that file (`scripts/fine_benchmark/`). COMPTEL (0.0263) sits below the |b|>20° range (0.0266–0.0271) and EGRET (0.0286) sits between the two ranges, so these are ~5 % matches to unknown regions.
- **HEAO's J can't come from any NFW sky average with the cached ρ_local.** ρ_local is 0.300 GeV cm⁻³. The column toward the anticentre — the lowest of any direction for a centred NFW — is 0.0143 (rs=20, Rsun=8.5) to 0.0152 (rs=24, Rsun=8.33) g cm⁻². Any average over a finite region is higher. HEAO's 0.0153 is therefore about one anticentre line of sight, or uses a different profile, normalization or truncation. Neither a |b| cut nor the full sky can produce it.
- **Effect on f_max: none.** HEAO's limit (0.318) is ~1000× weaker than COMPTEL's, so f_max = 3.04e-4 is unchanged. The report should still say the HEAO value is unexplained.
- **Small inconsistency:** `check_units.py:5` describes the column as "(1/4pi) Int rho dl [g cm^-2 sr^-1]", but the claim compares J with the column without 1/4π.

Replacement sentences for C7:
"C7. With cached ρ_local = 0.300 GeV cm⁻³, NFW (rs 20–24 kpc, Rsun 8.33–8.5 kpc) gives ⟨∫ρ dl⟩ = 0.0266–0.0271 g cm⁻² for |b| > 20° and 0.0295–0.0298 over the full sky. The cached J for COMPTEL (0.0263), EGRET (0.0286) and Fermi-LAT (0.0273) agree within ~5 %, which confirms that J carries no 1/(4π), but the instrument regions in region_columns() were not inspected. HEAO's J = 0.0153 is at the NFW minimum over all directions (anticentre column 0.0143–0.0152), so no |b| cut can produce it; it is unexplained, but it does not affect f_max because HEAO does not set the limit."

**Provenance (P3, confidence 0.6)**

`prepare_data.py:1-3` says the npz was built on collider, which has been flagged compromised since 2026-10-20. The script writes no hash or manifest, and J and ρ_local come from code outside the allowed sources. In C6 and C7, I can only show that the cached numbers are arithmetically consistent, not that they are physically right.