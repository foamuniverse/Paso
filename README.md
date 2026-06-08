# Paso

Proof of Walk: a consensus mechanism in which the scarce resource securing the network is authenticated human walk-like locomotion time.

This repository contains the protocol specification. It is a design document, not an implementation.

## Summary

Existing consensus mechanisms allocate block production by expenditure of a scarce, accumulable resource — computation (PoW), capital (PoS), storage (PoSpace) — all of which concentrate through economies of scale. Walking time does not accumulate: biological limits cap it per person per day, and capital does not raise that cap. Proof of Walk uses it as the consensus resource, distributing block production more equally than prior mechanisms.

The transaction layer is inherited from Chia — the coinset puzzle model and CLVM. Chia's proof-of-space-plus-VDF consensus is replaced with proof-of-walk. Continuous attested locomotion supplies the wall-clock anchor a VDF would otherwise provide.

## Trust model

Not trustless. Sensor integrity depends on the hardware attestation of mobile platform vendors (Play Integrity, App Attest). Block production is restricted to project-signed binaries via an on-chain signing key. Both the signing key and the locomotion classifier are consensus parameters and remain so indefinitely. These dependencies are stated rather than obscured; see §3.

## Mechanism

- **Activity detection.** Binary classification of locomotion vs. rest from accelerometer and gyroscope data.
- **On-chain classifier.** The definition of locomotion is published on-chain so every device classifies against the same definition; platform classifiers are not consensus-safe because vendors can change them independently. Classifier updates are consensus changes.
- **Sybil resistance.** Two layers. (1) Hardware attestation guarantees sensor data is not fabricated. (2) Bluetooth locomotion-cycle synchronization detects multiple devices on one body — they register identical cycle intervals — and invalidates them, enforcing one-body-one-share. Economic infeasibility (below) backs this against hardware evasion.
- **Consensus.** Any device in verified, continuously attested locomotion is eligible at each block interval; one is selected by chain-derived verifiable randomness, uniformly among eligible devices. Live nodes only; offline locomotion is not credited.
- **No identity, no biometric registration, no onboarding** beyond installing the gathering application. No personal data collected.

## Parameters

| | |
|---|---|
| Block interval | 18 s |
| Block reward | 2 pasos (no halving) |
| Emission | 9,600 pasos/day, fixed and perpetual; inflation declines asymptotically |
| Smallest unit | 1 pasito = 10⁻¹² paso; integer arithmetic throughout |
| Block capacity | bounded by what the cheapest attested device validates within the interval |

Expected per-device daily return is `R = (9,600 × P) / N` for token price `P` and `N` active gatherers. A faster phone does not gather faster; returns scale linearly with device count, with no economies of scale, which is what makes mechanical spoofing irrational rather than impossible.

## Known limitations

- Trust dependency on platform attestation infrastructure (§3.5, §3.6).
- Mechanical spoofing is economically deterred, not prevented (§6, Appendix A).
- Higher orphan rate than wired-node chains, accepted as the cost of cellular operation (§5.2).
- Wheelchair and other non-walking locomotion are out of scope at launch; the classifier and platform mechanisms are extensible to other periodic human-powered motion and device classes (§7).

## Contents

- `paso_consensus_v4.md` — current specification.

## Status

Specification stage. The consensus mechanism is unimplemented. The hard problems — bit-exact classifier determinism across heterogeneous sensors, the adversarial security of Bluetooth proximity synchronization, and the leader-election/fork-choice coupling — are open and unverified.

## Prior art

Builds on Bitcoin (Nakamoto), the Chia coinset model and CLVM (Cohen), and the COVID contact-tracing proximity protocols that establish Bluetooth cycle-detection as practical.
