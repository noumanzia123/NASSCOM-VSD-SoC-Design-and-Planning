# NASSCOM-VSD-SoC-Design-and-Planning
# Table of contents

# THEORY 1: OPEN-SOURCE EDA, OPENLANE & SKY130 PDK

## What is an RTL to GDSII flow?

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
