# Paso: Proof of Walk as a Consensus Mechanism for Mobile Payment Networks

## Abstract

We propose a novel consensus mechanism for distributed ledger systems in which the scarce resource securing the network is human walk-like locomotion time. By leveraging ubiquitous smartphone sensors and existing platform hardware attestation infrastructure, Proof of Walk establishes sybil resistance through two mechanisms: operating system-level guarantees that sensor data has not been fabricated, and Bluetooth-based locomotion-cycle synchronization that detects and invalidates simultaneous gathering on multiple devices carried by a single person. The mechanism requires no identity verification, no biometric registration, no onboarding step beyond installing the gathering application — the client that participates in block production — and no collection of personal data. Gathering is stochastic: any device in detected locomotion participates in a block production lottery, with expected earnings proportional to time spent walking. The resulting consensus mechanism requires no specialized hardware, no energy expenditure beyond normal human activity, and distributes block production capacity uniformly across all mobile participants regardless of wealth or geography. The network adopts the coinset puzzle model, supporting programmable coins, colored coins, and atomic swaps on a base layer designed to be validatable on commodity smartphones.

## 1. Introduction

Existing consensus mechanisms allocate block production rights according to expenditure of some scarce resource: computation in Proof of Work, staked capital in Proof of Stake, allocated storage in Proof of Space. Each creates a power law distribution in which participants with greater economic resources capture disproportionate consensus influence. This concentration is not incidental but structural — the resource must be scarce, and scarce resources are unequally distributed by definition.

We observe that one resource is distributed with near-perfect equality across the global adult population: time spent walking. Every mobile human, regardless of economic circumstance, can walk for a roughly comparable number of hours per day. This distribution resists concentration.

We further observe that the technical prerequisites for verifying walking — motion sensors, hardware attestation, and network connectivity — are already deployed at planetary scale in the form of smartphones. Unlike prior consensus mechanisms, Proof of Walk requires no additional infrastructure. The sensing, attestation, and communication layers already exist.

The trust model of this system differs from prior cryptocurrency design. We do not claim trustlessness. Sensor integrity depends on mobile operating system vendors maintaining hardware attestation — a guarantee they already provide and are economically incentivized to defend for their own health, fitness, payment, and media ecosystems. We argue this is not a deficiency but an honest acknowledgment of the trust structure that already underlies all smartphone-mediated economic activity.

The value proposition of a distributed ledger is not the elimination of institutional trust but the elimination of the central database as both an administrative burden and an attack surface. A distributed ledger has no single target to breach, no administrator credentials to compromise, and no single point at which records can be fraudulently altered. The protocol handles consistency and security; institutions handle political legitimacy; neither substitutes for the other. As with any cryptocurrency, the network also enables cross-border value transfer without intermediary fees.

Despite its reliance on platform attestation, the network collects no identity information, no biometric data, and no location data, and requires no onboarding beyond installing the gathering application. The trust dependency is on hardware integrity, not on knowing who the user is. The privacy model is detailed in Section 9.

## 2. Activity Detection

Modern smartphones contain accelerometers and gyroscopes capable of distinguishing locomotion from rest states. Activity detection — the two-state determination of whether a device is in walking or running motion — is a mature capability deployed in commercial fitness applications. The consensus mechanism requires only this binary signal: the device is in genuine locomotion, or it is not. No step counting, velocity measurement, or location tracking is involved.

The classifier recognizes human locomotion patterns from accelerometer data. The classifier definition is published on-chain. The gathering application ingests this definition and applies it locally to sensor data. Updates to the classifier are consensus changes, ensuring all nodes classify locomotion against the same definition.

Because each device classifies its own sensor data locally — no node ever validates another node's classification — minor cross-platform variance in sensor hardware and signal filtering is expected. Human locomotion has a characteristic multi-axis periodicity that is well-separated in frequency space from both rest states and spurious motion such as vehicle vibration. The decision boundary is comfortably far from the relevant attack vectors, and cross-platform variance in sensor filtering does not move it far enough to matter.

The integrity of the activity detection depends on the sensor data being genuine — that is, originating from physical hardware sensors rather than fabricated in software. This guarantee is provided by the hardware attestation mechanisms described in Section 3.

## 3. Hardware Attestation

### 3.1 Platform Integrity

The Paso gathering application requires hardware-backed attestation that the device is running an unmodified, certified operating system with an intact bootloader. On Android, this is provided by the Play Integrity API; on iOS, by equivalent jailbreak detection mechanisms. These attestation systems are maintained by platform vendors to serve banking applications, digital rights management, and payment systems. Paso is parasitic on this existing infrastructure — the incentive for platform vendors to maintain attestation integrity has nothing to do with Paso and everything to do with the billions of dollars transacted through banking and payment applications that depend on the same guarantees.

The trust criterion for platform attestation is not evaluated by Paso independently. It is evaluated by the financial industry. The moment a major bank ships a production application on a platform that relies on that platform's hardware attestation for transaction security, the attestation has been validated to a standard Paso need not replicate.

This criterion also governs expansion to additional platforms. The on-chain signing key mechanism supports multiple platforms: the project signs a binary for each, publishes each key hash on-chain, and the consensus verifies that a block was produced by a recognized binary on a recognized platform. Adding a platform is a soft fork at most. Which platforms are recognized is initially determined by the project, but the qualifying principle is the same in every case: demonstrated trust by the financial industry that the platform's attestation protects real money.

A device running a modified operating system (custom ROMs, rooted devices, unlocked bootloaders) fails attestation and cannot gather. This excludes a small percentage of devices whose operating system integrity cannot be verified. The consensus also enforces a maximum security patch age: the attestation report includes the device's OS patch level, and blocks produced by devices whose security patch level is older than a defined threshold are rejected. This excludes end-of-life devices that no longer receive security updates — which are also the cheapest second-hand devices, reinforcing the economic floor on per-device gathering cost.

### 3.2 Binary Integrity

The gathering application must be a signed native binary to access hardware attestation APIs — a browser-based or progressive web application cannot provide sensor integrity guarantees. The source code is fully open for public audit. Builds are reproducible, following the model established by Bitcoin Core and Tor: any party can compile the published source in a deterministic build environment and verify that the resulting binary is identical to the signed distribution. This is a stronger guarantee than open source alone — any divergence between the signed binary and the public source is detectable by anyone who reproduces the build.

Binary integrity is enforced through three interlocking mechanisms. First, the Paso project signs the official binary with its own cryptographic key. Second, the hash of this signing key is published on-chain as a consensus parameter. Third, hardware attestation reports include the signing certificate of the running binary, which validating nodes verify against the on-chain key hash. A block produced by a device running a binary signed with an unrecognized key is rejected by the network.

This design makes distribution independent of app stores. Users sideload the APK (on Android) or use alternative distribution channels (on iOS, as regulatory developments expand these options). Hardware attestation verifies the OS and hardware integrity; the on-chain key hash verifies the binary integrity. No app store is in the trust chain.

### 3.3 Key Rotation and Governance

The on-chain signing key is rotatable by consensus. If a platform vendor were to specifically target the Paso binary through attestation — refusing to attest devices running it — the project publishes a new key on-chain and releases a new signed binary. The platform vendor would then need to repeatedly target successive keys, publicly playing whack-a-mole against a walking application. Each round is a news cycle for the vendor and minutes of work for the project. This asymmetry makes sustained targeting operationally untenable, though the dependency on the vendor's hardware attestation infrastructure itself remains.

The network requires a signed binary, which requires a signer. This is a build-and-release role, not a position of authority. The signer compiles the public source, signs the result, and publishes it. The classifier definition is on-chain; the source is open; the build is reproducible. If the signed binary diverges from the public source, anyone can detect it. If the signer refuses to act, the signing key is rotated and the role passes to someone else. The signer publishes code (which might have been submitted by any competent collaborator); it does not hold funds. The trust dependency is on the integrity of a build, not on the honesty of a custodian.

Two protocol parameters carry real authority: the on-chain classifier definition, which determines what counts as locomotion, and the set of recognized signing keys, which determines what software can produce blocks. At launch, both are controlled by the project. This is the same bootstrap centralization present in every cryptocurrency — Satoshi mined the genesis block, Bram Cohen seeded the Chia network. The difference is that in Proof of Walk, the classifier and signing key remain consensus parameters indefinitely. The project's goal is to make itself replaceable, not to pretend it already is.

Governance of the signing key set is not solved by this paper. The classifier definition is a technical parameter with an objective ground truth — human locomotion is a well-characterized signal — and updates to it are engineering decisions, not political ones. Authority over which signing keys the network recognizes is the irreducible governance question. The project launches with centralized control, publishes all changes transparently, and regards governance design as open future work. The check on the project's authority in the interim is the same as in any open-source system: the code is public, the chain is forkable, and the project is replaceable.

### 3.4 What Attestation Guarantees

Hardware attestation does not verify the identity of the user. It does not assert who is walking. It guarantees two things: first, that the sensor data reaching the gathering application has not been intercepted, modified, or fabricated by software running on the device; and second, that the running binary matches the signing key published on-chain by the Paso project. Together with the on-chain locomotion classifier (Section 2), this produces a three-layer trust model:

- **Hardware attestation**: sensor data is real.
- **On-chain signing key**: the binary running on the device is the audited, official build.
- **On-chain classifier**: the binary uses the consensus-defined locomotion classifier, not a modified one.

The same operating system sandbox that guarantees sensor integrity also protects the gathering application's internal state — including the daily gathering cap — from tampering by other software on the device. To modify the application's local state, an attacker would need to root the device, which breaks attestation and disables gathering.

### 3.5 Platform Vendor Attack

A mobile operating system vendor could, in principle, ship a compromised update that fabricates walking activity across all devices running the update. This is the fundamental limit of any sensor-dependent consensus mechanism.

The practical barrier to this attack is substantial. Android holds approximately 70% of global smartphone market share, but this share is fragmented across dozens of device manufacturers — Samsung, Xiaomi, Oppo, Huawei, and others — each shipping modified Android builds on independent update schedules. A malicious update originating from Google would reach Pixel devices immediately but propagate to the broader Android ecosystem over weeks to months, with many devices never receiving it. Achieving 51% of active gathering nodes on a compromised build simultaneously would require coordination across Google and every major Android OEM — a logistical impossibility given the vendor ecosystem.

On iOS, Apple controls the full update pipeline with higher adoption rates, but iOS represents a minority of global devices, concentrated in wealthier markets. A unilateral Apple attack would not reach majority network participation.

A coordinated attack by both Apple and Google simultaneously, propagated through every Android OEM in lockstep, is the theoretical worst case. It requires collusion between direct competitors, cooperation from dozens of independent hardware vendors across multiple jurisdictions, and simultaneous global rollout — a scenario with no precedent in the history of the mobile industry.

### 3.6 Attestation Key Compromise

A more plausible catastrophic scenario is the theft of a platform vendor's hardware attestation keys. With stolen keys, an attacker could create virtual devices that pass attestation without physical hardware, fabricate walking activity in software at zero marginal cost per device, and mount a majority attack without any physical infrastructure. This attack is not solvable at the protocol level.

However, platform attestation keys are among the most heavily guarded secrets in the mobile industry. A compromise breaks not only Paso but every banking application, every digital rights management system, and every payment platform that depends on the same attestation infrastructure — simultaneously. The vendor's incentive to detect, revoke, and rotate compromised keys is existential and immediate. This is not a Paso vulnerability — it is an infrastructure catastrophe that Paso happens to be affected by alongside everything else.

This is the terminal trust dependency of the Proof of Walk mechanism and of every system built on mobile platform attestation.

## 4. Sybil Resistance

### 4.1 Bluetooth Locomotion-Cycle Synchronization

All gathering nodes must have Bluetooth enabled. During gathering, nodes broadcast locomotion-cycle interval data via Bluetooth Low Energy (BLE) advertising — the precise timing intervals between detected locomotion cycles. This requires no pairing, no connection establishment, and no user interaction. It is the same mechanism used by proximity beacons and device discovery systems.

Co-located devices compare locomotion-cycle interval patterns. The comparison does not depend on synchronized clocks — devices compare the sequence of intervals between cycles, not absolute timestamps. Two devices carried by the same person register cycles with the same interval pattern regardless of clock drift between devices.

Two devices on one body do not merely show similar movement patterns — they register cycle intervals that are identical to within sensor precision. The threshold is unambiguous: either two devices are experiencing the same locomotion cycles with the same timing, or they are not. Devices detecting synchronized cycle intervals with a nearby node flag the conflict, and both devices' gathering is invalidated for the duration of the detected synchronization.

### 4.2 Attack Scenarios

An attacker wishing to gather on multiple devices simultaneously must carry multiple phones while moving. All carried devices register the same locomotion-cycle intervals. With Bluetooth enabled — a requirement for gathering — the devices detect each other and detect the synchronization. There is no configuration of multiple phones on a single body that avoids this detection.

Disabling Bluetooth to avoid detection disables gathering. The attacker cannot gather without the proximity channel enabled. Physically shielding a device to block Bluetooth also degrades the cellular signal required for network participation — effective Bluetooth isolation requires shielding that disrupts connectivity at adjacent frequencies.

Hardware modification of the BLE radio can circumvent this detection by leaving the OS Bluetooth stack reporting "enabled" while no advertising packets are transmitted. The gathering application mitigates this by requiring WiFi to be enabled as a liveness check on the shared radio chip. Detailed analysis of hardware-level circumvention vectors and planned hardening measures is in Appendix A.

### 4.3 Edge Cases

Two people walking in step (marching soldiers, synchronized walking partners) could trigger false positives. The system accommodates this by requiring sustained synchronization over a threshold duration before flagging — brief incidental synchronization between strangers is distinguishable from the persistent lockstep of two devices on one body.

These threshold parameters can be refined as real-world data accumulates. Conservative initial settings may produce occasional false positives (brief gathering interruptions for co-located walkers), which is an annoyance rather than a security failure.

## 5. Consensus Mechanism

### 5.1 Gathering

Gathering consists of maintaining detected locomotion while connected to the Paso network and participating in the block production lottery. Any device currently in verified locomotion is eligible for block production. At each block interval, one eligible device is selected to produce the next block. The probability of selection is uniform among all currently-walking nodes.

The expected gathering reward is therefore proportional to time spent walking — more time walking means more lottery entries — without guaranteeing a fixed payout per hour. Individual gathering sessions are probabilistic; aggregate returns over time converge to a predictable expected value determined by the gatherer's share of total network walking time.

Gathering is capped at eight hours per calendar day per device. The cap is enforced locally by the gathering application, with the application's internal state protected from tampering by the operating system sandbox that hardware attestation guarantees (Section 3.4). The cap exists purely as a health measure to prevent overexertion, not as a security mechanism.

### 5.2 Block Production

Blocks are produced at a fixed interval of eighteen seconds. The selected gatherer assembles a block from pending transactions, validates it, and propagates it to the network. The eighteen-second interval balances payment latency (comparable to tapping a payment card) against propagation constraints on mobile networks, accepting a higher orphan rate than wired-node blockchains as the cost of operating on cellular infrastructure.

### 5.3 Block Reward and Emission

Each block carries a fixed reward of two pasos — the Paso Doble — paid to the gatherer who produced it, plus any transaction fees from the transactions included in the block. There is no halving schedule. The block reward is two pasos per block in perpetuity. The Paso Doble is a Spanish march-tempo dance in 2/4 time whose name translates literally as "double step" — two steps to a beat, two pasos to a block, and a rhythm that is, at its origin, simply the act of walking.

This yields a constant emission of 9,600 pasos per day (two per block, one block per eighteen seconds, 86,400 seconds per day). The smallest unit of the currency is one pasito, equal to 10^-12 pasos (one trillionth). All internal arithmetic uses integer pasito values, following the Chialisp convention of avoiding floating-point computation. The money supply grows linearly and predictably. Because the denominator (total supply) grows while the numerator (daily emission) remains constant, the inflation rate declines asymptotically toward zero without requiring scheduled reward reductions.

The absence of halving is a deliberate design choice. A halving schedule rewards early adopters disproportionately. A fixed perpetual reward means a walk in 2026 and a walk in 2046 both earn the same expected share of that day's emission, proportional only to the walker's fraction of total network walking time. This preserves the egalitarian character of the consensus mechanism.

The per-walker expected earnings decrease as the network grows — more walkers competing for the same fixed emission. This is the same dynamic as Bitcoin mining, where increasing hashrate dilutes per-gatherer earnings. However, as the network matures, transaction fees are expected to constitute an increasing share of gatherer revenue, supplementing and eventually exceeding the block reward.

### 5.4 Transaction Model

The network adopts the coinset puzzle model: each unspent output is a puzzle — a self-contained program encoding its own spending conditions. Spending a coin requires providing a solution that satisfies the puzzle. This is architecturally distinct from account-based models (Ethereum) and from output-only script models (Bitcoin): the puzzle is executable, stateless, and self-contained. Bitcoin Script is not Turing-complete; Ethereum's Solidity requires global state propagated across the network, with the performance consequences that entails. The puzzle model combines full programmability with the statelessness that makes validation tractable on commodity hardware. Puzzles are expressed in Chialisp, a functional language designed for this model. The base layer supports programmable coins, colored coins, atomic swaps, decentralized offers, decentralized identifiers, and NFTs.

### 5.5 Block Capacity

Block capacity is measured not in bytes but in computational cost, following the Chialisp Virtual Machine (CLVM) cost model. Each operation in a puzzle — arithmetic, hashing, signature verification, memory access — carries a defined cost. A block may contain any combination of transactions whose total CLVM cost does not exceed the block cost limit. This produces variable-size blocks: a block of simple transfers is small, a block containing complex puzzles is larger.

The cost limit is tuned so that a mid-range smartphone can validate one block well within the eighteen-second block interval. This is the binding constraint: block capacity is determined by what a phone can validate in time, not by an arbitrary byte limit. As phone hardware improves, the cost limit can be raised without changing the block interval, automatically increasing throughput.

### 5.6 State and Storage

Gathering nodes maintain the full unspent transaction output (UTXO) set — the current state of all unspent coins and their puzzles. This is required for block production and validation.

Current smartphones offer 128–256 GB of storage. The UTXO set fits comfortably. As the network grows over years, phone storage grows with it. Historical block data — the full transaction history — can be discarded, as only the UTXO set and recent blocks are needed for ongoing validation. The UTXO set itself is the irreducible minimum and must be retained in full.

## 6. Mechanical Spoofing

A mechanical device (a phone shaker, oscillating cradle, or similar apparatus) could potentially produce accelerometer signals that pass activity detection, allowing a phone to gather without a human walking. Hardware attestation prevents software-level spoofing but does not prevent physical manipulation of the sensor environment.

The defense against mechanical spoofing is economic rather than technical. Gathering reward is fixed per block and shared among all currently-walking nodes. A faster or more expensive phone does not gather faster. Returns scale linearly with the number of devices, with no economies of scale. At any given network size, the expected return per device per day is:

    R = (9,600 × P) / N

where P is the market price of one paso and N is the number of currently-gathering devices. Each additional spoofing device requires a phone and a mechanical apparatus. As the network grows, the per-device return shrinks. The attacker's capital expenditure grows linearly while revenue per device declines.

At small network sizes where per-device returns are high, the token has no established value, making speculative investment in spoofing hardware irrational. At large network sizes where the token may have significant value, per-device returns are negligible. At current device economics, mechanical spoofing has no stable profitable equilibrium.

This economic argument does not make spoofing impossible — it makes it irrational at current device economics. A small number of shaker devices is undetectable and tolerable — the emission leakage is negligible. A large number sufficient to meaningfully dilute the network requires capital investment disproportionate to gathering returns. The possibility that dedicated gathering hardware could shift this calculus at higher token valuations, and the available mitigations, are analyzed in Appendix A.

## 7. Health Externality

Proof of Walk is unique among consensus mechanisms in producing a positive externality: increased physical activity across the participating population. Walking is the single most broadly accessible form of exercise, recommended by health authorities globally for prevention of cardiovascular disease, metabolic syndrome, and cognitive decline.

The cap at eight hours per day and the absence of speed incentives (the reward is for time in locomotion, not for step count or velocity) prevent the mechanism from encouraging harmful overexertion. There is no reward for walking faster, only for walking longer within the daily cap.

This health externality creates a regulatory alignment absent from other cryptocurrency systems. Governments and health authorities have affirmative reason to support adoption of a payment network whose consensus participation is synonymous with the health intervention they already promote.

## 8. Security Analysis

### 8.1 Sensor Spoofing

An attacker who obtains a device that passes hardware attestation despite having compromised sensor firmware could fabricate walking data. This requires a hardware-level attack — modifying the sensor chip or its firmware — not merely a software exploit. Hardware attestation specifically guards against software-level spoofing. The cost and difficulty of hardware attacks, applied per-device, makes this economically irrational given the small per-device gathering reward. Mechanical spoofing (phone shakers, oscillating cradles) is addressed in Section 6; the defense there is economic infeasibility rather than technical prevention.

### 8.2 Multi-Device Gathering

Multi-device gathering does not compromise consensus — it does not enable double spends, transaction censorship, or block reordering. It allows one person to claim multiple shares of emission, eroding the egalitarian distribution. Bluetooth locomotion-cycle synchronization (Section 4) renders multi-device gathering by a single person detectable, preserving the one-body-one-share distribution. The mechanism is based on interval pattern matching, requires no clock synchronization, and is betrayed by the gatherer's own devices.

### 8.3 Majority Attack

In Proof of Work, a 51% attack requires controlling the majority of global hash rate. In Proof of Walk, an equivalent attack requires controlling the majority of global walking time on attested devices — that is, more than half of all currently-gathering devices must be under the attacker's control simultaneously. Each device must be in genuine physical locomotion with intact hardware attestation. The attack cannot be executed by proxy or at scale. Coordinating the physical movement of a majority of the global gathering population is not a feasible attack vector.

The platform vendor attack vector (Section 3.5) is the closest analog to a majority attack. Its infeasibility is discussed there.

## 9. Privacy

The Proof of Walk mechanism is notable for what it does not collect:

- **No identity**: the network does not know who is walking. There is no registration, no account creation, no identity verification, no KYC. A gatherer is an anonymous device in locomotion.
- **No biometrics**: no gait signature, fingerprint, or any biometric data is captured, stored, or transmitted. The system detects walking as an activity, not as a characteristic of a specific person.
- **No location**: the network does not record, transmit, or store the gatherer's location. Bluetooth is used for local proximity detection only; locomotion-cycle interval data exchanged between nearby devices is ephemeral and not relayed to the network.
- **No onboarding data**: there is no registration step, no enrollment, no baseline capture. The user installs the application and walks. Nothing about the user is recorded before, during, or after gathering.
- **Anonymous wallets**: Paso wallets are cryptographic key pairs with no binding to real-world identity, following the standard cryptocurrency model.

The only data the network processes is: (1) that a device has passed hardware attestation, (2) that the device is currently in locomotion, and (3) locomotion-cycle interval patterns exchanged locally with nearby devices for sybil detection. None of this data is personally identifying. None of it leaves the local Bluetooth radius.

Paso is pseudonymous in the same sense as Bitcoin or Chia — wallets are key pairs on a public ledger, and transaction history is visible. The privacy advantage is not in the transaction model but in the gathering process: Paso's consensus participation — a physical activity — collects no personal data whatsoever, despite being tied to the movement of a human body.

## 10. Conclusion

Proof of Walk proposes that human walk-like locomotion time, detected by commodity smartphone sensors and bounded by biological constants, constitutes a viable scarce resource for distributed consensus. The mechanism distributes gathering capacity with near-perfect equality, produces positive health externalities, requires no purpose-built infrastructure, collects no personal data, requires no onboarding beyond installing an application, and honestly acknowledges rather than obscures its institutional trust dependencies. The resulting network is a global payment ledger maintained by a population of self-interested participants who need no vetting, no compensation beyond protocol rewards, and no management — a self-healing infrastructure whose operational cost is a walk.

## Appendix A: Hardware-Level Attack Analysis

This appendix collects detailed analysis of attack vectors that involve physical modification of gathering devices or purpose-built gathering hardware. These attacks do not compromise consensus integrity — they do not enable double spends, transaction censorship, or block reordering. They allow an attacker to claim multiple shares of emission, eroding the egalitarian distribution. The main body presents the primary defenses (Bluetooth sybil detection in Section 4, economic infeasibility in Section 6). This appendix examines the limits of those defenses and available hardening measures.

### A.1 BLE Radio Modification

An attacker can modify a device's BLE radio at the hardware level — desoldering the antenna or RF front-end — leaving the OS Bluetooth stack reporting "enabled" while no advertising packets are transmitted or received. The device becomes invisible to nearby gatherers, bypassing locomotion-cycle synchronization entirely.

A witness-event requirement — flagging devices that encounter no other gatherer over a rolling window — is a partial mitigation but depends on gathering density. It cannot be enforced as a hard rule without penalizing legitimate gatherers in rural or low-density areas.

On current smartphones, WiFi and Bluetooth share a single combo chip (Qualcomm WCN series, Broadcom BCM43xx, and equivalents). There is no separate Bluetooth component to remove. Disabling BLE at the hardware level without also destroying WiFi requires selective antenna modification or RF filtering between the 2.4 GHz band (shared by BLE and WiFi) and the 5 GHz band (WiFi only) — a significantly more sophisticated attack than removing a component. The gathering application requires WiFi to be enabled, not for data exchange but as a liveness check on the combo chip: if the WiFi driver fails to initialize, the chip is absent or damaged, and BLE cannot be trusted. This does not prevent the attack entirely, but raises its difficulty from a soldering job to a targeted RF engineering exercise.

The residual defense is economic. For most devices the hardware modification is difficult or impossible to reverse cleanly, substantially diminishing resale value. The per-device gathering return remains governed by the same analysis as mechanical spoofing (Section 6). This vector removes one of the two sybil resistance layers but does not create a new category of threat.

### A.2 Dedicated Gathering Hardware

If the token achieves sufficient value, a manufacturer could produce stripped-down boards — no display, no camera, no speakers, just an SoC with accelerometer and gyroscope on a certified platform passing attestation and patch-level checks — at a fraction of retail smartphone cost. This would be the Paso analog of purpose-built mining hardware: a device that satisfies every attestation requirement while minimizing per-unit cost.

The attestation floor limits how far this cost can be reduced. Such a device still requires a certified, patched operating system on recognized hardware with real vendor support. The WiFi liveness requirement means the board must include a functional combo chip, not merely an accelerometer on a bare SoC. The gap between that floor and the retail price of a smartphone is the space in which this attack lives.

### A.3 Hardening Measures

If dedicated gathering hardware or BLE radio modification becomes a practical concern, two hardening paths are available.

First, the consensus can be tightened to require WiFi Aware (Neighbor Awareness Networking) capability, adding a second independent proximity detection channel using pre-association peer discovery. NAN support is currently too uneven across the device population to mandate — many devices with current patches lack the necessary firmware — but the hardware trend favors increasing availability. Restricting the eligible device pool is a contentious measure, but no more so than Ethereum's transition to Proof of Stake or Monero's repeated algorithm changes to resist ASICs.

Second, additional proximity detection layers can be introduced as the BLE and peer-discovery hardware landscape matures. The protocol's consensus parameters are designed to accommodate such changes: adding a new radio requirement is a soft fork, following the same mechanism used for platform expansion (Section 3.1).

The general principle is that sybil resistance at launch rests on two layers — Bluetooth proximity detection and economic infeasibility — and additional layers can be added as the threat landscape and hardware capabilities evolve. The economic argument is the foundation; the proximity mechanisms are reinforcement.

## Prior Art

Proof of Walk extends a lineage of distributed consensus research. Satoshi Nakamoto's proof of work established that a distributed ledger could be maintained without institutional intermediaries by anonymous, permissionless participants whose only stake was in their own infrastructure — and who held no authority over individual transactions or accounts.

Bram Cohen's coinset puzzle model — in which each coin encodes its own spend conditions as an executable puzzle, rather than tracking balance at account level — provides Paso's transaction layer. This is an architectural contribution distinct from Chialisp, the functional idiom used to express those puzzles; the innovation is the model, not the implementation language.

John Tromp proposed Cuckoo Cycle — a graph-cycle-based proof-of-work algorithm — as the basis for egalitarian consensus participation on commodity smartphones with a lottery mindset. The memory-bandwidth hardness he relied on for ASIC resistance proved ASIC-amenable, falsifying the egalitarian premise. The vision survived the algorithm: Proof of Walk inherits Tromp's framing of per-device lottery participation as the unit of consensus, and builds it on a resource that actually resists concentration.
