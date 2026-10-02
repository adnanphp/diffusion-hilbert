# Diffusion → Hilbert: Structure-Preserving ML Experiment

A small, self-contained experiment testing whether a neural network can learn a
**structure-preserving** mapping from a compressed latent space to a
Hilbert-space representation.

## Question

Can a small MLP learn to map autoencoder latent vectors (`z ∈ R⁸`) to
n-qubit state amplitudes (`ψ ∈ R^{2ⁿ}`) such that inner products between
points are approximately preserved?

## Setup

- **Data:** 5,000 MNIST images (1,000 held out)
- **Latent space:** Tiny autoencoder with 8-dimensional bottleneck
- **Target space:** n-qubit state via PCA-based Ry angle encoding
- **Models tested:** Linear / MLP (MSE only) / MLP (MSE + inner-product loss)
- **Metrics:** MSE, cosine similarity, inner-product preservation error (IP_err)

## Key findings

### Model C (with inner-product loss) across qubit counts, 3 seeds each

| N qubits | Mean IP_err | Std    | Random baseline |
|---------:|------------:|-------:|----------------:|
| 2        | 0.0654      | 0.0343 | ~0.82           |
| 3        | 0.0245      | 0.0049 | ~0.74           |
| 4        | 0.0317      | 0.0008 | ~0.75           |
| 5        | 0.0470      | 0.0045 | ~0.74           |

**Model C beats random by 15–30× across all qubit counts.**

### Model B vs Model C (IP_err, 3 seeds each)

| N qubits | B IP_err (mean) | C IP_err (mean) | Ratio |
|---------:|----------------:|----------------:|------:|
| 3        | 0.0381          | 0.0274          | 0.72× |
| 4        | 0.0731          | 0.0324          | 0.44× |

**The inner-product loss's advantage grows with dimension.**

## Interpretation

The inner-product loss acts as a **structural regularizer** that helps the
network discover the correct geometric structure rather than merely fitting
output values. Its relative advantage over pure MSE grows with the target
dimension, and its seed-to-seed variance is very low at 4 qubits (std ≈ 0.0008).

## Limitations

- Latent is a toy autoencoder, not a real diffusion model
- Target is a PCA-based angle encoding, not a learned quantum circuit
- Small scale: MNIST, 5,000 images, 8-dim latent

## Future work

- Replace autoencoder with a real DDPM
- Test with a variational autoencoder
- Extend to a PennyLane quantum circuit simulation
- Scale latent dimension and image complexity

## Running

```bash
pip install -r requirements.txt
jupyter notebook notebooks/02_minimum_experiment.ipynb
Runtime: ~20 minutes on CPU.

Structure
text
diffusion-hilbert/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── 02_minimum_experiment.ipynb
└── results/
    ├── summary.md
    ├── inner_product_preservation.png
    ├── inner_product_preservation_3q.png
    ├── training_curves.png
    └── training_curves_3q.png
