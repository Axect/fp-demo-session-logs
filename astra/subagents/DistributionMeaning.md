I found three wording/numeric problems in F1 and in how the report states its conventions. The framework itself (normalisation, superposition, the narrow limit) holds up. I used only the permitted files and short scipy checks: exact GB2 CDF, $F(M)=I_{y/(1+y)}(p,q)$ with $y=(M/M_c)^a$.

**Framework checks that pass**
- $\int\psi_{\ln}\,d\ln M = 1.000000$ for all seven shapes. $\psi = \psi_{\ln}/M$ is a standard GB2 density in $M$ with scale $M_c$.
- `intensity()` gives $K\rho_{DM} + J Q_{now}$ and the baseline report states $n = f\rho_{DM}/M_i$. So $P_b = \int \psi_{\ln} S_b\, d\ln M$ is the exact superposition.
- With $p=q$, $M_c$ is both the median and the mode of $\psi_{\ln}$ (confirmed numerically).

**Finding 1 (P2, conf 0.95). F1 says "two decades below M_c"; the gap is 1.28 decades.** $\log_{10}(10^{16}/5.21\times10^{14}) = 1.28$, a factor of about 19. Even the lower ends of the support windows (3.0e14 g) are only 1.5 decades below.

**Finding 2 (P2, conf 0.9). The F1 threshold "a p <~ 3" contradicts the table.**
- Row (2, 2, 0.5) has $ap = 4$. Its binding support is 5.7e14–2.9e15 g, which holds only 0.23 % of the mass. Its mode is $M_c(p/q)^{1/a} = 2\times10^{16}$ g.
- Row (5, 1, 1) puts 39 % of the mass in its window, and (10, 1, 1) puts 70 %. Those two are bulk-dominated.

Mass inside the 90 %-signal window, row by row: 3.2 %, 0.51 %, 1.7 %, 39 %, 70 %, 5.2 %, 0.23 %.

**Finding 3 (P2, conf 0.8). The "tail, not the bulk" reading mixes signal quantiles with abundance.** The support column gives quantiles of the binding-bin *signal*, not of the mass. $f_{max}$ is still a bound on the total $f$; it only becomes a statement about the bulk through the assumed tail shape.

**Finding 4 (P3, conf 0.85). "Mass fraction at formation" can be read as β.** β is the formation-epoch fraction of the total density, which is a different quantity. Here $f$ is normalised to today's $\rho_{DM}$ and counts initial masses, including black holes that have already evaporated. The mass below $M_*$ is 4.95 % for (1, 1, 1) and 7.8 % for (2, 0.5, 2).

**Finding 5 (P3, conf 0.8). The (0.5, 2) and (2, 0.5) rows move the centre as well as the tails.**
- (2, 0.5, 2): median 3.70e15 g, mode 5.0e15 g.
- (2, 2, 0.5): median 2.70e16 g, mode 2.0e16 g.

Part of the 4.67e-4 > 3.04e-4 result for (2, 2, 0.5) is that its median sits above $M_c$.

**Dropped mass (your question 4).** The mass below 1e13 g is at most 1.5e-3, for (2, 0.5, 2); for (1, 1, 1) it is 1.0e-3. Mass above 1e22 g is at most 1.5e-6. Dropping it can only raise the ceiling.

A rough lifetime scaling ($\tau \propto M^3$) puts the end of evaporation for 1e13 g holes around 2e5 yr, so their photons cannot reach today's bands [INFERENCE]. That mass falls under CMB/BBN bounds instead. The report says "(reported)" but shows no lost-mass number.

**Lognormal limit (comment in `emf_gbp.py`, not the report).** In ln M, $\mathrm{Var}\ln M = [\psi_1(p) + \psi_1(q)]/a^2 \approx (1/p + 1/q)/a^2$ and the mean offset is $\approx \ln(p/q)/a$. So the limit $a\to0$, $p,q\to\infty$ also needs $a^2p$, $a^2q$ and $\ln(p/q)/a$ held fixed.

**Naming (question 5).** Taking $q\to\infty$ with $M_c = M_f q^{1/a}$ turns GB2 into a generalized gamma: $\psi \propto M^{ap-1} e^{-(M/M_f)^a}$. The critical-collapse form $\psi \propto M^{\alpha} e^{-(M/M_f)^{\alpha}}$ then maps to $a = \alpha$, $p = 1 + 1/\alpha$, $q\to\infty$. With $\alpha \approx 2.85$ (my recollection of Carr et al. 2017; I could not check it without network), $p \approx 1.35$. No table row is that shape, so avoiding the name was correct.

**Replacement sentences**
- Framework bullet 1: "dn/dM_i = f rho_DM psi(M_i)/M_i with Int psi dM_i = 1, where rho_DM is today's dark-matter density and f is the initial-mass density of the population (including holes that have since evaporated) in units of rho_DM; f is not the formation-epoch fraction beta."
- Tails bullet: "Tails of the mass-weighted density psi_ln: M^(a p) below, M^(-a q) above (number density dn/dlnM: M^(a p - 1), M^(-a q - 1)). M_c is the median and mode of psi_ln only when p = q; otherwise the mode is M_c (p/q)^(1/a)."
- Before the table: "For (a, p, q) = (2, 0.5, 2) the median is 3.7e15 g and for (2, 2, 0.5) it is 2.7e16 g, so these rows shift the centre as well as the tails."
- F1: "F1. For a p <= 4 in the table (rows 1-3, 6, 7), 90 % of the binding signal comes from M_i between 3.0e14 and 2.9e15 g, a window just above M_* = 5.21e14 g, about 1.3 decades below M_c, that holds only 0.2-5 % of the mass. f_max then bounds the total f only through the assumed tail shape near M_*. For a p >= 5 the window holds 39-70 % of the mass and the bulk sets the bound."
- Quadrature bullet: "mass outside 1e13-1e22 g is dropped; it is at most 1.5e-3 of the mass (for (2, 0.5, 2)), and dropping it can only raise f_max."

**Verdict.** The framework, the normalisation and the narrow limit are correct. F1 is wrong in its decade count and its $ap$ threshold, and it mixes signal quantiles with abundance, so the report is not correct as written. Confidence 0.85.