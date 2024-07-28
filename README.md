# NASSCOM-VSD-SoC-Design-and-Planning
# Table of contents

# THEORY 1: OPEN-SOURCE EDA, OPENLANE & SKY130 PDK

## What is an RTL to GDSII flow?

# LAB 1: OPEN-SOURCE EDA, OPENLANE & SKY130 PDK

## Directory structure in openlane

## Design Preparation Step

## Review files after design prep and run synthesis

## Characterization of Synthesized Results
asdsadasdasdasdsdas
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



# LAB 2: FLOORPLANNING & PLACEMENT

## Steps to run floorplan
To ensure a smooth floorplanning process, designers must pay attention to certain variables, known as switches, which can significantly impact the floorplan. 
For instance, the utilization factor and aspect ratio are among the important switches. 
Designers need to verify that these parameters align with the project requirements before initiating the floorplanning stage. 
The file _README.md_  displays the different variables of the design flow. The path is shown below :
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/configuration
```
![image](https://github.com/user-attachments/assets/3b1c36f4-bad7-4770-b577-024ddda70ecf)

The image below illustrates various types of switches involved in the floorplanning phase and their description.
![image](https://github.com/user-attachments/assets/09b5c690-8318-4a79-a764-951d6043c29c)
One can set any of these variables depending on where we are in the flow.

* In the configuration folder of the openlane directory, there is a _floorplan.tcl_ file,  which includes the default floorplan settings for openlane.
The file path is shown in the image below
![image](https://github.com/user-attachments/assets/59da6a48-b544-4209-98aa-a5618ea858ba)


* In the picorv32a design, there are _config.tcl_ and _sky130A_sky130_fd_sc_hd_config.tcl_ files which contain design-specific floorplan settings. 
The file path is shown in the image below:
![image](https://github.com/user-attachments/assets/28f3bbe2-552e-4e43-baba-952ddb5c220b)

The priority is given to _sky130A_sky130_fd_sc_hd_config.tcl_, then _config.tcl _and then to the default _floorplan.tcl_ file above.





## Review floorplan files

## Review floorplan layout in magic

**Important steps to review floorplan in magic**
Action | Steps 
--- | :--- 
Seconds | 1. Press S to select the entire design.<br/> 2. Press V to align it vertically to the middle of the screen.
Seconds | 1. Press S to select the entire design.<br/> 2. Press V to align it vertically to the middle of the screen.
Seconds | 1. Press S to select the entire design.<br/> 2. Press V to align it vertically to the middle of the screen.

## Placement in openlane

After completing floorplanning, the design process moves on to the placement stage. The placement-related variables/switches can be seen in the README.md file
under the directory path:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/configuration
```
![image](https://github.com/user-attachments/assets/6fc649d5-ea91-4331-9a4c-24a27b655b6f)

**PL_TARGET_DENSITY:** describes placement density of cells in the core. It reflects how spread the cells would be in the core area. 
1 = closely dense, 0= widely spread (Default: `0.55`). Initially, the placement is coarse to avoid congestion. When the design approach towards closing then timing
constraints come in, where the cells would be closed as much as possible to avoid delays.

The placement step comprises two main phases:

**Global Placement:** It is a coarse placement where no legalization is happening. During this phase, the tool determines the approximate locations for all the standard cells in the design. 
**Detailed Placement:** In this phase, the tool finalizes the exact positions for all the standard cells and ensures the placement is legal. 
Legalization involves verifying that no standard cells overlap and that they are all correctly positioned within the 
designated site rows. To initiate the placement process, use the following command:
```
run_placement
```
After the Placement is done. To view the results Go to the following location:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/20-07_16-44/results/placement
```

And then we can see 'picorv32a.placement.def' file. To open it using MAGIC use the following command:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
```
