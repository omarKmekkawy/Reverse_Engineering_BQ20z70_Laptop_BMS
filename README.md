# Reverse Engineering BQ20z70 Laptop BMS
In this repository, I will talk about how I have reverse-engineered the communication protocol of the BQ20z70 Laptop BMS and how I got the full control of it.

# Why I am doing this work ??
I noticed that many lithium battery recyclers throwing the BMS boards in the garbage, although they are important and could be recycled also. I have noticed that you will find little information about how to reuse the laptop BMS on the internet (Because it needs experience and it could bring lots of money !), so I am doing this to encourage the community to share their experience and help each other. This repository is the result of studying more than 15 laptop BMS boards and experimenting with them.


# Introduction
The BMS is an electronic system that manages a rechargeble battery (cell or battery pack), such as by protecting the battery and monitoring its state, balancing each individual cell, and making sure that it operates within the safe operating area.

Features that the BMS provides:
```
1- Reports the appropriate charging current needed for constant current charging and the appropriate charging voltage needed for constant voltage charging to a smart charger using SMBus broadcasts.
2- Determines the chemical state of charge of each battery cell using Impedance Track™ and can reduce the charge difference of the battery cells in fully charged state of the battery pack gradually using cell balancing algorithm during charging. This prevents fully charged cells from overcharging and causing excessive degradation and also increases the usable pack energy by preventing premature charge termination.
3- Supports pre-charging/zero-volt charging.
4- Support fast charging.
5- Supports charge inhibit and charge suspend if battery pack temperature is out of temperature range.
6- Reports charging fault and also indicate charge status via charge and discharge alarms.
```

The BMS might also has a communication protocol that enables controlling it using any embedded system, also the embedded system could read many internal registers inside the BMS such as:

```
1- The voltage for each individual cell.
2- The voltage of the pattery pack
3- The current (Positive value for charging, Negative for discharging, and zero for idle).
4- The temperature of the battery pack.
5- Estimated time to full (when charging).
6- Estimated Time to empty (when discharging).
7- Full charge capacity.
8- Remaining capacity.
9- Cycle count.
10- Absolute/Relative state of charge.
11- Charging voltage.
12- Charging current.
14- Battery status flags.
15- Mosfets status.
16- Charging status.

and more ....
```

The Laptop BMS is controlled using the [SMBus](https://en.wikipedia.org/wiki/System_Management_Bus) protocol, its special type of I²C bus with (Low frequency speed ≃ 10KHz and it might requires low value pull up resistor such as 1.2 KOhm for both the clock and data lines). The computer uses this protocol to control the BMS and also could read its basic info.

We can do lots of projects using the BMS such as:
```
1- DIY power walls (using lithium batteries for storing solar energy) and monitoring each battery pack.
2- DIY electric scooters.
3- DIY electric cars and robots.
4- Battery backuped devices, power banks, and UPSs.

So many applications ..
```

Once the battery is used too many times, its life degrades by the usage and once the BMS detects that one of the cells is damaged, it locks down the battery and prevents any further charging/discharging process.

# Disclaimer
**Warning:** Laptop batteries are so dangerous, they contain high energy and could be exploded if you make any mistake with them or damage any device that connected with them. I am not responsible for any damages or injuries that occurs with anyone, you are doing this with your own risk.

# Safety First!!
When dealing with lithium batteries, you will need to keep them in well cleaned environment (Keeping the batteries far from any metal object), Keep the batteries in well protected boxes, keep them far from any liquids, use proper safety tools for handling and containing lithium fires, keep the batteries far from childrens, and use proper tools for protecting your own computer such as:
```
1- Using USB isolators for isolating the computer from the microcontroller that connected to the battery.
2- Make sure to fasten all your wires and keeping them intact.
```

# Getting Started

After doing some researches on my single cell BMS kit (its on my previous repository) [BQ27500EVM_Reverse_Engineering
](https://github.com/rxtxinv/BQ27500EVM_Reverse_Engineering), I discovered that I could use the EV2300 SMBus interface to read my laptop BMS information and also controling it.

This is the BMS that I am using currently:
<img src="./Pictures/BMS_Components.jpg" width="881" height="446">

By searching with the IC's name, we will find its datasheet [here](https://www.ti.com/product/BQ20Z70-V160)

In the datasheet we will find the system block diagram of the BMS:

<img src="./Pictures/BMS_Diagram.jpg" width="881" height="446">

The BMS is based on BQ20z70 BMS IC, and also contains a BQ29330 analog front end (AFE) IC so it could read all of the voltages of the cells, providing balancing feature for all cells, and it provides multible layers of protection (for increasing realability) such as:
```
1- Over/Under voltage protection for each cell.
2- Over current charge/discharge protection.
3- Short circuit protection.
3- Over temperature protection for charging and discharging.
4- Impalance protection.
5- Over charging protection.
```

# How To Identify The SMBus Pins On The BMS
Its easy to identify both of (Data, Clock) pins. In some cases, it might be written on the board under each pin like this (for example):

<img src="./Pictures/Socket_Pinout_Example.jpg" width="881" height="500">

| BMS Board  | Explanation  |
| ------------- | ------------- |
| P+  | Pack's positive pin  |
| P+  | Pack's positive pin  |
| D  | SMBus-Data  |
| C  | SMBus-Clock  |
| ID  | Detection pin for the laptop to identify the insertion of the battery  |
| B/I  | Unknown for now |
| P-  | Pack's negative pin  |
| P-  | Pack's negative pin |

The other method is nothing written on the BMS board, you will need to use the multimeter to trace both of the (Data, Clock) pins [You have to disconnect the batteries from the BMS].

Now after understanding how the BMS works, its time to connect it to the ([EV2300](https://www.ti.com/lit/ug/sluu159e/sluu159e.pdf?ts=1628836111764) or [EV2400](https://www.ti.com/tool/EV2400)) and inserting the logic analyzer inline with them for sniffing the protocol.

<img src="./Pictures/BQ20z70_EV2300_LogicAnalyzer.jpg" width="881" height="446">

The connection diagram:
<img src="./Pictures/Connection Diagram.jpg" width="765" height="397">



# Project Status
* Documenting the project (Current task, Not completed yet).

# Useful Resources
Check the [BQ20z70](https://www.ti.com/product/BQ20Z70-V160) product's page.
## Datasheet
* [SBS 1.1 Compliant Gas Gauge Enabled w/Impedance Track Tech for bq29330 datasheet (Rev. B)](https://www.ti.com/lit/gpn/bq20z70-v160)
## Application Notes
* [Data Flash Programing and Calibrating the bq20zxx Family of Gas Gauges (Rev. E)](https://www.ti.com/lit/pdf/slua379)
* [Battery Pack Production Flow With bq20zXX](https://www.ti.com/lit/pdf/slua391)
* [Quick-Start Guide for bq20zxx Family Gas Gauges (Rev. A)](https://www.ti.com/lit/pdf/slua421)

## User Guide
* [bq20z70-V160 + bq29330, bq20z75 TRM](https://www.ti.com/lit/pdf/sluu310)
# Support Me
If you seen my work and it helped you, please support me on LinkedIn by endorsing my skills. It will be appreciated :grinning:.
<p>
  <a href="https://www.linkedin.com/in/omar-mekkawy/" rel="nofollow noreferrer">
    <img src="https://img.shields.io/badge/linkedin%20-%230077B5.svg?&style=for-the-badge&logo=linkedin&logoColor=white"  height="40" width="90" alt="linkedin">
  </a> &nbsp;
</p>
