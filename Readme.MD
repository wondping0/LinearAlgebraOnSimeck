<div align="center">

# LinearAlgebraOnSimeck

**Generate the GF(2) key-recovery equations for reduced-round Simeck32/64 and SIMON32/64 with SageMath.**

![Python](https://img.shields.io/badge/Python-3.10%2B-38E1FF?style=flat-square&labelColor=0A1428&logo=python&logoColor=38E1FF)
![SageMath](https://img.shields.io/badge/SageMath-required-38E1FF?style=flat-square&labelColor=0A1428)
![Topic](https://img.shields.io/badge/topic-algebraic%20cryptanalysis-38E1FF?style=flat-square&labelColor=0A1428)

</div>

---

## Overview

This is a linear-algebra (algebraic) attack setup on Simeck, implemented in Python with SageMath.
The 64 key bits `k0..k63` are symbolic variables in a polynomial ring over GF(2).
An all-zero 32-bit plaintext is encrypted symbolically for 1 to 5 rounds, and each of the 32 ciphertext bits comes out as a polynomial in the key bits.
These polynomials are the system an algebraic or linearization attack would solve for the key. The scripts **generate and save** the system; solving it is not part of this repo.

Watching the output files grow (from under 1 KB at 1–2 rounds to about 1.7 MB at 5 rounds) shows how fast the equations become dense as rounds are added.

## The cipher model

Both scripts use the same Feistel structure on two 16-bit halves:

```
right = right XOR k_i XOR f(left);  swap(left, right)
```

They differ only in the round function `f` (`<<<` is a left rotation):

| Script | Cipher | `f(x)` |
| --- | --- | --- |
| `attack.py` | Simeck32/64 | `(x AND (x <<< 5)) XOR (x <<< 1)` |
| `attack_2.py` | SIMON32/64 | `((x <<< 1) AND (x <<< 8)) XOR (x <<< 2)` |

Round keys come from a simplified schedule shared by both scripts.
The first four round keys are the 16-bit words of the master key, then `k_i = k_{i-4} XOR f(k_{i-3}) XOR i`.
This is a simplified schedule, not the official SIMON/Simeck one (no z-sequence constants).

## Files

| File | Description |
| --- | --- |
| `attack.py` | Simeck model; writes `SIMECK_output_<r>_Round.txt` for `r = 1..5` |
| `attack_2.py` | SIMON model; writes `SIMON_output_<r>_Round.txt` for `r = 1..5` |
| `SIMECK_output_{1..5}_Round.txt` | Saved Simeck equations: 32 polynomials (`Equation #1` to `#32`) per file |
| `SIMON_output_{1..5}_Round.txt` | Saved SIMON equations, same format |

Each script also has a `test()` function (commented out in `__main__`) that prints the 2-round equations to the terminal.

## Requirements

- Python 3.10+ (per the original README)
- [SageMath](https://www.sagemath.org/), importable from Python (`from sage.all import PolynomialRing, GF`)

## Usage

```bash
python3 attack.py      # Simeck  -> SIMECK_output_1..5_Round.txt
python3 attack_2.py    # SIMON   -> SIMON_output_1..5_Round.txt
```

If SageMath is not installed into your system Python, run the scripts through Sage's interpreter instead:

```bash
sage -python attack.py
```

---

<div align="center">

Part of [wondping0](https://github.com/wondping0)'s cryptography notebooks · [wondping0.github.io](https://wondping0.github.io)

</div>
