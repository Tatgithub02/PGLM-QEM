---

## Noise Model

Two-tier strategy:

1. Attempt to load a Qiskit fake backend (FakeLima / FakeTokyo / FakeJakarta) 
   to inherit real IBM hardware T1/T2/readout calibration
2. If unavailable, construct manually:
   - 0.2% depolarizing error on single-qubit gates
   - 2.0% depolarizing error on two-qubit gates (cx, cz)
   - 2.0% readout error per qubit

All simulations use 1024 shots with seeded simulators for reproducibility.

---

## Results

| Circuit Size | Raw RMSE | PGLM RMSE | Improvement |
|-------------|----------|-----------|-------------|
| 1-qubit     | baseline | worse     | -127.3%     |
| 2-qubit     | baseline | better    | +17.8%      |
| 3-qubit     | baseline | better    | +50.1%      |
| 4-qubit     | baseline | better    | +32.3%      |
| Overall (with fallback) | baseline | better | +32.6% |

Single-qubit circuits degrade because most features collapse when n_cx = 0. 
The fallback strategy uses raw noisy values for 1-qubit circuits and PGLM 
for all others.

**Learned coefficients (example run):**

| Feature          | Coefficient | Physical interpretation                        |
|-----------------|-------------|-----------------------------------------------|
| 1 (bias)        | -0.022      | baseline offset                               |
| y_noisy         | +0.569      | systematic overestimation by noise            |
| y_noisy * n_cx  | -0.028      | CNOT gates reduce expectation magnitude       |
| y_noisy * depth | +0.068      | depth-dependent decoherence correction        |
| y_noisy * eps_ro| +0.011      | readout error scaling                         |
| n_cx            | -0.003      | structural bias from CNOT count               |
| depth           | +0.002      | structural bias from circuit depth            |
