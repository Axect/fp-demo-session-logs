I could state the report's core idea in one sentence after Section 2, but the headline numbers no longer match the data file now on disk. That data problem comes first; the clarity and physics issues follow by severity.

**Core idea as I understood it:** The predicted photon flux is linear in the abundance f, so each detector bin allows at most f_b = (measured intensity + 2σ) / (signal per unit f). The smallest f_b is the ceiling, and for a mass function you sum the signal over masses before dividing, rather than combining the single-mass ceilings.

## A. Critical

**A1. The data file disagrees with every COMPTEL-driven number in the report (§TL;DR, §4.1, §5, §6.1).**
- **Text:** "f_max = 3.04 × 10⁻⁴ … COMPTEL 6-9 MeV ceiling of 6.82 × 10⁻⁵ (data 4.22 × 10⁻⁵, σ = 1.30 × 10⁻⁵)". Also §6.1: "Its value is exactly √(0.778² + 1.045²) × 10⁻⁵".
- **What I found:** `data/isotropic_backgrounds.csv` (last modified 03:42) has, for COMPTEL 6-9 MeV, intensity 0.0288 and sigma_indep 0.018197. The loader divides by 1e3, so these are I = 2.88e-5 and σ = 1.82e-5. The file is newer than `work/ceiling_1e16.py` (03:19) but older than `report.md` (03:57).
- **Rerun:** `python3 work/ceiling_1e16.py` now gives f_max = 2.903e-4. The binding bin is still COMPTEL 6-9 MeV and the split is still 81 % Galactic / 19 % extragalactic, but the ceiling becomes 6.519e-5.
- **Plot data:** `plots/data/spectrum_mono_1e16.csv` contradicts itself. Its 6-9 MeV row carries the new I and σ but the old ceiling 6.818e-5, which is not I + 2σ.
- **Consequences:**
  - The claim that σ is a quadrature total fails, because 1.8197 ≠ 1.303.
  - The §5 "statistical-only ceiling 5.77 × 10⁻⁵" no longer follows.
  - §4.1 says the number "can be trusted as arithmetic", yet it does not reproduce from the inputs on disk.
- **Fix:** Find out who changed the CSV and why. Then regenerate everything downstream: the baseline, the GB2 rows that bind at COMPTEL ((5,1,1) and (10,1,1)), all plot CSVs, and every quoted number. Re-check the quadrature claim against whichever CSV is final. I sent EvidenceReview a note about this.

## B. Physics and formula issues (item 7)

**B1. §3.1 extragalactic flux: the prose contradicts the formula.**
- **Text:** "The number density dilutes as (1+z)³, and the energy interval stretches by (1+z). Integrating over the emission history gives I_eg = (c/4π) n₀ ∫ dz/H(z) …"
- **Problem:** Neither factor appears in the formula. A collaborator who follows the sentence would insert (1+z)^{-3}·(1+z) and get it wrong. The formula is right for a comoving n₀, but only because the factors cancel:
  - Comoving emission plus dilution of the photons gives no net (1+z)³.
  - The energy stretch dE′/dE = (1+z) cancels the 1/(1+z) in dt = dz/[(1+z)H].
- **Fix:** Write the one-line derivation: dn_γ/dE = ∫ dt n₀ (dN/dE′)(dE′/dE), with dt = −dz/[(1+z)H]. Then say explicitly that the comoving n₀ makes the (1+z)³ factors cancel.

**B2. §3.1: z_max, H(z) and the cosmology are never given.** Only ρ_DM uses Planck parameters. A collaborator cannot rebuild K without H₀, Ω_m, Ω_Λ (Ω_r?), z_max, and the definition of m(z) once a hole has evaporated (m = 0, so no emission). **Fix:** List the cosmology and z_max used to build `K`, or state that they are unknown properties of the cache.

**B3. The symbol τ means two different things.** It is the optical depth τ(E, z) in §3.1 and the lifetime τ(M) in §3.2. **Fix:** Rename the lifetime to t_ev(M).

**B4. The symbol ρ_DM means two different things.** It is the cosmological mean (2.254e-30 g cm⁻³) in n₀ and the halo profile ρ_DM(r) in 𝒟_ROI. **Fix:** Use ρ̄_DM for the mean and ρ_halo(r) for the profile. Also say that the halo density is normalized to the same initial-mass-equivalent convention as f, i.e. holes trace the halo with number density f ρ_halo ψ/M_i.

**B5. The Hawking formula mixes unit conventions and leaves g_i undefined.**
- **Text:** "Σ_dof (g_i/2π) Γ_i/(e^{E/T_H} − 1)". The neighbouring T_H keeps ħ, c, G and k_B explicitly.
- **Problem:** The rate needs 1/(2πħ) to have units of s⁻¹ MeV⁻¹. g_i (photon helicities, = 2) is not defined. The sum runs over photon degrees of freedom only, yet it is labelled "dof" as if it covered all species.
- **Fix:** Write (1/2πħ) Σ_{helicities} Γ_s=1(E, m)/(e^{E/k_B T_H} − 1). Then state "secondary photons from all other emitted species".

**B6. §4.3: the comparison of asymmetric shapes changes the peak as well as the tail.**
- **Text:** "The asymmetric shapes show that the low-mass tail, not the width as such, drives this. (2, 0.5, 2) … (2, 2, 0.5) has the same a with a heavy high-mass tail."
- **Problem:** By the report's own mode formula M_c(p/q)^{1/a}, the modes are 5 × 10¹⁵ g and 2 × 10¹⁶ g. The bulk moves by a factor of 4, so the factor 2.4 × 10³ is not purely a tail effect. This also explains why (2, 2, 0.5) comes out weaker than the monochromatic case.
- **Fix:** State the mode shift. Either compare at fixed mode, or soften the claim to "tail plus peak shift".

**B7. §3.4: the lognormal limit is stated imprecisely.**
- **Text:** "The lognormal is reached for a → 0 with p, q → ∞".
- **Problem:** No scaling is given. For p = q → ∞, ln x^a tends to a normal with variance of order 2/p even at fixed a, with width ~ 1/(a√p).
- **Fix:** Give the joint limit with a fixed σ_ln, or cite the exact McDonald statement.

**B8. §3.1: the ρ_DM expression is ambiguous.** "ρ_DM = 0.120 ρ_crit h⁻²" reads as Ω_c ρ_crit h⁻². The value 2.254e-30 is correct only if 0.120 is Ω_c h² and ρ_crit/h² = 1.878e-29 g cm⁻³. **Fix:** Write ρ_DM = (Ω_c h²)(ρ_crit/h²) = 0.120 × 1.878 × 10⁻²⁹ g cm⁻³.

**B9. §2.3 and §3.5: notation hides the instrument dependence.** ⟨I₁⟩_b and P_b[ψ] depend on the instrument through 𝒟_X, but no X superscript appears. **Fix:** Write ⟨I₁^{X(b)}⟩_b.

## C. Understandability (items 1–6)

**C1. Core-idea test (§2.1): passes, with one trap.**
- **Text:** "a mass function is simply a weighted sum of monochromatic populations".
- **Problem:** A reader can take this to mean f_max[ψ] is a weighted combination of the monochromatic f_max values. §3.5 later rejects exactly that ("not the per-instrument approximation…").
- **Fix:** "…the predicted signal is a ψ-weighted sum over masses; the ceiling is then taken bin by bin on that summed signal, not by combining monochromatic ceilings."

**C2. Stakes test (§1): partial.** The stake is entirely about trusting the internal pipeline: "if the baseline cannot be trusted there…". The reason a physicist would want this particular number is missing: 10¹⁶ g is at the lower edge of the asteroid-mass window where PBHs could make up all the dark matter. That is why a factor of 10³ from the mass-function tail matters. **Fix:** Add two sentences on the physics stake: where 10¹⁶ g sits relative to the open window, and what a tail-driven 10³ tightening implies for extended-mass-function claims.

**C3. Intuition before formalism.**
- §3.5: the I_eg[ψ] and I_gal[ψ] equations come before the paragraph that makes them intuitive ("The two integrals see the mass function differently…"). **Fix:** Move that paragraph above the equations.
- §3.1: the f-definition subtlety ("f is neither β nor the surviving fraction") is placed well, but the mass-function sections never recall it. **Fix:** In §4.3, add a reminder that for (1,1,1), 5 % of the mass-equivalent f has already evaporated.

**C4. Meaning before evidence (§4).** Most findings open with their meaning. Exceptions:
- §4.1, third paragraph: "Comparing the cached K with Q_now only fixes their relative normalization: 4πK_rebuilt/K is 0.982–1.003…" leads with method and numbers. **Fix:** Open with "The cache is per steradian; an independent photon-power prediction agrees to 3 %."
- Fig. 4 caption and text: panels (b), (e) and (f) are referred to by letter, but the text never maps letters to shapes. The plot script uses a = mono, b = (1,1,1), c = (3,1,1), d = (10,1,1), e = (2,0.5,2), f = (2,2,0.5). Panel (c) is never discussed. **Fix:** Name the shapes in the caption.

**C5. Payoff test (§5): mostly passes.** It answers each stake from §1 in turn: the angular factor, the error column, the confidence scope, and the mass-function behaviour. Two gaps:
- **Text:** "a statistical-only ceiling in that bin would be 5.77 × 10⁻⁵ instead of 6.82 × 10⁻⁵". This gives a number with no stated consequence. **Fix:** Translate it into f_max (about 2.57 × 10⁻⁴ under the old data, still COMPTEL 6-9 since the 9-12 bin sits at f_b ≈ 1.0e-3). Redo this after A1 is resolved.
- **Tension:** §4.4 says "There is no universal threshold in ap", while §5 recommends reporting "how the ceiling depends on the tail exponent ap". **Fix:** Recommend reporting the mass fraction within roughly a factor of 2 of M_* (or the 90 %-signal-interval mass) as the controlling quantity, with ap as a secondary descriptor.

**C6. Bullet-only sections.** §6.1 is all bullets, but each bullet is a full prose argument, which is acceptable. §6.2 and the Appendix are lists by design. No core section is bullet-only.

## D. Coverage of the five requested items

| Item | Clear enough to reproduce? | Gaps |
|---|---|---|
| 1. Galactic and extragalactic flux formulae | Mostly | B1 (misleading derivation prose), B2 (cosmology/z_max missing), B3–B5 (symbols), B8 (ρ_DM notation). Sky regions behind 𝒟 are untraced; the report admits this. |
| 2. Monochromatic procedure | Yes, step by step (§3.2) | Its numbers do not reproduce from the current CSV (A1). |
| 3. Detector signals | Yes (table plus per-instrument paragraphs) | The quadrature claim about COMPTEL's σ depends on the CSV state (A1). |
| 4. GBP distribution | Yes: normalization, mode, logistic width and the M_* fractions all check out (verified by hand) | B6 (peak shift confound), B7 (lognormal limit); table rows do not restate M_c = 10¹⁶ g. |
| 5. GBP Galactic and extragalactic flux | Yes (§3.5 integrals, S-matrix method, grid-edge and gap handling) | C3 ordering, B9 notation. |

**Arithmetic that checks out:** T_H ≈ 1.06 MeV; m_now/M_i = 0.99996; ρ_DM; the 4π ≈ 12 and 0.082 ratios; the MacGibbon value 6.41e-8 g s⁻¹; the 81/19 split; 1.28 decades from M_c to M_*; the GB2 lower-tail fractions; and the generalized-gamma limit.