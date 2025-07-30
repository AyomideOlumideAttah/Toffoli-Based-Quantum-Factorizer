# ⚡ Efficient Integer Factorization (Shor's Algorithm) with Toffoli Gates⚡

## Overview

This project is an implementation of a Toffoli-based modular exponentiation gate, a core component of Shor's algorithm for facotirng large integers. The implementation follows the principles outlined in the research paper "Factoring using 2n+2 qubits with Toffoli based modular multiplication" by Thomas Häner, Martin Roetteler, and Krysta M. Svore.

The primary goal of this implementation is to provide a space-efficient quantum circuit for modular multiplication that relies entirely on Toffoli gates. This approach offers several advantages over traditional Fourier-based arithmetic, including enhanced testability, easier debugging, and the avoidance of overheads associated with single-qubit rotation synthesis when quantum error correction (QEC) is employed.

## Key Features of the Implementation

The notebook constructs the modular exponentiation circuit from several fundamental quantum arithmetic components:

* **In-place Carry Circuit:** This circuit computes the most significant bit ($r_{n-1}$) of an $n$-bit addition $r = b + c$, where $c$ is a classical $n$-bit integer and $b$ is stored in a quantum register. It utilizes $(n-1)$ dirty ancilla qubits, carrying out the transformation $|b\rangle|g\rangle|0\rangle \rightarrow |b\rangle|g\rangle|(b + c)_n\rangle$.

* **In-place Quantum Adder:** This component performs the transformation $|b\rangle \rightarrow |(b+c)\rangle$, where $|b\rangle$ is an $n$-bit quantum register and $c$ is an $n$-bit classical integer (the final carry is ignored). A recursive implementation, involving an $n$-bit incrementer and the strategic use of multiple dirty ancilla qubits, achieves an $\mathcal{O}(n \log n)$ gate complexity. The adder leverages a "VanRentergem Subroutine" for subtraction to achieve incrementation.

* **In-place Quantum Modular Adder:** This circuit implements the transformation $|b\rangle \rightarrow |(b + c) \pmod N\rangle$, where $|b\rangle$ is an $n$-bit register, and $c$ and $N$ are $n$-bit classical integers. It combines the `CMP` (comparator) circuit with the (controlled) quantum addition circuit.

* **Modular Multiplication Circuit:** This circuit achieves the transformation $|x\rangle|0\rangle \rightarrow |x\rangle|ax \pmod N\rangle$. It employs a "repeated add-and-shift" technique, viewing modular multiplication as a series of controlled modular additions based on the identity $ax \pmod N = \sum_{i=0}^{n-1} (a2^i \cdot x_i) \pmod N$.

* **Modular Exponentiation Circuit:** This is the high-level circuit that performs $|x\rangle|b\rangle \rightarrow |x\rangle|b \cdot a^x \pmod N\rangle$. A key space-efficient innovation is the **uncompute method**. This involves swapping the two registers and then running the modular multiplication circuit in reverse (using $a^{-1} \pmod N$ as the constant of multiplication) to reset the ancillary register to $|0\rangle$, thereby maintaining a constant qubit count throughout iterated multiplications.

## Advantages of Toffoli-Based Circuits

As highlighted in the associated research paper, Toffoli-based quantum circuits offer significant benefits for practical quantum computing:

* **No Single-Qubit Rotation Synthesis Overhead:** Unlike circuits relying on the Quantum Fourier Transform (QFT), Toffoli-based networks do not incur synthesis overhead for arbitrary rotation gates. This means a better scaling of both circuit size and depth by a factor of $\Theta(\log n)$ when quantum error-correction is employed.
* **Design for Testability:** Toffoli networks can be efficiently simulated on classical computers as reversible circuits, enabling thorough testing of logical-level designs. This is crucial for debugging complex quantum hardware implementations.
* **Fault Localization:** When running on actual quantum hardware, Toffoli circuits can be debugged efficiently and faults (such as missing-gate faults or bitflips) can be localized using a binary search approach. This is not feasible for QFT-based circuits where intermediate states might be in a superposition of exponentially many basis states.


## Installation and Usage

To run the Jupyter Notebook, you will need to have Python and the necessary quantum computing libraries installed. It is recommended to use a virtual environment.

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/AyomideOlumideAttah/Toffoli-Based-Quantum-Factorizer.git](https://github.com/AyomideOlumideAttah/Toffoli-Based-Quantum-Factorizer.git)
    cd Toffoli-Based-Quantum-Factorizer
    ```

2.  **Create and activate a virtual environment (optional but recommended):**
    ```bash
    python -m venv venv
    # On Windows:
    .\venv\Scripts\activate
    # On macOS/Linux:
    source venv/bin/activate
    ```

3.  **Install the required dependencies:**
    The notebook explicitly specifies the versions of Qiskit and other libraries needed. You can install them using pip:
    ```bash
    pip install --upgrade pip
    pip install qiskit==1.3
    pip install qiskit-aer==0.15
    pip install pylatexenc==2.10
    pip install qiskit_ibm_runtime==0.34.0
    ```

4.  **Launch Jupyter Notebook:**
    ```bash
    jupyter notebook
    ```
    This will open a browser window where you can navigate to and open `Toffoli_based_Shor's_Algorithm_Circuit.ipynb`.

## References

1.  Thomas Häner, Martin Roetteler, and Krysta M. Svore. "Factoring using 2n+2 qubits with Toffoli based modular multiplication." *Quantum Information & Computation*, vol. 17, no. 7–8, pp. 673–684, Jun. 01, 2017. [arXiv:1611.07995](https://arxiv.org/abs/1611.07995)
2.  Craig Gidney. “Creating bigger controlled nots from single qubit, Toffoli, and CNOT gates, without workspace.” StackExchange Q&A, 2015. [https://cs.stackexchange.com/questions/40933/](https://cs.stackexchange.com/questions/40933/)
