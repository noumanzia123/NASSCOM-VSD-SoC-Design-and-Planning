# NASSCOM-VSD-SoC-Design-and-Planning
# Table of contents

# THEORY 1: OPEN-SOURCE EDA, OPENLANE & SKY130 PDK

## What is an RTL to GDSII flow?

## Different files in workflow?
* **Library Exchange Format (LEF) file**: Place and route (PnR) does not need information about the logic or complete layout.
It just needs the pin positions and boundary information. This minimal and abstract information is provided to the tool by the LEF file.
LEF file also serves the purpose of protecting intellectual property. LEF file contains the metal layers and pin locations. the difference between layout and LEF file is shown below:
![image](https://github.com/user-attachments/assets/4b9826d9-31d5-4588-9c4e-7651f074ffa8)


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

Now, to open this ".def" file in magic , use the following command:
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

<img width="470" alt="image" src="https://github.com/user-attachments/assets/836df4d9-8545-43c3-937d-cf45b3b49d24">

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
Go to the following tab as shown below to run the command to change IO placer settings and run the floorplan again.
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

As we see the spikes are a bit larger at the edges of inverter output. 
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
![image](https://github.com/user-attachments/assets/e81caba5-d17d-4b55-9f06-ee60073c573d)


Now, to check whether the std cell we have created has been included in the design or not. Go to the following directory :
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-07_10-33/results/placement
```
and use 
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![image](https://github.com/user-attachments/assets/d854c642-1f19-4774-93a9-262e3ef6d344)





