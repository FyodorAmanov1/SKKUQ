# SKKUQ · Learning Logic with Quantum Circuits

**What can a parameterized circuit learn when its measured qubit cannot access both inputs?**

This notebook study explores AND, OR and XOR through different quantum-circuit architectures. It combines truth-table experiments with matrix calculations to investigate input encoding, inter-qubit operations and optimization behavior.

## Start here

Open [main.ipynb](main.ipynb) for the expanded study. [main original.ipynb](main%20original.ipynb) retains the earlier notebook for comparison.

| Experiment | Question |
|---|---|
| Local single-qubit rotations | How does a disconnected architecture restrict observable information? |
| Controlled-gate architecture | How do interactions change the possible input–output behavior? |
| Angle encoding | How does the input representation change the circuit response? |
| MSE and cross-entropy | How do loss functions affect a finite-shot optimization experiment? |

The notebook builds circuits, samples measurement counts with Aer, optimizes parameters with SciPy and presents truth-table predictions and circuit visualizations.

## A useful architectural observation

In the separable example, each input is encoded on its own qubit, only local rotations follow, and only the second qubit is measured. Its output distribution therefore depends only on that qubit's input. Changing the other input cannot change the exact measurement probability.

This restriction prevents that particular architecture from representing any truth table requiring both inputs, including AND, OR and XOR. It is stronger and more specific than an analogy to the linear separability of classical logistic regression.

Some explanatory cells in the notebook make broader claims about entanglement and learning. Read those claims against the actual circuit and measurement definition: the experiment does not establish a universal requirement for entanglement, a general expressivity theorem or quantum advantage.

## Environment

Use Jupyter with compatible Qiskit and Aer versions. The source also imports NumPy, SciPy, Matplotlib and pandas.

```bash
python -m pip install jupyterlab numpy scipy matplotlib pandas qiskit qiskit-aer
python -m jupyter lab
```

Select `main.ipynb`, run the setup cells and inspect the circuit definitions before launching the optimization sections. There is no pinned dependency lockfile. Optimizer initialization and finite-shot estimates introduce variation, so a rerun need not reproduce an old displayed table exactly.

## Reading the results

Compare the full four-row truth table rather than only a final loss. Distinguish exact architectural constraints from finite-shot fluctuations and optimization failures. A failed fit alone does not prove lack of expressivity; a fitted truth table alone does not establish better learning efficiency than a classical model.

This repository is an educational circuit-analysis study rather than a benchmark on a real-world prediction dataset.
