This project aims to develop a basic, relatively cheap, but accurate ultrastable +10.0000VDC transfer standard for use in home metrology setups.

# **Background:**

In order to evaluate the accuracy of many DMMs, many references can be used to verify the validity of the measurements that are being made. These usually include current, voltage, and resistance sources. With DC voltage references, the Fluke 732 / 734C are such examples. However for the average hobbyist, these are simply too expensive. Thus, cheaper standards such as the AD584KH can be found online for around 20-50$. However these programmable standards usually have limited accuracy for 5.5 digits meters and above, and temperature coeffecients around the 15ppm/C mark. For 6.5, 7.5, and 8.5 digit meters, I decided to consider the LM399H, ADR1399KHZ, and ADR1000. 

The LM399 was more difficult to obtain, and the ADR1000 would require a much more precise voltmeter, so I decided to select the [ADR1399](https://www.analog.com/media/en/technical-documentation/data-sheets/adr1399.pdf). It is a 7.05V precise oven-compensated zener shunt reference. This has a temperature coeffecient of 0.2ppm/C, which is more favorable compared to the AD584. It is also generally affordable, at around CA$36/unit, which would be fine for a single unit.

# **Design:**

The design of the board is relatively simple:

![Board Block Diagram](BlockDiagram.png)

**Power Supply: **I decided to add the LT3045 in the chance that a linear, low-noise supply is not available when testing. This allows for a +16-20VDC input on the board's input connector which is then fed to the [LT3045](https://www.analog.com/media/en/technical-documentation/data-sheets/lt3045.pdf), which can be selected as the board's main source through a selector switch. The regulator also features a PG (Power Good) indicator. The ultra-high PSRR LDO used here is usually deisgned for RF applications, or applications where low voltage rail noise is needed, along with a settable voltage output. Low noise at this stage is important, else it would usually reflect in the output. 

**Reference: **Not much to be said, the ADR1399 is to be placed far enough from the power supply circuitry as to not have any influence from the thermal changes from the power supply section.

**10V Gain Stage: **This stage consists of a Sallen-Key filter to reduce noise above 1kHz, the LTC2057 Op-Amp, and the feedback resistor network. In LTspice, it was noticed that injected noise on the power rails was making its' way to the output easily, thus I added a 1kHz SK filter on the input of the Op-Amp. The LTC2057 was decided on by various discussions on the EEVblog forums, but the low noise and low drift charecteristics were key



