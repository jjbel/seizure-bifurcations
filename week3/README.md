# Week 3 — Neuron–glia interaction: a slow potassium loop makes seizures

> [!summary] This week
> Last week you turned the potassium dial by hand. Now the neuron turns it itself: every spike releases potassium, and pumps and glial cells slowly clear it. The result is a **fast–slow system** that switches itself between resting and seizing, with no external trigger.

## Goals

1. Add slow ion-concentration dynamics to the week 2 neuron.
2. Reproduce spontaneous, periodic seizure-like events when the bath potassium is raised.
3. Explain the events with the **fast–slow** method: freeze the slow variables, use week 2's bifurcation diagram, and watch the slow variables drift across it.
4. Map which combinations of bath potassium and glial strength are "epileptic".

## Symbols

All symbols from week 2 still apply. New ones:

| Symbol | Meaning | Units / typical value |
|---|---|---|
| $[K^+]_{\rm bath}$ | potassium concentration far from the cell (in the blood or bath) | mM; normally 4 |
| $\gamma$ | converts a membrane current into a rate of concentration change inside the cell | 0.0445 mM·cm²/(µC) |
| $\beta$ | ratio of intracellular to extracellular volume (the space outside cells is small) | 7 |
| $I_{\rm pump}$ | Na⁺/K⁺-ATPase pump: 3 Na⁺ out and 2 K⁺ in per cycle | mM/s |
| $\rho$ | maximal pump rate | 1.25 mM/s |
| $I_{\rm glia}$ | potassium uptake by glial cells (astrocytes) | mM/s |
| $G_{\rm glia}$ | maximal glial uptake | 66 mM/s |
| $I_{\rm diff}$ | diffusion of K⁺ towards the bath / blood vessels | mM/s |
| $\varepsilon$ | diffusion rate constant | 1.2 /s |
| fast variables | $V$, $n$, $h$, which change in milliseconds | |
| slow variables | $[K^+]_o$, $[Na^+]_i$, which change in seconds | |

## Theory

### 1. Conservation of ions

Each ion that crosses the membrane changes the concentrations on both sides. Outward K⁺ current $I_K$ raises $[K^+]_o$, and because the space outside cells is about $\beta=7$ times smaller than the inside, it does so seven times faster than it lowers $[K^+]_i$. Inward Na⁺ current raises $[Na^+]_i$. Three processes restore the balance:
- the **pump**, which needs ATP and is activated by high $[Na^+]_i$ and high $[K^+]_o$;
- **glial uptake**, which switches on steeply when $[K^+]_o$ rises above about 18 mM;
- **diffusion** to the bath, proportional to $[K^+]_o-[K^+]_{\rm bath}$.

In equations (rates in mM/s, so multiply by $10^{-3}$ when time is in ms) [1]:

$$\frac{d[K^+]_o}{dt}=\gamma\beta\,I_K-2\beta\,I_{\rm pump}-I_{\rm glia}-I_{\rm diff},\qquad \frac{d[Na^+]_i}{dt}=-\gamma\,I_{Na}-3\,I_{\rm pump},$$

$$I_{\rm pump}=\frac{\rho}{\big(1+e^{(25-[Na^+]_i)/3}\big)\big(1+e^{5.5-[K^+]_o}\big)},\quad I_{\rm glia}=\frac{G_{\rm glia}}{1+e^{(18-[K^+]_o)/2.5}},\quad I_{\rm diff}=\varepsilon\big([K^+]_o-[K^+]_{\rm bath}\big).$$

Electroneutrality fixes the other two concentrations: $[K^+]_i=140+(18-[Na^+]_i)$ and $[Na^+]_o=144-\beta([Na^+]_i-18)$. The Nernst potentials $E_K$ and $E_{Na}$ are recomputed at every instant from these concentrations.

### 2. The fast–slow method

Spikes last about 1 ms, while concentrations change over seconds. So:
1. **Freeze** the slow variables $([K^+]_o,[Na^+]_i)$ and treat them as parameters of the fast neuron. That is exactly what week 2 did.
2. In the slow plane, draw the curve where the fast neuron's resting state disappears. This is the week 2 saddle-node, now a curve because it depends on $[Na^+]_i$ too.
3. Let the slow variables move.
   - While the system rests, the slow variables drift towards the curve.
   - Once they cross it, the neuron fires. Firing releases K⁺ and lets in Na⁺, which drives the slow variables back.
   - Once they cross back, firing stops.

The repeated crossing is a **burst**, here a seizure-like event. This is a relaxation oscillation like FitzHugh–Nagumo's, one level up in time scale.

## Questions

**Q1. Spontaneous seizures.**
- *Build:* simulate 120 s with $[K^+]_{\rm bath}=4$ mM and then 8 mM. Plot $V$, $[K^+]_o$ and $[Na^+]_i$.
- *Predict:* before running at 8 mM, use your week 2 result for $K_{\rm on}$. Should a bath of 8 mM, with no other change, make the neuron fire? Then explain why it doesn't fire *continuously*.
- *Explain:* measure the duration and period of the events. What ends each event: is it a rising or a falling slow variable?

**Q2. The fast–slow picture.**
- *Build:* plot the trajectory in the $([K^+]_o,[Na^+]_i)$ plane, coloured by whether the neuron is firing. On the same axes, draw the curve where the frozen neuron loses its resting state. Compute it by bisection on the number of fixed points, as in week 2.
- *Explain:* where does each event start and end, relative to the curve? The trajectory crosses the curve twice per cycle. Which slow variable carries it across in each direction, and which physical process (spikes, pump, glia, diffusion) drives that variable?

**Q3. When is the brain "epileptic"?**
- *Build:* for $[K^+]_{\rm bath}\in\{4,5,6,7,7.5,8,9,10\}$ mM and $G_{\rm glia}\in\{30,45,66,90\}$ mM/s, compute the fraction of time spent seizing over 50 s. Show it as a heat map.
- *Explain:* does weakening the glia make seizures more or less likely? At high bath potassium, do you get seizures or continuous firing? Relate this to the depolarization block of week 2.

## References

[1] J. R. Cressman Jr., G. Ullah, J. Ziburkus, S. J. Schiff and E. Barreto, "The influence of sodium and potassium dynamics on excitability, seizures, and the stability of persistent states: I. Single neuron dynamics", *J. Comput. Neurosci.* **26**, 159 (2009); erratum (2011).
[2] Y. Wei, G. Ullah and S. J. Schiff, "Unification of neuronal spikes, seizures, and spreading depression", *J. Neurosci.* **34**, 11733 (2014).
[3] E. M. Izhikevich, *Dynamical Systems in Neuroscience*, MIT Press (2007), ch. 9 (bursting).
