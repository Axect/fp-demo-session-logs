F2's main conclusion holds, but its bound needs correcting. Zeroing the gap gives a ceiling that is not looser than the true one only within this forward model and bin rule, and only approximately: the guarantee fails on a stretch of about 1 % of P_b. The brief's bound is about 1.7× too large (21.5× P_b against 12.6× from the correct Jacobian). It also leaves out the extragalactic (K) part of the gap. Everything below comes from read-only `python3 -c` runs on the permitted files; I created or edited no files.

**1. The general Jacobian removes L(m).**
Every black hole has the same age t0, so τ(M_i) − τ(m) = t0, where τ is the lifetime from `clock()`. Differentiating gives dM_i / L(M_i) = dm / L(m), for any L that depends only on mass. The Galactic upper bound for the gap then follows from ∫_bin Q dE ≤ (1/E_lo)∫_bin E Q dE ≤ L(m)c²/(4π E_lo):

P_gap,gal ≤ J c² / (4π ΔE E_lo) · ∫_{M_*}^{M_i⁺} ψ(M_i) L(m)/M_i dM_i = J c² / (4π ΔE E_lo) · ∫_0^{m1} ψ(M_i) L(M_i)/M_i dm ≈ J c² ψ(M_*) L(M_*) m1 / (4π ΔE E_lo M_*).

Here ψ is normalised in dM. With ψ_ln in place of ψ, the last factor becomes 1/M_*². The bound depends only on L near M_*. It does not depend on the hot end of the dM/dt table, which stops at 1e12 g.

**2. The cubic mapping is not valid here.**
- m³ = M_i³ − M_*³ requires L·M² to be constant. In `dmdt_L`, L·M² is 9.70e25 at M_*, 1.29e26 at 1e14 g and 6.71e26 at 1e12 g, a factor of about 7.
- The clock gives m1 = 1.735e14 g; the cubic mapping gives 1.567e14 g. The report's 1.7e14 is correct.
- The brief's integral ∫L(m)m²dm / M_*⁴ comes out 1.60× the exact result with the clock m1, and 1.48× with the cubic m1. It is a valid but loose upper bound.

**3. Dimensions.**
- J ψ(M_*)/M_*⁴ · ∫L m² dm is dimensionally consistent only if ψ is ψ_ln, the dimensionless density per ln M. With ψ normalised in dM the factor must be ψ(M_*)/M_*³; M_*⁴ leaves one spare factor of mass.
- Both forms need c² to turn g/s into MeV/s. The brief's formula omits it.

**4. Size of the bound for a = p = q = 1, Fermi 140–200 MeV, relative to the sliver-dropped P_b.**
- The brief's formula gives 21.5×, which is where the report's "~20×" comes from.
- The exact Galactic bound gives 12.6×.
- The extragalactic bound, using ρc·(M_i − m) per black hole, gives at most 0.94×.
- So the total bound is about 13.5×. For the other shapes, Galactic plus extragalactic gives 7.5× (3,1,1), 10.6× (2,1,1) and 13.6× (2,0.5,2).
- I reproduced the default-versus-dropped difference as 6.78 %, i.e. f_drop/f − 1 = 7.28 %, and the Galactic share at the upper node as 87.8 %.

**5. K has its own gap problem.**
- K's bin average is 4.039 at the lower node (5.149e14 g) and 2.705 at the upper node (5.257e14 g), a 33 % drop. The lower node's black holes finished evaporating about 0.48 Gyr ago, so their final burst is already in K. The upper-node black holes have only lost mass down to 1.73e14 g.
- K is therefore unresolved across the gap as well, and log-interpolating it there has no justification.
- Zeroing the gap removes this signal too, but the bound in F2 ("all Hawking power into the bin", Galactic only) does not cover K's past emission.
- Q_now is exactly zero at the lower node and non-zero at the upper one, so the cache and `m_star()` agree on the age.

**6. Zeroing the gap with correct node values still does not guarantee a conservative ceiling everywhere.**
- The next interval (nodes 191–192) has unresolved structure too. S falls by a factor of 2.13 there (22.28 to 10.48), and the interval carries 11.9 % of P_b.
- The log-differences of S over successive intervals are −0.75, −0.20, −0.14, so log S looks convex there. If so, the log-linear chord overestimates S on that interval, which would make the ceiling too loose.
- Switching that interval from log to linear interpolation changes its contribution by 4.6 %, about 0.5 % of P_b.
- E3 does not test this: refining the grid between the same nodes cannot reveal node-spacing error.
- "Conservative" also covers only the quadrature. The bin rule itself has no stated coverage (C9 in the baseline report).

**Replacement sentences:**
- **F2, bound:** "An energy-conservation upper bound on the gap (all Hawking power into the bin; age-fixed Jacobian dM_i/L(M_i) = dm/L(m)) allows up to about 13× the sliver-dropped P_b for the binding Fermi bin: 12.6× from the Galactic term and at most 0.9× from extragalactic emission by the same black holes. The cubic mapping m³ = M_i³ − M_*³ overstates this by about 1.6× because L·M² rises towards small m."
- **F2, conservativeness:** "Zeroing the gap removes signal, but the ceiling is guaranteed to be no looser than the sliver-dropped value only if the log-linear interpolation of S overestimates nowhere. On the interval just above the gap (S falls 2.1× over one node, 12 % of P_b) the error is unknown in sign and about 1 % in size. The statement applies to this forward model and bin rule, not to a coverage level."
- **K issue:** "The extragalactic kernel is also unresolved across the gap (bin-averaged K falls 33 % between the bracketing nodes, because the lower node includes a completed burst); its gap content is bounded by ρ_DM c (M_i − m) c²/(4π ΔE E_lo) per unit ψ dM_i/M_i."
- **Brief formula (ψ normalised in dM):** "P_gap ≤ J c² ψ(M_*)/(4π ΔE E_lo) ∫_0^{m1} L(M_i(m))/M_i dm ≈ J c² ψ(M_*) L(M_*) m1/(4π ΔE E_lo M_*). Under the cubic approximation this becomes J c² ψ(M_*)/(4π ΔE E_lo M_*³) ∫ L(m) m² dm, which is M_*⁴ only for ψ_ln."