# Week 2 — Potassium as a bifurcation parameter

> [!NOTE]
> **This week.** Build a realistic neuron with Hodgkin–Huxley ion channels. Treat the potassium concentration outside the cell as a dial, and turn it up. The neuron goes from resting, to firing, to a silent "depolarization block". Map out exactly where each transition happens and what kind of bifurcation it is.

## Goals

1. Compute a reversal potential from the Nernst equation and see how it moves with concentration.
2. Find all fixed points of a 3-variable neuron model and classify their stability.
3. Build a **bifurcation diagram** by brute force (quasi-static sweeps up and down).
4. Use a scaling law to decide which bifurcation starts the firing.

## Symbols

| Symbol | Meaning | Units / typical value |
|---|---|---|
| $V$ | membrane voltage | mV |
| $C$ | membrane capacitance per area | 1 µF/cm² |
| $[K^+]_o$, $[K^+]_i$ | potassium concentration outside / inside the cell | mM; normally 4 and 140 |
| $[Na^+]_o$, $[Na^+]_i$ | sodium concentration outside / inside | mM; 144 and 18 |
| $E_K$, $E_{Na}$ | Nernst (reversal) potentials of K⁺ and Na⁺ | mV |
| $k_B$, $T$, $e$ | Boltzmann constant, absolute temperature, elementary charge | |
| $k_BT/e$ | thermal voltage at body temperature | 26.64 mV |
| $g_{Na}$, $g_K$ | maximal conductances of the voltage-gated Na⁺ and K⁺ channels | 100, 40 mS/cm² |
| $g_{NaL}$, $g_{KL}$, $g_{ClL}$ | leak conductances (always open) | 0.0175, 0.05, 0.05 mS/cm² |
| $E_{Cl}$ | chloride reversal potential | −81.93 mV |
| $m,\ h,\ n$ | gating variables: fraction of open Na activation, Na inactivation and K activation gates | 0 to 1 |
| $\alpha_x(V)$, $\beta_x(V)$ | opening and closing rates of gate $x$ | 1/ms |
| $x_\infty(V)$ | steady-state value of gate $x$, $\alpha_x/(\alpha_x+\beta_x)$ | |
| $\phi$ | speed-up factor of the gates (temperature) | 3 |
| $V^\ast$ | a fixed-point voltage | mV |
| $K_{\rm on}$ | the $[K^+]_o$ at which the resting state disappears | mM |

## Theory

### 1. Where reversal potentials come from

In equilibrium the Boltzmann distribution sets the ratio of ion concentrations across a potential difference. For an ion of charge $+e$, the ratio of concentrations outside to inside is $e^{eV/k_BT}$ when the inside sits at potential $V$ relative to the outside. Solving for $V$ gives the **Nernst potential**, the voltage at which diffusion and electric drift balance:

$$E_K=\frac{k_BT}{e}\ln\frac{[K^+]_o}{[K^+]_i}.$$

Each K⁺ channel pulls the membrane towards $E_K$. Normally $[K^+]_o\approx4$ mM, so $E_K\approx-95$ mV and the cell rests far below threshold. During intense activity K⁺ leaks out of the neurons into the tiny space between cells, and $[K^+]_o$ can reach 10–12 mM. That is a large change in $E_K$, and it is a central suspect in seizures.

### 2. The model neuron

The model uses three currents:

$$I_{Na}=g_{Na}\,m_\infty(V)^3\,h\,(V-E_{Na})+g_{NaL}(V-E_{Na}),$$

$$I_K=\big(g_K\,n^4+g_{KL}\big)(V-E_K),\qquad I_{Cl}=g_{ClL}(V-E_{Cl}),$$

and the voltage and two gates evolve as

$$C\frac{dV}{dt}=-(I_{Na}+I_K+I_{Cl}),\qquad \frac{dn}{dt}=\phi\,\big[\alpha_n(1-n)-\beta_n n\big],\qquad \frac{dh}{dt}=\phi\,\big[\alpha_h(1-h)-\beta_h h\big].$$

The activation gate $m$ is so fast that it is set to its steady state, $m_\infty=\alpha_m/(\alpha_m+\beta_m)$. The opening and closing rates (in 1/ms, with $V$ in mV) are the Hodgkin–Huxley-type formulas of [1]:

$$\alpha_m=\frac{0.1(V+30)}{1-e^{-0.1(V+30)}},\quad \beta_m=4e^{-(V+55)/18},\quad \alpha_n=\frac{0.01(V+34)}{1-e^{-0.1(V+34)}},\quad \beta_n=0.125\,e^{-(V+44)/80},$$

$$\alpha_h=0.07\,e^{-(V+44)/20},\qquad \beta_h=\frac{1}{1+e^{-0.1(V+14)}}.$$

Watch out: $\alpha_m$ and $\alpha_n$ have removable $0/0$ points at $V=-30$ and $-34$ mV. Reversal potentials use $k_BT/e=26.64$ mV.

This week all concentrations are held **fixed**, and only $[K^+]_o$ changes from run to run.

### 3. Fixed points without simulating

At a fixed point every derivative vanishes, so the gates sit at $n=n_\infty(V^\ast)$, $h=h_\infty(V^\ast)$. The fixed points are therefore the roots of a single function of one variable:

$$F(V)=I_{Na}+I_K+I_{Cl}\quad\text{evaluated with}\quad n=n_\infty(V),\ h=h_\infty(V).$$

Plot $F(V)$ and count its zeros. If two zeros merge and disappear as $[K^+]_o$ changes, that is a **saddle-node** bifurcation. Stability still needs the 3×3 Jacobian (compute it by finite differences).

### 4. Brute-force bifurcation diagrams

Not every attractor is a fixed point: periodic firing is a limit cycle. A practical way to see all attractors:
1. Change the parameter in small steps.
2. Start each run from the final state of the previous one (a *quasi-static* sweep).
3. After transients, record the minimum and maximum of $V$, and the firing rate.

Doing the sweep in both directions reveals **hysteresis**: parameter ranges where the answer depends on history, meaning two attractors coexist.

### 5. Which bifurcation? Use a scaling law

Week 1 showed two ways to start firing. If the onset is a SNIC, the firing rate just past onset at $K_{\rm on}$ scales as

$$f\propto\big([K^+]_o-K_{\rm on}\big)^{1/2},$$

starting from zero. If it is a Hopf, the rate starts at a finite value. On log–log axes a SNIC gives a straight line of slope ½.

## Questions

**Q1. Nernst.**
- *Predict:* by how many mV does $E_K$ shift when $[K^+]_o$ doubles from 4 to 8 mM? (No computer needed.)
- *Build:* plot $E_K$ against $[K^+]_o$ from 2 to 40 mM.

**Q2. Fixed points.**
- *Build:* for $[K^+]_o$ from 2 to 40 mM, find all roots of $F(V)$ and the largest real part of the Jacobian's eigenvalues at each. Plot $V^\ast$ against $[K^+]_o$, coloured by stability.
- *Explain:* how many fixed points are there at 4 mM, and what are they? At what $[K^+]_o$ does the resting state vanish? Locate it to 4 significant figures by bisection.

**Q3. The bifurcation diagram.**
- *Build:* sweep $[K^+]_o$ from 3 to 40 mM and back. Record min/max $V$ and the firing rate.
- *Explain:* name the three regimes. Is there hysteresis at either end? What does "depolarization block" mean physically, and why might a brain region *stop* seizing when potassium rises further?

**Q4. Onset scaling.**
- *Build:* measure the firing rate at $[K^+]_o=K_{\rm on}+\delta$ for $\delta$ from about 0.005 to 1 mM, and plot it on log–log axes.
- *Explain:* is the slope close to ½? If not exactly, why might a finite range of $\delta$ give a different slope? Which bifurcation starts the firing?

## References

[1] J. R. Cressman Jr., G. Ullah, J. Ziburkus, S. J. Schiff and E. Barreto, "The influence of sodium and potassium dynamics on excitability, seizures, and the stability of persistent states: I. Single neuron dynamics", *J. Comput. Neurosci.* **26**, 159 (2009); erratum (2011).
[2] A. L. Hodgkin and A. F. Huxley, "A quantitative description of membrane current and its application to conduction and excitation in nerve", *J. Physiol.* **117**, 500 (1952).
[3] E. M. Izhikevich, *Dynamical Systems in Neuroscience*, MIT Press (2007), ch. 6–7.
[4] S. H. Strogatz, *Nonlinear Dynamics and Chaos*, 2nd ed., CRC Press (2015), ch. 3 and 8.
