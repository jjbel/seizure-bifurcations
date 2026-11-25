# Week 1 — Excitability: where does a neuron's threshold come from?

> [!summary] This week
> We reduce a neuron to two differential equations, find its threshold in the geometry of the phase plane, and discover the two ways in which a resting neuron can start firing. These two ways will come back in week 4, as the two candidate ways a seizure can end.

## Goals

By the end of the week you should be able to:
1. Draw the nullclines of a two-variable system and read off its fixed points.
2. Decide the stability of a fixed point from the eigenvalues of its Jacobian.
3. Explain *excitability*: why a small kick decays while a big kick produces a spike.
4. Tell a **Hopf** onset from a **SNIC** onset by looking at a firing-rate curve.

## Symbols

| Symbol | Meaning | Units / typical value |
|---|---|---|
| $t$ | time | dimensionless (FHN) or ms (Morris–Lecar) |
| $v$ | fast, voltage-like variable of the FitzHugh–Nagumo (FHN) model | dimensionless |
| $w$ | slow "recovery" variable of FHN | dimensionless |
| $I$ | injected (applied) current | dimensionless (FHN), µA/cm² (ML) |
| $a,\ b$ | constants in the $w$-equation | $a=0.7$, $b=0.8$ |
| $\epsilon$ | ratio of the fast to the slow time scale | $\epsilon=0.08$ |
| $(v_\ast, w_\ast)$ | a fixed point, where both time derivatives vanish | |
| $J$ | Jacobian matrix: partial derivatives of the right-hand sides, evaluated at a fixed point | |
| $\lambda$ | eigenvalue of $J$ | |
| $V$ | membrane voltage (Morris–Lecar) | mV |
| $C$ | membrane capacitance per unit area | µF/cm² |
| $g_X$, $V_X$ | maximal conductance and reversal potential of ion channel $X$ (L = leak, K, Ca) | mS/cm², mV |
| $f$ | firing rate (spikes per second) | Hz |

## Theory

### The neuron as an electrical circuit

Let us begin with what a neuron looks like to a physicist. The cell membrane is a thin insulating layer that separates charged fluids, so it behaves like a **capacitor** $C$. Embedded in it are ion channels, small pores that let one kind of ion through. Each type of channel acts as a **conductance** $g_X$ in parallel with the capacitor. Each kind of ion also has its own **reversal potential** $V_X$: the voltage at which diffusion and the electric field balance, so that no net current of that ion flows. A channel therefore always pushes the voltage towards its own $V_X$.

Applying Kirchhoff's current law to a small patch of membrane, the current that charges the capacitor is the applied current minus everything that leaks through the channels:

$$C\frac{dV}{dt}=I-\sum_X g_X\,(V-V_X).$$

If the conductances were constant, this would be an ordinary RC circuit, and $V$ would simply relax to a weighted average of the $V_X$. What makes a neuron interesting is that the conductances themselves depend on $V$:
- Sodium channels open when $V$ rises. That lets Na⁺ into the cell, which raises $V$ further: **positive feedback**. This produces the fast upstroke of a spike.
- Potassium channels also open when $V$ rises, but more slowly. They let K⁺ out and pull $V$ back down: **negative feedback with a delay**. This ends the spike.

Every model this week is a way of capturing these two feedback loops with as few equations as possible.

### The FitzHugh–Nagumo model

For simplicity, let us use a model of the neuron with just one fast variable and one slow variable. The fast variable $v$ plays the role of the voltage and carries the positive feedback. The slow variable $w$ stands for everything that acts with a delay to restore the cell to rest: potassium channels opening and sodium channels closing. We call it the *recovery variable*.

What should $dv/dt$ look like? We want small deviations of $v$ to amplify themselves (positive feedback), but not without limit, because a real voltage saturates. The simplest function with a region of positive slope bounded by regions of negative slope is a cubic, $v-v^3/3$. The recovery variable should pull $v$ down, so it enters with a minus sign, and the injected current $I$ simply adds. For $w$, we want a slow relaxation towards a value that grows with $v$. A linear equation, slowed down by a small factor $\epsilon$, is enough. Putting this together gives the **FitzHugh–Nagumo** model:

$$\frac{dv}{dt}=v-\frac{v^3}{3}-w+I,\qquad \frac{dw}{dt}=\epsilon\,(v+a-bw).$$

### Reading the phase plane

Because there are only two variables, we can see the whole dynamics in the $(v,w)$ plane. The key curves are the **nullclines**, where one of the variables momentarily stops changing:
- the *$v$-nullcline*, where $dv/dt=0$: $w=v-v^3/3+I$, an N-shaped cubic curve;
- the *$w$-nullcline*, where $dw/dt=0$: the straight line $w=(v+a)/b$.

Where the two curves cross, both derivatives vanish, so the point $(v_\ast,w_\ast)$ is a **fixed point**: a state in which the neuron would stay forever. Since $\epsilon$ is small, $v$ changes much faster than $w$. Away from the $v$-nullcline, trajectories therefore move almost horizontally until they reach a stable branch of the N, and then creep slowly along it.

### Stability of a fixed point

A fixed point is only useful if the neuron returns to it after a small disturbance. To check this, we look at small deviations from the fixed point. Write $f(v,w)$ and $g(v,w)$ for the right-hand sides of the two equations. Near the fixed point, the deviations obey a linear system, whose solutions grow or decay as $e^{\lambda t}$, where $\lambda$ are the eigenvalues of the **Jacobian**

$$J=\begin{pmatrix}\partial f/\partial v&\partial f/\partial w\\ \partial g/\partial v&\partial g/\partial w\end{pmatrix}=\begin{pmatrix}1-v_\ast^2&-1\\ \epsilon&-\epsilon b\end{pmatrix}.$$

If every eigenvalue has a negative real part, small deviations die out and the fixed point is stable. For a 2×2 matrix with complex eigenvalues, the real part is $\tfrac12\operatorname{tr}J$. So a stable spiral loses its stability exactly when the trace of $J$ passes through zero.

### Two ways to start firing

Now imagine slowly increasing the injected current $I$. At some point the resting state must stop being the answer, because the neuron starts to fire repetitively. There are two generic ways in which this can happen, and they look very different from outside.

In the first, a pair of complex eigenvalues crosses the imaginary axis, and the resting state starts to spiral outwards. This is a **Hopf bifurcation**. The oscillation that appears has a *finite frequency* from the start, set by the imaginary part of $\lambda$.

In the second, the stable resting state collides with a nearby unstable *saddle* point, and both disappear. What remains is a closed loop, the old path of a spike. Just past the collision, the system still crawls very slowly through the place where the fixed points used to be (their "ghost"). The firing rate therefore starts from *zero* and grows as $f\propto\sqrt{I-I_c}$. This is a **saddle-node on an invariant circle (SNIC)**.

Real neurons come in both kinds. Neurons that can fire arbitrarily slowly are called **class I**, and those that jump straight to a minimum rate are called **class II** [3]. These are the SNIC and Hopf cases.

### The Morris–Lecar model

FitzHugh–Nagumo is a caricature: its variables are not measurable quantities. To see both kinds of onset in a model built from real currents, let us use one that keeps the circuit picture of the first section but has only two variables. In the **Morris–Lecar** model, the inward current is carried by calcium channels that open so quickly that we can treat them as instantaneous. Their open fraction is simply a function of voltage, $m_\infty(V)$. The outward current is carried by potassium channels that open more slowly. Their open fraction $w$ relaxes towards its steady value $w_\infty(V)$ with a voltage-dependent time constant $\tau_w(V)$:

$$C\frac{dV}{dt}=I-g_L(V-V_L)-g_{Ca}\,m_\infty(V)(V-V_{Ca})-g_K\,w\,(V-V_K),\qquad \frac{dw}{dt}=\phi\,\frac{w_\infty(V)-w}{\tau_w(V)}.$$

The steady-state curves are smooth steps, and the time constant is a bell shape:

$$m_\infty(V)=\tfrac12\Big[1+\tanh\frac{V-V_1}{V_2}\Big],\qquad w_\infty(V)=\tfrac12\Big[1+\tanh\frac{V-V_3}{V_4}\Big],\qquad \tau_w(V)=\frac{1}{\cosh\big((V-V_3)/2V_4\big)}.$$

Remarkably, changing only the shape of the potassium curve ($V_3$, $V_4$), the calcium conductance and the speed $\phi$ switches the neuron between class I and class II [1, 5]:

| Parameter | Shared value | | Parameter | Class I | Class II |
|---|---|---|---|---|---|
| $C$ | 20 µF/cm² | | $g_{Ca}$ | 4.0 mS/cm² | 4.4 mS/cm² |
| $g_L$, $g_K$ | 2, 8 mS/cm² | | $V_3$ | 12 mV | 2 mV |
| $V_L$, $V_K$, $V_{Ca}$ | −60, −84, 120 mV | | $V_4$ | 17.4 mV | 30 mV |
| $V_1$, $V_2$ | −1.2, 18 mV | | $\phi$ | 1/15 | 0.04 |

## Questions

Submit one notebook or script, the plots, and a short write-up (at most one page) answering the *Explain* parts.

**Q1. Nullclines and the rest state (FHN, $I=0$).**
- *Predict (by hand):* find the fixed point $(v_\ast,w_\ast)$ numerically by intersecting the two nullclines, and compute the eigenvalues of $J$ there. Is it a node or a spiral? Stable or unstable?
- *Build:* plot both nullclines, the vector field, and two trajectories: one starting from the fixed point kicked by $\Delta v=0.3$, one kicked by $\Delta v=0.7$. Also plot $v(t)$ for both.
- *Explain:* where exactly is the threshold in this plot? Why is there no single "threshold voltage"?

**Q2. The Hopf current.**
- *Predict:* show that the trace of $J$ vanishes when $v_\ast^2=1-\epsilon b$. Use the nullclines to turn this into a value of the current $I_H$.
- *Build:* compute the largest real part of the eigenvalues as a function of $I$ and find where it crosses zero. Compare with your prediction.
- *Explore:* for each $I$ from 0 to 1.6, simulate long enough to lose transients and record the peak-to-peak amplitude of $v$. Plot amplitude against $I$.
- *Explain:* does the amplitude grow from zero like $\sqrt{I-I_H}$? Start one simulation from a large initial condition just *below* $I_H$: what happens, and what does that tell you about the kind of Hopf bifurcation? Why does the oscillation stop again at large $I$?

**Q3. Class I vs class II (Morris–Lecar).**
- *Predict:* for the class-I parameters, find the current at which the two lower fixed points merge. Hint: at a fixed point $dw/dt=0$, so $w=w_\infty(V)$. The current needed to hold the voltage at $V$ is then a function $I(V)$, and a fold is a local maximum of $I(V)$.
- *Build:* compute the firing rate against $I$ for both parameter sets. Sweep $I$ upwards and then downwards, each time starting from the end state of the previous run.
- *Explain:* which curve starts from zero rate, and which jumps? Does the up-sweep match the down-sweep? Relate both observations to the section "Two ways to start firing".

## References

[1] B. Ermentrout and D. Terman, *Mathematical Foundations of Neuroscience*, Springer (2010), ch. 3.
[2] S. H. Strogatz, *Nonlinear Dynamics and Chaos*, 2nd ed., CRC Press (2015), ch. 3, 6–8.
[3] E. M. Izhikevich, *Dynamical Systems in Neuroscience: The Geometry of Excitability and Bursting*, MIT Press (2007), ch. 1–7.
[4] W. Gerstner, W. M. Kistler, R. Naud and L. Paninski, *Neuronal Dynamics*, Cambridge University Press (2014), ch. 4. Free online: [neuronaldynamics.epfl.ch](https://neuronaldynamics.epfl.ch).
[5] J. Rinzel and G. B. Ermentrout, "Analysis of neural excitability and oscillations", in C. Koch and I. Segev (eds.), *Methods in Neuronal Modeling*, 2nd ed., MIT Press (1998), ch. 7.
