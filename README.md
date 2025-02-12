# Note

This project is a work in progress. Progress notes are shared below.


# Hardi-Navigator-AOG-Section-Control

 A PCB that adds AgOpenGPS section and rate control functionality to a Hardi Navigator 3000 trailed sprayer. This PCB replaces a PCB installed on the sprayer from the factory. No cutting of wires is required to install the new PCB and the sprayer can quickly be reverted to factory operation. Based very heavily on SK21's RC15 PCB.


 ## The Sprayer

 The sprayer is a Hardi Navigator 3000. This sprayer is generally by a John Deere 7410 tractor equipped with an AgOpenGPS guidance system. In the past this tractor was equipped with a Trimble CFX-750 and the Hardi JobCom connection was used for automatic section control. Since upgrading to AgOpenGPS the sections have been controlled manually. The intent of this project is to regain automatic section control.

  ![Hardi Navigator 3000](/images/Hardi_Navigator_3000.jpg)
 

 The sprayer is equipped with a 90' eagle boom and 6 section valves. The 6 section valves are of the EVC type, utilizing a DC motor to direct flow between the boom and tank return.

 ![Section Valves](/images/Fluid_Junction_Box.jpg)


 In cab control is through an HC5500 controller and Spray II controller. The HC5500 offers rate control while the Spray II controller offers manual section control and manual flow override.

  ![HC5500 and Spray II control boxes mounted in 7410 cab](/images/HC5500_SprayII.jpg "Cab controls")

  
 The sprayer is equipped with a Hardi JobCom PCB. This JobCom PCB is an upgrade PCB offered by Hardi which allows an external GPS system (Trimble, AgLeader, etc) to control the sections and rate via a serial connection to the HC5500. The JobCom PCB replaced the originally equipped Hardi Break-out PCB, a much simpler PCB with no microcontroller. While this project is underway I plan to leave the JobCom PCB installed in the sprayer and to study the Break-out PCB closely on my lab bench.

  ![JobCom PCB](/images/JobCom_PCB.jpg "JobCom PCB")

  ![Break-out PCB](/images/Break-out_PCB.jpg "Break-out PCB")


## The upgrade paths

I have thought of many ways to add AgOpenGPS section control to this sprayer.

My preferred path was to decode the serial protocol between the JobCom/HC5500 and Trimble CFX-750 (with VRA unlock). After taking several serial logs I was able to see patterns that matched the section control, but I was ultimately unable to decode the handshaking required (bi-directional connection). I snooped tx and rx simultaneously with separate single circuits, the data incoming to windows did not always align between the two connection. Perhaps with a better serial snooper I may have made more progres.

There can be found on the internet similar sprayers with AgOpenGPS section control added. The installations all involve relays and/or cutting wires. Cutting wires is something that I do not wish to do. I want to be able to return the sprayer to it's original condition quickly with minimal effort.

Ultimately the path that I have decided on is to take an existing project, [SK21's RC15 control board](https://github.com/SK21/AOG_RC), and to modify the project to suit my own needs.


## SK21 RC15

SK21 has developed the RC15 rate and section control . It has the following features.

- uses an ESP32 with wifi communication
- optionally a W5500 Ethernet Module can be added
- mostly surface mount parts
- can control two rates and 7-14 sections
- (2) DRV8870 motor drivers with reversible outputs for rates
- (7) DRV8870 motor drivers for either 7 reversible (motorized ball valve) outputs or 14 single ended (high or low) section outputs
- (4) 5V analog inputs (or 2 differential inputs)
- (2) optically isolated digital (rate/pulse) inputs, up to 12V
- RS485 chip & header
- 3.3V I2C header
- supports optional external RelayDriver5 I2C board
- connections through ampseal plug

A render of the SK21 RC15 PCB below

![SK21 RC15](images/K21_RC15.jpg "SK21 RC15")

My needs are as described below
- 6 reversible (motorized ball valve) outputs
- 1 reversible output for rate control
- 1 12v input for rate
- wifi
- ethernet
- female db37 plug connection for valves and flow meter
- screw terminal or similar connection for regulator
- a very specific mounting pattern to fit in the control box
- input power via ring terminals

If the K21 RC15 was a different shape with different plugs then it would be ideal for this application. As a bonus, the firmware portion is already completed, the AgOpenGPS integration (through the rate control app) is already completed, the HC5500 and Spray II control boxes will be redundant, and the system will be capable of variable rate.

It should be straight forward to edit the SK21 RC15 schematic to add the plugs required, and then to create a custom PCB routing to create the required form factor.


## Data Collection - JobCom/Break-out PCB

The JobCom or Break-out PCB connects to the HC5500 and Spray II control boxes via a db37 connector. The new board will not utilize this connection of the HC5500/Spray II boxes. Still this connection is usefull, as the pinout is described both in the PCB silkscreen as well as in the Hardi manual.

![Break-out PCB](/images/Break-out_PCB_markup.jpg "Break-out PCB")


The power connection to both the JobCom and Break-out PCB boards is quite interesting. Ring terminals are bolted to the board where bare copper is exposed.

![JobCom Power Connection](/images/JobCom_power_connection.jpg "JobCom Power Connection")


The JobCom/Break-out PCB boards connect to the flow regulator with screwless terminals blocks. In my case there is also a connection to a foam marker harness. My sprayer is not equipped with foam markers and the harness appears to be dealer fabricated. I plan to remove this harness as it serves no purpose.

![JobCom Regulator Connection](/images/JobCom_regulator_connection.jpg "JobCom Regulator Connection")


What is of most interest is the DB37 connector that connects via cable to the Fluid Junction Box at the rear of the sprayer. Unfortunately the pinout at this connector does not appear to be described in any Hardi manual.

![Break-out PCB](/images/Break-out_PCB_markup.jpg "Break-out PCB")


## Data Collection - Fluid Junction Box Cable

At the rear of the sprayer exists the Fluid Junction Box. Connected to the Fluid Junction Box are the 6 section valves, the flow meter, and the wheel speed sensor. The cable does not appear to have a direct 1-1 pinout.

![Fluid Junction Box](/images/Fluid_Junction_Box_silkscreen.jpg "Fluid Junction Box")


## Data Collection - The missing pinout

The db37 to Fluid Junction Box connector pinout is crucial to know for the project to proceed. Through continuity tests between the connector in question and the labelled db37 to HC5500/Spray II connector I was able to determine much of the pinout. Some further continuity tests between the Fluid Junction Box and the connector were performed to verify the results. The secondary Fluid Junction Box tests were more troublesome to perform so not every pin was tested, but the results were as expected.

![DB37 Pinout](/images/db37_pinout.jpg "DB37 Pinout")

![DB37 Pinout](/images/EDA_db37_pinout.jpg "DB37 Pinout")


# Data Collection - The Red Mounting Plate

Ideally the new PCB could mount in a similar method to the JobCom or Break-out PCB. Those PCBs mount using plastic standoffs to a red steel plate. It appears possible to mount both the Break-out PCB and the new board simultaneously without needing to drill any new holes. Leaving the Break-out PCB installed (disconnected) will make it very simple to bring the sprayer back to factory condition.

The red steel mounting plate was modelled in CAD so that a dxf could be imported into the EDA.

![Mounting Plate](/images/RedMountingPlate.jpg "Mounting Plate")

![Mounting Plate](/images/EDARedMountingPlateDXF.jpg "Mounting Plate")


# Data Collection - PCB Standoff

The steel plate has a thickness of 1.80mm (0.071") with 4.63mm (0.182") diameter mounting holes. The Break-out PCB has a thickness of 1.66mm (0.065") with 3.80mm (0.150") diameter mounting holes. The distance between the steel mounting plate and the PCB is 6.35mm (0.25").

The plastic standoffs appear to be Keystone 8808 which is readily available from Digikey and the likes https://www.digikey.ca/en/products/detail/keystone-electronics/8803/317140

![PCB Standoff](/images/PCB_Standoff.jpg "PCB Standoff")
![PCB Standoff](/images/PCB_Standoff2.jpg "PCB Standoff")
![PCB Standoff](/images/PCB_Standoff3.jpg "PCB Standoff")



