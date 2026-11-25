# Week 4 — The Epileptor: how seizures start and stop

> [!summary] This week
> Strip away the biology and keep only the dynamics. A five-variable model, the **Epileptor**, reproduces the electrical signature of real seizures. A slow variable drags its fast part through one bifurcation at seizure onset and a different one at offset. You'll predict the onset analytically, and test whether the *slowing of spikes before a seizure ends* can tell the two candidate offsets apart.

## Goals

1. Read a fast–slow model and compute its **slow manifold** and **fold** by hand.
2. Predict the critical excitability $x_{0,c}$ above which seizures occur, and check it numerically.
3. Measure how the inter-spike interval grows before offset, and fit the two candidate scaling laws.
4. Judge honestly whether the data can distinguish them.

## Symbols

| Symbol | Meaning |
|---|---|
| $x_1,\ y_1$ | fast subsystem 1: generates the fast discharges of a seizure |
| $x_2,\ y_2$ | subsystem 2: generates slower "spike-and-wave" events |
| $z$ | slow "permittivity" variable: a lumped stand-in for slow processes such as ion concentrations and energy |
| $g$ | low-pass filtered $x_1$, coupling subsystem 1 into subsystem 2 |
| $x_0$ | excitability of the tissue (more positive = more epileptic) |
| $I_1,\ I_2$ | constant inputs to the two subsystems (3.1 and 0.45) |
| $\tau_0$ | time scale of $z$ (2857, very slow) |
| $\tau_2$ | time scale of $y_2$ (10) |
| LFP | local field potential, the signal an electrode would record: $-x_1+x_2$ |
| ISI | inter-spike interval |
| $t_{\rm end}$ | time of seizure offset |
| $\lambda_u$ | unstable eigenvalue of the saddle involved in a homoclinic bifurcation |
| $\mu$ | distance from a bifurcation point |

All variables are dimensionless. One time unit is roughly a millisecond if you map the model onto recordings.

## Theory

### 1. The model

$$\dot x_1=y_1-f_1(x_1,x_2,z)-z+I_1,\qquad \dot y_1=1-5x_1^2-y_1,$$

$$\dot z=\frac{1}{\tau_0}\big(4(x_1-x_0)-z\big),$$

$$\dot x_2=-y_2+x_2-x_2^3+I_2+2g-0.3(z-3.5),\qquad \dot y_2=\frac{1}{\tau_2}\big(-y_2+f_2(x_2)\big),\qquad \dot g=-0.01\,(g-0.1\,x_1),$$

with the piecewise functions

$$f_1=\begin{cases}x_1^3-3x_1^2 & x_1<0\\ \big(x_2-0.6(z-4)^2\big)x_1 & x_1\ge0\end{cases},\qquad f_2=\begin{cases}0 & x_2<-0.25\\ 6(x_2+0.25) & x_2\ge-0.25\end{cases}.$$

The model was built to reproduce recordings across species [1]. It is not derived from ion channels, but its *bifurcation structure* is the claim being tested.

### 2. The slow manifold and onset

Freeze $z$. On the $x_1<0$ branch, the fixed points of subsystem 1 satisfy $y_1=1-5x_1^2$ and $\dot x_1=0$, which together give a cubic curve

$$z=4.1-2x_1^2-x_1^3.$$

Its lower branch is the resting state. Where $dz/dx_1=0$ the rest branch folds over: a **saddle-node**. When $z$ drifts below the fold value, rest no longer exists and the system jumps into the seizure. Now combine this with the $z$-equation. In rest, $z$ relaxes towards $4(x_1-x_0)$. If the line $z=4(x_1-x_0)$ crosses the rest branch *below* the fold, the brain sits there forever. If not, $z$ keeps drifting into the fold. That gives a critical $x_{0,c}$.

### 3. Offset, and how to diagnose it

During a seizure, $x_1$ is high, so $z$ grows. Eventually the oscillation is destroyed. Two candidate ways [2, 3], with $\mu\propto t_{\rm end}-t$ if $z$ drifts at constant speed:
- **Homoclinic bifurcation:** the cycle runs into a saddle point. The period diverges logarithmically, $\text{ISI}\approx-\frac{1}{\lambda_u}\ln\mu$, and the amplitude stays finite.
- **SNIC:** a saddle-node appears on the cycle. The period diverges as $\text{ISI}\propto\mu^{-1/2}$, and the amplitude again stays finite.

Both predict spikes that *slow down* at constant amplitude. A supercritical Hopf offset would instead predict shrinking amplitude at constant frequency. Clinicians can read these fingerprints off EEG recordings [2].

## Questions

**Q1. Onset, by hand.**
- *Predict:* find the fold $(x_1,z)$ of the rest branch. Then find $x_{0,c}$, the excitability at which the $z$-nullcline passes through the fold.
- *Build:* simulate at $x_0=-1.6$ for 8000 time units. Plot the LFP, $x_1$ and $z$. Overlay the trajectory on the slow manifold in the $(z,x_1)$ plane.
- *Explain:* at what value of $z$ does each seizure start, and how close is it to your fold? Why is it slightly past the fold rather than exactly at it?

**Q2. Sweep the excitability.**
- *Build:* for $x_0$ from −2.3 to −1.4, measure the number of seizures, the time between seizures and the seizure duration over 30,000 time units.
- *Explain:* do seizures appear exactly at your $x_{0,c}$? How does the time between seizures behave as $x_0\to x_{0,c}$ from above, and why? (Think about the ghost of a saddle-node from week 1.)

**Q3. Slowing before offset.**
- *Build:* detect the large upward spikes in the LFP. For each seizure, compute the ISIs and the time remaining until the last spike. Fit both laws over the last 400 time units, with the bifurcation time as a free shift.
- *Explain:* do the spikes slow down? Does the amplitude stay constant? Which law fits better, and by how much? What would you need, in data or in experiment design, to tell them apart convincingly?

**Q4 (extension). Real seizures.** Download one seizure from the CHB-MIT scalp EEG database [4] and try the same ISI analysis. Discuss what makes real data harder.

## References

[1] V. K. Jirsa, W. C. Stacey, P. P. Quilichini, A. I. Ivanov and C. Bernard, "On the nature of seizure dynamics", *Brain* **137**, 2210 (2014).
[2] M. L. Saggio *et al.*, "A taxonomy of seizure dynamotypes", *eLife* **9**, e55632 (2020).
[3] E. M. Izhikevich, *Dynamical Systems in Neuroscience*, MIT Press (2007), ch. 6 and 9.
[4] A. H. Shoeb, *Application of machine learning to epileptic seizure onset detection and treatment*, PhD thesis, MIT (2009); CHB-MIT Scalp EEG Database, [physionet.org/content/chbmit/1.0.0](https://physionet.org/content/chbmit/1.0.0/).
