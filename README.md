# Proof of Walk
 
A consensus mechanism in which the scarce resource securing the network is verified human walk-like locomotion.
 
This repository holds the protocol specification and design rationale.
 
Proof of Walk is meant to be a drop-in replacement for Proof of Work. It keeps the Nakamoto-style structure — permissionless, lottery-based block production over a stateless coin layer — and swaps the resource: locomotion over time. A device gathering verified, continuously attested locomotion is eligible to produce a block; one eligible device is selected each interval by chain-derived randomness.
 
Biological locomotion which is assumed to be normally walking but could be running, or in principle the trot of a pet if a user decided to strap their phone to it, is the Sybil-resistant cost of participating in consensus. The block reward exists to motivate people to contribute that security. The purpose of the locomotion is to secure a distributed ledger.
 
Walking is biologically capped: the time one body can spend in walk-like locomotion is bounded by the hours in a day, so block production resists the economies of scale and capital concentration that centralize other mechanisms.
 
## Verification, emission, and Sybil resistance
 
Proof of Walk is a consensus mechanism to secure a distributed eventually consistent transactional ledger, aka a blockchain. Hardware attestation, an on-chain locomotion classifier, and Bluetooth proximity checks are consensus rules. Emission is metered by time. Proof of Walk mints a fixed quantity per unit of wall-clock time, independent of participant count. Increased user adoption dilutes each walker's share, and so would a successful spoofing attack which means until the attack reaches a majority, which is catastrophic in any Nakamoto style distributed ledger, the results is just unfair distribution of newly minted tokens.

## Precedent
 
Proof of Walk inherits its structure from Bitcoin and Proof of Work, including the principle that the right to produce a block is bought with expended effort. Move-to-earn applications demonstrated a real, large appetite for walking-for-tokens, but showed how the appetite can get wasted on a marketing stunt when token supply is proportional to activity and left to inflate rather than fixed + stochastic.
 
## Status
 
Specification stage. Transaction and execution layer are an open best-of-breed choice. The consensus mechanism is unimplemented, and wants adversarial review by other humans.
 
