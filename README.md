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

### Lecture 1: Why is SPICE Simulation needed?

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

### Lecture 2: Introduction to basic element in Circuit Design - NMOS

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

### Lecture 3: Strong inversion and threshold voltage

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

