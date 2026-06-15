# Paso: Proof of Walk, a Consensus Mechanism for Distributed Ledgers

## Abstract

We propose a novel consensus mechanism for distributed ledger systems in which the scarce resource securing the network is human walk-like locomotion time. 

By leveraging ubiquitous smartphone sensors and existing platform hardware attestation infrastructure, Proof of Walk establishes sybil resistance through two mechanisms: operating system-level guarantees that sensor data has not been fabricated, and short-range radio locomotion-cycle synchronization that detects and invalidates simultaneous gathering on multiple devices carried by a single person. The mechanism requires no identity verification, no biometric registration, no onboarding step beyond installing the gathering application — the client that participates in block production — and no collection of personal data. Gathering is stochastic: any device in detected locomotion participates in a block production lottery, with expected earnings proportional to time spent walking. 

The resulting consensus mechanism requires no specialized hardware, no energy expenditure beyond normal smartphone operation and human activity, and distributes block production capacity across all mobile participants far more equally than any prior consensus mechanism. The network uses a stateless coin model in which each unspent output carries its own spending conditions as a self-contained program, supporting programmable coins, issued assets, and atomic swaps on a base layer designed to be validatable on commodity smartphones. The system replaces the central authoritative ledger that conventional finance is built around — the single administered database that is its point of control, failure, and cost — without claiming to eliminate trust.

## 1. Introduction

Conventional financial infrastructure is organized around central authoritative databases: a bank's record of its balances, a card network's record of transactions, a clearing house's register of obligations. Each is owned and administered by a trusted institution, and each is that system's single point of control, failure, and cost — the target that can be breached, the credentials that can be compromised, the administrator who can freeze a record, reverse it, or simply enter it wrong, and the reconciliation machinery that exists only because every institution keeps its own copy. A distributed ledger removes that central database. The record is maintained jointly: no single target, no privileged administrator, no reconciliation between competing copies, because there is one shared ledger.

Maintaining such a ledger without a central operator is the work of a consensus mechanism. Existing consensus mechanisms allocate block production rights according to expenditure of some scarce resource: computation in Proof of Work, staked capital in Proof of Stake, allocated storage in Proof of Space. In practice, these resources are economically accumulable and exhibit strong economies of scale, producing power-law distributions in which participants with greater capital capture disproportionate consensus influence. Scarcity alone does not determine this concentration — votes and rations are scarce yet distributed by design rather than by accumulation. What concentrates these particular resources is the combination of accumulability and capital-driven returns to scale.

Human locomotion time differs structurally from these resources. Biological constraints sharply limit accumulation: one person can walk only a finite number of hours per day, and additional capital does not proportionally increase that limit.

We observe that one resource is distributed with near-perfect equality across the global adult population: time spent walking. Every mobile human can walk, and the range of hours per day available for walking — while not uniform across health, age, and occupation — is narrower than the distribution of any economic resource used by prior consensus mechanisms. A tenfold disparity in available walking time is large by human standards and negligible compared to the millionfold disparities in capital that govern Proof of Stake. This distribution resists concentration.

We further observe that the technical prerequisites for verifying walking — motion sensors, hardware attestation, and network connectivity — are already deployed at planetary scale in the form of smartphones. Unlike prior consensus mechanisms, Proof of Walk requires no additional infrastructure. The sensing, attestation, and communication layers already exist.

Prior consensus mechanisms also impose an ongoing operational burden on participants: hardware procurement, storage management, node monitoring, software updates, and chain resynchronization. Gathering requires none of this. The device is already purchased, already charged, already connected, and already patched by its platform vendor. The participant's only operational contribution is walking.

The system's trust dependency on hardware is specific: sensor integrity rests on mobile operating system vendors maintaining hardware attestation — a guarantee they already provide and are economically incentivized to defend for their own health, fitness, payment, and media ecosystems. We treat this not as a deficiency but as an honest acknowledgment of the trust structure that already underlies all smartphone-mediated economic activity.

Despite its reliance on platform attestation, the network collects no identity information, no biometric data, and no location data, and requires no onboarding beyond installing the gathering application. The trust dependency is on hardware integrity, not on knowing who the user is. The privacy model is detailed in Section 9.

## 2. Activity Detection

Modern smartphones contain accelerometers and gyroscopes capable of distinguishing locomotion from rest states. Activity detection — the binary determination of whether a device is in locomotion or at rest — is a mature capability deployed in commercial fitness applications. The consensus mechanism requires only this binary signal: the device is in genuine locomotion, or it is not. No step counting, velocity measurement, or location tracking is involved.

Both major mobile platforms ship their own activity classifiers at the operating system level — Core Motion on iOS, the Activity Recognition API on Android. These classifiers are sophisticated, but each is controlled independently by its platform vendor and can be modified silently in any OS update. If the gathering application consumed the platform classifier directly, Apple and Google would each independently control a consensus-critical parameter. An iOS update that narrowed the definition of locomotion would change the effective gathering rate for every iOS device on the network without any consensus decision. Two platforms with two independent classifiers is two definitions of walking — which is two networks pretending to be one.

The gathering application therefore reads raw accelerometer and gyroscope data and classifies it against a definition published on-chain. The classifier recognizes human locomotion from the aggregate shape of sensor readings over an interval. Updates to the classifier are consensus changes, ensuring all nodes on all platforms classify locomotion against the same definition. No node validates another node's determination — each device evaluates its own sensor output and reports the result. The on-chain classifier controls what counts as locomotion; reproducible builds and binary integrity (Section 3) ensure that every gathering device is running the classifier it claims to be running.

Human activity recognition from inertial sensor data is a well-studied classification problem with extensive open-source literature, open datasets, and no patent encumbrances. The general multi-class problem — distinguishing walking, running, cycling, sitting, standing — is solved to high accuracy. The binary collapse to locomotion-versus-rest is simpler, since the multi-class confusions (walking misclassified as walking-upstairs, sitting as standing) fall on the same side of the binary boundary. Sensor hardware and signal filtering vary across manufacturers and models, but human locomotion has a characteristic multi-axis periodicity that is well-separated in frequency space from both rest states and the relevant attack vectors — mechanical oscillation, vehicle vibration. Cross-platform sensor variance is real but small relative to that separation. The classifier does not need to be better than the platform vendors' classifiers. It needs to be the same on every device.

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

The on-chain signing key is rotatable by consensus. If a platform vendor were to specifically target the Paso binary through attestation — refusing to attest devices running it — the project publishes a new key on-chain and releases a new signed binary. The platform vendor would then need to repeatedly target successive keys, publicly engaging in an indefinite suppression campaign against a walking application. Each round is a news cycle for the vendor and minutes of work for the project. This asymmetry makes sustained targeting operationally untenable, though the dependency on the vendor's hardware attestation infrastructure itself remains.

The network requires a signed binary, which requires a signer. This is a build-and-release role, not a position of authority. The signer compiles the public source, signs the result, and publishes it. The classifier definition is on-chain; the source is open; the build is reproducible. If the signed binary diverges from the public source, anyone can detect it. If the signer refuses to act, the signing key is rotated and the role passes to someone else. The signer publishes code (which might have been submitted by any competent collaborator); it does not hold funds. The trust dependency is on the integrity of a build, not on the honesty of a custodian.

Two protocol parameters carry real authority: the on-chain classifier definition, which determines what counts as locomotion, and the set of recognized signing keys, which determines what software can produce blocks. At launch, both are controlled by the project. This is the same bootstrap centralization present in every cryptocurrency — Satoshi mined the genesis block, and every network since has launched from a point of concentrated initial control. The difference is that in Proof of Walk, the classifier and signing key remain consensus parameters indefinitely.

Governance of the signing key set is not solved by this paper. The classifier definition is a technical parameter with an objective ground truth — human locomotion is a well-characterized signal — and updates to it are engineering decisions, not political ones. Authority over which signing keys the network recognizes is the irreducible governance question. The project launches with centralized control, publishes all changes transparently, and regards governance design as open future work. The check on the project's authority in the interim is the same as in any open-source system: the code is public, the chain is forkable, and the project is replaceable.

The on-chain signing key means only project-signed binaries can gather. This is a real centralization constraint, and should be recognized as such. Without it, any party — a state actor, a surveillance vendor, a well-resourced competitor — can ship a protocol-compatible client that gathers on the real chain while modifying behavior the protocol cannot inspect: disabling the walking requirement, logging user activity, or capturing wallet keys. The signing key is what forces such a client off the network. The key controls which code runs; reproducible builds and public source are what ensure that the code which runs is the code which was audited.

### 3.4 What Attestation Guarantees

Hardware attestation does not verify the identity of the user. It does not assert who is walking. It guarantees two things: first, that the sensor data reaching the gathering application has not been intercepted, modified, or fabricated by software running on the device; and second, that the running binary matches the signing key published on-chain by the Paso project. Together with the on-chain locomotion classifier (Section 2), this produces a three-layer trust model:

- **Hardware attestation**: sensor data is real.
- **On-chain signing key**: the binary running on the device is the audited, official build.
- **On-chain classifier**: the binary uses the consensus-defined locomotion classifier, not a modified one.

### 3.5 Platform Vendor Attack

A mobile operating system vendor could, in principle, ship a compromised update that fabricates walking activity across all devices running the update. This is the fundamental limit of any sensor-dependent consensus mechanism.

The practical barrier to this attack is substantial. Android holds approximately 70% of global smartphone market share, but this share is fragmented across dozens of device manufacturers — Samsung, Xiaomi, Oppo, Huawei, and others — each shipping modified Android builds on independent update schedules. A malicious update originating from Google would reach Pixel devices immediately but propagate to the broader Android ecosystem over weeks to months, with many devices never receiving it. Achieving 51% of active gathering nodes on a compromised build simultaneously would require coordination across Google and every major Android OEM — a logistical undertaking that appears operationally implausible given the vendor ecosystem.

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

Two devices on one body do not merely show similar movement patterns — they register cycle intervals that are identical to within sensor precision. The separation is expected to be operationally robust under realistic sensor variance: either two devices are experiencing the same locomotion cycles with the same timing, or they are not. Devices detecting synchronized cycle intervals with a nearby node flag the conflict, and both devices' gathering is invalidated for the duration of the detected synchronization.

### 4.2 Attack Scenarios

An attacker wishing to gather on multiple devices simultaneously must carry multiple phones while moving. All carried devices register the same locomotion-cycle intervals. With Bluetooth enabled — a requirement for gathering — the devices detect each other and detect the synchronization. We are not aware of a practical configuration of multiple phones on a single body that reliably avoids this detection.

Disabling Bluetooth to avoid detection disables gathering. The attacker cannot gather without the proximity channel enabled. Physically shielding a device to block Bluetooth also degrades the cellular signal required for network participation — effective Bluetooth isolation requires shielding that disrupts connectivity at adjacent frequencies.

Hardware modification of the BLE radio can circumvent this detection by leaving the OS Bluetooth stack reporting "enabled" while no advertising packets are transmitted. The gathering application mitigates this by requiring WiFi to be enabled as a liveness check on the shared radio chip. Detailed analysis of hardware-level circumvention vectors and planned hardening measures is in Appendix A.

### 4.3 Edge Cases

Two people walking in step (marching soldiers, synchronized walking partners) could trigger false positives. The system accommodates this by requiring sustained synchronization over a threshold duration before flagging — brief incidental synchronization between strangers is distinguishable from the persistent lockstep of two devices on one body.

These threshold parameters can be refined as real-world data accumulates. Conservative initial settings may produce occasional false positives (brief gathering interruptions for co-located walkers), which is an annoyance rather than a security failure.

## 5. Consensus Mechanism

### 5.1 Gathering

Proof of Walk is a consensus mechanism, independent of the execution layer it secures. It requires only a stateless, programmable transaction model with deterministic, cost-metered execution — the properties detailed in Sections 5.4 and 5.5 — and is otherwise agnostic to the specific coin model or contract language used. The scarce resource in this consensus is not walking in the abstract but live attested locomotion by connected devices participating in block production.

Gathering consists of maintaining detected locomotion while connected to the Paso network and participating in the block production lottery. Any device currently in verified locomotion is eligible for block production. At each block interval, one eligible device is selected to produce the next block, with the probability of selection uniform among all currently-walking nodes.

Eligibility derives from current attested locomotion: a device's attestation chain must demonstrate continuous locomotion across the relevant block interval. Leader selection among eligible devices uses verifiable randomness derived from prior chain state, as in modern Nakamoto-style systems. Because eligibility requires continuous real-time attestation that cannot be precomputed or accumulated in advance, the mechanism is structurally resistant to the grinding attacks that other systems counter with a verifiable delay function. Live walking attestation itself supplies the wall-clock anchor that such systems must obtain from sequential computation.

The network model follows conventional Nakamoto-style assumptions: only live, connected nodes participate in consensus. Devices that lose connectivity simply cease participating in the block production lottery until connectivity is restored, analogous to a proof-of-work miner operating while disconnected from the network. The protocol does not buffer or retroactively credit offline locomotion.

The expected gathering reward is therefore proportional to time spent walking while connected — more time walking means more lottery entries — without guaranteeing a fixed payout per hour. Individual gathering sessions are probabilistic; aggregate returns over time converge to a predictable expected value determined by the gatherer's share of total network walking time.

### 5.2 Block Production

Blocks are produced at a fixed interval of eighteen seconds. The selected gatherer assembles a block from pending transactions, validates it, and propagates it to the network. The eighteen-second interval balances payment latency (comparable to tapping a payment card) against propagation constraints on mobile networks, accepting a higher orphan rate than wired-node blockchains as the cost of operating on cellular infrastructure.

### 5.3 Block Reward and Emission

Each block carries a fixed reward of two pasos — the Paso Doble — paid to the gatherer who produced it, plus any transaction fees from the transactions included in the block. There is no halving schedule. The block reward is two pasos per block in perpetuity. The Paso Doble is a Spanish march-tempo dance in 2/4 time whose name translates literally as "double step" — two steps to a beat, two pasos to a block, and a rhythm that is, at its origin, simply the act of walking.

This yields a constant emission of 9,600 pasos per day (two per block, one block per eighteen seconds, 86,400 seconds per day). The smallest unit of the currency is one pasito, equal to 10^-12 pasos (one trillionth). All internal arithmetic uses integer pasito values, avoiding floating-point computation, whose results are not reproducible across platforms and so cannot be used in consensus. The money supply grows linearly and predictably. Because the denominator (total supply) grows while the numerator (daily emission) remains constant, the inflation rate declines asymptotically toward zero without requiring scheduled reward reductions.

The absence of halving is a deliberate design choice. A halving schedule rewards early adopters disproportionately. A fixed perpetual reward means a walk in 2026 and a walk in 2046 both earn the same expected share of that day's emission, proportional only to the walker's fraction of total network walking time. This preserves the egalitarian character of the consensus mechanism.

The per-walker expected earnings decrease as the network grows — more walkers competing for the same fixed emission. This is the same dynamic as Bitcoin mining, where increasing hashrate dilutes per-miner earnings. However, as the network matures, transaction fees are expected to constitute an increasing share of gatherer revenue, supplementing and eventually exceeding the block reward.

### 5.4 Transaction Model

Proof of Walk requires a stateless coin model: each unspent output is a self-contained program that encodes its own spending conditions, and spending a coin requires providing a solution that satisfies that program. This is architecturally distinct from account-based models (Ethereum) and from output-only script models (Bitcoin): the spend program is executable, stateless, and self-contained. Bitcoin Script is not Turing-complete; Ethereum's account model requires global mutable state propagated across the network, with the performance and parallelism consequences that entails. A stateless coin model combines full programmability with the statelessness that makes validation tractable on commodity hardware and permits parallel verification of non-conflicting transactions. Several existing execution layers satisfy these requirements; the choice among them is independent of the consensus mechanism. The base layer supports programmable coins, issued assets, atomic swaps, decentralized offers, decentralized identifiers, and NFTs.

### 5.5 Block Capacity

Block capacity is measured not in bytes but in computational cost. Each operation in a spend program — arithmetic, hashing, signature verification, memory access — carries a defined cost under the execution layer's cost model. A block may contain any combination of transactions whose total execution cost does not exceed the block cost limit. This produces variable-size blocks: a block of simple transfers is small, a block containing complex spend programs is larger.

The cost limit is tuned so that the cheapest smartphone that passes hardware attestation with current security patches can validate one block well within the eighteen-second block interval. This is the binding constraint: block capacity is determined by what the lowest-end attested device can validate in time, not by an arbitrary byte limit. As the attestation floor rises and older devices age out, the cost limit can be raised without changing the block interval, automatically increasing throughput.

### 5.6 State and Storage

Gathering nodes maintain the full unspent transaction output (UTXO) set — the current state of all unspent coins and their puzzles. This is required for block production and validation.

Current smartphones offer 128–256 GB of storage. The binding storage constraint is the UTXO set, which must be retained in full; historical block data can be pruned, as only the UTXO set and recent blocks are needed for ongoing validation. For reference, Bitcoin's UTXO set — the product of sixteen years of transaction history on a network processing several hundred thousand transactions per day — is on the order of ten gigabytes. A new network begins with an empty set and will not approach that scale for years.

In prior consensus mechanisms, running a non-producing full node — one that validates blocks without mining, farming, or staking — is a voluntary act that costs electricity and bandwidth with no direct reward. The validation layer depends on altruism. In Paso, every device running the gathering application is a full node whether it is currently gathering or not. At any moment, the vast majority of devices on the network are at rest — not walking, not producing blocks, but validating and relaying. The validation layer is a byproduct of participation, not a volunteer effort.

## 6. Mechanical Spoofing

A mechanical device (a phone shaker, oscillating cradle, or similar apparatus) could potentially produce accelerometer signals that pass activity detection, allowing a phone to gather without a human walking. Hardware attestation prevents software-level spoofing but does not prevent physical manipulation of the sensor environment.

The defense against mechanical spoofing is economic rather than technical. The expected per-device gathering return is fixed by the emission rate and the number of active gatherers. A faster or more expensive phone does not gather faster. Returns scale linearly with the number of devices, with no economies of scale. At any given network size, the expected return per device per day is:

    R = (9,600 × P) / N

where P is the market price of one paso and N is the number of currently-gathering devices. Each additional spoofing device requires a phone and a mechanical apparatus. As the network grows, the per-device return shrinks. The attacker's capital expenditure grows linearly while revenue per device declines.

At small network sizes where per-device returns are high, the token has no established value, making speculative investment in spoofing hardware irrational. At large network sizes where the token may have significant value, per-device returns are negligible. At current device economics, mechanical spoofing has no stable profitable equilibrium.

This economic argument does not make spoofing impossible — it makes it irrational. A small number of shaker devices is undetectable and tolerable — the emission leakage is negligible. A large number sufficient to meaningfully dilute the network requires capital investment disproportionate to gathering returns. The possibility that dedicated gathering hardware could shift this calculus at higher token valuations, and the available mitigations, are analyzed in Appendix A.

## 7. Extensibility

At launch, the network operates with one classifier variant and one device class: smartphones detecting walk-like locomotion from a device carried on the body. The architecture is not limited to this configuration. The on-chain classifier mechanism supports additional variants tuned to different periodic human-powered motion patterns and different device orientations. The platform expansion mechanism (Section 3.1) supports additional device form factors — including wearable devices — provided they meet the attestation criteria. Wearable processors that can validate blocks within the eighteen-second interval (Section 5.5) could participate as standalone gathering nodes. Alternatively, a paired-device model — in which a wearable handles sensing and locomotion detection while a phone handles block validation — would extend gathering to wearable form factors without waiting for their processors to close the gap. Both paths are architectural extensions, not redesigns.

## 8. Security Analysis

Most attacks against Proof of Walk target reward fairness rather than ledger safety. The central challenge is preventing disproportionate emission capture, not preventing arbitrary transaction rewriting. Where an attack does threaten ledger integrity — chiefly the platform vendor attack and the majority attack — this is called out explicitly below.

### 8.1 Sensor Spoofing

An attacker who obtains a device that passes hardware attestation despite having compromised sensor firmware could fabricate walking data. This requires a hardware-level attack — modifying the sensor chip or its firmware — not merely a software exploit. Hardware attestation specifically guards against software-level spoofing. The cost and difficulty of hardware attacks, applied per-device, makes this economically irrational given the small per-device gathering reward. Mechanical spoofing (phone shakers, oscillating cradles) is addressed in Section 6; the defense there is economic infeasibility rather than technical prevention.

### 8.2 Multi-Device Gathering

Multi-device gathering does not compromise consensus — it does not enable double spends, transaction censorship, or block reordering. It allows one person to claim multiple shares of emission, eroding the egalitarian distribution. Bluetooth locomotion-cycle synchronization (Section 4) renders multi-device gathering by a single person detectable, preserving the one-body-one-share distribution. The mechanism is based on interval pattern matching, requires no clock synchronization, and is betrayed by the gatherer's own devices.

### 8.3 Majority Attack

In Proof of Work, a 51% attack requires controlling the majority of global hash rate. In Proof of Walk, an equivalent attack requires controlling the majority of global walking time on attested devices — that is, more than half of all currently-gathering devices must be under the attacker's control simultaneously. The attack appears difficult to execute at economically meaningful scale: each gathering device must be in genuine physical locomotion with intact hardware attestation, and coordinating the physical movement of a majority of the global gathering population is not a feasible attack vector.

The platform vendor attack vector (Section 3.5) is the closest analog to a majority attack. Its infeasibility is discussed there.

### 8.4 Network Topology

In Bitcoin, the relay network consists of long-running full nodes at fixed IP addresses. Lists of these nodes are public. A targeted denial-of-service attack against a few hundred well-known relay nodes can thin the propagation layer, increase orphan rates, and in the worst case cause miners to diverge on chain tips.

In Paso, the relay and validation network is composed of phones on cellular networks with dynamic IP addresses, moving between WiFi and cellular, going offline and coming back on unpredictably. There are no well-known relay nodes to target. The topology is a swarm of transient peers, not a backbone of long-lived servers. The protocol already assumes that nodes appear and disappear continuously, because phones are inherently unreliable — the network treats churn as a baseline condition rather than an exceptional event.

This topology also resists eclipse attacks — the isolation of a target node by surrounding it with attacker-controlled peers to feed it a false view of the chain. Eclipse attacks against Bitcoin nodes exploit stable IP addresses and persistent peer lists. A phone that changes networks, re-peers on every WiFi-to-cellular transition, and draws from a population of millions of transient nodes is not a target that can be reliably surrounded.

## 9. Privacy

The Proof of Walk mechanism is notable for what it does not collect:

- **No identity**: the network does not know who is walking. There is no registration, no account creation, no identity verification, no KYC. A gatherer is an anonymous device in locomotion.
- **No biometrics**: no gait signature, fingerprint, or any biometric data is captured, stored, or transmitted. The system detects walking as an activity, not as a characteristic of a specific person.
- **No location**: the network does not record, transmit, or store the gatherer's location. Bluetooth is used for local proximity detection only; locomotion-cycle interval data exchanged between nearby devices is ephemeral and not relayed to the network.
- **No onboarding data**: there is no registration step, no enrollment, no baseline capture. The user installs the application and walks. Nothing about the user is recorded before, during, or after gathering.
- **Anonymous wallets**: Paso wallets are cryptographic key pairs with no binding to real-world identity, following the standard cryptocurrency model.

The only data the network processes is: (1) that a device has passed hardware attestation, (2) that the device is currently in locomotion, and (3) locomotion-cycle interval patterns exchanged locally with nearby devices for sybil detection. None of this data is personally identifying. None of it leaves the local Bluetooth radius.

Paso is pseudonymous in the same sense as Bitcoin — wallets are key pairs on a public ledger, and transaction history is visible. The privacy advantage is not in the transaction model but in the gathering process: Paso's consensus participation — a physical activity — collects no personal data whatsoever, despite being tied to the movement of a human body.

## 10. Conclusion

Proof of Walk proposes that human walk-like locomotion time, detected by commodity smartphone sensors and bounded by biological constants, constitutes a viable scarce resource for distributed consensus. The mechanism distributes gathering capacity more equally than any prior consensus mechanism, requires no purpose-built infrastructure, collects no personal data, requires no onboarding beyond installing an application, and honestly acknowledges rather than obscures its institutional trust dependencies. The resulting network is a global ledger maintained by a population of self-interested participants who need no vetting, no compensation beyond protocol rewards, and no management — a self-healing infrastructure whose operational cost is a walk.

## Appendix A: Hardware-Level Attack Analysis

This appendix collects detailed analysis of attack vectors that involve physical modification of gathering devices or purpose-built gathering hardware. These attacks affect emission fairness rather than ledger integrity, allowing an attacker to claim multiple shares of emission and erode the egalitarian distribution. The main body presents the primary defenses (Bluetooth sybil detection in Section 4, economic infeasibility in Section 6). This appendix examines the limits of those defenses and available hardening measures.

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

Second, additional proximity detection layers can be introduced as the BLE and peer-discovery hardware landscape matures. The protocol's consensus parameters are designed to accommodate such changes: adding a new radio requirement is a soft fork, following the same mechanism used for platform expansion (Section 3.1). Other proximity detection channels using independent hardware subsystems are also available but carry platform permission costs that must be weighed against their detection value.

The general principle is that sybil resistance at launch rests on two layers — Bluetooth proximity detection and economic infeasibility — and additional layers can be added as the threat landscape and hardware capabilities evolve. Bluetooth synchronization is the protocol mechanism that enforces one-body-one-share; economic infeasibility is what prevents hardware evasion of that mechanism from scaling.

## Prior Art

Proof of Walk extends a lineage of distributed consensus research. Satoshi Nakamoto's proof of work established that a distributed ledger could be maintained without institutional intermediaries by anonymous, permissionless participants whose only stake was in their own infrastructure — and who held no authority over individual transactions or accounts.

John Tromp proposed Cuckoo Cycle — a graph-cycle-based proof-of-work algorithm — as the basis for egalitarian consensus participation on commodity smartphones with a lottery mindset. The memory-bandwidth hardness he relied on for ASIC resistance proved ASIC-amenable, falsifying the egalitarian premise. The vision survived the algorithm: Proof of Walk inherits Tromp's framing of per-device lottery participation as the unit of consensus, and builds it on a resource that actually resists concentration.

The most direct precedents for rewarding human movement are the move-to-earn applications of the 2022 cycle, principally STEPN and Sweatcoin. STEPN paid tokens for running and walking, verified through combined GPS and accelerometer data and policed by a centralized, server-side machine-learning anti-cheat system; earning first required purchasing sneaker NFTs. Sweatcoin — a step-rewards application since 2016, issuing the SWEAT token in 2022 — is the closer precedent: it rewards steps counted by the device pedometer and cross-checked against GPS, requires no purchase, and reached over a hundred million users.

Sweatcoin shares Paso's surface premise — that commodity smartphones can measure human walking and issue a token for it — closely enough that the distinction must be drawn precisely. It is not a difference in what is measured but in what the measurement is for.

STEPN and Sweatcoin are centralized applications. A company operates a server, owns the ledger, and dispenses tokens from a treasury; walking is a rewarded activity, and the token is a loyalty instrument the operator elects to make tradable. Proof of Walk is a consensus mechanism: there is no operator, no treasury, and no central ledger. Walking is not rewarded — it is the sybil-resistant cost of producing blocks, occupying the position that computational work occupies in Proof of Work. In this sense Proof of Walk is a drop-in replacement for Proof of Work: it substitutes one form of expended effort, human locomotion, for another, computation, while leaving the rest of the Nakamoto-style design intact — permissionless, lottery-based block production over a stateless transaction layer. The difference from Proof of Work is the resource itself: locomotion is biologically capped and resists the accumulation and economies of scale that concentrate hash power (Section 1). The transaction model and execution layer are a separate, best-of-breed choice, independent of the consensus mechanism (Sections 5.4, 5.5).

This categorical difference produces concrete distinctions from the move-to-earn systems:

- **Verification is structural, not reactive.** A centralized application can trust spoofable client sensors and detect fraud after the fact with statistical anti-cheat, because its ultimate sanction is banning an account. Paso has no account to ban and no operator to ban it, and fraudulent gathering would mint consensus blocks directly. Verification is therefore moved to the source: hardware attestation (Section 3), the on-chain classifier (Section 2), and Bluetooth synchronization (Section 4) are hard consensus rules rather than server-side heuristics.

- **Emission is metered by time, not by effort.** Sweatcoin mints per step on an uncapped schedule, so aggregate supply scales with adoption and activity; its governance history is a sequence of reactive measures against the resulting inflation. Paso mints a fixed quantity per unit of wall-clock time regardless of participant count (Section 5.3), so growth dilutes each walker's share rather than expanding supply, and the inflation rate declines toward zero without intervention.

- **Sybil failure is bounded by the emission model.** Because Sweatcoin's emission tracks activity, a successful spoof mints real new supply, making sybil failure a form of monetary debasement. Because Paso's emission is fixed, a successful spoof can only capture a larger share of a constant issue, making sybil failure a matter of unequal distribution rather than inflation.

- **No location data.** GPS is the primary verification signal, the primary spoofing surface, and the primary privacy cost in move-to-earn systems. Proof of Walk uses none (Section 9).

The move-to-earn systems established that measuring walking well enough to motivate exercise is straightforward, and that tying token supply to activity inflates faster than demand can form. Proof of Walk treats the measurement of walking as a problem of consensus security rather than reward, and meters emission by time rather than by effort.
