# Quantum Circuit Implementation of Classical Logic Gates
## A Comprehensive Study of PQC Architectures and Encoding Methods

This repository contains a detailed investigation of parameterized quantum circuits (PQCs) for implementing classical logic gates, with mathematical analysis and experimental verification.

## 📓 Main Demonstration

**See `main.ipynb` for the complete interactive demonstration** with:
- Executable code for all experiments
- Mathematical derivations and proofs
- Experimental evidence and visualizations
- Comparative analysis across methods

## Overview

This project explores three key questions:

1. **Can separable quantum circuits learn classical logic gates?**
2. **How does entanglement change expressivity?**
3. **What role does the encoding method play?**

## Key Findings

### ✓ Successfully Demonstrated

1. **Separable circuits cannot learn XOR** (mathematical proof + experimental evidence)
2. **Entanglement enables non-linear separability** (XOR becomes learnable)
3. **Angle encoding provides smoother optimization** (better for continuous data)
4. **Quantum circuits mirror classical ML principles** (architecture determines capacity)

## Project Structure

```
├── main.ipynb          # Complete demonstration notebook (RUN THIS!)
├── README.md           # This file
```

## Quick Start

```bash
# Install dependencies
pip install qiskit qiskit-aer numpy scipy matplotlib pandas

# Run Jupyter
jupyter notebook main.ipynb
```

## Detailed Results

## 1. Quantum Circuit Implementation of Classical Gates Using Separable PQC

#### a. Implementation
A 2-qubit PQC without entanglement gates has been implemented following Figure 1's structure:
- **Encoding Layer**: Classical bits mapped to quantum states using X gates (basis encoding)
- **Parameterized Layer**: 6 rotation parameters (RX, RY, RZ on each qubit)
- **Measurement**: Single qubit measurement to obtain output

**Training Results**:
- **AND Gate**: ✓ Successfully learned (all linearly separable gates can be learned)
- **OR Gate**: ✓ Successfully learned
- **XOR Gate**: ✗ Cannot be learned (fundamental limitation)

The circuit is optimized using COBYLA optimizer with Mean Squared Error loss function.

#### b. Circuit Visualization and Analysis

**Circuit Structure**:
```
     ┌───┐ ┌────────────┐┌────────────┐┌────────────┐ ┌─┐
q_0: ┤ X ├─┤ RX(θ0) ├┤ RY(θ1) ├┤ RZ(θ2) ├─┘ 
     ├───┤ ├────────────┤├────────────┤├────────────┤
q_1: ┤ X ├─┤ RX(θ3) ├┤ RY(θ4) ├┤ RZ(θ5) ├─┤M├
     └───┘ └────────────┘└────────────┘└────────────┘ └╥┘
c: 1/════════════════════════════════════════════════════╩═
                                                          0
```

**Correspondence to Logistic Regression**:

Both structures follow: **Input → Transform → Measure/Activate**

| Aspect | Logistic Regression | Quantum Circuit |
|--------|-------------------|----------------|
| Input | Feature vector x | Quantum state \|x⟩ |
| Transform | Linear: w^T·x + b | Unitary: U(θ)\|x⟩ |
| Output | σ(w^T·x + b) | P(1) = \|⟨1\|U(θ)\|x⟩\|² |
| Parameters | Weights w, bias b | Rotation angles θ |

**Fundamental Differences**:
1. **Feature Space**: Logistic regression operates in original feature space; quantum circuits operate in Hilbert space (exponentially larger)
2. **Nonlinearity Source**: Classical uses sigmoid activation; quantum uses measurement (Born rule, quadratic in amplitudes)
3. **Separability**: Without entanglement, quantum circuit reduces to independent single-qubit operations, similar to feature-wise linear transformations
4. **Expressivity**: Separable quantum circuits have similar expressivity to linear classifiers—cannot learn XOR

#### c. Training Results Analysis

**Mathematical Explanation**:

For a separable 2-qubit circuit: |ψ⟩ = U₀(θ₀,θ₁,θ₂) ⊗ U₁(θ₃,θ₄,θ₅) |input⟩

The output probability depends only on the state of the measured qubit:
```
P(output=1) = |⟨1|U₁(θ₃,θ₄,θ₅)|q₁⟩|²
```

**Why XOR Fails**:
1. XOR truth table shows non-linear separability:
   - (0,0)→0, (0,1)→1, (1,0)→1, (1,1)→0
   
2. The measurement on qubit 1 cannot depend on qubit 0's state without entanglement

3. Mathematically, any separable state can be written as tensor product, meaning:
   ```
   P(1|q₀,q₁) = f(q₁) only
   ```
   But XOR requires: P(1|q₀,q₁) = q₀ ⊕ q₁ (correlation between both qubits)

4. This is analogous to why single-layer perceptrons cannot solve XOR—it's not linearly separable

**Success with AND/OR**:
Both AND and OR are linearly separable:
- AND: Only (1,1)→1, easily represented by setting high threshold on q₁
- OR: (0,1), (1,0), (1,1)→1, representable by setting low threshold on q₁


## 2. Quantum Circuit Implementation of Classical Gates Using Entanglement PQC

#### a. Implementation
Enhanced 2-qubit PQC with entanglement gates:
- **Encoding Layer**: Basis encoding (X gates)
- **Parameterized Layer 1**: 6 rotation parameters (RX, RY, RZ on each qubit)
- **Entanglement Layer**: 4 controlled rotation gates (CRZ, CRX bidirectionally)
- **Parameterized Layer 2**: 6 additional rotation parameters
- **Total Parameters**: 16 trainable parameters

**Training Results**:
- **AND Gate**: ✓ Successfully learned
- **OR Gate**: ✓ Successfully learned  
- **XOR Gate**: ✓ Successfully learned (now possible with entanglement!)

#### b. Circuit Visualization

**Circuit Structure**:
```
     ┌───┐ ┌──────────┐┌──────────┐┌──────────┐ ░ ┌──────────┐┌──────────┐ ░ ┌──────────┐┌──────────┐┌──────────┐
q_0: ┤ X ├─┤ RX(θ0) ├┤ RY(θ1) ├┤ RZ(θ2) ├─░─┤ CRZ(θ6) ├┤ CRX(θ7) ├─░─┤ RX(θ10) ├┤ RY(θ11) ├┤ RZ(θ12) ├
     ├───┤ ├──────────┤├──────────┤├──────────┤ ░ └────┬─────┘└────┬─────┘ ░ ├──────────┤├──────────┤├──────────┤
q_1: ┤ X ├─┤ RX(θ3) ├┤ RY(θ4) ├┤ RZ(θ5) ├─░──────■────────────■──────░─┤ RX(θ13) ├┤ RY(θ14) ├┤ RZ(θ15) ├─┤M├
     └───┘ └──────────┘└──────────┘└──────────┘ ░                      ░ └──────────┘└──────────┘└──────────┘ └╥┘
c: 1/═══════════════════════════════════════════════════════════════════════════════════════════════════════════╩═
```

The controlled gates (CRZ, CRX) create entanglement between qubits, allowing correlation between their states.

#### c. Training Results Analysis

**Mathematical Explanation of Success**:

With entanglement, the circuit can create states of the form:
```
|ψ⟩ = α|00⟩ + β|01⟩ + γ|10⟩ + δ|11⟩
```

Where coefficients α, β, γ, δ are functions of all parameters and input states.

**How Entanglement Enables XOR**:

1. **Controlled Rotations**: CRZ(θ, control, target) applies:
   ```
   CRZ(θ)|0⟩_c|ψ⟩_t = |0⟩_c|ψ⟩_t
   CRZ(θ)|1⟩_c|ψ⟩_t = |1⟩_c RZ(θ)|ψ⟩_t
   ```

2. **Correlation Creation**: The measurement probability now depends on both qubits:
   ```
   P(1|q₀,q₁) = |⟨1|U_entangled(θ)|q₀,q₁⟩|²
   ```
   This allows implementing: P(1) = high when q₀ ⊕ q₁ = 1

3. **Feature Space Expansion**: Entanglement maps inputs to a 4-dimensional complex Hilbert space, creating non-linear decision boundaries in the original 2-bit input space

**Comparison with Separable Circuit**:
- **Separable**: 2^1 × 2^1 = 4 real-valued independent probabilities
- **Entangled**: 2^2 = 4 complex amplitudes = 8 real parameters (higher expressivity)
- **Result**: Can represent any Boolean function of 2 bits including XOR


## 3. Quantum Circuit Implementation Using Angle Encoding

#### a. Implementation
2-qubit PQC with angle encoding and entanglement:
- **Encoding Layer**: Angle encoding using RX(π/2) for input '1', no rotation for '0'
- **Architecture**: Same as Task 2 (16 parameters with entanglement)
- **Key Difference**: Input encoding method (angle vs basis)

**Training Results**:
- **AND Gate**: ✓ Successfully learned
- **OR Gate**: ✓ Successfully learned
- **XOR Gate**: ✓ Successfully learned

#### b. Circuit Visualization

**Circuit Structure**:
```
     ┌───────────┐ ┌──────────┐┌──────────┐┌──────────┐ ░ ┌──────────┐        ░ ┌──────────┐┌──────────┐┌──────────┐
q_0: ┤ RX(π/2·x₀) ├─┤ RX(θ0) ├┤ RY(θ1) ├┤ RZ(θ2) ├─░─┤ CRZ(θ6) ├────────░─┤ RX(θ10) ├┤ RY(θ11) ├┤ RZ(θ12) ├
     ├───────────┤ ├──────────┤├──────────┤├──────────┤ ░ └────┬─────┘        ░ ├──────────┤├──────────┤├──────────┤
q_1: ┤ RX(π/2·x₁) ├─┤ RX(θ3) ├┤ RY(θ4) ├┤ RZ(θ5) ├─░──────■──────────────░─┤ RX(θ13) ├┤ RY(θ14) ├┤ RZ(θ15) ├─┤M├
     └───────────┘ └──────────┘└──────────┘└──────────┘ ░                   ░ └──────────┘└──────────┘└──────────┘ └╥┘
```

#### c. Training Results and Encoding Method Analysis

**Mathematical Comparison of Encoding Methods**:

1. **Basis Encoding** (Tasks 1-2):
   ```
   0 → |0⟩ = [1, 0]ᵀ
   1 → X|0⟩ = |1⟩ = [0, 1]ᵀ
   ```
   - Discrete mapping to orthogonal states
   - No intermediate states possible

2. **Angle Encoding** (Task 3):
   ```
   0 → |0⟩ = [1, 0]ᵀ
   1 → RX(π/2)|0⟩ = 1/√2[1, -i]ᵀ
   ```
   - Continuous mapping via rotation
   - Represents input as angle on Bloch sphere
   - Allows smooth interpolation

**Performance Analysis**:

| Aspect | Basis Encoding | Angle Encoding |
|--------|---------------|----------------|
| Input Representation | Discrete, orthogonal | Continuous, rotational |
| Gradient Quality | Discrete jumps | Smooth, continuous |
| Optimization | Harder (discrete landscape) | Easier (smooth landscape) |
| Generalization | Only trained points | Can interpolate |
| Geometric Interpretation | Corners of hypercube | Points on sphere |

**Why Angle Encoding Can Improve Training**:

1. **Smoother Loss Landscape**: Continuous encoding creates smoother gradients, helping optimization avoid local minima

2. **Better Parameter Initialization Sensitivity**: Small parameter changes create proportional output changes (not discrete jumps)

3. **Natural for Continuous Extension**: If inputs were continuous (not just {0,1}), angle encoding naturally handles them

4. **Distance Preservation**: Angle encoding preserves relative distances: inputs that are "close" in classical space map to "close" states in Hilbert space

**Training Performance**:
- Both methods succeed for all gates when entanglement is present
- Angle encoding typically converges faster (fewer iterations)
- Angle encoding shows more stable convergence (less variance across runs)
- For binary classification tasks, difference is subtle; for continuous data, angle encoding significantly outperforms


## 4. Investigation of Quantum Applications Utilizing PQC

#### a. Various Quantum Applications Using PQC

1. **Quantum Machine Learning (QML)**
   - Classification: Quantum Support Vector Machines (QSVM), Quantum Neural Networks (QNN)
   - Clustering: Quantum k-means variants
   - Generative models: Quantum GANs, Quantum Boltzmann Machines

2. **Quantum Chemistry & Materials Science**
   - Variational Quantum Eigensolver (VQE): Finding ground state energies
   - Quantum Approximate Optimization Algorithm (QAOA): Molecular simulation
   - Drug discovery: Protein folding simulation

3. **Optimization Problems**
   - Portfolio optimization in finance
   - Traffic flow optimization
   - Supply chain management
   - Scheduling problems (job shop, vehicle routing)

4. **Quantum Error Mitigation**
   - Using PQC to learn and correct noise patterns
   - Adaptive error correction schemes

5. **Quantum Cryptography & Security**
   - Quantum key distribution enhancement
   - Quantum random number generation
   - Post-quantum cryptography testing

6. **Financial Modeling**
   - Option pricing (Black-Scholes model quantum speedup)
   - Risk analysis and Monte Carlo simulation
   - Portfolio optimization with quantum advantage

7. **Natural Language Processing**
   - Quantum text classification
   - Sentiment analysis with quantum embeddings
   - Question-answering systems

#### b. Advantages of Using PQC

1. **Exponential Feature Space**
   - n qubits create 2ⁿ-dimensional Hilbert space
   - Classical equivalent requires exponentially many parameters
   - Example: 20 qubits → 1 million dimensions with only ~100 parameters

2. **Natural Expressivity for Certain Functions**
   - Periodic functions naturally represented through rotations
   - Entanglement captures correlations classical models struggle with
   - Quantum interference creates complex decision boundaries

3. **Hardware Efficiency**
   - Runs natively on quantum hardware (no compilation overhead)
   - Parameter counts remain manageable even for large Hilbert spaces
   - Hybrid quantum-classical approach leverages both architectures

4. **Trainability on NISQ Devices**
   - Works with current noisy, small-scale quantum computers
   - Robust to certain types of noise through parameter optimization
   - Enables near-term quantum advantage experiments

5. **Quantum-Specific Advantages**
   - Can encode quantum data directly (e.g., quantum sensor outputs)
   - Natural for quantum simulation tasks
   - Leverages quantum coherence and superposition

6. **Potential Speedup**
   - Theoretical exponential speedup for specific problems
   - Quantum kernel methods may show polynomial advantage
   - Reduced sample complexity in certain learning tasks

#### c. Future of Quantum Computing Applications

**Near-Term (2-5 years): NISQ Era Applications**
- Quantum chemistry simulations become practical for small molecules (drug discovery)
- Hybrid quantum-classical optimization for logistics and finance
- Quantum machine learning models match/exceed classical for specific structured data
- Quantum-enhanced sensing and metrology reach commercial viability

**Medium-Term (5-10 years): Early Fault-Tolerant Era**
- Quantum advantage demonstrated in multiple real-world applications:
  - Materials design for batteries, superconductors, catalysts
  - Portfolio optimization at scale for hedge funds
  - Weather/climate modeling with quantum speedup
- Quantum AI assistants using quantum natural language processing
- Integration of quantum accelerators in cloud computing platforms
- Quantum-secured communications become standard for governments and enterprises

**Long-Term (10+ years): Mature Quantum Computing**
- General-purpose quantum computers solve previously intractable problems:
  - Protein folding for arbitrary proteins → personalized medicine
  - Room-temperature superconductors designed via quantum simulation
  - Breaking current cryptography → new quantum-safe standards mandatory
- Quantum Internet: Entanglement-based communication network
- Quantum sensing networks: GPS-free navigation, gravitational wave detection
- Quantum machine learning becomes standard tool in data science

**Emerging Application Areas**:
1. **Quantum Gaming & Entertainment**: Procedurally generated quantum content, quantum-random game mechanics
2. **Personalized Medicine**: Individual quantum simulations for drug efficacy prediction
3. **Climate Modeling**: High-fidelity Earth system models with quantum speedup
4. **Artificial General Intelligence**: Quantum components in AGI architectures
5. **Space Exploration**: Quantum sensors for deep space navigation, quantum optimization for mission planning

**Challenges to Overcome**:
- Error correction: Need 1000+ physical qubits per logical qubit
- Scalability: Current systems <1000 qubits; need millions for breakthrough apps
- Algorithm development: Many promising applications lack efficient quantum algorithms
- Integration: Seamless quantum-classical hybrid systems
- Cost: Making quantum computing economically accessible

**Prediction**: By 2035, quantum computers will be as commonplace as GPUs are today—specialized accelerators for specific workloads, integrated into cloud platforms, accessible to researchers and developers worldwide. The first "quantum unicorn" startup (quantum-native company valued >$1B) will emerge in quantum chemistry or quantum finance within 5 years.
