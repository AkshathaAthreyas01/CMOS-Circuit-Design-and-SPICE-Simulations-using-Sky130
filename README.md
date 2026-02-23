# CMOS-Circuit-Design-and-SPICE-Simulations-using-Sky130

Design and simulation of CMOS circuits using the Sky130 process node, focusing on schematic development, SPICE-based performance analysis, and the implementation and verification of fundamental analog and digital building blocks in an open-source PDK environment.

## 🖥️ VirtualBox Setup

Follow the steps below to load and run the provided CMOS Virtual Disk Image (VDI) in Oracle VirtualBox.

---

### Step 1: Install VirtualBox
Download and install Oracle VirtualBox from the official site:  
🔗 https://www.virtualbox.org/wiki/Downloads

---

### Step 2: Create the Virtual Machine
- Open **VirtualBox**
- Click **New**
- Enter the following configuration:

  - **Type:** Linux  
  - **Version:** Ubuntu 18.04 (Bionic Beaver) (64-bit)

- Click **Next** to proceed

---

### Step 3: Configure Memory
- Allocate RAM according to your system capacity  
- **Recommended:** 4096 MB  
- Click **Next**

---

### Step 4: Link the CMOS VDI
- Choose **Use an existing virtual hard disk file**
- Click the **yellow folder** button
- Navigate to the extracted CMOS `.vdi` file and select it
- Confirm and complete the VM creation (**Next → Finish**)

  <img width="700" height="500" alt="Screenshot (64)" src="https://github.com/user-attachments/assets/9f85efd5-c100-433f-b2e3-419432a33c85" />


---

### Step 5: Launch the VM
- Highlight the newly created virtual machine
- Click **Start** to boot the environment


> **Tip:** If the VM does not start, verify that hardware virtualization is enabled in your BIOS/UEFI settings.
---

# Day 1: NgSpiceSky130 - Basics of NMOS Drain Current (Id) vs Drain to Source Voltage (Vds)

## Introduction to Circuit Design and SPICE Simulations

### 0 - Lecture 1: Why is SPICE Simulation needed?

- A basic CMOS circuit uses a PMOS and NMOS transistor pair to implement logic functions efficiently.
- The most fundamental example is the CMOS inverter, where PMOS connects to VDD and NMOS to GND.
- When input is low → PMOS ON, NMOS OFF → output high.
- When input is high → NMOS ON, PMOS OFF → output low.

<p align="center">
   <img width="350" height="400" alt="Screenshot (65)" src="https://github.com/user-attachments/assets/3045ebf2-42aa-4372-804e-2b7e029c1711" /><br>
   <em>Figure: CMOS Inverter Circuit</em>
</p>


**DC transfer characteristics of a basic CMOS inverter**

The plot below illustrates the DC transfer characteristics of a basic CMOS inverter obtained through SPICE simulations. The upper curves show the NMOS and PMOS drain current (Id) versus output voltage (Vout) for different input voltages, explaining how the switching behavior occurs. The lower plot represents the inverter voltage transfer characteristic (VTC), highlighting the different operating regions (cutoff, linear, saturation) of NMOS and PMOS as Vin transitions from low to high.

<p align="center">
   <img width="400" height="500" alt="Screenshot (66)" src="https://github.com/user-attachments/assets/e7f9ccfa-031c-4b5f-8776-566f8640fd55" /><br>
   <em>Figure: DC transfer characteristics</em>
</p>

**Propagation Delay Modeling Using SPICE Characterization**

<p align="center">
   <img width="500" height="400" alt="Screenshot (70)" src="https://github.com/user-attachments/assets/c6977af6-2b76-4601-93a7-53840b1433ce" /><br>
   <em>Figure: CTS Delay Characterization</em>
</p>


The figure shows buffer insertion during Clock Tree Synthesis (CTS), where SPICE-generated delay tables are used to model how each buffer drives different capacitive loads.
Cell delay is obtained by selecting the corresponding input slew row and output load column from the delay table, with interpolation used when exact values are unavailable.
Different delay tables represent different Wn/Wp sizing ratios, illustrating how transistor sizing directly impacts propagation delay.
Overall, detailed SPICE characterization of NMOS and PMOS devices enables accurate delay modeling and helps optimize buffer sizing for timing closure.

**Why do we need SPICE?**

- Cell delay mainly originates from transistor switching behavior and the load capacitance seen at the output.  
- Analytical delay models are only approximations and may not capture all second-order effects accurately.  
- SPICE provides detailed device-level simulation to validate and calibrate STA results, ensuring timing accuracy.

---

### 1 - Lecture 2: Introduction to basic element in Circuit Design - NMOS

**NMOS (N-Channel MOSFET)**

- A four-terminal device consisting of *Gate (G), Source (S), Drain (D), and Body (B)*.  
- Fabricated on a *p-type substrate*, with *n+ diffusion regions* forming the source and drain.  
- *SiO₂ isolation and gate oxide* electrically isolate and control the channel region.  
- The *poly-Si or metal gate* modulates channel formation between source and drain.  
- When sufficient gate voltage is applied, an inversion channel forms, allowing current to flow.  
- The *body (substrate)* is typically connected to ground to maintain proper device biasing.

  <p align="center">
   <img width="400" height="400" alt="Screenshot (75)" src="https://github.com/user-attachments/assets/c88dc212-4a6f-4b02-af12-e979942c6f2e" /><br>
   <em>Figure: NMOS</em>
</p>



**Threshold voltage (Vth)** 
It is the minimum gate-to-source voltage (Vgs) required to create a strong inversion layer that forms a conducting channel between the source and drain.

- When *Vgs = 0* and the *Drain, Source, and Bulk* are connected to ground, no inversion channel exists.  
- The *Body–Source (B–S)* and *Body–Drain (B–D)* junctions behave as p–n diodes and remain reverse-biased (OFF).  
- Because no channel is formed, the *Source–Drain resistance is very high*, resulting in negligible current flow.  
- When a *positive Vgs* is applied and exceeds *Vth*, an inversion layer forms at the oxide–substrate interface, allowing current to flow between source and drain.

<p float="left">
  <img src="https://github.com/user-attachments/assets/65a0fa91-4e15-458d-b173-d1de14c775f8" width="300" style="margin-right: 10px;" />
  <img src="https://github.com/user-attachments/assets/8a89b4ea-15de-4b93-9b5c-d92ddd7537d8" width="300" style="margin-right: 10px;" />
  <img src="https://github.com/user-attachments/assets/b69c91bc-f827-4acf-8ac1-6feb74dbca43" width="300" />
</p>
<p align="center">
  <b>Figure :</b> NMOS switching behavior
</p>

---

### 2 - Lecture 3: Strong inversion and threshold voltage

  **NMOS Operation — Strong Inversion Region**

- When a **positive Vgs* is applied and increased, the electric field attracts electrons toward the gate region.  
- Once *strong inversion* occurs, a conductive channel is formed under the gate.  
- The *Vgs* at which strong inversion begins is called the *threshold voltage (Vt)*.  
- Further increase in *Vgs* does not significantly change the depletion width but increases channel charge.  
- Electrons from the heavily doped *n+ source* are pulled into the channel region.  
- A continuous *n-channel* forms between source and drain, and its conductivity is controlled by *Vgs*.

<img width="400" height="300" alt="Screenshot (84)" src="https://github.com/user-attachments/assets/bdd95b71-3456-4213-890a-d035d8e98224" style="margin-right: 10px;" /> <img width="400" height="300" alt="Screenshot (85)" src="https://github.com/user-attachments/assets/d7cdfe93-edf8-4f79-8042-e667ba064045" />

<p align="center">
  <b>Figure :</b> NMOS Strong Inversion and Channel Formation
</p>


**NMOS Body Effect (Impact of Vsb)**

- The figure compares NMOS behavior for *Vsb = 0* and *Vsb > 0*.  
- When *Vsb increases (bulk more negative than source)*, the source–bulk junction becomes more reverse biased.  
- This increases the *depletion region width near the source*, requiring a higher gate voltage to form inversion.  
- As a result, the *threshold voltage (Vt) increases* — this phenomenon is called the *body effect*.  
- Therefore, a larger *Vgs* is needed to turn ON the NMOS when *Vsb is positive*.



<p align="center">
   <img width="400" height="400" alt="Screenshot (88)" src="https://github.com/user-attachments/assets/0632b213-7c66-468f-9f8d-412ef4534b33" />
<br>
   <em>Figure: Effect of substrate bias (body effect) on NMOS threshold voltage</em>
</p>

---


### 3 - Lecture 4: Strong inversion and threshold voltage

**Surface Inversion with Substrate Bias**

Consider the above (NMOS Strong Inversion and Channel Formation) figure.
- *Case 1 (Vsb = 0):* The semiconductor surface inverts to n-type when *Vgs = Vto* (threshold without body bias).  
- *Case 2 (Vsb > 0):* Due to substrate bias, additional reverse bias widens the depletion region.  
- Hence, surface inversion now occurs at a higher gate voltage: *Vgs = Vto + V1*.  
- This demonstrates the *body effect*, where substrate bias increases the effective threshold voltage.

*Conclusion:*  
In the presence of substrate bias (*Vsb*), extra gate potential is required to achieve strong inversion.


**Threshold Voltage Variation with Body Bias** 

- The figure below illustrates the *body effect*, where the NMOS threshold voltage increases with substrate bias (*Vsb*).  
- The relationship is given by:

  **Vt = Vto + γ(√|−2Φf + Vsb| − √|−2Φf|)**

- Here, *Vto* is the threshold voltage at *Vsb = 0* and depends on the fabrication process.  
- The *body effect coefficient (γ)* quantifies how strongly *Vsb* influences *Vt* and is determined by process parameters such as substrate doping (*NA*), oxide capacitance (*Cox*), and silicon permittivity (*εsi*).  
- As shown in Fig. 2, increasing *Vsb* widens the depletion region, increases the surface potential requirement, and therefore raises the effective *Vt*.  
- The *Fermi potential (Φf)* depends on substrate doping (*NA*) and intrinsic carrier concentration (*ni*), linking device physics to the observed shift in threshold voltage.

*Conclusion:*  
Higher substrate bias ⇒ larger depletion width ⇒ higher required gate voltage for strong inversion ⇒ increased threshold voltage.


<p align="center">
   <img width="400" height="300" alt="Screenshot (93)" src="https://github.com/user-attachments/assets/baf9e89a-98a4-46e9-9f55-54b41ac4e319" />
<br>
   <em>Figure: NMOS Body Effect: Threshold Voltage Shift with Vsb</em>
</p>

---

## NMOS resistive region and Saturation region of operation

### 4 - Lecture 1: Resistive region of operation with small drain-source voltage

**Effect of Increasing Vgs on the Depletion Region**

Consider increasing *Vgs* from *1 V to 2.5 V* in steps of *0.5 V* (with Vgs > Vt).

- *Vgs ≈ 1.0 V:* Strong inversion begins. A thin inversion layer forms, and the depletion region reaches near its maximum width.  
- *Vgs ≈ 1.5 V:* Inversion charge increases. The depletion width changes very little (almost saturated), while channel conductivity improves.  
- *Vgs ≈ 2.0 V:* More electrons accumulate in the channel. Depletion region remains nearly constant; current capability increases.  
- *Vgs ≈ 2.5 V:* Channel becomes highly conductive. The device drive strength improves, but depletion width still shows minimal change.

<img width="400" height="300" alt="Screenshot (95)" src="https://github.com/user-attachments/assets/7d913797-8279-472b-83ad-f89f73f3e8ef" />  <img width="400" height="300" alt="Screenshot (97)" src="https://github.com/user-attachments/assets/1d7549f2-4a6e-47ad-8eb9-e645dd4b672d" />

<p align="center">
  <b>Figure :</b> NMOS Linear Region: Channel Formation vs Increasing Vgs
</p>

Once strong inversion is reached, further increase in *Vgs* mainly increases *inversion charge (channel carriers)* rather than significantly expanding the *depletion region*.

**NMOS Operation in Resistive (Linear) Region**

This figure below illustrates the behavior of an NMOS transistor when it is operating in the *linear (resistive) region*.

<p align="center">
<img width="500" height="400" alt="Screenshot (99)" src="https://github.com/user-attachments/assets/f706c167-74d4-402f-a22e-779b851c19ea" />
<br>
<em>Figure: Linear region operation of NMOS</em>
</p>

*Bias Conditions*
- Gate voltage: *Vgs = 1 V*
- Drain voltage: *Vds = 0.05 V (small)*
- Threshold voltage: *Vt = 0.45 V*
- Since *Vgs > Vt*, the device is in *strong inversion* and a channel is formed.


*Channel Formation*
- A conductive *n-type inversion layer* forms under the gate.
- Electrons from the *n+ source* are attracted into the channel region.
- A continuous path exists from *source to drain*, allowing current flow.
- The induced channel charge *Qi ∝ (Vgs − Vt)*.

*Voltage Variation Along the Channel*
- The channel potential is not uniform when *Vds ≠ 0*.
- At any point *x* along the channel:
  
  *Gate-to-channel voltage = Vgs − V(x)*

- Near the *source*, V(x) ≈ 0 → channel is strongest.  
- Near the *drain*, V(x) ≈ Vds → channel charge slightly reduces.


*Why This is Called Resistive Region*
- Because *Vds is small*, the channel exists along the entire length *L*.
- No pinch-off occurs.
- The NMOS behaves like a *voltage-controlled resistor*.
- Drain current increases approximately *linearly with Vds*.

*Effective Channel Length*
- The highlighted region shows the *effective channel length (L)*.
- Current flows through this inversion channel.
- Conductivity of the channel is controlled by *Vgs*.

*Conclusion:*  
When *Vgs > Vt* and *Vds is small*, the NMOS forms a continuous inversion channel and operates in the *linear (ohmic) region*, behaving like a gate-controlled resistor.

---
### 5 - Lecture 2: Drift current theory

**Channel Charge and Current Mechanisms**

*Local Inversion Charge in the Channel*

<p align="center">
<img width="500" height="400" alt="dggagfhwufwyigtiuw" src="https://github.com/user-attachments/assets/854e0e76-14a0-401d-899c-6bf443380fc8" />
<br>
<em>Figure: Variation of inversion charge and current components along the NMOS channel</em>
</p>

In the highlighted yellow region where the MOSFET channel forms, the amount of inversion charge present at any position **x** is strongly governed by the effective gate overdrive at that specific location.

As we move along the channel from source to drain, the channel potential is not constant. Instead, it gradually varies, which means the effective gate-to-channel voltage at any point becomes:

\[
V_{GS,eff}(x) = V_{GS} - V(x)
\]

Because *V(x)* increases along the channel length, the gate’s control over the channel is spatially non-uniform. Each point along the channel experiences a slightly different electrical environment. The local inversion charge therefore depends on the instantaneous difference between the applied gate voltage and the local channel potential.


The inversion charge density at position *x* is directly proportional to the local gate overdrive \(V_{GS} - V(x)\). As this effective voltage changes along the channel, the charge distribution also varies accordingly.


Physical Meaning of \(C_{ox}\)

The oxide capacitance per unit area, *\(C_{ox}\)*, represents the ability of the gate oxide layer to store electric charge for a given area. Physically, it quantifies how effectively the gate electrode can control the channel through the thin insulating oxide.

- Higher \(C_{ox}\) → stronger electrostatic control by the gate  
- Lower \(C_{ox}\) → weaker coupling between gate and channel  

Thus, \(C_{ox}\) is a key parameter determining how efficiently the gate voltage can induce inversion charge in the channel.

*Current Transport Mechanisms in the Channel*

Charge transport in a MOSFET channel occurs through two fundamental mechanisms: *drift* and *diffusion*.

*Drift Current*

When a drain-to-source voltage (\(V_{DS}\)) is applied, an electric field is established along the channel. This field exerts a force on the charge carriers, causing them to move in a directed manner from source toward drain.

This field-driven motion of carriers constitutes the *drift current*.

*Characteristics of drift current:*

- Requires an electric field  
- Carrier motion is directional (not purely random)  
- Continues as long as the electric field is present  
- Carrier velocity depends on the strength of the electric field  
- Most carriers participate when the electric field is sufficiently strong  

Mathematically,

\[
I_d = (\text{carrier velocity}) \times (\text{available inversion charge across channel width})
\]


*Diffusion Current*

Diffusion current arises due to spatial variations in carrier concentration rather than from an external electric field.

Whenever carriers are unevenly distributed—crowded in one region and sparse in another—they naturally move from regions of high concentration to regions of low concentration. This process continues until equilibrium is approached.

*Key features of diffusion current:*

- Driven by carrier concentration gradient  
- Does not require an external electric field  
- Occurs due to natural spreading of carriers  
- Direction depends on density imbalance  
- Prominent where carrier distribution is non-uniform  

In essence, diffusion current emerges wherever there is a gradient in carrier density along the channel.

- Local inversion charge depends on *\(V_{GS} - V(x)\)* and therefore varies along the channel.  
- *\(C_{ox}\)* determines how strongly the gate can control channel charge.  
- MOSFET channel current consists of:
  - *Drift current* → field-driven carrier motion  
  - *Diffusion current* → concentration-gradient-driven motion  

Together, these mechanisms govern the overall drain current in the device.
