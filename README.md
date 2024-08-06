# NASSCOM-VSD-SoC-Design-and-Planning
# Table of contents

# THEORY 1: OPEN-SOURCE EDA, OPENLANE & SKY130 PDK

## What is an RTL to GDSII flow?

## Different files in workflow?
* **Library Exchange Format (LEF) file**: Place and route (PnR) does not need information about the logic or complete layout.
It just needs the pin positions and boundary information. This minimal and abstract information is provided to the tool by the LEF file.
LEF file also serves the purpose of protecting intellectual property. LEF file contains the metal layers and pin locations. the difference between the layout and the LEF file is shown below:

![image](https://github.com/user-attachments/assets/4b9826d9-31d5-4588-9c4e-7651f074ffa8)

* **Design Exchange Format (DEF) file**: DEF represents the complete physical layout of an integrated circuit. LEF is more metal-specific whereas DEF is placement-specific. DEF could contains netlist, routing, placement, scan info, port... 
  
# LAB 1: OPEN-SOURCE EDA, OPENLANE & SKY130 PDK
Characterization of Synthesized Results
## Directory structure in openlane

## Design Preparation Step

## Review files after design prep and run synthesis

## Characterization of Synthesized Results
First, we calculate the flip-flop ratio. Now, if we see the synthesized results we find that
```
Number of D Flipflops : 1613
Total number of Cells : 14876
```
![image](https://github.com/user-attachments/assets/ed93bbb4-4d19-42d3-88ce-cf1d5a8f89e8)

Hence, flip flop ratio = (Number of D Flipflops)/(Total number of Cells) and Flipflop percentage = FF ratio * 100
```
FF Ratio : 0.1084
FF Percentage : 10.84 %
```

Also, Now if we go to:
```
/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/11-07_06-37/results/synthesis$
```
we can find the synthesis results stored here now.

# THEORY 2: GOOD FLOORPLAN VS BAD FLOORPLAN & INTRODUCTION TO LIBRARY CELLS
## LIBRARY BINDING AND PLACEMENTS
### Netlist binding and initial place design
Each component in the netlist (AND gate, OR gate, FF, etc) has a corresponding physical dimension of square or rectangle shape i.e. they have width and height. 
These components are available in the library as boxes. In addition to dimensions, the library has timing (delay) information for these boxes or components. The library also 
has information about when the component's output is changed. Additionally, there are different versions of the same component, each with varying properties (size/delay). 
![image](https://github.com/user-attachments/assets/269f2225-b339-46e0-b390-9b030b3a20c7)

In the image above, there are three different sets of each element. The larger elements are faster but take up more space, while the smaller elements occupy less area but operate more slowly compared to the larger ones.

### Placement
In placement, each netlist component which is in the shape of boxes from the library is placed on the floorplan. The figure shown below shows the floorplan on
the left, the netlist in the middle, and the physical view of the components on the right.
![image](https://github.com/user-attachments/assets/fe00b6db-6199-435b-bd9c-e74401f69287)
In the floorplan, the preplaced cells are already there and they are not moved.

### Optimize placement using estimated wire length and capacitance
In this phase, the physical blocks corresponding to each component are placed such that the input block is close to the input port and
the output block is close to the output port. The tool positions the blocks based on the estimated wire lengths, as illustrated in the figure below.
![image](https://github.com/user-attachments/assets/9bcc882c-80d0-4c9e-b604-7a3b3f8111f3)

Based on the estimated positioning of the blocks the buffers/repeaters are placed if the wire length becomes too large. This is due to the reason that
large wires result in large capacitance. The buffer placement is shown below
![image](https://github.com/user-attachments/assets/8ffefe36-2851-49f2-9937-5d5d88d7e711)

## CELL DESIGN AND CHARACTERISATION FLOWS
* **Standard Cells:** These are basic logic gates (like NAND, NOR, XOR, etc.) or more complex functional blocks (like flip-flops or adders) designed to be reusable across different IC designs. They are characterized by their functionality, area, power consumption, and timing characteristics.

* **Library:** In the context of IC design, a library (or cell library) consists of a collection of standard cells.

  Each cell in the library is optimized for specific parameters such as speed (delay), power consumption, area (size), and drive strength.

# LAB 2: FLOORPLANNING & PLACEMENT

## Steps to run floorplan
To ensure a smooth floorplanning process, designers must pay attention to certain variables, known as switches, which can significantly impact the floorplan. 
For instance, the utilization factor and aspect ratio are among the important switches. 
Designers need to verify that these parameters align with the project requirements before initiating the floorplanning stage. 
The file _README.md_  in the openlane configuration displays the different variables of the design flow. The path is shown below :
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/configuration
```
![image](https://github.com/user-attachments/assets/3b1c36f4-bad7-4770-b577-024ddda70ecf)

The image below illustrates various types of switches involved in the floorplanning phase and their description.
![image](https://github.com/user-attachments/assets/09b5c690-8318-4a79-a764-951d6043c29c)

One can set any of these variables depending on where we are in the flow.

* The default value of these variables is set in the  _floorplan.tcl_ file of openlane configuration. The file path is the same as for _README.md_ and is shown below :
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/configuration
```
![image](https://github.com/user-attachments/assets/9267d8b0-ceb6-4677-aeb6-f3b508e9e084)

In the figure above several floorplan variables are set by default. One important variable to mention here is **FP_IO_MODE** below:
```
set ::env(FP_IO_MODE) 1; # 0 matching mode - 1 random equidistant mode
```
which is set as '1' when IO pins are random equidistant.
* In the picorv32a design, there are _config.tcl_ and _sky130A_sky130_fd_sc_hd_config.tcl_ files which contain design-specific floorplan settings. 
The file path is shown in the image below:

![image](https://github.com/user-attachments/assets/28f3bbe2-552e-4e43-baba-952ddb5c220b)

The file contents of _sky130A_sky130_fd_sc_hd_config.tcl_ is shown below
![image](https://github.com/user-attachments/assets/d44738ca-58fe-44c3-a465-ca594fc5bca6)


The file contents of _config.tcl_ is shown below file
![image](https://github.com/user-attachments/assets/47a741d5-8e56-41a5-859a-10831525c622)

The core utilization and IO metal layers are added in the file through following text
```
set ::env(CLOCK_NET) $::env(CLOCK_PORT)
set ::env(FP_CORE_UTIL) 65 
set ::env(FP_IO_VMETAL) 4
set ::env(FP_IO_HMETAL) 3
```

The priority is given to _sky130A_sky130_fd_sc_hd_config.tcl_, then _config.tcl _and then to the default _floorplan.tcl_ file above.





## Review floorplan files
After floorplan execution we go to the _runs_ folder of picorv32a and open the latest date file name. Here one can check the implemented floorplan variables from the logs. 
The file name _ioPlacer.log_ will give metal layers number for verical and horizontal IO pins. The path to the file is below:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-07_10-33/logs/floorplan
```
![image](https://github.com/user-attachments/assets/78509e67-8751-4889-bc4e-7f0e2a810242)

The contents of file are shown below
![image](https://github.com/user-attachments/assets/c67d5f7b-953b-482b-b1f7-4bf4dfec2bf3)

Remember that if FP_IO_VMETAL was set to 4, the floorplan will have the FP_IO_VMETAL 5 and similarly if FP_IO_HMETAL was set to 3, the floorplan will have the FP_IO_HMETAL 4.

Now to check what configuration settings were used to run floorplan we can open the _config.tcl_ from the directory given below
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-07_10-33
```
![image](https://github.com/user-attachments/assets/43876961-bcc4-4c17-aed4-e37a85c63325)

The above figure shows that **FP_CORE_UTIL = 35, FP_IO_HMETAL = 4 and FP_IO_VMETAL = 3.**


## Review floorplan layout in magic

When the floorplaning is completed, to view the results go to the path as shown below :
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-07_10-33/results/floorplan
```
![image](https://github.com/user-attachments/assets/d366b180-139e-47e3-9e90-f754c7ee8805)

open the design exchanfe file (.def):
These results are useful. For example: we can see the die area :
![image](https://github.com/user-attachments/assets/0d3c43b7-9e3d-4dbb-b4bf-c3d20656b50d)

1µm = 1000 data base units and therefore the area is (0 0) (660.6µm 671.4µm)

Now, to open this ".def" file in magic, use the following command:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def
```

**Important steps to review floorplan in magic**
Action | Steps 
--- | :--- 
Seconds | 1. Press S to select the entire design.<br/> 2. Press V to align it vertically to the middle of the screen.
Seconds | 1. Press S to select the entire design.<br/> 2. Press V to align it vertically to the middle of the screen.
Seconds | 1. Press S to select the entire design.<br/> 2. Press V to align it vertically to the middle of the screen.

<img width="490" alt="image" src="https://github.com/user-attachments/assets/655325c8-1702-4494-aea1-c850d8c8f513">
<br/> 
<img width="490" alt="image" src="https://github.com/user-attachments/assets/836df4d9-8545-43c3-937d-cf45b3b49d24">
<br/> 
<img width="525" alt="image" src="https://github.com/user-attachments/assets/76ec4a65-69ba-4592-93b8-6dd1c1fd5bb7">


## Placement in openlane

After completing floorplanning, the design process moves on to the placement stage. The placement-related variables/switches can be seen in the README.md file
under the directory path:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/configuration
```
The variable **PL_TARGET_DENSITY:** describes the placement density of cells in the core. It reflects how spread the cells would be in the core area. 
1 = closely dense, 0= widely spread (Default: `0.55`). Initially, the placement is coarse to avoid congestion. When the design approach towards closing then timing
constraints come in, where the cells would be closed as much as possible to avoid delays.

![image](https://github.com/user-attachments/assets/6fc649d5-ea91-4331-9a4c-24a27b655b6f)

The placement step comprises two main phases:

**Global Placement:** It is a coarse placement where no legalization is happening. During this phase, the tool determines the approximate locations for all the standard cells in the design. 
**Detailed Placement:** In this phase, the tool finalizes the exact positions for all the standard cells and ensures the placement is legal. 
Legalization involves verifying that no standard cells overlap and that they are all correctly positioned within the 
designated site rows. To initiate the placement process, use the following command:
```
run_placement
```
During placement execution the reduction of half parameter wire length is the main focus. The placement is stop when the overflow is converged
After the Placement is done. To view the results Go to the following location:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-07_10-33/results/placement
```

And then we can see 'picorv32a.placement.def' file. To open it using MAGIC use the following command:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
```

![image](https://github.com/user-attachments/assets/0b680081-0bfa-4fcf-97f4-df6021a3aa33)

<img width="635" alt="image" src="https://github.com/user-attachments/assets/d8abc2cd-e549-43ca-ba6c-d203ff721da8">

# LAB 3: INTRODUCTION TO SKY130A

## HOW TO MAKE CHANGES WHILE BEING IN THE FLOW?
One can change the floorplan variables like core utilization and IO mode
**Example** : TO CHANGE THE IO pins alignment in the layout, first we can verify the current configuration of the Pins, Go to the following directory as shown in the image below:

```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-07_10-33/results/floorplan
```

Then use the command to open the '.def' file in magic:
```
 magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def
```

![image](https://github.com/user-attachments/assets/1807d4f2-b6f0-487d-ac58-3ceb942f87b6)
As we can see pins are randomly equidistant. There are four strategies supported by IO placer (Open source EDA tool). 
Now, if want it to change to some other IO pins strategy, first go to the following directory and open _floorplan.tcl_ file:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/configuration
```

![image](https://github.com/user-attachments/assets/aa38c38a-6754-4fbc-beb4-7b68bfdd05da)

From here we can see the switching variable **FP_IO_MODE = 1**, hence pins are randomly equidistant. Now, we run the following command and change the IO placer settings:
```
set ::env(FP_IO_MODE) 2
```
Go to the tab below to run the command to change the IO placer settings and run the floorplan again.
![image](https://github.com/user-attachments/assets/ab53d829-9875-48b1-9bfb-3f0a7f210270)

 Now, we can check the change in the IO placer strategy: We can see that _.def_ file has been updated from the time stamps and date:
 
 ![image](https://github.com/user-attachments/assets/eddeb530-db58-4f4d-8d86-516d7750446a)

 Now, let us open it in magic using the earlier used command and we see that IO pin configuration is changed

![image](https://github.com/user-attachments/assets/6f83ebd8-84ea-478d-91cf-84d7bb20c128)


## Steps to get clone of git "vsdstdcelldesign" repo

The repository "vsdstdcelldesign" contains the _.mag_ file for the inverter and spice models for sky130 nmos/pmos transistors.

Got to the openlane directory and run the following command to clone the git repository:
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
![image](https://github.com/user-attachments/assets/1e1f7759-6098-4136-bd07-da9e49a831f6)

As we can see from the above image the repo has been successfully copied to the openlane directory. Now, we will open the _.mag_ file and to do that we require the _sky130A.tech_ file from the following directory:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic
```
we copy the file using the following command
```
cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
```
![image](https://github.com/user-attachments/assets/c69fb6bf-13b6-41da-9cd7-53fb42cd155a)

Now, open the _sky130_inv.mag_ file in magic:
```
magic -T sky130A.tech sky130_inv.mag &
```

![image](https://github.com/user-attachments/assets/75cae491-1763-4a4d-8cb4-8befff7bb9b2)

## SKY130 INVERTER LAYOUT BASIC LAYERS 

The source, drain, gate, VDD, and ground terminals of the inverter opened in the magic tool are shown in Figure below. 
When polysilicon crosses the ndiffusion region it is termed as 'NMOS' and when it crosses the pdiffusion region it is termed as 'PMOS', the same is verified in the image below:

<image src = "https://github.com/user-attachments/assets/65412cfb-bc71-41b4-b827-c4525de7e3f5" width="500" height="500" >

## STEPS TO CREATE SRD CELL LAYOUT

Details to create a std cell layout are here [Click here](https://github.com/nickson-jose/vsdstdcelldesign?tab=readme-ov-file)

## EXTRACT THE SPICE NETLIST IN MAGIC
In the tckon window, use the following command:
```
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```
![image](https://github.com/user-attachments/assets/5061124b-37e9-463d-a2e9-aa9a9cbfb147)

![image](https://github.com/user-attachments/assets/0e526a51-7f51-4564-8d41-2a9d1123e294)

Now let's open the extracted SPICE file of sky130A inverter
![image](https://github.com/user-attachments/assets/b06d14d6-187c-4b1d-845a-e4739b783014)

# SKY130 TECH FILE LABS
## CREATING FINAL SPICE DECK USING SKY130 TECH
Make the following changes in the '_sky130_inv.spice_' file:

![image](https://github.com/user-attachments/assets/6bb40648-5912-49f7-88c5-a82445f6562f)

Now before running the _sky130_inv.spice_ file let's check the model files (nshort.lib and pshort.lib) under the libs directory.
![image](https://github.com/user-attachments/assets/44979c5e-3473-4570-9cc4-cef72ae77e9f)

Now to simulate in **ngspice**, use the following command while in the 'vsdstdcelldesign' directory:

```
ngspice sky130_inv.spice
```
![image](https://github.com/user-attachments/assets/80d30b3a-120f-45ec-ba24-2b558aa56db6)

Now, to open the plot use ```plot y vs time a```  in the ngspice terminal

![image](https://github.com/user-attachments/assets/d9c38300-4f8a-4b99-b5c8-7b8678e98138)

The spikes are a bit larger at the edges of inverter output. 
We can increase the value of load capacitor (C3) in spice netlist to 2fF. 
The results are shown below.
![image](https://github.com/user-attachments/assets/13ff72a8-e892-46b9-9e9e-ce49f5269608)

## CHARACTERIZATION OF INVERTER USING SKY130 TECH FILES

CHARACTERIZE INVERTER USING SKY130 TECH FILES
To characterize the inverter, we analyze the ngspice plot and determined the following parameters:

* **Rise Time:** The time for the output waveform to transition from 20% to 80% of its maximum value.

From plot points: (x0 = 2.18192ns, y0 = 0.66049) to (x0 = 2.24571ns, y0 = 2.64018). Calculated Rise Time = 0.0634 ns

* **Fall Time:** The time for the output waveform to transition from 80% to 20% of its maximum value.

From plot points: (x0 = 4.0525ns, y0 = 2.63976) to (x0 = 4.09516ns, y0 = 0.659249). Calculated Fall Time = 0.0422 ns

* **Propagation Delay(Cell Rise Delay):** The time for the output to transition 50% in response to a 50% change at the input.

From plot points: Input(x0 = 2.15018ns, y0 = 1.65018) to Output(x0 = 2.21088ns, y0 = 1.65). Calculated Propagation Delay = 0.064 ns

* **Cell Fall Delay:** The delay for the output to transition 50% due to a 50% change at the input.

From plot points: (x0 = 4.04997ns, y0 = 1.65) to (x0 = 4.07748ns, y0 = 1.65). Calculated Cell Fall Delay = 0.0277 ns

We have now characterized the inverter cell for a room temperature of 27 degC. Similarly, this cell can be characterized for different process, voltage, and temperature (PVT) corners to fully
characterize this cell for different PVT corners.

With these parameters successfully characterized, the next step is to create a LEF file from this cell, which will be plugged into openlane picorv32a design flow.

## INTRODUCTION TO MAGIC TOOL AND DRC RULES

The details about the MAGIC tool and its DRC rules can be seen [here](http://opencircuitdesign.com/magic/)

## INTRODUCTION TO SKY130 PDK

SKYWATER SKY130 PKD documentation can be found [here](https://skywater-pdk.readthedocs.io/en/main/). 
We use the following command to download the Lab files used in this tutorial. The current location should be the home directory:

```
sudo wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
```
Once downloaded the zip file we extract it using the command

```
sudo tar xfz drc_tests.tgz
```

![image](https://github.com/user-attachments/assets/2edf16fe-2b29-4148-8ba1-5780800ceaaf)

In the downloaded files, the .magicrc file serves as the start-up script for MAGIC.

## INTRODUCTION TO MAGIC & STEPS TO LOAD SKY130 TECH-RULES

Run the ```command magic -d XR``` to open the Magic tool.

Now open the _met3.mag_ file in magic.

![image](https://github.com/user-attachments/assets/d5364299-6d90-4efd-a168-f332c7b401f7)

## FIXING POLY.9 ERROR IN SKY120 TECH FILE

Open the _poly.mag_ file in magic
![image](https://github.com/user-attachments/assets/c031f955-df01-4394-bad3-1914ac6735d6)

Zoom in on the "Incorrect poly.p" layout and measure the spacing between the poly resistor and poly, using the "box" command.
The measurement shows a spacing of 0.210 µm, which is smaller than the "poly.9" DRC rule (0.480 µm). But, we do not get a DRC error. 
Let's correct this problem.
![image](https://github.com/user-attachments/assets/1dc2220c-fbbb-4657-aed9-a350615bdaba)

![image](https://github.com/user-attachments/assets/6e466798-cb03-4479-a6b5-8c851c211531)

Open the _Sky130a.tech_ file located in the drc_tests directory. Search for the "poly.9" keyword and apply the changes shown in the images below. Save the file after making the modifications.

![image](https://github.com/user-attachments/assets/b09a5dd1-c5c2-4e5b-ab85-d49484d353ed)

Reload the _sky130A.tech_ file and re-run the DRC check by running the following commands in tkcon 2.3
```
tech load sky130A.tech
drc check
```
Now we see that DRC error is shown by magic.
![image](https://github.com/user-attachments/assets/12e8e56d-0e85-44bb-bf25-64d51ce7e7ef)

# LAB 4: PRE-LAYOUT TIMING ANALYSIS & IMPORTANCE OF GOOD CLOCK TREE

## Steps to convert the Grid Info to Track Info

**Certain conditions for making a standard cell are**: 1) The input and output ports should lie on the intersection of the vertical and horizontal tracks, 2) the width of the std cells should be odd multiple of the
track horizontal pitch and height should be odd multiple of track vertical pitch. 
To check the track go to the following directory

**Tracks:** Tracks are used in routing stages, where routes are metal traces (met1, met2, ...). Track information tells where the routes (metal layers) will go during the routing stage. During PnR we need to specify where the routes will go 
and this info is given by tracks.  _tracks.info_ can be found from the following path
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd
```
open the tracks.info file
```
less tracks.info
```
Open the file name _tracks.info_. This file specifies pitch, spacing, and other relevant details necessary for efficient routing. Each metal layer has an X and Y direction.
![image](https://github.com/user-attachments/assets/5e81a07a-2c58-4812-be1b-d9b0bdd73f4c)


* Now to open the custom Inverter Layout in Magic, first go to the 'vsdstdcelldesign' directory and then run command:
```
magic -T sky130A.tech sky130_inv.mag &
```
Ports are in the li1 (loci) metal layer and we check if these ports are on the intersection of the li1 () horizontal and vertical track. To check this, in magic press G to turn on the grids

To set grids as tracks of locali layer, use the following command:
```
grid 0.46um 0.34um 0.23um 0.17um
```
![image](https://github.com/user-attachments/assets/46bdd678-9377-40e3-8110-babdea509036)

* li1 layer is fully on the grid layer. We can see that input and output ports lie on the intersections of vertical and horizontal tracks. This satisfies the condition 1.

![image](https://github.com/user-attachments/assets/e7104d96-4fda-40b9-8495-8b235085f8a0)

* We can also see that the width of the std cell is equal to the 3 grid/track boxes. As mentioned the standard cell's width should be an odd multiple of the horizontal track pitch, 
and its height should be an odd multiple of the vertical track pitch. This satisfies the condition 2.
![image](https://github.com/user-attachments/assets/6844d7e5-4454-4c93-9562-6a900c1ed247)

![image](https://github.com/user-attachments/assets/ab3e1532-3d42-475f-9f91-37744a1d2439)
## Converting magic layout to standard cell LEF

* **Create port definition:** Now to convert the label to a port for LFE abstraction select the port region and open file-->text. Then fill in the entries as shown below [No need to do it here as it is already done]:
![image](https://github.com/user-attachments/assets/e2921c35-43ce-4cbc-8b2d-0837a280fd4d)

A similar process is revised for Y, VPWR, and VGND ports. Note that A and Y ports are connected to the _locali_ metal layer, whereas VPWR and VGND ports are connected to the _metal2_ layer.
* **Port class and port use attributes for a layout:** After port definition, the next step is setting port class and port use attributes. These attributes are used to define the purpose of the port. Class and use properties are used by the LEF format for read-and-write routines. Press the button "s" to select the right port layer, then use the following commands:

```
# to confirm the port
what
# to define the port class and use
port class input
port use signal
```
![image](https://github.com/user-attachments/assets/9e33c15f-6f69-4bb5-89d6-ffca982802cb)

```
# for Y port
port class output
port use signal
```
![image](https://github.com/user-attachments/assets/d00e2331-ea1a-4821-b47d-7b88bdd67f7f)
```
# for VPWR port
port class inout
port use power
```
![image](https://github.com/user-attachments/assets/861ba362-9f4d-459b-a99f-e30b809191bd)
```
# for VGND port
port class inout
port use ground
```
![image](https://github.com/user-attachments/assets/caf65580-9734-4437-a17a-9897b173dac0)

Now we need to extract the LEF file. Before that, lets save .mag file by using the command ``save sky130_vsdinv.mag``` in the tkcon terminal.

Now, use the following command to open the saved mag file:
```
magic -T sky130A.tch sky130_vsdinv.mag &
```
To extract the LEF file, use the following command in tckon window:

```
lef write
```
![image](https://github.com/user-attachments/assets/4754d606-73ef-48d7-88aa-562fe56449ed)

Let's open the LEF file

![image](https://github.com/user-attachments/assets/7b6f0b07-489d-401b-939f-e1b04ece04f1)

Next, we plug in the LEF file in the picorv32a design.


## Introduction to timing libs and steps to include new cell in the synthesis

Now we redo the Synthesis, Placement, and Route steps. For this, we add our custom cell in the picorv32a openlane design flow. 
We make sure that our pwd is ```/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign```.
From here, we copy the _.lef_ file using the following commands:
```
cp sky130_vsdinv.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src
```
For the synthesis step, we need the std cell library files. Therefore we copy the _.lib_ files from the directory _vsdstdcelldesign/libs_ using the following commands:
```
cp sky130_fd_sc_hd__* /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src
```
![image](https://github.com/user-attachments/assets/18d830d0-b09e-4b2b-b48b-c81779df8de0)
The _.lib_ file includes the characterization information (cell power, cell rise, cell transition, ...) of every std cell.
The files _sky130_fd_sc_hd__typical.lib_, _sky130_fd_sc_hd_fast.lib_, and _sky130_fd_sc_hd__slow.lib_ files are defined for different speeds, temperature and voltage values.
In **sky130_fd_sc_hd__typical.lib** the nmos/pmos transistors are typical (neither fast nor slow) and are characterized at temp. of  25degC and voltage of 1.8V.
![image](https://github.com/user-attachments/assets/146cbe54-4788-4580-9a05-7b026d18d446)
In **_sky130_fd_sc_hd__slow.lib** the nmos/pmos transistors are slow or have maximum delays and are characterized at temp. of  100degC and voltage of 1.6V.
![image](https://github.com/user-attachments/assets/27ad047a-6b64-43b1-b68c-fac2f8ac0880)
In **_sky130_fd_sc_hd_fast.lib** the nmos/pmos transistors are fast and characterized at temp. of  -40degC and voltage of 1.95V.
![image](https://github.com/user-attachments/assets/2fed93b1-caa9-4045-8e53-268f4ac08da5)

Now, we need to modify the _config.tcl_ file: Go to the _picorv32a_ directory and open the file using vim and we make the following modifications:
![image](https://github.com/user-attachments/assets/eb1b1e65-9f32-49d4-a8ce-bde6e48fc988)
added lines to point to the _lef_ location which is required during spice extraction.


Now, invoke the docker and perform the regular steps as shown:
```
./flow.tcl -interactive
package require openlane 0.9

#to continue the work in the already made directory in the runs folder
prep -design picorv32a -tag 26-07_10-33 -overwrite
```
 '-overwrite' will continue the work with the changes we made and overwrite them in '26-07_10-33'.

![image](https://github.com/user-attachments/assets/fbb9c4bb-88af-4433-ae19-575049be03e7)

Include the below command to include the additional lef into the flow:
```
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
  
add_lefs -src $lefs
```
Now run synthesis ```run_synthesis```

![image](https://github.com/user-attachments/assets/335283d0-47ba-45f2-80c3-44755f5d308f)

![image](https://github.com/user-attachments/assets/daee63b5-9b87-4c01-8ee6-2ac4ce788056)

From the figure above it is clear that synthesis was successful. A total of 1554 instances of our _vsdinverter_ are used. We can also see the worst slack is -23.89 and the total negative slack is -711.59. 
The chip area is 147712.918 

At this stage, we can try to minimize the slack by performing a timing-driven synthesis. For this, we have to trade off the chip area to improve the delay.

If we open the README.md from the directory ```/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/configuration```, we see the variable **"SYNTH STRATEGY"**
```
“SYNTH STRATEGY | Strategies for abc logic synthesis and technology mapping <br> Possible values are 0, 1 (delay), 2, and 3 (area)<br> (Default: ‘2°)|
```
If **"SYNTH STRATEGY"** is 0 or 1 then synthesis is delay-driven and if it is 2, and 3 then it is area driven.
we check **"SYNTH STRATEGY"** with following command:
```
# check the current value
echo $::env(SYNTH STRATEGY)
```
which is "0", therefore synthesis is already delay-driven. There are some other variables we can check
```
“SYNTH BUFFERING’ | Enables abc cell buffering <br> Enabled
“SYNTH SIZING’ | Enables abc cell sizing (instead of buffering) <br> Enabled = 1, Disabled = © <br> (Default: ‘@°)|
“SYNTH DRIVING CELL’ | The cell to drive the input ports. <br>(Defaul
```
**SYNTH BUFFERING:** enables the buffers if the fan output is high and it is set to "1" for delay-driven synthesis.<br/>
**SYNTH SIZING:** is upsizing or downsizing the buffers based on delay strategy and we set it "1" for delay-driven synthesis.<br/>
**SYNTH DRIVING CELL:** sets the std cell used to drive the input ports. If the input port has a lot of fan-outs then it needs more drive-strength cells to drive the input.
```
# Now once again we have to prep the design to update the variables
prep -design picorv32a -tag 26-07_10-33 -overwrite
# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
# Command to display the current value of variable SYNTH_STRATEGY
echo $::env(SYNTH_STRATEGY)
# Command to set a new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"
# Command to display the current value of variable SYNTH_BUFFERING to check whether it's enabled
echo $::env(SYNTH_BUFFERING)
# Command to display the current value of variable SYNTH_SIZING
echo $::env(SYNTH_SIZING)
# Command to set a new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1
# Command to display the current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)
# Now that the design is prepped and ready, we can run synthesis using the following command
```
![image](https://github.com/user-attachments/assets/be69c83c-04ba-4185-ba46-c9bfba1de079)

Now run synthesis ```run_synthesis``` again and see if the delay is improved. From the new synthesis report, we noticed that slack was not changed so it was already optimized.
![image](https://github.com/user-attachments/assets/c577fc07-e0e8-4abe-998c-1e2f77014667)
A total of **1434** instances of our vsdinverter are used. 


![image](https://github.com/user-attachments/assets/b0053411-e188-4f30-b4b2-dfff4669554b)

As we have completed the synthesis stage now we complete the floorplan using the following command:
```
init_floorplan
place_io
tap_decap_or
```
![image](https://github.com/user-attachments/assets/65ad0f92-86b0-4b46-990a-8fd8f0450c06)

Now, as the floorplan stage is completed, we run placement
```
run_placement
```
![image](https://github.com/user-attachments/assets/565b9565-63a0-4b78-8d47-fcd0f74a13df)

![image](https://github.com/user-attachments/assets/df5e8a39-9375-4ac8-b390-8139932430da)
We can also see the **worst slack and the total negative slack is 0**. Similarly, the _**slack = data required time - data arrival time = 4.54ns**_ **as the slack is positive, so there is no slack violation.**

Now, to check whether the std cell we have created has been included in the design or not. Go to the following directory:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-07_10-33/results/placement
```
and use 
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![image](https://github.com/user-attachments/assets/d854c642-1f19-4774-93a9-262e3ef6d344)
# Timing analysis with ideal clocks using openSTA

## Configure OpenSTA for post-synth timing analysis

Let's create an STA conf file (pre_sta.config) first in the directory ```/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane ```. In this file, we provide the fast and slow corner lib file, an old verilog file, and an SDC file. After STA a new Verilog file is created. The file is shown below:
![image](https://github.com/user-attachments/assets/b887b477-9cfa-4341-976a-19fad4667c2b)

my_base in the directory ```sky130_vsdinv.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src```
![image](https://github.com/user-attachments/assets/9337ea98-bb97-4dec-a083-1d75a2239f6a)

now run the static timing analysis using the following command
 ```
sta pre_sta.config
 ```
![image](https://github.com/user-attachments/assets/fc619288-6b0a-40fa-9c82-fe8d6b887f43)

* **As we can see Slack is not equal to what we got in the synthesis stage. However, we proceed with the CTS and routing**
  
 <!-- Therefore, we try to re-run the openlane flow using the following commands in sequence.

 ```
# exit first from the openlane flow
exit
# start flow
./flow.tcl -interactive
# Now once again we have to prep the design to update the variables
prep -design picorv32a -tag 26-07_10-33 -overwrite
# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
# proceed with the Floorplan
init_floorplan
place_io
tap_decap_or
# placement
run_placement
 ```
Note that we did not use the commands  ```set ::env(SYNTH_STRATEGY) "DELAY 3" and set ::env(SYNTH_SIZING) 1 ``` in the above flow.

synthesis
![image](https://github.com/user-attachments/assets/38093de7-0224-4cb7-b7e4-740330af5e62)

placement
![image](https://github.com/user-attachments/assets/0655eb2c-6863-4c0f-a88e-b21bcf0a8af8)

now run the static timing analysis using the following command
 ```
sta pre_sta.config
 ```

![image](https://github.com/user-attachments/assets/734b0fa4-ddc4-4cbc-87ed-18ad4221d8d0)

As we can see Slack is equal to what we got in the synthesis stage. So STA is successful.
-->
<!-- a normal html comment -->


When using ideal clocks i.e. before clock tree synthesis, the hold time is not important and therefore we look at setup-time violations only (_**slack = data required time - data arrival time**_ ).

# Clock tree synthesis TritonCTS and signal integrity

## Steps to run CTS using TritonCTS

TritonCTS is the EDA tool that generates the CTS. This tool so far generates the CTS for typical corner of std cells. Different CTS variables are listed in the _README.md_ file in the  ```...openlane/configuration directory ```
![image](https://github.com/user-attachments/assets/d363a542-2d2c-4bbc-805a-c8c6219887b6)
 ```
write_verilog //path of the previous design//
#In our case:
write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/20-07_16-44/results/synthesis/picorv32a.synthesis.v
 ```
![image](https://github.com/user-attachments/assets/33295672-fac3-4ac0-aac5-dac75f5c049a)
This will replace the old design with the optimized one. Once the update is complete, proceed with the Floorplan stage using the same commands as before.
 ```
init_floorplan
place_io
tap_decap_or
 ```
After successful completion of the Floorplan, we proceed to the placement step by using the commands:
run_placement

![image](https://github.com/user-attachments/assets/d2a36538-b972-48c5-9b8c-13a922f2601c)
![image](https://github.com/user-attachments/assets/8e0ddd5d-3160-4715-b52e-7d40ad57dd08)


 ```
run_cts
 ```

![image](https://github.com/user-attachments/assets/1e1672eb-65ab-4efe-94e3-b20cb329a17a)

In the cts stage the clock buffers get added, modifying the netlist. After completing the cts, we can observe that a new _.cts_ file has been added to the synthesis results directory. 
The newly added CTS file contains both the previous netlist and the clock buffers added during the CTS stage.

![image](https://github.com/user-attachments/assets/ae735f0d-6449-4346-8190-b0bf66157e92)

Openroad is an EDA tool that performs floorplan to global routing. It takes the _.tcl_ files shown in the directory _openroad_ below:
![image](https://github.com/user-attachments/assets/05d6d8e0-73f5-436f-ba45-969a60efd8a2)

In the figure above _or_cts.tcl_ file has the CTS settings

## Timing analysis with real clocks using openSTA

Openroad has an OpenSTA integrated in it used for the STA. 
 ```
openroad
 ```
For timing analysis in openroad, we  first create the database (created from lef and def files). Run the follwoing commands:

 ```
read_lef /openLANE_flow/designs/picorv32a/runs/26-07_10-33/tmp/merged.lef
 ```
![image](https://github.com/user-attachments/assets/20a54325-00a6-40a9-8ed3-0658b468ce40)

 ```
read_def /openLANE_flow/designs/picorv32a/runs/26-07_10-33/results/cts/picorv32a.cts.def
 ```
![image](https://github.com/user-attachments/assets/2eb61839-c234-419f-8c9d-e1e475181a77)
 ```
write_db pico_cts.db
 ```
![image](https://github.com/user-attachments/assets/425824b5-9249-4351-8f55-6eb1bec27248)
 ```
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/26-07_10-33/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -format full_clock_expanded -digits 4
 ```

The figure below shows the buffers netlist used in the CTS. This also shows that slack in the hold time is satisfied.
![image](https://github.com/user-attachments/assets/5cc71ced-3182-4b54-baa3-002c657379c3)

The slack in the setup time is satisfied
![image](https://github.com/user-attachments/assets/7dcf5938-e4e6-4839-80b2-901a050354b6)



In routing actual metal layers are being laid. Therefore, the metals' capacitance and resistance are also included and hence the arrival time will increase so we can decrease the slack for hold time but it will degrade for setup time. 


# Steps to execute OpenSTA with the right timing libraries and CTS assignment

First use ```exit ``` to exit from openroad. Now at openlane flow check the buffers used in the CTS netlist
```
echo $::env(CTS_CLK_BUFFER_LIST)
```
![image](https://github.com/user-attachments/assets/64b2ecea-0184-4e82-86f5-c444ab1ce044)
Lets remove _sky130_fd_sc_hd__clkbuf_1 _ from the list:
```
set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]
```

Now, to check we run the cts again 
```
run_cts
```

The cts fails or hangs and then we kill this task:
```
# find the process ID
top
# kill process
kill -9 1964
```

This is due to the reason that the current DEF value as we see below is incorrect. Becuase we have removed the buffer1, so we have to use the DEF value for placement,
but after CTS the DEF value was changed to CTS DEF value. 
![image](https://github.com/user-attachments/assets/35cd4ab3-8c45-40bb-a030-882fd349d390)
set def as placement def
```
set ::env(CURRENT_DEF) /openLANE_flow/designs/picorv32a/runs/12-07_11-26/results/placement/picorv32a.placement.def
```
![image](https://github.com/user-attachments/assets/33e6a034-0b53-4ce5-91fc-81a5640d7a8e)


When openlane is building the CTS it tries to meet the skew value by inserting buffers from left to right and checks the skew value. The skew value is within the 10% of the clock period.
If we remove any of the clock buffers

![image](https://github.com/user-attachments/assets/37c8b646-39be-4642-9fd0-50476ca1955e)

![image](https://github.com/user-attachments/assets/b20fa22b-5b5d-4217-9885-82578f39159a)

Now, We need to follow the similar steps that we have followed earlier in the openroad. go to openroad again and then:

```
read_lef /openLANE_flow/designs/picorv32a/runs/26-07_10-33/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/26-07_10-33/results/cts/picorv32a.cts.def
write_db pico_cts1.db
read_db pico_cts1.db
read_verilog /openLANE_flow/designs/picorv32a/runs/26-07_10-33/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4
```

hold time is improved from 0.0772 to 0.2456
![image](https://github.com/user-attachments/assets/16b7b7bb-86a3-4309-a08d-2c0d935fd6c8)

setup time remained the same as 14.1462
![image](https://github.com/user-attachments/assets/37d76bf8-94a4-4bff-925c-22f8fc4b8a01)
```
report_clock_skew -hold
```
![image](https://github.com/user-attachments/assets/acc0b711-e462-4389-8eb3-b8aa66c20f38)
```
report_clock_skew -setup
```
![image](https://github.com/user-attachments/assets/6c7eb278-ea78-489e-8e2f-3534194ac419)

The skew for both setup and hold time is 0.51 which is less than 10% of clock period.

Now if we want to include buf_1 again:

```Exit``` the openroad first. To check the current value of clock buffers list
```
echo $::env(CTS_CLK_BUFFER_LIST)
```
![image](https://github.com/user-attachments/assets/f3233413-fa52-453a-b326-7372520c42d9)

To insert sky130_fd_sc_hd__clkbuf_1 from the list
```
set ::env(CTS_CLK_BUFFER_LIST) [linsert $::env(CTS_CLK_BUFFER_LIST) 0 sky130_fd_sc_hd__clkbuf_1]
```
![image](https://github.com/user-attachments/assets/385289dc-95cc-4da9-a5ff-3745fc1326ee)

![image](https://github.com/user-attachments/assets/d2151dce-74da-481e-8440-30dc6408ad44)

# LAB 5: FINAL STEPS FOR RTL2GDS USING TRITONROUTE & OPENSTA

## Steps to build a power distribution network
Once CTS is ready we can generate a power distribution network (PDN) before routing.
Let's check our current DEF file, using the following commands:
```
echo $::env(CURRENT_DEF)
```
which should be a CTS DEF file (picorv32a.cts.def)
![image](https://github.com/user-attachments/assets/5966a8c6-9b7a-403c-a3c3-2631bdcaa5fe)
use the following commands for PDN
```
gen_pdn
```
![image](https://github.com/user-attachments/assets/3e4593d9-80f3-44c8-ae84-6dea0f3150c4)

The PDN output above shows that PDN writes the LEF file, reads the CTS DEF and creates the grid and straps for the power and ground. As we know STDcells are placed in the std rows, therefore STDcell power rails are placed along the stdcell rows. The stdcell rails have a
pitch of 2.720, equivalent to the height of the stdcell inverter. Thus the power and ground stdcell rails match with the GND and PWR ports of stdcell inverter.

The diagram below shows power planning. 
![image](https://github.com/user-attachments/assets/647fa47a-14af-49ac-b0ab-476a05bb59fe)
In the figure above, the green area corresponds to the picorv32a design. The red pads are for power, while the blue pads provide the ground connection.

From the pads, power is supplied to the rectangular close-loop rings. The vertical lines connected to the rings are power straps. The stdcells power and ground rails are attached to vertical straps. 
The height of the std cells must be multiple of the rail pitch to ensure proper power and ground connection. This image shows how the power comes from the outside to the pads, pads to the rings, rings to strap/stripe, and strap/stripe to stdcell rows.

##  Global and detail routing and configure TritonRoute
Now the last stage in the design flow is Routing. In summary, we have done the following openlane flow steps so far: 1) the design setup using -prep commands, 2) the floorplan, 3) placement of stdcells, 4) clock tree synthesis, and 
5) power distribution network generation. <br/> 
TritonRoute is the engine used for Routing. The routing is divided into GLOBAL ROUTING and DETAIL ROUTING.  The FAST ROUTE does GLOBAL ROUTING, while TritonRoute does DETAIL ROUTING. This is shown below.
![image](https://github.com/user-attachments/assets/4d19f35c-18e9-4d3f-8a1f-57ae858be165)

Before routing, first check the current DEF file:
![image](https://github.com/user-attachments/assets/b94f1082-6735-4ce1-907d-d33c8d024dc0)
The figure above shows that the DEF file is the floorplan's PDN DEF. This DEF file has both the CTS DEF and PDN. Before routing let's check the routing variables from the directory
```/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/configuration```
![image](https://github.com/user-attachments/assets/d731b96d-3647-408e-a83a-9c0d082551fb)
Run the following command for routing:
```
run_routing
```
**Inputs:** to TritonRoute are the LEF file, DEF file, and preprocessed route guides from FAST ROUTE. 
**Outputs:** are detailed routing with optimized wire-length and via count. 
**Constraints:** Route guides, connectivity constraints and design rules

![image](https://github.com/user-attachments/assets/44222aa0-6c53-4d7c-b36c-0a2368b4521a)
![image](https://github.com/user-attachments/assets/096e85e0-1996-4b29-8f2a-133d3bd2a163)

**The routing has been completed with zero violations and no slack. Hence, routing is successful.** <br/> 
To view the routing results go to the routing directory of picorv32a results:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-07_10-33/routing
```
![image](https://github.com/user-attachments/assets/a0405556-d098-49ff-9bf2-979dac2b305b)

To view the final layout, use the following command:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def
```
![image](https://github.com/user-attachments/assets/0fd3d5f8-a9b7-40a2-8f30-75183d6de498)

![image](https://github.com/user-attachments/assets/c34ef8b2-edf9-47e9-92e3-b513abe2ab3f)

![image](https://github.com/user-attachments/assets/35f6fcef-1b52-4793-8807-7ce7d2f60efa)

The custom inverter was successfully included in the _picorv32a_ design. Below is the final zoomed routed layout of the picorv32a design with the custom cell highlighted.

![image](https://github.com/user-attachments/assets/be26e952-8957-4721-b041-d9f1f0f0801c)


# REFERENCES

* [VSD Standard Cell Design](https://github.com/nickson-jose/vsdstdcelldesign)
* [Google Skywater PDK](https://skywater-pdk.readthedocs.io/en/main/index.html)
* [Materials provided in the NASSCOM VSD SoC Design Program]
# AUTHOR
[Nouman Zia](https://github.com/noumanzia123)




