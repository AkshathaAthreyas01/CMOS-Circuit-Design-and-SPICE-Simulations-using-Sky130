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

---

### 6 - Lecture 3 : Drain current model for Linear region of operation

**First-Order Analysis of NMOS Drain Current**

This section derives the drain current equation of an NMOS transistor using the *charge-sheet approximation* and first-order device physics.

Inversion Charge Along the Channel

At a position *x* along the channel, the inversion charge density is proportional to the local gate overdrive:

\[
Q_i(x) \propto -\left([V_{GS} - V(x)] - V_T\right)
\]

Using oxide capacitance per unit area:

\[
Q_i(x) = -C_{ox}\left([V_{GS} - V(x)] - V_T\right)
\]


The effective gate voltage is reduced locally by the channel potential \(V(x)\), making the inversion charge non-uniform from source to drain.


Types of Current in MOSFET

From a device perspective, current flow has two components:

1. *Drift current* → due to electric field (dominant in strong inversion)  
2. *Diffusion current* → due to carrier concentration gradient  

In strong inversion and for this first-order model, *drift current dominates*, so we focus on drift.

Drift Current Expression

Drain current is given by:

\[
I_D = (\text{carrier velocity}) \times (\text{available charge}) \times (\text{channel width})
\]

\[
I_D = -v_n(x)\,Q_i(x)\,W
\]

Carrier Velocity

Carrier velocity is proportional to the electric field:

\[
v_n(x) = \mu_n E_x
\]

Since:

\[
E_x = \frac{dV}{dx}
\]

we get:

\[
v_n(x) = \mu_n \frac{dV}{dx}
\]

Substitute Velocity into Current

\[
I_D = -\mu_n \frac{dV}{dx} \cdot Q_i(x) \cdot W
\]

Substitute \(Q_i(x)\):

\[
I_D = \mu_n C_{ox} W \left([V_{GS} - V(x)] - V_T\right)\frac{dV}{dx}
\]

Separate Variables for Integration

Rearrange:

\[
I_D\,dx = \mu_n C_{ox} W \left([V_{GS} - V(x)] - V_T\right)\,dV
\]

Apply Integration Limits

We integrate:

- *x:* from 0 → L  
- *V:* from 0 → \(V_{DS}\)

\[
\int_0^L I_D\,dx = \mu_n C_{ox} W \int_0^{V_{DS}} \left([V_{GS}-V]-V_T\right)\,dV
\]

Since \(I_D\) is constant along the channel:

\[
I_D L = \mu_n C_{ox} W \left[(V_{GS}-V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
\]

Final Drain Current Equation

\[
\boxed{
I_D = \mu_n C_{ox}\frac{W}{L}\left[(V_{GS}-V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
}
\]

Technology Parameters

The following are process/device parameters:

- \(C_{ox}\) → oxide capacitance per unit area  
- \(W/L\) → device geometry  
- \(\mu_n\) → electron mobility  
- \(V_T\) → threshold voltage  
- \(V_{GS}\) → gate-source voltage  

Using Process Transconductance

Define:

\[
k_n' = \mu_n C_{ox}
\]

Then:

\[
I_D = k_n' \frac{W}{L}\left[(V_{GS}-V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
\]

Define gain factor:

\[
k_n = k_n' \frac{W}{L}
\]

So,

\[
\boxed{
I_D = k_n\left[(V_{GS}-V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
}
\]


Region of Operation Check

Given:

- \(V_{GS} = 1\,\text{V}\)  
- \(V_{DS} = 0.05\,\text{V}\)  
- \(V_T = 0.45\,\text{V}\)

Compute gate overdrive:

\[
V_{GS}-V_T = 1 - 0.45 = 0.55\,\text{V}
\]

Condition for Linear Region

If:

\[
V_{DS} \le (V_{GS}-V_T)
\]

then the MOSFET operates in the *linear (triode) region*.

Here:

\[
0.05 < 0.55 \quad \Rightarrow \quad \text{Linear region}
\]


Numerical Substitution

\[
I_D = k_n\left[(1-0.45)(0.05) - \frac{0.05^2}{2}\right]
\]

\[
I_D = k_n\left[0.0275 - 0.00125\right]
\]

\[
\boxed{
I_D = k_n \times 0.02625
}
\]


Small \(V_{DS}\) Approximation (Resistive Behavior)

When \(V_{DS}\) is very small:

\[
I_D \approx k_n (V_{GS}-V_T)V_{DS}
\]

In this region, NMOS behaves like a *voltage-controlled resistor* and current is approximately *linear in \(V_{DS}\)*.

Summary

- Inversion charge varies along the channel due to \(V(x)\).  
- Drift current dominates in strong inversion.  
- Integration of channel charge yields the NMOS I–V equation.  
- For \(V_{DS} < V_{GS}-V_T\), the device operates in the *linear region*.  
- For very small \(V_{DS}\), the MOSFET behaves like a *resistor*.

---
### 7 - Lecture 4 : SPICE conclusion to resistive operation

**Impact of VGS and VDS on Drain Current**

<p align="center">
<img width="500" height="400" alt="Screenshot (100)" src="https://github.com/user-attachments/assets/062b9715-bfa2-49b0-bba0-904506a58968" />
<br>
<em>Figure: NMOS Triode Region: ID Variation with VGS and VDS Sweep</em>
</p>


To understand how *\(V_{GS}\)* and *\(V_{DS}\)* influence the drain current (\(I_D\)), the device is evaluated across different voltage combinations. By varying one parameter while keeping the other fixed, we can observe how their interaction governs the current behavior. The resulting changes depend not only on the individual magnitudes of these voltages but also on their combined effect on channel conduction.

Condition for Triode (Linear) Region

For a fixed *\(V_{GS}\)*, the NMOS transistor remains in the *triode region* as long as:

\[
V_{DS} < (V_{GS} - V_T)
\]

In this regime, the drain-to-source voltage must stay sufficiently below the gate overdrive voltage. When this condition is satisfied, the channel remains continuous from source to drain, and the device exhibits linear (ohmic) behavior. If *\(V_{DS}\)* approaches or exceeds the overdrive voltage, the operation begins to transition out of the triode region.


Measurement Procedure

The evaluation typically follows these steps:

1. Fix a value of *\(V_{GS}\)*.  
2. Sweep *\(V_{DS}\)* from *0* up to *\(V_{GS} - V_T\)*.  
3. Record the corresponding *\(I_D\)* at each step.  
4. Repeat the process for multiple *\(V_{GS}\)* values.  

This point-by-point sweep allows us to map how the drain current evolves with changing bias conditions.


Observed Behavior

- In the triode region, the drain current follows an approximately *linear relationship with \(V_{DS}\)*.  
- Increasing *\(V_{GS}\)* increases the available inversion charge and therefore the current level.  
- Simulation results align closely with the first-order analytical model across the swept range.


Conclusion

The drain current in the linear region is jointly controlled by *\(V_{GS}\)* and *\(V_{DS}\)*. Maintaining  
\[
V_{DS} < (V_{GS} - V_T)
\]  
ensures continuous channel conduction and predictable linear behavior, which can be accurately verified using SPICE simulations.

---

### 8 - Lecture 5 : Pinch-off region condition

**Channel Behavior Near Drain and Onset of Pinch-Off**

At the drain end of the MOSFET, the presence of inversion depends on the local gate-to-channel voltage. As long as:

\[
(V_{GS} - V_{DS}) > V_T
\]

a conducting inversion layer still exists near the drain. Under this condition, carriers continue to be induced beneath the gate across the entire channel length.

<p align="center">
<img width="500" height="400" alt="Screenshot (102)" src="https://github.com/user-attachments/assets/3b011b42-6acb-4638-b3d9-a8107f074ea0" />
<br>
<em>Figure: Variation of drain current (ID) with VDS at fixed VGS showing triode region operation </em>
</p>

Continuous Channel Formation

When inversion is present from *x = 0 (source)* to *x = L (drain)*:

- A continuous conductive path connects source and drain.  
- The MOSFET operates in the *linear (triode) region*.  
- For small *\(V_{DS}\)*, the drain current increases approximately proportionally with *\(V_{DS}\)*.  

Even modest changes in drain voltage influence the current flow through the channel.

Condition for Onset of Pinch-Off

As *\(V_{DS}\)* increases, the effective gate voltage near the drain reduces. The critical condition occurs when:

\[
(V_{GS} - V_{DS}) = V_T
\]

<p align="center">
<img width="400" height="500" alt="Screenshot (103)" src="https://github.com/user-attachments/assets/85d10617-6d98-473d-b370-f7825219bc56" /><br>
<em>Figure: Variation of Vgs-Vds </em>
</p>

At this point:

- The surface potential at the drain end is just sufficient for inversion.  
- The inversion charge density at the drain edge approaches *zero*.  
- The channel no longer fully reaches the drain terminal.  

This marks the beginning of *pinch-off*.

What Happens After Pinch-Off

Once pinch-off occurs:

- The inversion layer disappears near the drain.  
- A depletion region forms at the drain end.  
- The channel effectively terminates slightly before the drain.  

However, *drain current does not stop*. Electrons reaching the pinch-off point are swept through the depletion region into the drain by the strong electric field.

Transition to Saturation Region

When:

\[
V_{DS} > (V_{GS} - V_T)
\]

the MOSFET enters the *saturation region*.

*Characteristics:*

- Channel loses inversion near the drain.  
- A pinched-off region forms.  
- Further increases in *\(V_{DS}\)* produce little change in *\(I_D\)* (first-order view).

Spatial Variation of Channel Charge

Along the channel, the effective gate control is:

\[
V_{GS,eff}(x) = V_{GS} - V(x)
\]

Since *\(V(x)\)* increases from source to drain:

- Effective gate drive decreases toward the drain.  
- Inversion charge density gradually reduces along the channel.  
- The weakest inversion occurs near the drain edge.

Movement of Pinch-Off Point

As *\(V_{DS}\)* continues to increase beyond *\(V_{GS} - V_T\)*:

- The pinch-off point shifts slightly toward the *source*.  
- The depletion region near the drain widens.  
- The effective channel length shortens (basis of channel length modulation in advanced models).


Pinch-off occurs when the local gate overdrive at the drain falls to the threshold voltage. Beyond this point, the MOSFET operates in saturation, where current is primarily controlled by *\(V_{GS}\)* rather than *\(V_{DS}\)*.

---

### 9 - Lecture 6 : Drain current model for saturation region of operation

**MOSFET Saturation Region Behavior**

When *(VGS − VDS) ≤ Vt*, the inversion channel pinches off near the drain, marking the transition into the saturation region.

In saturation, the channel voltage no longer varies linearly along the channel as it does in the triode region. Instead, the channel potential near the drain remains approximately fixed at *(VGS − Vt)*, and the channel appears pinched off at the drain end.

Once the device enters saturation, the drain current becomes largely independent of *VDS*. The current is primarily controlled by the gate overdrive voltage *(VGS − Vt)* and is ideally given by:

[
I_D = \frac{k_n}{2}(V_{GS} - V_t)^2
]

<p align="center">
<img width="400" height="300" alt="Screenshot (104)" src="https://github.com/user-attachments/assets/b5b00bcb-4420-4099-bf4d-1eb09916b6ab" />
<br>
<em>Figure: Figure: MOSFET saturation with drain pinch-off and nearly constant ID.</em>
</p>

Under these conditions, the MOSFET behaves like a constant current source. After saturation is reached, the drain current remains nearly constant even with further increases in *VDS*.

However, the current is not perfectly independent of *VDS* in practical devices. As *VDS* increases further, the depletion region near the drain widens, effectively shortening the channel length. This phenomenon, known as *channel length modulation*, slightly increases the drain current by reducing the effective channel resistance.

Thus, while the MOSFET ideally provides a constant current in saturation, a small dependence on *VDS* remains due to channel length modulation.

---

## Introduction to SPICE

### 10 - Lecture 1: Basic SPICE setup

- SPICE uses well-defined device models to accurately represent transistor behavior. Instead of estimating performance, the simulator relies on fixed model parameters that closely match real fabricated devices. Circuit details are provided through a netlist, which describes each component and their interconnections.

- When the SPICE simulation runs, it computes circuit behavior and produces both numerical data and graphical outputs. From these results, timing delays and cell responses become clear, supporting analyses such as Static Timing Analysis and overall performance verification.

- Technology parameters such as VTO, k′n, γ, and λ are fixed once the technology node is chosen. These values are determined by fabrication and device physics, so they are treated as technology constants. They are supplied by the foundry within the SPICE model file and remain unchanged during simulation.

- Simulation begins only after both the technology model and the circuit netlist are loaded. The tool then evaluates the electrical behavior of each component.

<p float="left">
  <img width="280" alt="Screenshot (105)" src="https://github.com/user-attachments/assets/51df81e4-364a-4164-9e3e-91b0b94b2e41" style="margin-right: 10px;" /> 
  <img width="280" alt="Screenshot (106)" src="https://github.com/user-attachments/assets/5f4ddafa-9889-47b7-acb3-4483c5f83ffe" style="margin-right: 10px;" /> 
  <img width="280" alt="Screenshot (107)" src="https://github.com/user-attachments/assets/3dbc2b17-4d1b-4159-aa12-c4d3746b8cd8" />

<p align="center">
  <b>Figure :</b>SPICE MOSFET setup and ID–VDS simulation.
</p>

- During a typical sweep, gate voltage is increased gradually from zero while the drain current is recorded. Each VGS step generates a new curve, building the full ID–VDS characteristic.

- A correct netlist syntax is essential—SPICE recognizes devices only when the expected format is followed. The MOSFET is defined by its four terminals: Drain, Gate, Source, and Bulk, along with its W/L ratio. Bias supplies establish VGS and VDS, while the bulk of an NMOS is usually tied to ground for stable operation.

- The simulator does not rely on physical geometry directly; instead, it uses mathematical models stored in the model file to predict electrical behavior.

- A small resistor is often placed at the gate to limit transient current spikes. Although the MOSFET gate draws no steady-state current, its capacitance can cause brief surges during switching. The resistor helps smooth these spikes and protects the thin gate oxide and the driving source from electrical stress.


---


### 11 - Lecture 2: Circuit description in SPICE syntax

**SPICE Basics: Nodes and Device Syntax**

Node Definition:
A node is an electrical junction where two or more terminals are connected. If two terminals of the same device are shorted together, they form a single node. In most circuits, nodes typically connect terminals from different components.

<p align="center">
<img width="400" height="300" alt="Screenshot (109)" src="https://github.com/user-attachments/assets/30d6ef57-ddc3-4fbd-97df-6cd8e8ca7cfc" /> <br>
<em>Figure: Figure: MOSFET biasing schematic with node connections.</em>
</p>

To identify nodes, trace the wiring in the schematic—each continuous wire represents one node. These node names are then used when writing the SPICE netlist.

MOSFET Syntax
In SPICE, MOSFET terminals must be listed in the following order:
Drain, Gate, Source, Substrate
Format:
Mname Drain Gate Source Substrate Model W=1.8u L=1.2u

Resistor Syntax
A resistor is defined between two nodes.
Format:
Rname Node1 Node2 Value

Voltage Source Syntax
Voltage sources are specified between a positive node and a negative node (often ground).
Format:
Vname PositiveNode NegativeNode Value

<p align="center">
<img width="300" height="300" alt="Screenshot (108)" src="https://github.com/user-attachments/assets/ce6bc081-cce3-4f88-9180-db82efffd0c8" /> <br>
<em>Figure: Figure: SPICE netlist with node and device definitions</em>
</p>


---

### 12 - Lecture 3: Define Technology parameters

**NMOS Technology Model and SPICE Usage**

NMOS Model in Tech File
To model a specific NMOS device, SPICE uses parameters stored in the *technology (tech) file*.  
This file contains the electrical and physical constants that define device behavior, such as:

- Threshold voltage (VTH0 / VTO)  
- Transconductance parameter (kn′)  
- Body effect coefficient (γ)  
- Channel length modulation (λ)

<img width="400" height="300" alt="Screenshot (111)" src="https://github.com/user-attachments/assets/682d6bae-1a99-4a09-ae42-82f6f025dbd7" /> <img width="400" height="300" alt="Screenshot (112)" src="https://github.com/user-attachments/assets/85228977-66ba-4206-b3f2-79bc861655c3" />

<p align="center">
  <b>Figure :</b> NMOS modeling via tech file
</p>

Because these values are predefined, accurate NMOS modeling becomes straightforward once the correct tech file is linked.  
The model corresponding to the NMOS name in the netlist is searched within this file.

Impact of Technology Scaling
With shrinking technology nodes:

- Short-channel effects become prominent  
- Leakage and Vt variations increase  
- Device behavior becomes highly sensitive  

Therefore, precise parameter extraction is essential—small errors can noticeably affect simulation results.


Model Name Matching Requirement
The model name in the MOSFET statement must *exactly match* the name in the `.MODEL` declaration.

SPICE requires:

- Identical spelling  
- Same letter case  
- No extra spaces  

Any mismatch prevents proper model recognition.

Example: 
M1 vdd n1 0 0 nmos W=1.8u L=1.2u
.MODEL nmos NMOS ( ... )

Consequences of Model Mismatch
An incorrect model reference may:

- Cause netlist errors  
- Invoke the wrong device type  
- Produce misleading results  

Common MOSFET Model Parameters

- *nmos* → Model name  
- *NMOS* → Device type  
- *TOX* → Oxide thickness  
- *VTH0* → Zero-bias threshold  
- *U0* → Carrier mobility  
- *GAMMA1* → Body effect coefficient  

Technology Model Library
NMOS and PMOS models are stored in a library file (e.g., `xxxx_025um_model.mod`).  
The technology node is usually implied by the filename.


<p align="center">
<img width="400" height="300" alt="Screenshot (110)" src="https://github.com/user-attachments/assets/39cd59d3-12e8-4a9c-bef4-b98a6d68cc8d" />
<br>
<em>Figure: Contents of xxxx 025um model.mod</em>
</p>

Linking Model File to Netlist
The tech library is included in the main netlist using a library reference command, which connects the circuit to the correct technology parameters.

Device Characterization
After linking the model:

- Sweep *VGS* and *VDS*  
- Run DC analysis  
- Observe operating regions  

These simulations verify model accuracy and device performance.

---

