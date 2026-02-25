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

### 13 - Lecture 4: First SPICE simulation

**SKY130 NMOS Simulation Flow**

Open VirtualBox and clone the workshop repository:

git clone https://github.com/kunalg123/sky130CircuitDesignWorkshop.git

After cloning, a directory named sky130CircuitDesignWorkshop is created.

Navigate to the design folder:

<img width="400" height="400" alt="Screenshot (118)" src="https://github.com/user-attachments/assets/a8a5b50e-c440-4a01-9ef8-e5af59f5fe20" />


cd sky130CircuitDesignWorkshop/design/
ls

The ls command lists all files inside the design directory.

To explore the available device libraries, move to the sky130_fd_pr folder:

cd sky130_fd_pr/
ls

Then enter the cells directory:

cd cells/

This directory contains device folders such as nfet_01v8 and pfet_01v8.

Go into the NMOS folder:

<img width="400" height="400" alt="Screenshot (119)" src="https://github.com/user-attachments/assets/d9b8ed56-5d84-4e40-bc49-191d8ecb2fa8" />


cd nfet_01v8
ls

Here you will find model files corresponding to different process corners (TT, FF, SS, etc.).  
These files define NMOS behavior under various process conditions.

To inspect the Typical (TT) corner model:

less sky130_fd_pr__nfet_01v8___tt.pm3.spice

This file contains the NMOS model parameters for the TT corner.  
Press q to exit the viewer.

The supported width (W) and length (L) values are predefined in:

sky130_fd_pr__nfet_01v8___tt.corner.spice

Important: The W and L used in simulation must match one of the allowed values in this file; otherwise, ngspice may report an error.

<img width="400" height="400" alt="Screenshot (120)" src="https://github.com/user-attachments/assets/23399469-84c5-4e11-9884-3cfa8402504b" />


Return to the models directory:

cd ../../models

Locate the master library file:

sky130.lib.spice

This file aggregates NMOS and PMOS models for all process corners.

Next, go back to the design folder and open the example netlist:

cd ../../design
vim day1_nfet_idvds_L2_W5.spice

In this netlist:

- The green-highlighted line includes the SKY130 library  
- The yellow-highlighted text selects the process corner  

Corner meanings:
tt → Typical-Typical  
sf → Slow-Fast  
ff → Fast-Fast  
ss → Slow-Slow  

The chosen corner determines which parameter set is used during simulation.

Netlist snippet:

XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2  
R1 n1 in 55  
Vdd Vdd 0 1.8  
Vin in 0 1.8  

Key points:

- Device used: sky130_fd_pr__nfet_01v8  
- W and L are selected from the corner file  
- Supply voltage is 1.8 V (valid for this device)

DC sweep command:

.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

Meaning:

- VDS swept from 0 to 1.8 V in 0.1 V steps  
- VGS swept from 0 to 1.8 V in 0.2 V steps  

Run the simulation using:

ngspice day1_nfet_idvds_L2_W5.spice

To plot the drain current curves:

<img width="400" height="400" alt="Screenshot (115)" src="https://github.com/user-attachments/assets/1072fe40-3ba1-4f3c-ada6-28f105753bcb" />


plot -vdd#branch

This generates ID vs VDS characteristics for multiple VGS values.

Observed behavior:

- Just above threshold, ID increases approximately quadratically with (VGS − Vt)  
- At higher VGS, the curve appears more linear due to mobility degradation and short-channel effects  

To read the current at a specific point on the graph, left-click the point.  
The terminal displays the coordinates, where the Y-value represents the drain current (ID).

---

### 14 - Lecture 5: SPICE lab with Sky130 models


**Reading Values from the Plot and Model Scaling**

Checking Width and Length Scaling

Navigate to the **models** directory and locate the file:

all.spice

Opening this file reveals the scaling information for the transistor **Width (W)** and **Length (L)** parameters used in the technology library.

<img width="400" height="400" alt="Screenshot (123)" src="https://github.com/user-attachments/assets/65280b8a-5c0d-4962-8e34-ed4ae5360cda" />

When a point on the plotted curve is clicked, the simulator prints the corresponding coordinates in the terminal window.

As illustrated, the terminal shows values labeled **x0** and **y0** for the selected point:

- **y0** → represents the drain current (Id)  
- **x0** → represents the voltage corresponding to that point on the graph  

This feature allows precise observation of device behavior directly from the waveform.

<img width="400" height="400" alt="Screenshot (122)" src="https://github.com/user-attachments/assets/e1b8951c-a180-4da2-9ee8-e8eb7d87c677" />

---

# Day 2: NgSpiceSky130 - Velocity saturation and basics of CMOS inverter VTC

## SPICE simulation for lower nodes and velocity saturation effect

### 15 - Lecture 1: SPICE simulation for lower nodes

**MOSFET Operating Regions and Node Comparison**

From the plotted characteristics:

- The region **left of the boundary** defined by *(Vds = Vgs − Vt)* corresponds to the **Linear (Triode) region**, where the drain current increases approximately linearly with Vds.  
- The region **to the right of this boundary** represents the **Saturation region**, where the drain current becomes nearly constant, showing only a slight rise due to velocity saturation and other short-channel effects.  
- The **bottom portion** of the graph, where current is nearly zero, indicates the **Cut-off region**.

<img width="300" height="300" alt="Screenshot (124)" src="https://github.com/user-attachments/assets/3318c2a4-4078-43bf-ae88-cc198e2ad814" />

Across these regions, the MOSFET exhibits distinctly different conduction behavior.  
The shown characteristics primarily correspond to a **long-channel device**.

<img width="300" height="300" alt="Screenshot (126)" src="https://github.com/user-attachments/assets/bd24bef0-6f9b-433b-a08d-694bc031b074" />

Small Node Case

Next, the same analysis is performed for a **smaller technology node** using a modified SPICE deck.

*Key change:*
- Only the **W and L values** are updated compared to the previous simulation.

Small Node vs Large Node Output Characteristics

<img width="300" height="300" alt="Screenshot (125)" src="https://github.com/user-attachments/assets/c2301b6d-b3e8-46a2-8b45-17a697320a97" />


- In the **small-node device**, a noticeable step or sharper rise in drain current appears near the cutoff boundary.  
- The **large-node device** shows a smoother transition in the same region.  
- In the **saturation region**, the spacing between the \(I_D\) curves remains roughly similar for both devices.  
- However, the **absolute drain current** differs: the **large-node device delivers higher current** than the small-node device.


This comparison highlights the impact of device scaling on MOSFET output behavior.

---

### 16 - Lecture 2: Drain current vs gate voltage for long and short channel device

**Id–Vgs Characteristics and Channel Length Effects**

The Id–Vgs (drain current vs gate-to-source voltage) curve shows how MOSFET drive current varies with gate bias. Long-channel and short-channel devices exhibit noticeably different behavior mainly due to velocity saturation and other short-channel effects.

<img width="400" height="400" alt="Screenshot (127)" src="https://github.com/user-attachments/assets/ebbad05d-a6ec-4a15-a51e-e3d02a82f4c1" />

SPICE Configuration
- Model inclusion: `.include sky130.lib.spice tt` (Typical corner)  
- DC sweep: `.dc Vgs 0 1.8 0.05 Vds 1.8`  
  - VDS is fixed in saturation  
  - VGS is swept  
- MOSFET instance:  
  `M1 drain gate source bulk nfet01 W=__u L=__u`  
- Use library-supported W/L values for valid simulation.

Long-Channel Device Behavior (e.g., L = 1.2 µm, W/L = 1.5)
- Follows the classical square-law model.  
- In saturation:  
  Id = (kn / 2) × (Vgs – Vt)² × (1 + λVds)  

**Observations**
- Id–Vgs shows a parabolic (quadratic) rise above threshold.  
- Current increases rapidly with Vgs due to strong gate control.  
- Higher peak current (~410 µA at Vgs = 1.8 V, Vds = 1.8 V).

<img width="300" height="300" alt="Screenshot (128)" src="https://github.com/user-attachments/assets/fc0f95eb-e780-4176-9c44-f77e41afd4bf" />

Short-Channel Device Behavior (e.g., Sky130, L ≈ 0.15–0.25 µm, W/L = 1.5)
- Velocity saturation dominates at high lateral fields.  
- Carrier velocity reaches vsat, so current deviates from square-law.

In saturation:  
Idsat ≈ W × Cox × (Vgs – Vt) × vsat  

**Observations**
- Id–Vgs becomes more linear at high Vgs.  
- Peak Id is significantly lower (~210 µA at same bias).  
- Saturation-like behavior appears earlier even at moderate Vgs.

<img width="300" height="400" alt="Screenshot (129)" src="https://github.com/user-attachments/assets/1ed854e6-8dce-4abc-8985-f0b941e20588" />

 Long-channel MOSFETs follow square-law behavior, while short-channel devices show reduced current and linearized Id–Vgs due to velocity saturation.

 ---

### 17 - Lecture 3: Velocity saturation at lower and higher electric fields

**Velocity Saturation Impact on Id–Vgs Behavior**

From earlier observations:

- The **long-channel device** (W = 1.8 µm, L = 1.2 µm) exhibits a **quadratic relationship** between Id and Vgs in saturation.  
- The **short-channel device** (W = 0.375 µm, L = 0.25 µm) shows an **almost linear increase** of Id with Vgs.  
- This linearization occurs primarily due to the **velocity saturation effect** in short-channel MOSFETs.

<img width="400" height="400" alt="Screenshot (130)" src="https://github.com/user-attachments/assets/f44b6ac1-4876-43e2-ac63-baf2a1ff59c1" />

Carrier Velocity Behavior

The carrier velocity vs electric field characteristic explains this shift:

- At **low electric fields**, carrier velocity increases linearly  
  → \( v = \mu E \)  
- At **high electric fields**, velocity approaches a maximum limit  
  → \( v_{sat} \) (velocity saturation)

Because short-channel devices experience higher lateral electric fields, they enter velocity saturation earlier than long-channel devices.

Continuity Condition

To find the transition point, the electric field is equated to the critical field:

\[
E = E_c
\]


Using this boundary condition, the drain current equation can be re-derived. However, the resulting expression becomes mathematically complex.

<img width="300" height="400" alt="Screenshot (131)" src="https://github.com/user-attachments/assets/7121dfd1-0bb2-41e0-83f0-9db79e0d4ec9" />

Practical Modeling Approach

To simplify analysis:

- A unified compact model is typically used.  
- The **basic regions of operation (cutoff, linear, saturation)** remain the same for both long- and short-channel devices.  
- The **key difference** is the appearance of **velocity saturation effects** in short-channel operation, which modifies the current behavior.

Long-channel MOSFETs follow square-law behavior, while short-channel devices deviate due to velocity saturation, leading to a more linear Id–Vgs response in saturation.

---

### 18 - Lecture 4: Velocity saturation drain current model

**Gate Overdrive and Velocity Saturation in Short-Channel MOSFETs**

The **gate overdrive voltage** is defined as:

\[
V_{gt} = V_{gs} - V_t
\]

It represents the effective gate bias controlling channel charge, especially under strong inversion. For small values of Vds, channel length modulation is typically ignored in first-order analysis.

<img width="400" height="400" alt="Screenshot (132)" src="https://github.com/user-attachments/assets/e62b94bf-29b7-4c4c-99a4-0cd0ceaa786e" />
Velocity Saturation and Vdsat

As the lateral electric field inside the channel increases, carrier velocity eventually reaches a maximum limit. The drain voltage at which this velocity limit is reached is called **Vdsat**.

- Before Vdsat → drain current increases with Vds  
- After Vdsat → current increase becomes minimal due to carrier scattering and velocity saturation  

Short-Channel Impact

In short-channel MOSFETs:

- Strong electric fields appear even at relatively low Vds  
- Carriers reach saturation velocity much earlier than in long-channel devices  
- Current no longer follows the classical square-law behavior  

<img width="400" height="400" alt="Screenshot (133)" src="https://github.com/user-attachments/assets/7c4bc0f2-4654-4944-82d2-2984241bd166" />

Technology Scaling Effect

With continued technology scaling:

- Effective carrier transport to the drain reduces  
- Saturation current decreases compared to long-channel devices  
- Velocity saturation becomes a dominant non-ideal effect  

As channel lengths shrink, early velocity saturation limits current growth, causing short-channel devices to exhibit lower saturation current and more linear Id behavior compared to long-channel MOSFETs.

---

### 19 - Lecture 5: Labs Sky130 Id-Vgs

In this SPICE simulation, the behaviour of a short-channel NMOS transistor with an effective channel length of approximately 0.12–0.15 µm is investigated at a supply voltage of 1.8 V. To obtain the output characteristics, the drain-to-source voltage (VDS) is swept from 0 V to 1.8 V in increments of 0.1 V. Simultaneously, the gate-to-source voltage (VGS) is varied in steps of 0.2 V, enabling the generation of a complete family of ID–VDS curves.

<img width="400" height="400" alt="Screenshot (134)" src="https://github.com/user-attachments/assets/5b854ce0-be30-44a7-b177-68d93654c0f3" />

Analysis of the plotted characteristics shows that at low VDS values, the transistor operates in the expected linear (ohmic) region, where the drain current increases with VDS. However, as VDS increases further, the curves begin to flatten earlier than in long-channel devices and exhibit a more linear appearance in the saturation region. This behaviour indicates that the channel carriers are reaching their velocity limit.

<img width="500" height="400" alt="Screenshot (135)" src="https://github.com/user-attachments/assets/b67fc1b1-b097-43c5-92ae-167e6c1d6c27" />

At VDS = 1.8 V, the maximum drain current extracted from the plot is approximately 0.196 mA (196 µA). The early onset of saturation combined with the relatively reduced peak drain current is a clear signature of velocity saturation, which is a dominant short-channel effect caused by high electric fields in scaled MOSFET technologies.

<img width="400" height="400" alt="Screenshot (136)" src="https://github.com/user-attachments/assets/98ec1612-3fb3-4c4f-b269-2548af79b617" />


---

### 20 - Lecture 6: Labs Sky130 Vt

Next, the threshold voltage (Vt) is extracted from the ID–VGS transfer characteristic curve. The threshold voltage is defined as the gate-to-source voltage at which the drain current begins to rise rapidly, indicating the formation of a strong inversion channel.

From the plotted curve, Vt is identified at the point where a small increase in VGS results in a sharp increase in drain current. For improved accuracy, a tangent is drawn along the steep (linear) portion of the ID–VGS curve and extrapolated back to the voltage axis. The intercept of this tangent with the VGS axis gives the threshold voltage.

<img width="400" height="400" alt="Screenshot (137)" src="https://github.com/user-attachments/assets/835455e3-7488-4253-be9a-c2c25eeb5502" />

Using this method, the threshold voltage (Vt) is found to be approximately 0.76 V.

---

## CMOS voltage transfer characteristics (VTC)

### 21 - Lecture 1: MOSFET as a switch

A MOS transistor conducts only when the magnitude of the gate-to-source voltage exceeds the threshold voltage, i.e., |VGS| > Vt. The modulus is used because VGS is positive for an NMOS device and negative for a PMOS device.

When |VGS| is less than Vt, the transistor remains in the OFF (cutoff) region and no significant current flows. The device turns ON and conducts only when |VGS| becomes greater than the threshold voltage.

<img width="400" height="400" alt="Screenshot (138)" src="https://github.com/user-attachments/assets/dccc6e05-0ce4-4108-9231-b45929c5ae6d" />

Now consider CMOS (Complementary MOSFET) operation.

When Vin is high and equal to VDD:

• For the PMOS (upper transistor), the gate-to-source voltage is determined by Vin and VDD, since the PMOS source is connected to VDD.  
• For the NMOS (lower transistor), the gate-to-source voltage is determined by Vin and VSS (ground), since its source is connected to VSS.

Thus, in a CMOS inverter, the input voltage Vin simultaneously controls both transistors by forming VGS with VDD for the PMOS and with VSS for the NMOS.

---

### 22 - Lecture 2: Introduction to standard MOS voltage current parameters

The behavior of a CMOS inverter depends on whether the input voltage is HIGH or LOW.

When Vin = VDD (logic HIGH), the NMOS transistor turns ON while the PMOS transistor turns OFF. In this condition, the output node is connected to ground through the conducting NMOS device, creating a strong pull-down path and driving the output LOW.


When Vin = 0 (logic LOW), the NMOS transistor turns OFF and the PMOS transistor turns ON. The output node is then connected to VDD through the PMOS device, forming a pull-up path and driving the output HIGH.

Under steady-state logic conditions, only one of the two transistors conducts at any given time. This is the key reason CMOS circuits exhibit very low static power consumption.

<img width="600" height="400" alt="Screenshot (139)" src="https://github.com/user-attachments/assets/9693e80b-8653-4e61-b3a8-36e8e4984228" />

However, during the switching transition (when Vin is between LOW and HIGH), both the NMOS and PMOS transistors conduct momentarily. Applying current conservation at the output node, the drain current of the PMOS equals the negative of the NMOS drain current (Idsp = −Idsn). Their magnitudes are equal, but the current directions are opposite, ensuring charge balance at the output node.

---

### 23 - Lecture 3: PMOS/NMOS drain current vs drain voltage

Since the source terminal of the NMOS transistor is connected to ground, the analysis becomes simple and direct. The gate-to-source voltage of the NMOS is equal to the input voltage, and the drain-to-source voltage equals the output voltage. Hence, for the NMOS device, VGSN = Vin and VDSN = Vout.

In the case of the PMOS transistor, the direction of drain current is opposite to that of the NMOS. When referenced using the NMOS current direction convention, the PMOS drain current appears negative because its actual current flow is in the reverse direction relative to the chosen reference.

<img width="500" height="400" alt="Screenshot (140)" src="https://github.com/user-attachments/assets/bdb73d8b-4742-4273-a538-bdb4fe6e8699" />

A transistor begins to turn ON once the magnitude of the gate voltage exceeds the threshold voltage. As the gate voltage increases further, the drain current correspondingly increases. The characteristic I–V curve clearly shows both the linear (triode) region at lower drain voltages and the saturation region at higher drain voltages.

The PMOS transistor exhibits the same fundamental behavior as the NMOS, but with reversed voltage and current polarities. Due to this polarity inversion, the PMOS current–voltage characteristics appear mirrored in the negative quadrant of the graph.

---

### 24 - Lecture 4: Step1- Convert PMOS gate-source-voltage to Vin


To derive the CMOS Voltage Transfer Characteristic (VTC), the device equations must be rewritten using only VIN and VOUT, because internal voltages such as VGSN, VGSP, VDSN, and VDSP are not directly observable at the logic level. Although NMOS and PMOS devices have their own terminal voltages, a digital inverter is functionally defined only by its input and output.

<img width="300" height="300" alt="Screenshot (142)" src="https://github.com/user-attachments/assets/921293d0-da54-4461-a56a-445280c4082a" />

The first step is to eliminate VGSP by expressing it in terms of the input voltage. Since VGSP = VIN − VDD, it can be rearranged as VIN = VGSP + VDD. For a long-channel inverter with VDD = 2 V, each PMOS gate voltage corresponds to a unique VIN value (for example, VGSP = 0 → VIN = 2 V, VGSP = −0.5 → VIN = 1.5 V). This mapping allows PMOS characteristics to be represented directly using VIN instead of VGSP.


<img width="500" height="400" alt="Screenshot (141)" src="https://github.com/user-attachments/assets/528331ee-0266-4939-97e2-f993c4a6010f" />

Next, the PMOS current is converted into the NMOS current reference. Because PMOS current flows in the opposite direction to NMOS current, IDSP = −IDSN. By flipping the PMOS current axis, both devices can be expressed using a single current variable (IDSN) without altering the physical interpretation.

After these transformations, the PMOS curves become IDSN versus VIN plots, and VGSP is completely removed from the formulation. The remaining step (performed later) is to replace VDSP and VDSN with VOUT. Once this is done, the NMOS and PMOS load curves can be combined to obtain the complete CMOS voltage transfer characteristic (VTC).

---

### 25 - Lecture 5: Step2 & Step3- Convert PMOS gate-source-voltage to Vin

Initially, the transfer characteristics are expressed in terms of Vdsp, which is an internal PMOS voltage and cannot be measured directly.

To rewrite the behavior using external node voltages, we use the circuit relation:
Vout = Vdd + Vdsp  →  Vdsp = Vout − Vdd

For Vdd = 2 V, when Vdsp varies from −2 V to 0 V, the corresponding Vout varies from 0 V to 2 V. Substituting this relation allows the PMOS characteristics to be fully expressed using the observable variables Vin and Vout.

<img width="400" height="400" alt="Screenshot (143)" src="https://github.com/user-attachments/assets/18d63460-3210-426a-8b0b-122297ff8672" />

When Vout = 2 V (with Vdd = 2 V), Vdsp = 0 V. There is no voltage drop across the PMOS, so the drain current becomes zero. The load capacitor CL is fully charged, representing the steady HIGH output state of the CMOS inverter.

When Vout = 0 V, the magnitude of Vdsp becomes 2 V. The load capacitor CL is fully discharged, and depending on Vin, current may flow through the PMOS to recharge the output node.

<img width="600" height="300" alt="Screenshot (144)" src="https://github.com/user-attachments/assets/8ca591cf-e5bd-4535-a3ae-3bedc2276540" />

These operating points describe normal CMOS inverter action, where PMOS and NMOS together control the charging and discharging of the output node.

For the NMOS device, VGSN = Vin and VDSN = Vout. Since both are already external node voltages, the NMOS drain current can be written directly in terms of Vin and Vout. Therefore, the NMOS load curve can be plotted without any voltage transformation.

---

### 26 - Lecture 6: Step4- Merge PMOS-NMOS load curves and plot VTC

The CMOS Voltage Transfer Characteristic (VTC) is obtained by superimposing the NMOS and PMOS load curves on a common graph. Since both devices share the same Vin and Vout, the inverter operating point for any Vin is given by the intersection of their load curves.


<img width="600" height="400" alt="Screenshot (145)" src="https://github.com/user-attachments/assets/784ae2ab-4dc2-4dc6-b2c8-e6cc3a4ac21e" />

For each input voltage Vin, the NMOS–PMOS intersection determines the corresponding output voltage Vout. Plotting all (Vin, Vout) pairs forms the VTC.

Extreme points  
Vin = 0 V → NMOS OFF, PMOS linear → Vout = VDD = 2 V  
Vin = 2 V → NMOS linear, PMOS OFF → Vout = 0 V  
These define correct inverter logic.

Intermediate region  
Vin = 0.5 V → NMOS saturation, PMOS linear → Vout high  
Vin = 1 V → both in saturation → Vout mid (high-gain region)  
Vin = 1.5 V → NMOS linear, PMOS saturation → Vout low  

Final VTC  
Typical points: (0,2), (0.5,~1.7), (1,~1), (1.5,~0.3), (2,0).  
Connecting them smoothly gives the CMOS inverter VTC.

Key observations  
• Maximum gain occurs when both transistors are in saturation.  
• The steep middle slope enables fast digital switching.  
• Load-curve analysis clearly indicates each device’s region of operation.

---

# Day 3: NgSpiceSky130 - CMOS switching threshold and dynamic simulations

## Voltage transfer characteristics – SPICE simulations

### 27 - Lecture 1: SPICE deck creation for CMOS inverter

A SPICE deck begins by defining the connectivity of all circuit components, followed by the input stimulus applied to the inverter.

Component connectivity  
First, the netlist specifies how each device terminal is connected within the circuit.

<img width="400" height="400" alt="Screenshot (146)" src="https://github.com/user-attachments/assets/278a3c4b-d341-4dff-a907-8eb51174ee4b" />

Model inclusion  
Next, the appropriate transistor models are included to accurately represent device behavior during simulation.

Observation setup  
Finally, the required nodes or signals are selected for monitoring. Together, these steps complete the simulation configuration.

Netlist and node naming  
M1 out in VDD VDD pmos w=0.375 L=0.25  
M2 out in 0   0   nmos w=0.375 L=0.25  

Here, M1 is the PMOS and M2 is the NMOS forming the CMOS inverter. The body terminals are properly tied to their respective supplies to account for body-bias effects on threshold voltage.

<img width="600" height="300" alt="Screenshot (147)" src="https://github.com/user-attachments/assets/4da6d0cf-bb6c-4916-b6f4-9d1bf7c9730e" />

Device sizing and parameters  
Both transistors use the same reference sizing (W = 0.375 µm, L = 0.25 µm), although in practical CMOS design the PMOS is usually made wider to compensate for lower hole mobility.

Simulation conditions  
• Cload = 10 fF  
• VDD = 2.5 V  
• Vin sweep: 0 → 2.5 V  

These values correspond to a long-channel technology where higher supply voltages are typically used.

---

### 28 - Lecture 2: Step4- Merge PMOS-NMOS load curves and plot VTC
