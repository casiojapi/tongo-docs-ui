---
title: "Introduction to Tongo"
weight: 1
type: docs
---

Tongo is a confidential payment system designed for ERC20 tokens on Starknet. It uses ElGamal encryption over elliptic curves and builds zero-knowledge proofs using only exponentiation (no custom circuits or trusted setup).

Tongo provides private balance management while enabling compatibility with on-chain computation. This document introduces the key concepts behind Tongo.

## Why Tongo?

- **No Trusted Setup**: Unlike zkSNARKs, Tongo uses only EC operations (e.g. Pedersen commitments, proofs of exponent).
- **Additively Homomorphic**: Encrypted balances can be modified without decryption.
- **Auditable**: The protocol supports both user-granted and global audit flows.

## Encryption Format

Each account generates a keypair \((x, y)\), where:

- \(x\) is the private key (scalar)
- \(y = g^x\) is the public key (point on Stark curve)

Balances are encrypted using ElGamal:

$$
\text{Enc}[y](b, r) = (L, R) = (g^b y^r,\; g^r)
$$

This encryption is additively homomorphic:

$$
\text{Enc}[y](b_1, r_1) \cdot \text{Enc}[y](b_2, r_2) = \text{Enc}[y](b_1 + b_2, r_1 + r_2)
$$

## Decryption

The user decrypts their balance by computing:

$$
\frac{L}{R^x} = g^b
$$

Then solves for \(b\) via brute-force. This is feasible when \(b \in [0, 2^{32})\).

## Transfers

To transfer an amount \(b\), the sender must:

1. Prove knowledge of \(x\) such that \(y = g^x\) — PoE
2. Encrypt \(b\) with recipient \(\bar{y}\):

$$
(\bar{L}, \bar{R}) = \text{Enc}[\bar{y}](b, r)
$$

3. Encrypt \(b\) with sender \(y\):

$$
(L, R) = \text{Enc}[y](b, r)
$$

4. Optionally encrypt \(b\) with auditor key \(y_a\):

$$
(L_a, R) = \text{Enc}[y_a](b, r)
$$

5. Prove:
    - \(R = g^r\) (PoE)
    - \(L = g^b y^r\) (Pedersen)
    - \(\bar{L} = g^b \bar{y}^r\) (Pedersen)
    - \(L_a = g^b y_a^r\) (Pedersen)
    - \(b\) and resulting balance \(b' = b_0 - b\) are in range \([0, 2^{32})\) — Range proof

This allows encrypted subtraction from the sender and encrypted addition to the recipient.

---

Continue reading:
- [Audit Flows](/docs/audits)
- [ZK Proof Blocks](/docs/zk-proofs)
- [SDK Overview](/docs/sdk)
- [Multisig & Viewing Keys](/docs/multisig)
- [Key Rotation](/docs/key-rotation)
- [Anonymity via Ring Signatures](/docs/anonymity)

