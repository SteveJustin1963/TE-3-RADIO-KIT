# TE-3-RADIO-KIT

<img width="872" height="602" alt="image" src="https://github.com/user-attachments/assets/144c4abf-b3e0-49ae-8f1a-d527cdfa9ddf" />

Awesome question—this little set is a **crystal set + one-transistor audio amp**, and every part earns its keep. I’ll walk the signal from the aerial to your ear and call out the nuances of each connection.

---

# 1) Aerial coupling and RF tuning

**Aerial → 100 pF capacitor → LC tank (coil + tuning capacitor) → ground**

* **Aerial (long wire):** brings in a mix of AM broadcast RF.
* **100 pF series capacitor:** *loose coupling* into the tuned circuit.

  * Keeps the aerial from **heavily loading** (detuning) the tank.
  * At \~1 MHz, $X_C \approx 1/(2\pi f C) \sim 1.6\;k\Omega$: enough to inject energy, light enough to preserve Q.
* **Coil + variable capacitor (parallel):** the **resonator**.

  * You tune the capacitor so $f_0 = 1/(2\pi\sqrt{LC})$ lands on the station.
  * The tank **filters** everything except a narrow slice around $f_0$, and develops a comparatively large RF voltage across itself.
* **Ground (0 V / battery negative):** the return path for both RF and audio. A good earth improves sensitivity.

**Nuances**

* If the 100 pF were larger, the aerial would **damp** the tank—stations get louder but separation gets worse. Smaller gives better selectivity but weaker audio.
* The variable capacitor value effectively sets the *L required* for the AM band; with \~200–365 pF, L is typically tens of µH.

---

# 2) Detection (AM envelope detection)

**Tank hot end → OA90 germanium diode → “detector node” → 10 nF to ground**

* **OA90 diode (anode at tank, cathode toward the amp):** a **half-wave detector**.

  * Germanium is chosen for its **low forward drop** (\~0.2–0.3 V), critical when signals are tiny; a silicon 1N4148 (\~0.6–0.7 V) would throw away more of the audio.
* **10 nF capacitor to ground:** **RF bypass / envelope filter.**

  * At RF (\~1 MHz): $X_C \sim 16\;\Omega$, so it **shorts the carrier** to ground.
  * At audio (say 1 kHz): $X_C \sim 16\;k\Omega$, so it **lets audio remain**.
  * Result: the node carries mostly the **audio envelope** plus a little DC bias from rectification.

**Nuances**

* That 10 nF value sets an **RC time-constant** with the source impedance; bigger smooths more (warmer, less treble), smaller lets more treble through (but may leak RF into the next stage).

---

# 3) Audio pre-bias / input node

**Detector node → transistor base (BC547)**

* The detector node feeds the **base** of the BC547. There’s no explicit bias divider; the stage relies on the tiny **rectified DC** and the base-emitter junction characteristics.
* This makes the transistor work **near the edge of conduction**—think “light class-B/AB.” Weak signals will sound, but with some distortion (acceptable for a one-transistor kit).

**Nuances**

* The 10 nF also tames RF from entering the transistor (which would otherwise self-detect or squeal).
* If you ever add a proper bias (e.g., 470 k–1 M from +9 V to base, and a small emitter resistor), audio gets cleaner and gain steadier.

---

# 4) The audio amplifier

**BC547 NPN in common-emitter**
**Collector → transformer primary → +9 V**
**Emitter → ground**

* **Topology:** classic **common-emitter** voltage amplifier.

  * **Input:** base gets the detected audio.
  * **Output:** collector current swings with the audio.
* **Load:** a **small audio transformer** sits between collector and +9 V.

  * The collector current **modulates** the current through the primary, inducing a voltage in the secondary to drive a **low-impedance earphone** (e.g., 8–32 Ω).
  * The transformer **matches impedances**: high-ish collector load ↔ low-Ω earphone. High voltage / low current on the primary becomes low voltage / **higher current** on the secondary—exactly what the earphone wants.

**Nuances**

* With no emitter resistor and minimal biasing, the BC547 gives plenty of gain but isn’t hi-fi; it’s optimized for **“make it audible”**.
* The collector node also carries DC through the **primary**; small audio transformers tolerate a few mA of DC in this application.

---

# 5) The 500 kΩ across the primary

**Collector node ↔ 500 kΩ ↔ +9 V (across the primary)**

* Acts as a **light shunt load / bleed** across the primary.

  * Prevents the primary from being “open-circuit” at audio (which can cause harshness/ringing).
  * Stabilizes gain and **damps** the transformer a bit to keep the stage from peaking or oscillating when coupling is strong.
* At 500 kΩ it barely loads the primary, so it’s not a volume control—just a **gentle damper** and DC bleed.

**Nuances**

* If you find the sound “peaky” or the stage squeals when the aerial is very strong, reducing this to e.g. 220 kΩ–100 kΩ will tame it (at the expense of a little volume).

---

# 6) Output transformer and earphone

**Transformer secondary → earphone**

* Provides **galvanic isolation** from the DC in the primary—only **AC audio** reaches the earphone.
* The turns ratio steps the collector swing down and **steps current up**, so a cheap **low-impedance earbud** gets enough drive.

**Nuances**

* Transformers around **1–3 kΩ primary to 8–32 Ω secondary** work well here (turns ratio \~10:1 to \~20:1). Using a “line-matching” or small interstage transformer is common in kits.

---

# 7) Power and returns

**+9 V battery → primary bottom; ground rail everywhere else**

* The **+9 V** connects at the bottom of the primary; the circuit’s ground is the battery negative.
* All the RF and audio returns share this ground, so a **good earth** (and short ground wiring) notably boosts performance.

---

## How the whole thing breathes (put together)

1. **The aerial** dumps a wide spectrum into the **tuned LC**, which rings at the station you selected.
2. The **OA90** rectifies that RF; the **10 nF** filters away the RF, leaving the **audio envelope + a sniff of DC**.
3. The **BC547 base** rides on that audio. On positive half-cycles it conducts more; on negative, less.
4. The **collector current** swings through the **transformer primary**; the secondary makes an audio voltage/current that drives the **earphone**.
5. The **500 kΩ** across the primary keeps the transformer and collector node civil (mild damping), and the **100 pF** up front keeps the aerial from ruining the LC’s selectivity.

---

## Little “intricacies” to appreciate or tweak

* **Selectivity vs loudness:** change the 100 pF (or move to a coil tap) to trade off **station separation** vs **volume**.
* **Audio tone:** the 10 nF shapes the **treble**. 4.7 nF = brighter; 22 nF = warmer/more muffled.
* **Stability:** if the stage ever squeals with a very long aerial, a **small base stopper** (e.g., 1–4.7 kΩ in series with the base) plus a **100 nF decoupler** across the battery helps.
* **Cleaner audio (optional):** add 470 k–1 M from +9 V to base and a 100–220 Ω emitter resistor (bypassed with 47–100 µF). That biases the transistor in **class-A**, reducing distortion while keeping the same signal flow.

---

### TL;DR signal path

Aerial → (100 pF) → **Tuned LC** → (OA90) → **10 nF** → **BC547** (common-emitter) → **transformer** → earphone,
with a gentle **500 kΩ** damper across the primary and +9 V feeding the primary.

If you’d like, I can sketch a labeled version of the schematic so each node and component above is lettered and easier to follow during build/debug.
