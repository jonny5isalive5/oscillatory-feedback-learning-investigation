# Research note 001: Oscillatory feedback-learning dynamics

**Record date:** 2026-11-07  
**Status:** preliminary, handover-derived research record  
**Repository visibility:** private  
**Scope:** preserve the equations, results, derivation, limitations, and provenance present in the cited conversation. This note is not a reproduction, proof, novelty claim, or public paper.

## Provenance

Primary source: ChatGPT conversation **Kep 3.0 Handover Welcome**, conversation ID **6ae8db26-6d08-83eb-9cdb-a28878efb8bd**. The source conversation was last updated on 2026-11-07 (UTC).

Relevant assistant messages:

- Experiment 015, “Learning-rate intervention”: message ID **d4d7360a-3ca5-4f54-8bb5-bb2a6be4608b**.
- Mathematical investigation 001, “Is oscillation built into the learning dynamics?”: message ID **eaee6a30-0b35-4ff1-8719-0d7b9ed8eb16**.

This note records what those messages report. No source code, raw run logs, seeds, plots, frozen manifest, or complete earlier experiment records were attached to the handover excerpt. Numerical outcomes below are therefore conversation-reported and have not been independently reproduced here. Missing information is marked rather than reconstructed.

## Question and current reading

The investigation asks whether oscillation can arise from the interaction of an adaptive weight update and feedback, without adding a separate oscillation mechanism. The reported learning-rate intervention changed only the learning rate while holding the prime-ratio rule, feedback equation, floor, target, and initial conditions fixed.

The long run reports repeated target crossings at all three learning rates extended to 100,000 cycles. The short run’s apparent suppression at two rates did not persist. The local constant-ratio analysis below identifies a neutral oscillatory mode under its assumptions. Together these make oscillation a plausible feature to investigate in this system; they do not establish that oscillation is necessary for learning or that the full nonlinear, time-varying system must oscillate indefinitely.

## Frozen-system record and parameter inventory

Experiment 015 is described as keeping the frozen architecture unchanged and varying only the learning rate \(\eta\). The handover reports the core equations as

$$
y_n = w_n x_n
$$

$$
w_{n+1}=w_n+\eta(t-w_nx_n)x_n
$$

The feedback update is described as using a changing prime ratio. Writing that changing ratio symbolically as \(a_n\), its described form is

$$
x_{n+1}=a_n w_nx_n
$$

The handover does not provide the exact formula, indexing convention, or sequence for \(a_n\). The mathematical investigation replaces it temporarily with a constant \(a\):

$$
x_{n+1}=a w_nx_n
$$

That constant-\(a\) system is an analysis approximation, not a claim that the original trajectory used a constant ratio.

| Quantity | What the handover supplies |
|---|---|
| Learning rate \(\eta\) | Varied over 0.1, 0.02, 0.01, 0.005, 0.002, 0.001, and 0.0005. |
| Target \(t\) | Held constant across Experiment 015, but its numeric value is not directly stated. The reported late-time period table is consistent with \(t=4\) when \(a\to1\); this is an inference from the equations and table, not a source-confirmed baseline value. |
| Prime ratio \(a_n\) | Described as changing and approaching 1 at late time. Its exact definition and finite-time schedule are absent. |
| Input floor | Present in the frozen system; it sometimes activates in the long simulations. Its value and activation counts are absent. It is assumed inactive for the local derivation. |
| Initial conditions | Reported as identical across learning-rate runs; numeric values are absent. |
| Other run details | Cycle counts are reported below. Seeds, run repetitions, code revision, numeric precision, and raw logs are not supplied. |

## Experiment 015: learning-rate intervention

The first results were measured after 500 cycles:

| \(\eta\) | Target crossings | Final output |
|---:|---:|---:|
| 0.1 | 200 | 3.846 |
| 0.02 | 62 | 2.565 |
| 0.01 | 19 | 5.533 |
| 0.005 | 6 | 1.168 |
| 0.002 | 0 | 0.090 |
| 0.001 | 0 | 0.070 |
| 0.0005 | Not reported at 500 cycles | Not reported at 500 cycles |

The apparent suppression at \(\eta=0.002\) and \(0.001\) was a temporary short-run observation, not evidence that oscillation had been eliminated. Those two rates and the still lower rate were extended to 100,000 cycles:

| \(\eta\) | Target crossings over 100,000 cycles | Final output |
|---:|---:|---:|
| 0.002 | 5,110 | 3.375 |
| 0.001 | 3,092 | 2.459 |
| 0.0005 | 1,410 | 1.531 |

The conversation interprets this as lower learning rates delaying and slowing the dynamics rather than eliminating oscillation within these runs. No valid non-oscillatory control was reported.

## Mathematical investigation 001: constant-\(a\) local analysis

Assume \(x>0\), \(a>0\), and that the minimum-input floor is inactive. Consider

$$
F(x,w)=
\left(
a w x,\;
w+\eta(t-wx)x
\right).
$$

At a positive fixed point, \(x^*=a w^*x^*\) gives \(w^*=1/a\). The weight fixed-point condition gives \(w^*x^*=t\), so

$$
w^*=\frac{1}{a}, \qquad x^*=at.
$$

Using state order \((x,w)\), the Jacobian is

$$
J(x,w)=
\begin{bmatrix}
a w & a x \\
\eta(t-2wx) & 1-\eta x^2
\end{bmatrix}.
$$

At the fixed point,

$$
J^*=
\begin{bmatrix}
1 & a^2t \\
-\eta t & 1-\eta a^2t^2
\end{bmatrix}.
$$

Its determinant is

$$
\det(J^*)=
1-\eta a^2t^2+\eta a^2t^2=1.
$$

Let \(q=\eta a^2t^2\). The trace is \(2-q\), and the characteristic polynomial is

$$
\lambda^2-(2-q)\lambda+1=0.
$$

For \(0<q<4\), the eigenvalues are a complex-conjugate pair with unit modulus. Equivalently, their angle \(\theta\) satisfies

$$
\cos\theta=1-\frac{q}{2}.
$$

The constant-\(a\) linearization therefore has a neutral oscillatory mode and no local contraction of area at that fixed point. Its predicted full linearized period is \(2\pi/\theta\). This local result does not prove a conserved quantity for the nonlinear system, persistence along a changing \(a_n\), or sustained oscillation in every trajectory.

### Period comparison reported in the conversation

The late-time prediction assumes \(a\to1\). The values below follow the reported table; numerically, that table is consistent with \(t=4\) in the local formula, although the source excerpt does not state \(t\) directly.

| \(\eta\) | Predicted full period (cycles) |
|---:|---:|
| 0.1 | 4.59 |
| 0.02 | 10.96 |
| 0.01 | 15.60 |
| 0.005 | 22.14 |
| 0.002 | 35.08 |
| 0.001 | 49.64 |
| 0.0005 | 70.22 |

The reported measured median spacing between target crossings in the final 10,000 cycles of the longer runs was compared with half of the predicted full period:

| \(\eta\) | Measured median crossing spacing | Predicted half-period spacing |
|---:|---:|---:|
| 0.002 | 16 | 17.54 |
| 0.001 | 21 | 24.82 |
| 0.0005 | 25 | 35.11 |

The reported direction agrees, while the match is imperfect and worsens at the lowest rate. The handover itself attributes this limitation to the nonlinear trajectory, changing prime ratio, and occasional activation of the input floor.

## Failed, superseded, and unavailable evidence

- The 500-cycle “no crossings” observation at \(\eta=0.002\) and \(0.001\) is retained as a short-run result. Any interpretation that it showed oscillation had been removed is superseded by the 100,000-cycle results.
- The handover reports that no valid non-oscillatory control was obtained within the frozen architecture.
- The source excerpt does not identify any other test as withdrawn, and it contains no results for the earlier numbered experiments. Their status and outcomes are unknown here; this note does not infer them.

## Limitations and unresolved questions

1. The exact frozen prime-ratio rule, target value, floor value, initial state, random seed or deterministic run identity, and raw results are not in the handover excerpt.
2. The determinant result is local to a positive equilibrium of the constant-\(a\), floor-inactive map. The actual system uses a changing ratio and can activate the floor.
3. Unit-modulus eigenvalues describe the linearized map near that equilibrium. They do not by themselves prove long-term nonlinear behavior, an invariant, or oscillation as a necessary condition for learning.
4. The source excerpt gives one reported summary row per learning rate but does not state the replicate count, run-level uncertainty, or any independent reproduction.
5. No literature review or independent mathematical review is included. No novelty, priority, or proof claim is made.

## Provenance and record boundary

Prepared on 2026-11-07 from the cited bounded conversation excerpt. The repository records the statements and calculations available in that source and labels the inferred \(t=4\) separately. It does not contain or modify baseline code, experiment files, or a frozen manifest.

This private Git history is a versioned record of this note. By itself it is not an independent legal timestamp, patent filing, proof of authorship, or public disclosure.
