This project aims to develop a basic, relatively cheap, but accurate ultrastable +10.000VDC transfer standard for use in home measurement / metrology setups.

# **Background:**

In order to evaluate the accuracy of many DMMs, many references can be used to verify the validity of the measurements that are being made. These usually include current, voltage, and resistance sources. With DC voltage references, the Fluke 732 / 734C are such examples. However for the average hobbyist, these are simply too expensive. Thus, cheaper standards such as the AD584KH can be found online for around 20-50$. However these programmable standards usually have limited accuracy for 5.5 digits meters and above, and temperature coeffecients around the 15ppm/C mark. For 6.5, 7.5, and 8.5 digit meters, I decided to consider the LM399H, ADR1399KHZ, and ADR1000. 

The LM399 was more difficult to obtain, and the ADR1000 would require a much more precise voltmeter, so I decided to select the [ADR1399](https://www.analog.com/media/en/technical-documentation/data-sheets/adr1399.pdf). It is a 7.05V precise oven-compensated zener shunt reference. This has a temperature coeffecient of 0.2ppm/C, which is more favorable compared to the AD584. It is also generally affordable, at around CA$36/unit, which would be fine for a single unit.

# **Design:**

The design of the board is relatively simple:

![Board Block Diagram](Images/BlockDiagram.png)

**Power Supply:** I decided to add the LT3045 in the chance that a linear, low-noise supply is not available when testing. This allows for a +16-20VDC input on the board's input connector which is then fed to the [LT3045](https://www.analog.com/media/en/technical-documentation/data-sheets/lt3045.pdf), which can be selected as the board's main source through a selector switch. The regulator also features a PG (Power Good) indicator. The ultra-high PSRR LDO used here is usually deisgned for RF applications, or applications where low voltage rail noise is needed, along with a settable voltage output. Low noise at this stage is important, else it would usually reflect in the output. 

**Reference:** Not much to be said, the ADR1399 is to be placed far enough from the power supply circuitry as to not have any influence from the thermal changes from the power supply section.

**10V Gain Stage:** This stage consists of a Sallen-Key filter to reduce noise above 1kHz, the LTC2057 Op-Amp, and the feedback resistor network. In LTspice, it was noticed that injected noise on the power rails was making its' way to the output easily, thus I added a 1kHz SK filter on the input of the Op-Amp. The LTC2057 was decided on by various discussions on the EEVblog forums, but the low noise and low drift charecteristics were key. The LTC2057 is also listed under its' applications as designed for high-resolution data acquisition. For the resistor divider, a gain of approximately 1.41844 is required. While it would be possible and favorable to use a resistor network with an external trim resistor, i've opted for a 59k//2k//5k + 10k//5k feedback network combination. This combination provides nearly exactly the required gain.

The design was first simulated in LTSpice to verify its' functionality before implementing the design in Altium:

![LTSpice Simulations](Images/sims.png)

Initially, a 5mA draw was simulated, which should be more than plenty for this current design. White noise was added on the input to the LDO to simulate the output voltage and find any noise. Equally, a thermal simulation was performed with the tempcos of the various resistor network elements. Note, the tempcos are around 25ppm/C in the simulation, while the resistors used in the design are around 5ppm/C. 

The schematic is as follows:

![Schematic Diagram](Images/sch.png)

The board was laid out to prioritize thermal isolation of the ADR1399 from any other potential sources anywhere else on the board such as the LDO and Op-Amp:

**Layout:**

![Board Layout](Images/layout.png)

**Physical Board:**

![Board Top Side](Images/topside.png)
![Board Bottom Side](Images/bottomside.png)

Note: 10VREFv1.zip are the associated gerbers for this project that can be imported directly into the ordering utility of any major manufacturer

Building the boards is relatively simple, the DigiKey BOM is attached in the files for this project, and can be directly imported. The BOM cost w/o the WAGO latch terminals for the input and output side are about CA$70/board, or US$51.11/board. I will opt to test with Banana terminals for testing purposes, and to minimise thermal EMFs for now.

# **Verification:**

### 12/30/2025 - ADR1399K's Recieved - Burn In Started:

BOM Link: https://www.digikey.ca/short/1pw31f8j

![ADR1399K Burn In Tests](Images/adr1399testing.png)

The initial test setup is far from perfect, but I built a simple burn-in jig for the ADR1399KHZ part I recieved from DigiKey with a 2.67kOhm resistor in series with the zener for the 3mA zener current setting with a +15VDC supply for the heater and zener. The initial measurements were made with my Fluke 8845A 6-1/2 digit bench DMM which has been warming for several days before measurements. Equally, the ADR1399 had been burning in constantly for >48H before the measurements began. Measurements went overnight and around the 12H mark, I installed an insulating foam cap over the ADR1399's plastic package. As shown, the average range of the measurements after that point was around 25uV.

However, it is here that the initial accuracies of the ADR1399 parts need to be highlighted. The original voltage divider on the board was designed with a 7.05V input voltage. However the recorded zener voltage was around **6.93945V**, which is 110.55mV lower than expected, but within AD's specified initial accuracy range of -300 to +250mV. In order to combat this, the 3-parallel resistor divider on the board consisting of the (59K || 2K || 5K) can be swapped for (75K || 3K || 3K) to fix the gain. However, you must first determine your specific zener voltage, as the initial Vz is not guaranteed.

### 01/05/2026 - First PCBs recieved:

![PCBs](Images/pcbs.jpg)

The boards were produced by JLCPCB in China and they are up to standard. I opted for ENIG-RoHS finish on the boards mainly for the flatness of the finish and the boards. As a result, this will place less strain on component leads which is especially crucial for MLCCs and chip resistors which can fail prematurely or exhibit other effects under mechanical stress. However, despite meeting RoHS lead free criteria, the boards are assembled with ChipQuik T5 Sn63/Pb37 leaded solder paste for U1 and U2.

Note: Hot air was used for the ICs first, but a hot plate will work equally as well. The capacitors and resistors were hand soldered due to a lack of a paste stencil at the time, but can be assembled with hot air and solder paste (leaded)
Note 2: Some parts are delayed, thus the board is not fully assembled.

### 01/23/2026 - Overnight Tests

I was testing relatively quickly after the ADR1399s  were recieved. I started testing for the zener voltage around the 130H mark and they are now around **6.93930V**. Constraints on my test environment required a better test setup / enclosure so I quickly threw together a 3D printed box for the boards and then resin printed the lid, and clear coated so I could see through the top for testing:

![Enclosed Board](Images/boardenclosure.jpg)

The enclosure was fit for 4mm binding posts, and for solid 12 / 14AWG wire to be soldered to the board and then to the posts. The idea here is to avoid large temperature gradients or air currents past the ADR1399.

Since the boards had been sitting in this enclosure for a handful of weeks, I had the oppertunity to watch the voltage drift. Without statistics, the output voltage was around +10.000108V on my Fluke 8845A. I began strapping 20Mohm and 10Mohm resistors in parallel with the 75K || 3K || 3K bank to very slightly trim the gain downwards. I managed +10.00044 - 10.00045V with an approximate trim of 5Mohm in parallel with the network. 

![10MOhm Trim](10MTrim.jpg)

After about a week and a half of sitting at the same value, I have trimmed to hopefully the final value, of +10.00003V by using another 10M resistor in parallel; or 10/3 MOhm trim resistance. The data shown is for approximately 9 hours after the change took place:

![Final Trim](benchtest.jpg)

![Voltage Settling](ScreenShot_2.jpg)
