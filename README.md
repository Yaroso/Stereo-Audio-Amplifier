# Stereo-Audio-Amplifier
2nd Year, 1st Semester (2023) Labs module group project.

This repository contains the documentation, simulations, and schematics for a battery-powered stereo audio amplifier designed to drive a pair of 8-ohm loudspeakers from a standard 3.5mm audio jack.

> **Note:** This project was completed during the first semester of my second year at university. It serves as an early exploration of analog circuit design. The project experienced significant hurdles—specifically regarding the PCB mistakes and signal attenuation, and timing misallocation —which forced us to pivot to a functional Veroboard prototype. I have included an [**Engineering Retrospective**] section below to critique these early design choices based on my current industry experience.

## My Contributions

As this was a collaborative group project, I want to clearly outline my specific responsibilities. My work focused primarily on the core amplifier stages and physical prototyping, while other team members handled the tone control stage and PCB layout and physical enclosure. 

My specific roles included:
* **Project Leader:** Task allocation, timeline management, and troubleshooting coordination.
* **Circuit Simulation:** Simulating the pre-amplifier, final amplifier, and voltage splitter stages in Multisim.
* **Schematic Design:** Drafting the final overall schematic merging the sub-circuits.
* **Physical Assembly:** Breadboard testing and final Veroboard soldering of the amplifier stages.

## Hardware & System Constraints

The project had to be built using a restricted inventory of laboratory components (e.g., CA3140E/OPA642P op-amps, standard discrete BJT transistors) and had to operate under a strict **4.5V voltage ceiling** powered by a 3x AA battery pack.

## System Architecture (Intended vs. Actual)

The initial design was highly ambitious, aiming to process the audio signal through several distinct stages. 

1. **Voltage Splitter:** Designed to convert the 4.5V single supply into a dual-rail supply (+2.25V, -2.25V, GND) using an op-amp to bias the subsequent stages.
2. **Pre-Amplifier Stage:** A non-inverting op-amp configuration intended to boost the low-voltage input from the 3.5mm jack.
3. **Tone Control Stage (Bypassed in final build):** A passive high-pass and low-pass filter network controlled by variable resistors. 
4. **Output Stage:** We initially attempted a Bridge Amplifier using op-amps, but due to a lack of current amplification, we pivoted to a **Class AB Push-Pull Amplifier** using NPN/PNP complementary transistors to successfully drive the 8-ohm speakers.

<img width="444" height="463" alt="image" src="https://github.com/user-attachments/assets/bcdafe41-4d3d-409f-8ade-537c59b25f27" />

*(Figure 1: Enclosured finished circuit soldered on the Veroboard)*



<img width="498" height="664" alt="image" src="https://github.com/user-attachments/assets/b4dbd464-40ef-42e2-863f-a076ae5b8542" />

*(Figure 2: Finished circuit soldered on the Veroboard)*

The PCB design was attempted and the results can be seen in the section following below.


## ⚠️ The Results

The manufactured PCB failed to produce audible output due to routing misconnections and excessive current draw. However, by bypassing the highly attenuating passive tone-control stage, **we successfully achieved a working prototype on a Veroboard** that could amplify the input signal and drive the speakers.



<img width="1012" height="342" alt="image" src="https://github.com/user-attachments/assets/0df7c7db-ec52-47f5-9ac5-49227acafabe" />


*(Figure 3: Final Schematic diagram of the audio amplifier circuit, designed on Eagle)*

<img width="440" height="532" alt="image" src="https://github.com/user-attachments/assets/23538e72-271d-48d1-8159-4f5de572d0f3" />


*(Figure 4: Attempted PCB, designed on Eagle )*


<img width="396" height="433" alt="image" src="https://github.com/user-attachments/assets/78c23768-9233-4060-8e32-d4dab008dc17" />

*(Figure 5: Soldered PCB, which didn't work as intended)*


---

## 🛠️ Engineering Retrospective: What I Would Do Differently Today

Looking back at this project, it serves as a perfect case study in the differences between theoretical simulation and practical analog hardware design. If I were assigned this same project today, here is how I would overhaul the engineering approach:

### 1. Current Amplification vs. Voltage Amplification
* **The Past Issue:** We initially designed a Bridge Amplifier using op-amps for the final output stage. While it looked great in Multisim (showing a high voltage gain), it completely failed in reality because op-amps cannot source the current required to drive an 8-ohm load. We scrambled to replace it with a Class AB transistor amplifier late in the project.
* **Current Approach:** I now inherently design audio output stages around power delivery ($P = I^2R$), not just voltage swing. I would immediately utilize dedicated audio amplifier ICs (like the LM386 or TDA series) if allowed, or design a robust, thermally-stable Class AB output stage with proper diode biasing from day one.

### 2. Impedance Matching and Passive Attenuation
* **The Past Issue:** The passive Tone Control stage attenuated the signal so severely that the final amplifier stage couldn't recover it, forcing us to bypass the tone control entirely on the final Veroboard build.
* **Current Approach:** Passive filters inherently cause insertion loss. Today, I would never place a passive filter network between two unbuffered stages without accounting for impedance mismatch. I would use an **Active Tone Control** topology (incorporating op-amps into the filter feedback loop) or, at minimum, place unity-gain op-amp buffers before and after the passive filter network to prevent it from loading down the signal chain.

### 3. Power Supply Topology
* **The Past Issue:** We lost significant time trying to run op-amps that expected a dual-rail supply on a single 4.5V rail, leading to clipping and stalling progress until we built an active voltage splitter.
* **Current Approach:** Running standard op-amps off a split 4.5V supply leaves a usable swing of less than $\pm2V$, which is disastrous for audio headroom. Today, I would select modern, single-supply, rail-to-rail op-amps specifically designed for low-voltage battery applications, entirely eliminating the need for a complex and inefficient voltage splitter.

### 4. PCB Design and Verification
* **The Past Issue:** The PCB layout (done by a team member) failed due to misconnections and high current traces being routed improperly.
* **Current Approach:** I now enforce a strict peer-review process for PCB layouts. I would ensure that high-current paths (like those driving the speakers) use thick copper traces or polygon pours to minimize resistance, and I would rigorously run Design Rule Checks (DRC) and Electrical Rule Checks (ERC) against the schematic before signing off on manufacturing.

### 5. Iterative Hardware Design and Testability
* **The Past Issue:** We treated the first PCB as the final product. When the board failed, debugging was difficult and the hard deadline forced us to abandon the PCB entirely for a Veroboard backup.
* **Current Approach:** I now approach hardware with the assumption that a Rev 1 PCB is a validation board, not a final product. I design for testability by adding dedicated test points, standardizing connectors, and utilizing 0-ohm jumpers to isolate functional blocks (e.g., separating the pre-amp from the output stage for independent debug). Crucially, I now bake the lead time and budget for a Rev 2 board into the baseline project schedule.

### 6. Analog Team Workflows and Timeline Estimation
* **The Past Issue:** We divided the sub-circuits among team members to work in parallel but severely underestimated the friction of merging them. Circuits that worked perfectly in isolation failed when chained together due to loading effects and grounding issues.
* **Current Approach:** I learned that in analog hardware development, system integration is a massive bottleneck that takes just as much time as the initial design. I now structure team workflows around early and frequent integration checkpoints. I baseline project timelines with wide margins specifically allocated for resolving impedance mismatches, signal attenuation, and system-level troubleshooting.
