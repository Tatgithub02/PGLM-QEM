# PGLM-QEM: Physics-Guided Linear Mapper for Quantum Error Mitigation

A lightweight, interpretable quantum error mitigation framework that encodes 
depolarizing noise physics directly into a feature vector and applies ridge 
regression to recover ideal expectation values from noisy quantum circuit 
measurements.

Accepted at Computing Conference 2026, Springer Lecture Notes in Networks 
and Systems (LNNS).

---

## Overview

Existing quantum error mitigation methods either require prohibitive sampling 
overhead (Zero-Noise Extrapolation, Probabilistic Error Cancellation) or 
thousands of training circuits and hours of training time (neural network 
approaches). PGLM addresses both problems by encoding known noise physics 
into seven hand-designed features, then fitting a ridge regression model with 
a closed-form solution.

**Key properties:**
- 100 training circuits vs 1,000-10,000 for neural network approaches
- Training time under 3 seconds on standard hardware
- Sub-millisecond inference per circuit
- 5 KB model size vs hundreds of megabytes for neural networks
- Interpretable coefficients that confirm the underlying noise physics
- 32.6% overall RMSE reduction with circuit-size-aware fallback strategy

---

## The Feature Vector

PGLM constructs a 7-dimensional physics-informed feature vector per circuit:

    phi = [1, y_noisy, y_noisy * n_cx, y_noisy * depth, y_noisy * eps_ro, n_cx, depth]

where:
- `y_noisy` is the measured noisy expectation value
- `n_cx` is the CNOT gate count extracted from the transpiled circuit
- `depth` is the circuit depth after transpilation
- `eps_ro` is the average readout error rate for measured qubits

Each term is physically motivated. Depolarizing noise corrupts expectation 
values multiplicatively: E[O]_noisy ~ (1-p)^n_gates * E[O]_ideal. This means 
the correction depends on the noisy value interacting with circuit structure, 
not just the noisy value alone.

---
