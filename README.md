# RTL_Design_Using_Verilog_with_SKY130_Technology_Workshop-May2021
Documentation of labs activity and theory of my attendance of the RTL to Netlist front end design ASIC EDA 5 day workshop held by VSD


![image](https://user-images.githubusercontent.com/75379398/120273729-cc20e680-c2a6-11eb-8e0d-aeab7719de04.png)

# RTL_Design_Using_Verilog_with_SKY130_Technology_Workshop-May2021
Capture lab steps during VSD 5 day RTL open source/hardware EDA course from verilog to RTL/Netlist
# Summary
Continuing with my ASIC design learning quest after attending the OpenLANE and Sky130PDK course run by VSD company (https://github.com/edwar1r/VSD-Openlane_Workshop-April2021), I participated in the RTL Design using Verilog with Sky130 Technology workshop. Again, this was an intensive five day course with the aim of guiding students along the path of going from a Verilog design to RTL/netlist and the steps involved in verifying the design through simulation of the design and the generated RTL. Essential theory was covered in this course along with real labs hands-on activities to put the theory into practice and experience working with the tools (which are open source in nature but being used in commercially). The course covered some of the essential steps within the front-end stages of the design process whereas the previous workshop was focused on the back-end stages of the EDA process. The course uses Verilog for the HDL designs, the principles taught are equally applicable in other HDL languages such as VHDL and EDA tool chains from companies like Siemens, Synopsis, Cadence. I’m excited to attend the next ASIC/FPGA design related workshop from VSD since its such a great learning experience.
http://linkedin.com/in/robertedwardstelecommssw
# Table of Contents
## Day 1 – Introduction to Verilog RTL design and Synthesis
## Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles
## Day 3 - Combinational and sequential optimizations
## Day 4 - GLS, blocking vs non-blocking and Synthesis-Simulation mismatch
## Day 5 - If, case, for loop and for generate

# Day 1 - Introduction to Verilog RTL design and Synthesis
## Iverilog, test bench and gtkwave Introduction
It is necessary to determine if the RTL design (implemented in an HDL like Verilog) conforms to the specification by simulation.
iverilog is an open source test simulation tool that can be used for this purpose.
Any form of testing requires input to stimulate the UUT (Unit Under Test) in order to produce output that can be verified.
There is an associated Test Bench to apply this stimulus where appropriate test vectors (signal stimuli) can be applied to the design.
The simulation tool (iverilog) examines input changes and applies to the design (UUT) and records the output changes in a .vcd file which is a Value Change Dump format file.
gtkwave is an open source tool used to observe the waveforms derived from the .vcd file

![image](https://user-images.githubusercontent.com/75379398/120276713-f70d3980-c2aa-11eb-803c-a98a065618f5.png)


Figure 1. End to End Test Bench

## Iverilog, test bench and gtkwave Labs

Make VLSI directory and git clone Kunal reference repository for the workshop
```
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
```

![image](https://user-images.githubusercontent.com/75379398/120276776-0c826380-c2ab-11eb-8809-651e7114c4a1.png)


Figure 2. Gitclone sky130 Repository

Check that design and test bench files necessary for the workshop are present in one of the cloned directories (verilog_files)

![image](https://user-images.githubusercontent.com/75379398/120276841-20c66080-c2ab-11eb-9a22-f15d8c474042.png)


Figure 3. View design/test bench files in verilog_files (*.v)

Check that standard cell model files are present in the cloned directory

![image](https://user-images.githubusercontent.com/75379398/120276896-320f6d00-c2ab-11eb-9348-8bb491f6755b.png)


Figure 4. View standard cell model files in verilog_model (*.v)

```
Run iverilog with good_mux design and associated test bench
VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog good_mux.v tb_good_mux.v
After running, a.out file generated
```

![image](https://user-images.githubusercontent.com/75379398/120276927-42274c80-c2ab-11eb-9e6c-8e67f0ed953d.png)


Figure 5. a.out generated after running iverilog on good_mux.v & tb_good_mux.v

Execute a.out file to derive the VCD (Value Change Dump) file

![image](https://user-images.githubusercontent.com/75379398/120276960-523f2c00-c2ab-11eb-824f-ef9d00d3dd0f.png)


Figure 6. tb_good_mux.vcd file generated from a.out execution

Execute gtkwave to analyse the associated VCD file (tb_good_mux.vcd)

```
VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_good_mux.vcd
```
![image](https://user-images.githubusercontent.com/75379398/120277022-6551fc00-c2ab-11eb-9fdd-458a6ae88f6e.png)


Figure 7. gtkwave executed

![image](https://user-images.githubusercontent.com/75379398/120277065-7569db80-c2ab-11eb-9d57-2ccb797545d3.png)


Figure 8. Simulated design waveform for tb_good_mux.vcd

Examine test bench file for the multiplexor
```
VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ vi tb_good_mux.v
```

![image](https://user-images.githubusercontent.com/75379398/120275055-a5fc4600-c2a8-11eb-8693-901b17e9a620.png)

Figure 9.  tb_good_mux.v analysis

Examine multiplexor Verilog design
```
VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ vi good_mux.v
```

![image](https://user-images.githubusercontent.com/75379398/120275104-bad8d980-c2a8-11eb-95ce-061cd7e2607b.png)

Figure 10. good_mux.v analysis

## yosys and logic synthesis Introduction
The HDL design file (verilog in this course) needs to be converted to RTL (Register Transfer Level) and then Netlist. The tool used to perform this in this course is the yosys tool.
The following diagram shows a simplified flow of the various steps in yosys that was used in the labs; yosys is a complex tool that has many possibilities for the design flow, so the diagram shows the major steps.

![image](https://user-images.githubusercontent.com/75379398/120275190-d8a63e80-c2a8-11eb-9c88-53cb122ac4e6.png)

Figure 11. yosys design flow

The generated netlist from yosys can be ran through iverilog with the same test bench stimuli in order to compare/check for differences in output due to any inference in synthesis.
Some concepts need to be understood before embarking on yosys usage.
RTL Design : Describes the functionality/behaviour of the required specification; this is implemented in an HDL, verilog is the HDL used in this labs/yosys
Synthesis : Transforms RTL into a gate level representation, including connections between the gates, that is ready to be injected into the backend process of an EDA flow to ultimately produce an ASIC
Standard cell : gate that contain some unit functionality that is used in conjunction with other gates to be build the logic/circuitry required; examples are the UNIVERSAL FUNCTIONALITY cells of NAND/NOR that can be used to build any circuits plus there can be more specialised gates that internally have a collection of gates to provide specific logic function
.lib : collection of different flavours of standard cells that have different characteristics that need to be selected to respect performance requirements of the design and environment. 
We will be using the following standard cell library for these labs : sky130_fd_sc_hd__tt_025C_1v80.lib 

![image](https://user-images.githubusercontent.com/75379398/120275337-0ee3be00-c2a9-11eb-85fc-79d6487c398a.png)

Figure 12. sky130_fd_sc_hd__tt_025C_1v80.lib filename parameter meaning

The following diagram shows the mapping for a standard cell (2 input OR->3 input AND -> Invertor)

![image](https://user-images.githubusercontent.com/75379398/120275385-1f943400-c2a9-11eb-855a-25d0ab89794d.png)

Figure 13. Explain how a standard cell in standard lib is specified (2 input OR->3 input AND -> Invertor)

The standard cell library will contain multiple mappings for the various cells allowing an appropriate cell to be chosen at a particular point in the netlist that satisfies the system performance and not violating hold time of destination cell gate (in a time sensitive design this becomes critical).
So we will find a collection of fast and slow cell instance for each standard cell where:-
-	Fast cell : has wider transistor width, occupies more silicon area, consumes more power, but has less delay
-	Slow cell : narrow transistor width, occupies less silicon area, consumes less power, has more delay
Sometimes it is necessary to select a slow cell in order for the signal to arrive at the destination cell at the appropriate point in time or/and also to consume less power/space where this is critical for the end product – engineering compromises need to be made in order to ensure the end product will still satisfy critical requirements (i.e. maybe not appropriate to use too many slow cells since then product may not meet performance requirements).

## yosys and logic synthesis Labs
Invoke Yosys
```
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ yosys
```

![image](https://user-images.githubusercontent.com/75379398/120277140-96323100-c2ab-11eb-8691-2f7a701609a8.png)


Figure 14. Entering yosys

Read standard cell library:-
```
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![image](https://user-images.githubusercontent.com/75379398/120277195-a5b17a00-c2ab-11eb-9b1d-c60285ae43b6.png)


Figure 15. yosys – read_liberty standard cell

Read Verilog design:-
```
yosys> read_verilog good_mux.v
```

![image](https://user-images.githubusercontent.com/75379398/120277239-b4982c80-c2ab-11eb-9386-5595f28eb029.png)


Figure 16.  yosys – read Verilog (good_mux.v)

Perform synthesis:-
```
yosys> synth -top good_mux
```

![image](https://user-images.githubusercontent.com/75379398/120277284-c11c8500-c2ab-11eb-92fd-d60ed59b247b.png)


Figure 17. yosys – synthesis of good_mux.v

Synthesis consistent with design, pure combinational logic no sequential thus no flip flops
Use abc to generate netlist from RTL and standard cell library
```
yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

![image](https://user-images.githubusercontent.com/75379398/120277347-d98c9f80-c2ab-11eb-84cb-299e720660ae.png)



Figure 18. yosys - abc with standard cell of good_mux.v

Use show command to give diagrammatic view of generated netlist
```
yosys> show
```

![image](https://user-images.githubusercontent.com/75379398/120276291-66cef480-c2aa-11eb-84d5-bfcc05b5c975.png)

Figure 19. yosys – show of good_mux.v netlist with confirmation of Boolean logic of 2:1 MUX when gates overlayed 

Run iverilog with bad_mux design and associated test bench
```
VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog bad_mux.v tb_bad_mux.v
After running, a.out file generated
Execute a.out file to derive the VCD (Value Change Dump) file
Execute gtk to analyse the associated VCD file (tb_bad_mux.vcd)
VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_bad_mux.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120276344-79e1c480-c2aa-11eb-80e7-f3d93747b408.png)

Figure 20. gtkwave of bad_mux.v

# Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles
## Introduction to timing .lib

Examine Characteristics file

```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/lib$ vi sky130_fd_sc_hd__tt_025C_1v80.lib
```

![image](https://user-images.githubusercontent.com/75379398/120277522-10fb4c00-c2ac-11eb-9915-95758ad1ca4d.png)


Examine sky 130nm tt (typical) 025c (temperature) 1v80 indicates the voltage
PVT
Process : variations due to fabrication
Voltage : vary voltage there will change in behaviour in circuit
Temperature : semiconductor dependent on temperature variation

Imagine making CD player
Swiss -> 20C to -ve
Dubai >30C sometimes 50C


## Introduction to dot Lib part2

Cmos power
Unit of time (ns)
Unit of voltage/current
Indicates operational conditions (process voltage temperature)
```
    default_operating_conditions : "tt_025C_1v80";
    operating_conditions ("tt_025C_1v80") {
        voltage : 1.8000000000;
        process : 1.0000000000;
        temperature : 25.000000000;
        tree_type : "balanced_tree";
```
look for keyword “cell “ to find different type of cells, various flavours of and/invert gates
Find characteristics of various cells e.g:-
a2111o : signifies 2 input AND gate with output fed into OR gate with three other inputs; since there are a total of 5 inputs into the cell there are 32 combinations of input voltage/current combinations that needs to be considered for “leakage_power” parameter
```   
 cell ("sky130_fd_sc_hd__a2111o_2") {
        leakage_power () {
            value : 0.0021893000;
            when : "!A1&!A2&!B1&!C1&D1";
        }
        leakage_power () {
            value : 0.0093488000;
            when : "!A1&!A2&!B1&!C1&!D1";
        }
        leakage_power () {
            value : 0.0009247000;
            when : "!A1&!A2&!B1&C1&D1";
        }
        leakage_power () {
            value : 0.0013982000;
            when : "!A1&!A2&!B1&C1&!D1";
        }
        leakage_power () {
            value : 0.0009175000;
            when : "!A1&!A2&B1&!C1&D1";
        }
        leakage_power () {
```

![image](https://user-images.githubusercontent.com/75379398/120277590-27090c80-c2ac-11eb-9e0f-fdfd1358199a.png)


Its interesting to view how this cell (a2111o) is designed in the associated Verilog file
Open file as follows:-
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model$ vi sky130_fd_sc_hd.v
```
![image](https://user-images.githubusercontent.com/75379398/120277812-6fc0c580-c2ac-11eb-80ee-1c974c136c1d.png)

## Introduction to dot Lib part3

Compare different transistor gate widths for the same simple 2 input AND cell (“and2_”), see how leakage power and area increase as gate width increase.
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/lib$ vi sky130_fd_sc_hd__tt_025C_1v80.lib
```

![image](https://user-images.githubusercontent.com/75379398/120277862-8535ef80-c2ac-11eb-8dd4-2f3d696cef51.png)

Going from left to right in the diagram the three snap shots of the .lib file captures the gate width of the transistor increasing. The files confirms that as the transistor gate width increases that the area of the cell increases and the leakage power increases for the various input combinations. 

## Hierarchical vs Flat Synthesis

![image](https://user-images.githubusercontent.com/75379398/120277933-97b02900-c2ac-11eb-90b8-1a2788adc5f7.png)

![image](https://user-images.githubusercontent.com/75379398/120277970-a1d22780-c2ac-11eb-99fb-2ed2ee7608c8.png)

![image](https://user-images.githubusercontent.com/75379398/120278014-ad255300-c2ac-11eb-90c9-cb6c8f806085.png)

![image](https://user-images.githubusercontent.com/75379398/120278067-bd3d3280-c2ac-11eb-97bd-7ba480918f47.png)


Understand “synth -top” option in synthesis
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ vi multiple_modules.v
```

![image](https://user-images.githubusercontent.com/75379398/120278134-ce863f00-c2ac-11eb-976d-9b5b68cea22a.png)

Two modules, sub module 1 and sub module 2
The module has 3 inputs : a, b, c
One output : y
```
module sub_module2 (input a, input b, output y);
        assign y = a | b;
endmodule

module sub_module1 (input a, input b, output y);
        assign y = a&b;
endmodule
module multiple_modules (input a, input b, input c , output y);
        wire net1;
        sub_module1 u1(.a(a),.b(b),.y(net1));  //net1 = a&b
        sub_module2 u2(.a(net1),.b(c),.y(y));  //y = net1|c ,ie y = a&b + c;
endmodule
```
launch yosys:-

First task is to read liberty sky130 file
```
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Now read Verilog
```
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
Observe how RTL generated for both sub_module1 and sub_module2

![image](https://user-images.githubusercontent.com/75379398/120278208-e6f65980-c2ac-11eb-9208-7dafa7cf7195.png)

Set “synth -top multiple_modules”
```
yosys> synth -top multiple_modules
```
![image](https://user-images.githubusercontent.com/75379398/120278261-f8d7fc80-c2ac-11eb-9421-f9dd462b739e.png)

Confirmation of one AND (sub_module1) and one OR (sub_module2) in following lines:-
```
   multiple_modules                  1
     sub_module1                     1
     sub_module2                     1

   Number of wires:                 11
   Number of wire bits:             11
   Number of public wires:          11
   Number of public wire bits:      11
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                  2
     $_AND_                          1
     $_OR_                           1
```
Run liberty (with abc) to link design to standard cell characteristics library:

![image](https://user-images.githubusercontent.com/75379398/120278320-0c836300-c2ad-11eb-9adf-a91a96d51d53.png)


To view design of the top level module issue command: 
```
yosys> show multiple_modules
```

![image](https://user-images.githubusercontent.com/75379398/120278384-1efd9c80-c2ad-11eb-9435-ef6bee263f92.png)

![image](https://user-images.githubusercontent.com/75379398/120278417-291f9b00-c2ad-11eb-858a-c36848ce3eed.png)

Not showing AND or OR gate representation, show as U1 and U2 respectively, as per Verilog design – this is called the HIERARCHICAL DESIGN (the hierarchies are preserved)
Now write out the netlist through:
```
yosys> write_verilog multiple_modules_hier.v
```

![image](https://user-images.githubusercontent.com/75379398/120278567-5b30fd00-c2ad-11eb-99b5-d51d1e080b96.png)

Now examine the netlist through:
```
yosys> !vi multiple_modules_hier.v
```

![image](https://user-images.githubusercontent.com/75379398/120278636-6c7a0980-c2ad-11eb-946b-6cd9430a3919.png)

Repeat with no attribute option:
``` 
yosys> write_verilog -noattr multiple_modules_hier.v
```

![image](https://user-images.githubusercontent.com/75379398/120278693-8287ca00-c2ad-11eb-9db0-e8f8ec4d4f60.png)

We see that the hierarchies are preserved with the higher level “multiple_modules” instantiating the sub_module1 and sub_module2 modules:
```
module multiple_modules(a, b, c, y);
  input a;
  input b;
  input c;
  wire net1;
  output y;
  sub_module1 u1 (
    .a(a),
    .b(b),
    .y(net1)
  );
  sub_module2 u2 (
    .a(net1),
    .b(c),
    .y(y)
  );
endmodule
```

SUBMODULE_1 USES AND GATE AS EXPECTED:
```
module sub_module1(a, b, y);
  wire _0_;
  wire _1_;
  wire _2_;
  input a;
  input b;
  output y;
  sky130_fd_sc_hd__and2_2 _3_ (
    .A(_0_),
    .B(_1_),
    .X(_2_)
  );
  assign _0_ = b;
  assign _1_ = a;
  assign y = _2_;
endmodule
```

SUBMODULE2 USES TWO INVERTORS AND NAND GATE TO PERFORM THE OR FUNCTIONALITY:
```
module sub_module2(a, b, y);
  wire _0_;
  wire _1_;
  wire _2_;
  wire _3_;
  wire _4_;
  input a;
  input b;
  output y;
  sky130_fd_sc_hd__clkinv_1 _5_ (
    .A(_0_),
    .Y(_3_)
  );
  sky130_fd_sc_hd__clkinv_1 _6_ (
    .A(_1_),
    .Y(_4_)
  );
  sky130_fd_sc_hd__nand2_1 _7_ (
    .A(_3_),
    .B(_4_),
    .Y(_2_)
  );
  assign _0_ = b;
  assign _1_ = a;
  assign y = _2_;
endmodule
```
Have two options for implementing an OR gate:-

OPTION 1 : 2 or more INV gates (on each input line) + NAND gate
-	Stacked NMOS in NAND gate
OPTION 2 : NOR + INV
-	Stacked PMOS in NOR, leads to poor mobility + need a large cell to get good “Logical Effort”

Look at standard cell library to determine effect of Option 1 and Option 2:
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/lib$ vi sky130_fd_sc_hd__tt_025C_1v80.lib
```

## Parameters of 2 input NAND

![image](https://user-images.githubusercontent.com/75379398/120278818-a8ad6a00-c2ad-11eb-8248-53f4e4f20e63.png)

```
   cell ("sky130_fd_sc_hd__nand2_1") {
        leakage_power () {
            value : 0.0002796000;
            when : "!A&B";
        }
        leakage_power () {
            value : 3.005879e-05;
            when : "!A&!B";
        }
        leakage_power () {
            value : 0.0079423000;
            when : "A&B";
        }
        leakage_power () {
            value : 0.0002199000;
            when : "A&!B";
        }
        area : 3.7536000000;
        cell_footprint : "sky130_fd_sc_hd__nand2";
        cell_leakage_power : 0.0021179600;
        driver_waveform_fall : "ramp";
        driver_waveform_rise : "ramp";
        pg_pin ("VGND") {
            pg_type : "primary_ground";
            related_bias_pin : "VPB";
            voltage_name : "VGND";
        }
        pg_pin ("VNB") {
            pg_type : "nwell";
            physical_connection : "device_layer";

       pin ("A") {
            capacitance : 0.0023150000;
            clock : "false";
            direction : "input";
            fall_capacitance : 0.0022540000;
            internal_power () {
                fall_power ("power_inputs_1") {
                    index_1("0.0100000000, 0.0230506000, 0.0531329000, 0.1224740000, 0.2823110000, 0.6507430000, 1.5000000000");
                    values("0.0047914000, 0.0048016000, 0.0048250000, 0.0048257000, 0.0048275000, 0.0048316000, 0.0048409000");
                }
                rise_power ("power_inputs_1") {
                    index_1("0.0100000000, 0.0230506000, 0.0531329000, 0.1224740000, 0.2823110000, 0.6507430000, 1.5000000000");
                    values("-0.003895500, -0.003894100, -0.003890900, -0.003884500, -0.003869900, -0.003836300, -0.003758800");
                }
            }
            max_transition : 1.5000000000;
            related_ground_pin : "VGND";
            related_power_pin : "VPWR";
            rise_capacitance : 0.0023750000;
        }
        pin ("B") {
            capacitance : 0.0023240000;
            clock : "false";
            direction : "input";
            fall_capacitance : 0.0022200000;
            internal_power () {
                fall_power ("power_inputs_1") {
                    index_1("0.0100000000, 0.0230506000, 0.0531329000, 0.1224740000, 0.2823110000, 0.6507430000, 1.5000000000");
                    values("0.0041316000, 0.0041310000, 0.0041296000, 0.0041311000, 0.0041345000, 0.0041424000, 0.0041606000");
                }
                rise_power ("power_inputs_1") {
                    index_1("0.0100000000, 0.0230506000, 0.0531329000, 0.1224740000, 0.2823110000, 0.6507430000, 1.5000000000");
                    values("-0.004132500, -0.004132200, -0.004131700, -0.004131700, -0.004131700, -0.004131700, -0.004131700");
                }
            }
            max_transition : 1.5000000000;
            related_ground_pin : "VGND";
            related_power_pin : "VPWR";
            rise_capacitance : 0.0024280000;
        }

max_capacitance : 0.1666360000;
            max_transition : 1.4963760000;
            power_down_function : "(!VPWR + VGND)";
```
## Parameters of 2 input NOR

![image](https://user-images.githubusercontent.com/75379398/120278955-caa6ec80-c2ad-11eb-9f9a-ab7af0ed1871.png)


```
   cell ("sky130_fd_sc_hd__nor2_1") {
       leakage_power () {
            value : 0.0042527000;
            when : "!A&B";
        }
        leakage_power () {
            value : 0.0005535000;
            when : "!A&!B";
        }
        leakage_power () {
            value : 0.0003034000;
            when : "A&B";
        }
        leakage_power () {
            value : 0.0027691000;
            when : "A&!B";
        }
        area : 3.7536000000;
        cell_footprint : "sky130_fd_sc_hd__nor2";
        cell_leakage_power : 0.0019697000;
        driver_waveform_fall : "ramp";
        driver_waveform_rise : "ramp";
        pg_pin ("VGND") {
            pg_type : "primary_ground";
            related_bias_pin : "VPB";
            voltage_name : "VGND";
        }
        pg_pin ("VNB") {
            pg_type : "nwell";
            physical_connection : "device_layer";
            voltage_name : "VNB";
        }
        pg_pin ("VPB") {
            pg_type : "pwell";
            physical_connection : "device_layer";
            voltage_name : "VPB";
        }
        pg_pin ("VPWR") {
            pg_type : "primary_power";
            related_bias_pin : "VNB";
            voltage_name : "VPWR";

      pin ("A") {
            capacitance : 0.0023730000;
            clock : "false";
            direction : "input";
            fall_capacitance : 0.0022790000;
            internal_power () {
                fall_power ("power_inputs_1") {
                    index_1("0.0100000000, 0.0230506000, 0.0531329000, 0.1224740000, 0.2823110000, 0.6507430000, 1.5000000000");
                    values("0.0040287000, 0.0040304000, 0.0040343000, 0.0040313000, 0.0040243000, 0.0040084000, 0.0039716000");
                }
                rise_power ("power_inputs_1") {
                    index_1("0.0100000000, 0.0230506000, 0.0531329000, 0.1224740000, 0.2823110000, 0.6507430000, 1.5000000000");
                    values("-0.003655800, -0.003724700, -0.003883500, -0.003886400, -0.003893300, -0.003909000, -0.003945400");
                }
            }
            max_transition : 1.5000000000;
            related_ground_pin : "VGND";
            related_power_pin : "VPWR";
            rise_capacitance : 0.0024670000;
        }
        pin ("B") {
            capacitance : 0.0023530000;
            clock : "false";
            direction : "input";
            fall_capacitance : 0.0022060000;
            internal_power () {
                fall_power ("power_inputs_1") {
                    index_1("0.0100000000, 0.0230506000, 0.0531329000, 0.1224740000, 0.2823110000, 0.6507430000, 1.5000000000");
                    values("0.0024958000, 0.0024923000, 0.0024842000, 0.0024914000, 0.0025080000, 0.0025464000, 0.0026348000");
                }
                rise_power ("power_inputs_1") {
                    index_1("0.0100000000, 0.0230506000, 0.0531329000, 0.1224740000, 0.2823110000, 0.6507430000, 1.5000000000");
                    values("-0.001703600, -0.001700200, -0.001692300, -0.001693300, -0.001695500, -0.001700800, -0.001712900");
                }
            }
            max_transition : 1.5000000000;
            related_ground_pin : "VGND";
            related_power_pin : "VPWR";
            rise_capacitance : 0.0025010000;

max_capacitance : 0.0860700000;
            max_transition : 1.4944790000;
            power_down_function : "(!VPWR + VGND)";
            related_ground_pin : "VGND";
            related_power_pin : "VPWR";
            timing () {
                cell_fall ("del_1_7_7") {
                    index_1("0.0100000000, 0.0230506000, 0.0531329000, 0.1224740000, 0.2823110000, 0.6507430000, 1.5000000000");
                    index_2("0.0005000000, 0.0011792800, 0.0027814000, 0.006560

max_capacitance : 0.1666360000;
            max_transition : 1.4963760000;
```
<Need to return and do the calculations>

## Hier synthesis flat synthesis part2

Issue flatten command 
And generate netlist through:-
```
yosys> write_verilog -noattr multiple_modules_flat.v
```

  ![image](https://user-images.githubusercontent.com/75379398/120279033-e3170700-c2ad-11eb-8fe0-6b2d466c8a84.png)


```
module multiple_modules(a, b, c, y);
  wire _0_;
  wire _1_;
  input a;
  input b;
  input c;
  wire net1;
  wire \u1.a ;
  wire \u1.b ;
  wire \u1.y ;
  wire \u2.a ;
  wire \u2.b ;
  wire \u2.y ;
  output y;
  sky130_fd_sc_hd__and2_2 _2_ (
    .A(\u1.b ),
    .B(\u1.a ),
    .X(\u1.y )
  );
  sky130_fd_sc_hd__clkinv_1 _3_ (
    .A(\u2.b ),
    .Y(_0_)
  );
  sky130_fd_sc_hd__clkinv_1 _4_ (
    .A(\u2.a ),
    .Y(_1_)
  );
  sky130_fd_sc_hd__nand2_1 _5_ (
    .A(_0_),
    .B(_1_),
    .Y(\u2.y )
  );
  assign \u1.a  = a;
  assign \u1.b  = b;
  assign net1 = \u1.y ;
  assign \u2.a  = net1;
  assign \u2.b  = c;
  assign y = \u2.y ;
endmodule
```
![image](https://user-images.githubusercontent.com/75379398/120279115-f924c780-c2ad-11eb-9a42-ef1713f9e1c5.png)

 check submodules through show
, not saying U1 and U2, seeing all components in those blocks flattened out:

  ![image](https://user-images.githubusercontent.com/75379398/120279177-0c379780-c2ae-11eb-9d7e-a7388ccaea99.png)

<NEED TO REDO SECTION SINCE SCREEN SHOT DISTORTED>

Now do sub module level synthesis:

relaunch yosys
```
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
1. Executing Liberty frontend.
Imported 428 cell types from liberty file.

yosys> read_verilog multiple_modules.v 

2. Executing Verilog-2005 frontend.
Parsing Verilog input from `multiple_modules.v' to AST representation.
Generating RTLIL representation for module `\sub_module2'.
Generating RTLIL representation for module `\sub_module1'.
Generating RTLIL representation for module `\multiple_modules'.
Successfully finished Verilog frontend.

yosys> synth -top sub_module1
```

  ![image](https://user-images.githubusercontent.com/75379398/120279250-207b9480-c2ae-11eb-9cb7-f4d940273d3f.png)

sub_module1 synthesised:-

![image](https://user-images.githubusercontent.com/75379398/120279311-2ec9b080-c2ae-11eb-9b96-53b1b0dfd486.png)

  Observed only an AND gate synthesised, there is no OR gate
Link the design using:-
```
yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

![image](https://user-images.githubusercontent.com/75379398/120279361-3e48f980-c2ae-11eb-9f5a-da2a42120ffb.png)
  
Issue show, just see sub_module1
  
 ![image](https://user-images.githubusercontent.com/75379398/120279433-56207d80-c2ae-11eb-97ae-c3e6f7132478.png)

  ## Various Flop Coding Styles and optimization

How to code a flop and the different flops available, different coding styles possible

  ![image](https://user-images.githubusercontent.com/75379398/120279701-abf52580-c2ae-11eb-9aa6-ab7723ffad19.png)

  ![image](https://user-images.githubusercontent.com/75379398/120279728-b44d6080-c2ae-11eb-9cb6-9e978cf07bd6.png)
  
  ```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$
counter_opt.v
dff_asyncres_syncres.v
```
 

## Flop synthesis simulations part1

 ![image](https://user-images.githubusercontent.com/75379398/120279790-c7f8c700-c2ae-11eb-92dc-13ce66de7953.png)

![image](https://user-images.githubusercontent.com/75379398/120279825-d1822f00-c2ae-11eb-97e4-23fc40d3ca7b.png)
  
  Run simulator (iverilog) for asynchronous RESET D flip flop
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog dff_asyncres.v tb_dff_asyncres.v
```
Execute generated file
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_dff_asyncres.vcd opened for output.
```
Analyze simulated VCD waveform through gtkwave

![image](https://user-images.githubusercontent.com/75379398/120279904-ee1e6700-c2ae-11eb-8729-d2432bbe92f6.png)

Shows reset going low before the positive clock edge, even though d input has gone high, it is not considered until the next positive clock edge where we see that output q then goes high (d is aligned to the clock and q is synchronous to the clock)
  
  ![image](https://user-images.githubusercontent.com/75379398/120279981-07bfae80-c2af-11eb-946d-b0d3cbc2d57f.png)

When async_reset goes high again, q immediately goes low even though d is high at that point and will not wait for positive clock edge
So simulation maps to design

  ![image](https://user-images.githubusercontent.com/75379398/120280028-160dca80-c2af-11eb-8560-3be79e560a04.png)
  Run simulator (iverilog) for asynchronous SET D flip flop:
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog dff_async_set.v tb_dff_async_set.v
```

 ![image](https://user-images.githubusercontent.com/75379398/120280077-258d1380-c2af-11eb-811c-e674321415f6.png)
 
When async_set goes high, q is already high (since d is high from last positive clock edge) and q remains high until async_set goes low
  
![image](https://user-images.githubusercontent.com/75379398/120280142-39d11080-c2af-11eb-9848-471253a8f8ad.png)
  
When async_set goes low, q is only evaluated on next positive clock edge so stays high and remains high at next positive clock edge since d high; q then goes low on the subsequent positive clock edge since d is low when evaluation is performed in the code
  
  
  ![image](https://user-images.githubusercontent.com/75379398/120280205-4e150d80-c2af-11eb-8417-45b9f3870af3.png)

  Now look at synchronous reset behaviour flip flop:
Run simulator (iverilog) for synchronous reset behaviour flip flop:
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog dff_syncres.v tb_dff_syncres.v
```

  ![image](https://user-images.githubusercontent.com/75379398/120280447-96343000-c2af-11eb-90f3-64ddda876944.png)

When sync_reset goes high, q only goes low on the next positive clock edge (it is already low due to d value being low); when reset goes low, q goes high on next positive clock edge since d is high
  
  ![image](https://user-images.githubusercontent.com/75379398/120280522-ad731d80-c2af-11eb-95da-6d5bee789a99.png)

## Flop synthesis simulations part2

Synthesise the three circuits in yosys
Read liberty files
Read design (Verilog) for async reset flip flop
```
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
1. Executing Liberty frontend.
Imported 428 cell types from liberty file.
yosys> read_verilog dff_asyncres.v
yosys> synth -top dff_asyncres
```
![image](https://user-images.githubusercontent.com/75379398/120280574-c380de00-c2af-11eb-87c9-6d2ede306797.png)
  
Synthesis has resulted in a D flip flop cell
  
  ![image](https://user-images.githubusercontent.com/75379398/120280630-d7c4db00-c2af-11eb-92ca-aaac6171f790.png)
  
  
Supposed to use keyword called “dfflibmap” since many times in the flow there will be a separate flop library and standard cell library; so need to instruct the tool where to get the flip flop in the design from.
In this example we point back to the one standard library since we do not have a special library for flip flops
```
 yosys> dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
not looking for all the standard cells just the D flip flops

![image](https://user-images.githubusercontent.com/75379398/120280717-ef9c5f00-c2af-11eb-957e-cb3b712c0e32.png)

Now synthesise netlist using abc
```
yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

![image](https://user-images.githubusercontent.com/75379398/120280931-3be79f00-c2b0-11eb-9f06-ab05ec20d72f.png)

Visualise netlist through show
  
  ![image](https://user-images.githubusercontent.com/75379398/120280981-4dc94200-c2b0-11eb-80b6-f11f63680734.png)
  
  
Invertor in the design, we wrote a flip flop design that has an active high reset BUT the flip flop cell used has an active low reset so the tool added the invertor

Now do Asynchronous set D flip flop
```
yosys> read_verilog dff_async_set.v
yosys> synth -top dff_async_set
```
Confirms below D flip flop cell being used

  ![image](https://user-images.githubusercontent.com/75379398/120281050-65082f80-c2b0-11eb-8d39-462fb6c0ff83.png)

Repeat dfflibmap process for this flip flop
  
  ![image](https://user-images.githubusercontent.com/75379398/120281127-76e9d280-c2b0-11eb-83d9-0bb5b8d567f9.png)
  
  
```
yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
  
![image](https://user-images.githubusercontent.com/75379398/120281191-8b2dcf80-c2b0-11eb-8852-827e750f5ad6.png)
  
Now visualise through show command, we see that RESET INPUT is now SET INPUT, invertor still required due to being active high at source but cell input being active low 
  
  ![image](https://user-images.githubusercontent.com/75379398/120281305-af89ac00-c2b0-11eb-8ce4-9f68eba3f1c2.png)


Now do processing for asynchronous and synchronous reset
Examine synchronous reset
```
yosys> read_verilog dff_syncres.v
yosys> synth -top dff_syncres
```
cells used are different from previous flip flops
```
=== dff_syncres ===

   Number of wires:                  7
   Number of wire bits:              7
   Number of public wires:           5
   Number of public wire bits:       5
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                  3
     $_AND_                          1
     $_DFF_P_                        1
     $_NOT_                          1
```

  ![image](https://user-images.githubusercontent.com/75379398/120281374-c8925d00-c2b0-11eb-95ec-0d91007528ac.png)

```
yosys> dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

![image](https://user-images.githubusercontent.com/75379398/120281442-db0c9680-c2b0-11eb-8ade-2e06b5fae486.png)
  
```
yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

  ![image](https://user-images.githubusercontent.com/75379398/120281491-ebbd0c80-c2b0-11eb-9e66-c432819a5c2f.png)
  
Visualise through show
No reset pin since only synchronous reset
A_N is an inverted A input
  
![image](https://user-images.githubusercontent.com/75379398/120281582-04c5bd80-c2b1-11eb-967e-a94003e11811.png)

![image](https://user-images.githubusercontent.com/75379398/120281619-0d1df880-c2b1-11eb-9022-aaf210fb87c9.png)
  
  
## Interesting optimisations part1

Optimisations of mult_2 and mult_8 (view below)
Multiple by 2 (2power1) == shift all bits one bit to the left
Multiple by 8 (2power3) == shift all bits 3 bit to the left
Multiple by 9 (2power3 + 1) == shift all bits 3 bit to the left + orig.val

  ![image](https://user-images.githubusercontent.com/75379398/120281685-258e1300-c2b1-11eb-869b-6727639fc855.png)

  Test principle in labs, don’t need special HW, just connect up pins appropriately
```
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
1. Executing Liberty frontend.
Imported 428 cell types from liberty file.

yosys> read_verilog mult_2.v

2. Executing Verilog-2005 frontend.
Parsing Verilog input from `mult_2.v' to AST representation.
Generating RTLIL representation for module `\mul2'.
Successfully finished Verilog frontend.

yosys> synth -top mul2
below confirms no HW/cells for this design
=== mul2 ===

   Number of wires:                  2
   Number of wire bits:              7
   Number of public wires:           2
   Number of public wire bits:       7
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                  0
```

  ![image](https://user-images.githubusercontent.com/75379398/120281778-45253b80-c2b1-11eb-81ca-943dd87ce3f1.png)

Try and generate netlist with gates/cells through abc:-
```
yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
No HW/cells so abc does not actually execute
  
![image](https://user-images.githubusercontent.com/75379398/120281963-7e5dab80-c2b1-11eb-997e-fb6d3fc46347.png)
  
Visualise through show
  
  ![image](https://user-images.githubusercontent.com/75379398/120281982-874e7d00-c2b1-11eb-9251-e73950de3760.png)

  ## Interesting optimisations part2

Write out netlist (still on multiply by 2 example)
```
yosys> write_verilog -noattr mul2_net.v
yosys> !vi mul2_net.v
```
![image](https://user-images.githubusercontent.com/75379398/120282063-9f260100-c2b1-11eb-8d9a-d435ccb584fd.png)

  yosys has optimised the code such that a is written to top 3 bits of y and remaining lower bit of y is set t 0

Now look at mult_8
```
yosys> read_verilog mult_8.v
8. Executing Verilog-2005 frontend.
Parsing Verilog input from `mult_8.v' to AST representation.
Generating RTLIL representation for module `\mult8'.
Successfully finished Verilog frontend.
yosys> synth -top mult8
No HW/cells
=== mult8 ===

   Number of wires:                  2
   Number of wire bits:              9
   Number of public wires:           2
   Number of public wire bits:       9
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                  0
```
Try abc, same as mult2 (no HW to map to netlist)
  
  ![image](https://user-images.githubusercontent.com/75379398/120282153-b9f87580-c2b1-11eb-886f-53d87c90f1e0.png)

  Visualise through show
  
  ![image](https://user-images.githubusercontent.com/75379398/120282188-c41a7400-c2b1-11eb-8acc-9207111c693b.png)
  
  write netlist
```
yosys> write_verilog -noattr mul8_net.v
yosys> !vi mul8_net.v
```
![image](https://user-images.githubusercontent.com/75379398/120282249-d0063600-c2b1-11eb-9eed-2e8bf79a9ca5.png)

yosys has optimised the code such that a is written to top 3 bits of y and a is written to lower 3 bits of y
# Day 3 - Combinational and sequential optimizations
## Combinational Logic Optimisations part1
Constant propagation example follows:-

![image](https://user-images.githubusercontent.com/75379398/120282322-e14f4280-c2b1-11eb-8813-1f1f4789969a.png)
 
Start from verilog_files folder and use *opt_check* files

opt_check.v :-

![image](https://user-images.githubusercontent.com/75379398/120282383-f1672200-c2b1-11eb-966a-cefd87da853b.png)
  
opt_check.v is effectively a 2 in 1 Multiplexor
Boolean optimisation makes this a two input AND gate
```
assign y = a?b:0; 	
becomes => y=a.b
```
opt_check2.v
  
 ![image](https://user-images.githubusercontent.com/75379398/120282447-02b02e80-c2b2-11eb-8d97-318022aba83f.png)
 
  
```
assign y = a?1:b;
2 in 1 multiplexor
Y = a1+a’b
= a+ (a+b)
=2a+b Idempotent (2a=a+a=a)
= a+b
```
Start yosys, read_liberty and now read_verilog design (opt_check.v):-

![image](https://user-images.githubusercontent.com/75379398/120282517-19ef1c00-c2b2-11eb-8d12-42cd2fd29f19.png)

```
yosys> synth -top opt_check
```
result shown below:
  
  ![image](https://user-images.githubusercontent.com/75379398/120282602-2ecbaf80-c2b2-11eb-9a43-8a25a7ee554a.png)
  
  
execute command to perform the constant propagation and other optimizations is called opt_clean
```
yosys> opt_clean -purge
```
![image](https://user-images.githubusercontent.com/75379398/120282656-40ad5280-c2b2-11eb-83d9-e11a62c256de.png)
  

After execution all optimizations executed
Now perform abc (generate net list with cells)
```
yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
 ![image](https://user-images.githubusercontent.com/75379398/120282696-4efb6e80-c2b2-11eb-9fad-9794f9683bfe.png)
 
Now visualise netlist design 

  ![image](https://user-images.githubusercontent.com/75379398/120282755-6175a800-c2b2-11eb-9261-50272c323beb.png)
  
  Expected AND gate from the thoery and got this “__and2_2” for $51 standard cell

Now process opt_check2 using the same procedure:
```
yosys> read_verilog opt_check2.v
```

 ![image](https://user-images.githubusercontent.com/75379398/120282851-78b49580-c2b2-11eb-9c1a-14485418e8cb.png)
 
Logically expected OR gate from logic BUT this is not implementation due to stacked PMOS therefore NAND (__nand2_) and invertor on each input is used which is functionally equivalent
NAND = Inverted Inputs + OR (Demorgan’s theorem)
Inverted Inputs + Inverted Inputs + OR == OR (since back to back inverters cancel out)

![image](https://user-images.githubusercontent.com/75379398/120282936-8bc76580-c2b2-11eb-9b6f-8b8a3dc35a71.png)
  
## Combinational Logic Optimisations part2

View opt_check3.v

![image](https://user-images.githubusercontent.com/75379398/120282993-9b46ae80-c2b2-11eb-8db0-0310a4af8168.png)
  
assign y = a?(c?b:0):0;
two instances of 2:1 MUX
(cb+c’0) – first stage output
(first stage output)a+a’0
(cb+c’0)a+a’0 = abc – expect 3 input AND gate

![image](https://user-images.githubusercontent.com/75379398/120283062-b31e3280-c2b2-11eb-8059-d9790ac4f72c.png)

Go to yosys to confirm (repeat read_liberty, read_verilog, syth, opt_clean, abc for opt_check3):
abc output confirms 3 input AND gate in netlist

  ![image](https://user-images.githubusercontent.com/75379398/120283136-c6c99900-c2b2-11eb-8b1f-263ab87ac137.png)
  
  Visualise with show (three input AND gate confirmed):-
  
  ![image](https://user-images.githubusercontent.com/75379398/120283198-d648e200-c2b2-11eb-886f-ef3aafb70691.png)

View opt_check4.v
  
![image](https://user-images.githubusercontent.com/75379398/120283254-e496fe00-c2b2-11eb-898d-2ab00f694226.png)
  
 ```
assign y = a?(b?(a & c ):c):(!c);
```
<see log book for diagram>
  
 ![image](https://user-images.githubusercontent.com/75379398/120283333-f9739180-c2b2-11eb-8aba-0dbe753b3f03.png)
  
  ![image](https://user-images.githubusercontent.com/75379398/120283367-02fcf980-c2b3-11eb-8889-cfcb1264f2d1.png)
  
  ![image](https://user-images.githubusercontent.com/75379398/120283394-0bedcb00-c2b3-11eb-8cfe-e68c25142d8d.png)
  
  <NEED TO CHECK THE BOOLEAN LOGIC>
View multiple_module_opt.v

![image](https://user-images.githubusercontent.com/75379398/120283447-1c9e4100-c2b3-11eb-880a-e9043f2d9793.png)
    
First look at un flattened view in yosys
show multiple_module_opt

![image](https://user-images.githubusercontent.com/75379398/120283509-2aec5d00-c2b3-11eb-949e-df819fa018ed.png)
    
    Now use flatten and write_verilog commands after synth and before opt_clean
```
yosys> flatten
4. Executing FLATTEN pass (flatten design).
Mapping multiple_module_opt.U1 using sub_module1.
No more expansions possible.
Deleting now unused module sub_module1.

yosys> write_verilog -noattr multiple_module_opt_flat.v
```

![image](https://user-images.githubusercontent.com/75379398/120283577-3d669680-c2b3-11eb-826e-6b16532f2ebf.png)
    
Sub_module1 deleted   
    
 ![image](https://user-images.githubusercontent.com/75379398/120283656-52dbc080-c2b3-11eb-8027-97fba4c38b75.png)
    
  View multiple_module_opt2.v
    
 ![image](https://user-images.githubusercontent.com/75379398/120283713-638c3680-c2b3-11eb-84b9-48450d8b9556.png)
 
 
```
module sub_module(input a , input b , output y);
 assign y = a & b;
endmodule
module multiple_module_opt2(input a , input b , input c , input d , output y);
wire n1,n2,n3;
sub_module U1 (.a(a) , .b(1'b0) , .y(n1));
sub_module U2 (.a(b), .b(c) , .y(n2));
sub_module U3 (.a(n2), .b(d) , .y(n3));
sub_module U4 (.a(n3), .b(n1) , .y(y));
endmodule
```
perform synth of multiple_module_opt2.v
   
![image](https://user-images.githubusercontent.com/75379398/120284094-cda4db80-c2b3-11eb-87a8-bb456da6558c.png)

Issue flatten
    
![image](https://user-images.githubusercontent.com/75379398/120284170-e4e3c900-c2b3-11eb-8d19-c9a03dc90195.png)
    

```
write_verilog multiple_module_opt2_flat.v
```
![image](https://user-images.githubusercontent.com/75379398/120284292-03e25b00-c2b4-11eb-82ca-2bd28b6155ae.png)
    
 ```
opt_clean_purge
```
![image](https://user-images.githubusercontent.com/75379398/120284393-1bb9df00-c2b4-11eb-8df4-f1eb28bdde7b.png)
    
```
abc -liberty
```
    
![image](https://user-images.githubusercontent.com/75379398/120284472-2f654580-c2b4-11eb-96b1-e4f59e686d69.png)

```
show multiple_module_op2: 
```
Unflattened

![image](https://user-images.githubusercontent.com/75379398/120284548-4441d900-c2b4-11eb-91c2-b19d7a46a645.png)
    
Flattened
    
![image](https://user-images.githubusercontent.com/75379398/120284644-5de32080-c2b4-11eb-9fc3-5f0ee4c803e2.png)

<Need to come back and inspect the flattening for multiple_module_opt2>
## Day3 test
```
module opt_check4 (input a , input b, output y);
assign y = a?(b?0:a):b;
endmodule
```
synth shows XOR cell being selected

![image](https://user-images.githubusercontent.com/75379398/120284771-83702a00-c2b4-11eb-84e5-50f99fc45a3c.png)
  
![image](https://user-images.githubusercontent.com/75379398/120284819-91be4600-c2b4-11eb-9235-51933618557a.png)

<need to verify results>
## Sequential Logic Optimisations part1

![image](https://user-images.githubusercontent.com/75379398/120284875-a0a4f880-c2b4-11eb-97f7-8807d271a539.png)
    
dff_const1.v 
  
![image](https://user-images.githubusercontent.com/75379398/120284969-b74b4f80-c2b4-11eb-8e1d-6b318769ca67.png)
  
 
```
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
        if(reset)
                q <= 1'b0;
        else
                q <= 1'b1;
end
endmodule
```
q will become 1 on next rising edge of the clock when reset removed

dff_const2.v
```
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
        if(reset)
                q <= 1'b1;
        else
                q <= 1'b1;
end

endmodule
```
Simulate to examine behavior
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog dff_const1.v tb_dff_const1.v
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_dff_const1.vcd opened for output.
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_dff_const1.vcd
```
 
![image](https://user-images.githubusercontent.com/75379398/120285072-cfbb6a00-c2b4-11eb-9bf1-17829006e281.png)
                  
 
Q goes high on next positive clock edge even though reset cleared mid cycle
Repeat for dff_const2.v
```
iverilog dff_const2.v tb_dff_const2.v
./a.out
gtkwave tb_dff_const2.vcd
```
  
![image](https://user-images.githubusercontent.com/75379398/120285223-f1b4ec80-c2b4-11eb-89aa-f53742529c91.png)
                  
 Since active reset makes q high, when reset is cleared q remains high because input (d) is tied high, so it doesn’t matter if processing evaluated at +ve clock edge as in the design (or indeed on a -ve clock edge)
Go into yosys to check synthesised behaviour
After synth -top step we run dfflibmap command in order to state the standard cell library to use for the flip flop since it is possible to select a different one then what is used for the combinational logic. In this case we use the same standard cell library
```

yosys> dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
  
![image](https://user-images.githubusercontent.com/75379398/120285546-42c4e080-c2b5-11eb-93cb-5796895825e5.png)
                  
Do abc (skip optional optimization step of opt_clean) then show
                  
![image](https://user-images.githubusercontent.com/75379398/120285695-6b4cda80-c2b5-11eb-91a2-c51605dfd470.png)
                  
 We observe a FF standard cell library, the reset of this library is implemented as ACTIVE_LOW, since our design is implemented with ACTIVE_HIGH reset, we observe an invertor standard cell between the reset input and input into FF.
This is corresponding code in Verilog (dff_const1.v):
```
if(reset)
                q <= 1'b0;
```
if we wanted to use active low (and get rid of invertor then we would write this code):-
```
if(!reset)
                q <= 1'b0;
```
## Sequential Logic Optimisations part2
We repeat the process for dff_const2.v
```
3.22.2. Analyzing design hierarchy..
Top module:  \dff_const2
Removed 0 unused modules.

3.23. Printing statistics.

=== dff_const2 ===

   Number of wires:                  3
   Number of wire bits:              3
   Number of public wires:           3
   Number of public wire bits:       3
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                  0
```

  ![image](https://user-images.githubusercontent.com/75379398/120285809-8a4b6c80-c2b5-11eb-850f-b601ce6bb278.png)
  
 ![image](https://user-images.githubusercontent.com/75379398/120285866-96372e80-c2b5-11eb-93a9-4bcb226b529c.png)
  
  We observe after synth step that number of cells is 0 for dff_const2 as opposed to 1 DFF cell for dff_const1
We observe generated netlist through show

![image](https://user-images.githubusercontent.com/75379398/120285942-abac5880-c2b5-11eb-898f-acb7ea5ef73a.png)
  
  No cells have been placed in the netlist because the output/q is always 1 (as shown in the simulation) so we see q tied to 1’1.
This is sequential optimisation.
View dff_const3.v

![image](https://user-images.githubusercontent.com/75379398/120286071-c979bd80-c2b5-11eb-9a09-d930241c403f.png)
  
![image](https://user-images.githubusercontent.com/75379398/120286194-e3b39b80-c2b5-11eb-90ec-65a6a6375d36.png)
  
Flops cannot be optimised away due to their respective outputs changing, even though Q is always high apart from one clock cycle due to delay in Q1 changing after reset goes low because it does not change its value exactly on next +ve clock edge, there is a slight delay.
## Sequential Logic Optimisations part3

![image](https://user-images.githubusercontent.com/75379398/120286265-f62dd500-c2b5-11eb-928a-9cdb3c9e438f.png)
  
From gtkwave we see that q is low for one clock cycle after reset goes low which is consistent with the theory
After synthesis (synth)
```
   Number of cells:                  2
     $_DFF_PP0_                      1
     $_DFF_PP1_                      1
```

![image](https://user-images.githubusercontent.com/75379398/120286341-0ba2ff00-c2b6-11eb-803b-3e5e3f166d10.png)
  
 After dfflibmap stage we observe the cells that have been mapped
  
 ![image](https://user-images.githubusercontent.com/75379398/120286427-237a8300-c2b6-11eb-947e-b0f1633e6b6c.png)
  
 ![image](https://user-images.githubusercontent.com/75379398/120286487-31c89f00-c2b6-11eb-862f-706b30fedf82.png)
  
  Flips flops mapped as expected.
Output from first FF mapped to input of second
Also RESET_B and SET_B of first and second FFs respectively are active low hence requiring reset input to be inverted (as shown) to achieve active high system behaviour

Processing of dff_const4.v 
```
module dff_const4(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
begin
        if(reset)
        begin
                q <= 1'b1;
                q1 <= 1'b1;
        end
        else
        begin
                q1 <= 1'b1;
                q <= q1;
        end
end
endmodule
```

![image](https://user-images.githubusercontent.com/75379398/120286575-4efd6d80-c2b6-11eb-891f-f0352942008b.png)
                       
As expected, irrespective of reset value and clocks Q1 and Q2 always 1
As expected, no cells used

 ![image](https://user-images.githubusercontent.com/75379398/120286686-6f2d2c80-c2b6-11eb-85c8-3a9534ce085a.png)
                       
As expected, abc had nothing to map and show confirms that outputs hardwired to 1  
                       
![image](https://user-images.githubusercontent.com/75379398/120286815-8ff58200-c2b6-11eb-963e-7573cf6ae53c.png)
                       

Processing of dff_const5.v 

```
module dff_const5(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
        if(reset)
        begin
                q <= 1'b0;
                q1 <= 1'b0;
        end
        else
        begin
                q1 <= 1'b1;
                q <= q1;
        end
end
endmodule
```
![image](https://user-images.githubusercontent.com/75379398/120286988-b7e4e580-c2b6-11eb-8f81-901d97da502a.png)
  
In gtkwave we observe q following q1 one clock cycle after q1 goes positive due to reset clearing due to delay of q1 changing on the previous clock cycle
Synth confirms cells being used
  
 Show confirms two FFs with invertors as in dff_const3 (however second FF is a RESET not a SET as in dff_const3)
  
  ![image](https://user-images.githubusercontent.com/75379398/120287189-ef539200-c2b6-11eb-9ba5-b8fd60e97311.png)
  
  ![image](https://user-images.githubusercontent.com/75379398/120287267-04302580-c2b7-11eb-996e-ac9d922a403a.png)
  
  ## Seq optimisation unused outputs part1
  
  ![image](https://user-images.githubusercontent.com/75379398/120287375-1c07a980-c2b7-11eb-8b56-8a919f2714ec.png)


```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
        if(reset)
                count <= 3'b000;
        else
                count <= count + 1;
end
endmodule
```
Go to yosys to synthesise counter_opt.v, after synth:-
                  
![image](https://user-images.githubusercontent.com/75379398/120287561-3fcaef80-c2b7-11eb-8d29-b2ab8ae10346.png)
                  
Only one FF, expect 3 for 3 bit counter to store the 3 states of count BUT 2 unused outputs are optimised away….
Perform show to confirm what we see:-
                  

![image](https://user-images.githubusercontent.com/75379398/120287730-6ee16100-c2b7-11eb-9fd3-5e202e1c5285.png)
                  
Diagram confirms only one FF

## Seq optimisation unused outputs part2
Produce counter_optRob.v (based on modification of counter_opt.v – should be called counter_opt2)

Modify assign command to the following to force all 3 bits of count to be used which should give us 3 FF in netlist
assign q = [count[2:0] == 3'b100];

synth in yosys, we expect 3 FFs, confirmed below:-

 ![image](https://user-images.githubusercontent.com/75379398/120288650-5d4c8900-c2b8-11eb-91f6-c9c8ae3f7140.png)
                         
 We see the three flops for the 3 bits of count    
                         
 ![image](https://user-images.githubusercontent.com/75379398/120288897-9dac0700-c2b8-11eb-98fd-3ca733bfd09f.png)
                        
![image](https://user-images.githubusercontent.com/75379398/120288943-a8ff3280-c2b8-11eb-9048-639f2720e40c.png)
                         
 ### Test, checking number of FFs
```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count == 3'b101);

always @(posedge clk ,posedge reset)
begin
        if(reset)
                count <= 3'b000;
        else
                count <= count + 3'h4;
end
endmodule
```

![image](https://user-images.githubusercontent.com/75379398/120289964-c4b70880-c2b9-11eb-9124-4f0a7f5e94e5.png)


show confirms that there are 0 FFs for this code
# Day 4 - GLS, blocking vs non-blocking and Synthesis-Simulation mismatch
## Synthesis Simulation Mismatch

### GLS – Gate Level Simulation

When writing RTL code we validate functionality of RTL code by testing it, give stimulus to design and check output as per the specifications – setup is called a testbench

We want to run the testbench for the generated netlist (will be Design under test)
Originally when run code, the RTL was the code under test
Netlist logically the same as the RTL code
Netlist will seamlessly fit into the test bench

### Why GLS
Verify the correctness of design under synthesis
Ensure timing is met (in RTL there is no mention of timing)
Need to verify setup and hold time are met for the various cells that have been used
GLS needs to be run with delay annotation (won’t do this in labs)

![image](https://user-images.githubusercontent.com/75379398/120290072-e2846d80-c2b9-11eb-8ba9-788b522d2682.png)

![image](https://user-images.githubusercontent.com/75379398/120290120-f203b680-c2b9-11eb-9a8d-04f31c61eaf7.png)
  
## GLS Synth Sim Mismatch part1
 
![image](https://user-images.githubusercontent.com/75379398/120290195-02b42c80-c2ba-11eb-9fa5-ea76d4ddb992.png)

 Files to use in Verilog_files dir:-
ternary_operator_mux.v
bad_mux.v
good_mux.v

![image](https://user-images.githubusercontent.com/75379398/120290289-1cee0a80-c2ba-11eb-8450-c6aa962ed008.png)
  
good_mux.v uses “always @(*)” as opposed to just waiting for sel to change
assign y = sel?i1:i0;
Ternary operator, condition and two operands
First do RTL simulation through iverilog
```
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
VCD info: dumpfile tb_ternary_operator_mux.vcd opened for output.
gtkwave tb_ternary_operator_mux.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120290366-31ca9e00-c2ba-11eb-9bc9-a3975333e160.png)
  
When sel is 1 y is i1 and when sel is 0 y is i0 – as expected
Now synthesis the design through yosys
yosys> read_verilog ternary_operator_mux.v
yosys> synth -top ternary_operator_mux

MUX included in synthesis

![image](https://user-images.githubusercontent.com/75379398/120290455-4313aa80-c2ba-11eb-83ad-4b840c2c982d.png)

After performing abc step, write out the generated netlist into a specified Verilog file using the following command:
```
yosys> write_verilog ternary_operator_mux_net.v
```
view netlist using show:

![image](https://user-images.githubusercontent.com/75379398/120290571-5e7eb580-c2ba-11eb-81eb-1f60857fe5d0.png)

![image](https://user-images.githubusercontent.com/75379398/120290598-66d6f080-c2ba-11eb-9130-95bee36503ce.png)
  
 
<log book explanation>
Netlist consistent with 2:1 MUX equation

Now do GLS
Need to include 
-Verilog standard cell models libraries (two files)
- generated netlist from yosys abc
- associated testbench for the design
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_ternary_operator_mux.vcd opened for output.
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_ternary_operator_mux.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120290716-84a45580-c2ba-11eb-9f79-46b4bebb37d2.png)
  
Know this is GLS since we do not have _6_,_7_,_8_ in RTL (as shown under UUT)
The waveform confirms that GLS output is following 2:1 MUX behaviour

## GLS Synth Sim Mismatch part2

bad_mux.v
Problem with “always @ (sel)” since block only entered when sel changes (should be entered when inputs change)
```
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
        if(sel)
                y <= i1;
        else
                y <= i0;
end
endmodule
```
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog bad_mux.v tb_bad_mux.v
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out 
VCD info: dumpfile tb_bad_mux.vcd opened for output.
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_bad_mux.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120290868-a3a2e780-c2ba-11eb-9b67-b9627e80be33.png)
  
At beginning with sel low and no changes, we see that i0/i1 changes but does not get to y. We see that when sel changes the value of i1 (when sel goes high) or i0 (when sel goes low) goes to y, not working as MUX. So. for simulation the behaviour is not working as a MUX.
Look at synthesis through yosys
```
yosys> read_verilog bad_mux.v
yosys> synth -top bad_mux
after abc
yosys> write_verilog bad_mux_net.v
```
Now simulate bad_mux netlist (bad_mux.net.v)
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_bad_mux.vcd opened for output.
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_bad_mux.vcd
```
  
![image](https://user-images.githubusercontent.com/75379398/120291015-ca611e00-c2ba-11eb-9741-be9e84564ab5.png)

 The trace clearly shows that when sel changes between high and low that i1 and i0 input respectively is reflected in realtime on the y output

Comparison of RTL Simulation (top) with Netlist Synthesis (bottom), we clearly see how Synthesis is behaving correctly as a MUX AND the mismatch is clear between the two traces; in practice this kind of mismatch in production can increase verification/validation cycles as the issue is hunted down in a complex design. Simulation mismatch due to missing sensitivity list.

![image](https://user-images.githubusercontent.com/75379398/120291143-e5cc2900-c2ba-11eb-8b75-0df4635c93e9.png)

## Synth sim mismatch blocking statement part1
blocking_caveat.v
```
module blocking_caveat (input a , input b , input  c, output reg d);
reg x;
always @ (*)
begin
        d = x & c;
        x = a | b;
end
endmodule
```
First do RTL simulation
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog blocking_caveat.v tb_blocking_caveat.v 
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_blocking_caveat.vcd opened for output.
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_blocking_caveat.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120291230-fed4da00-c2ba-11eb-8322-407921a459b8.png)

Look at highlighted point in trace, A is high (B is low) and C is high meaning that D should be high BUT it is going low, A was previously low and looks like this is being used to calculate D value, looks like there is a flop in the design storing previous value of A to be used in the current calculation
## Synth sim mismatch blocking statement part2
Now do Netlist simulation for same design to compare
```
yosys> read_verilog blocking_caveat.v
yosys> synth -top blocking_caveat
yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
yosys> write_verilog -noattr blocking_caveat_net.v
```
Now do show to examine the generated netlist for the design

![image](https://user-images.githubusercontent.com/75379398/120291325-16ac5e00-c2bb-11eb-9f1d-4df69cbc94bd.png)

No latch here, so cannot examine past value of A in the calculation of D
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_net.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120291392-2a57c480-c2bb-11eb-984b-f5cb99c5f171.png)

Netlist simulation is behaving correctly, when A is high and B is low and C is high, D is high which respects the design.
Non blocking statements should be avoided.

![image](https://user-images.githubusercontent.com/75379398/120291573-5bd09000-c2bb-11eb-8322-1ff9a45528d1.png)
  
Compare both traces so mismatch is obvious                         

# Day 5 - If, case, for loop and for generate
## IF CASE Constructs

![image](https://user-images.githubusercontent.com/75379398/120291663-74d94100-c2bb-11eb-9120-dd202a84f0f2.png)
  
![image](https://user-images.githubusercontent.com/75379398/120291684-7c98e580-c2bb-11eb-96cd-bc101a820917.png)
  
![image](https://user-images.githubusercontent.com/75379398/120291709-84588a00-c2bb-11eb-99e2-4571f04ba903.png)
  
 ![image](https://user-images.githubusercontent.com/75379398/120291736-8c182e80-c2bb-11eb-8b64-967ccd7bf245.png)
  
 ![image](https://user-images.githubusercontent.com/75379398/120291767-94706980-c2bb-11eb-90c1-f0f192e8c5e9.png)
  
 ## Incomplete IF part1

Perform labs for “Incomplete IF” situations
Relevant files:-
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ls *incomp*
incomp_case.v  incomp_if.v  incomp_if2.v  tb_incomp_case.v  tb_incomp_if.v  tb_incomp_if2.v

Look at
```
incomp_if.v
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
        if(i0)
                y <= i1;
end
endmodule
```
There are no ELSE IF or/and ELSE statement

![image](https://user-images.githubusercontent.com/75379398/120291846-a81bd000-c2bb-11eb-8319-a83d7b709c3f.png)
                       
2:1 MUX => D LATCH

Use iverilog for RTL Simulation to examine behaviour
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog incomp_if.v tb_incomp_if.v 
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_incomp_if.vcd opened for output.
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_incomp_if.vcd
```

 ![image](https://user-images.githubusercontent.com/75379398/120291918-b833af80-c2bb-11eb-9e25-7316f8970bc9.png)
 
                       
 When i0 high (select EN line) -> y follows i1
When i0 low (select EN line) -> y latches onto its current value

Lets compare to netlist simulation
After synth incomp_if in yosys we see DLATCH being inferred in synthesis

![image](https://user-images.githubusercontent.com/75379398/120291980-c7b2f880-c2bb-11eb-9194-85b30f9f4bad.png)
  
Show after abc (synthesis netlist complete) confirms DLATCH in netlist
 i0 is enable input, i2 not used (don’t want a latch, we a inferred DLATCH due to incomplete IF)

 ![image](https://user-images.githubusercontent.com/75379398/120292057-d9949b80-c2bb-11eb-9a14-ab17495271cc.png)

  ## Lab Incomplete IF part2

incomp_if2.v
```
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
        if(i0)
                y <= i1;
        else if (i2)
                y <= i3;
end
endmodule
```

![image](https://user-images.githubusercontent.com/75379398/120292112-eadda800-c2bb-11eb-84de-efa05e40e492.png)
  
 Multiple conditions result in 2 multiplexors but DLATCH with EN condition on i0 OR i2 and combinational logic driving the input
If both i0 and i2 are low the output latches (i.e. output keeps the same value)

### RTL Simulation
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog incomp_if2.v tb_incomp_if2.v 
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_incomp_if2.vcd opened for output.
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_incomp_if2.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120292472-3f812300-c2bc-11eb-8807-e9c79618a045.png)
  
 i0 is high, y follows i1
i0 is low, if i2 high, y follows i3
i0 is low, if i2 low, y remains the same value (i.e. latches) – this is confirmed in the traces


netlist simulation
after synth incomp_if2

![image](https://user-images.githubusercontent.com/75379398/120292635-62133c00-c2bc-11eb-89aa-e31a8ec571eb.png)

We see DLATCH, OR, MUX cells inferred 
  
![image](https://user-images.githubusercontent.com/75379398/120292728-73f4df00-c2bc-11eb-8d87-45c4ab072142.png)
  
 Combo logic on the left which is a function of i3, i1, i0,i2
## Incomplete overlapping Case part1
incomp_case.v
```
module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
        case(sel)
                2'b00 : y = i0;
                2'b01 : y = i1;
        endcase
end
endmodule
```
No default and nothing specified for 01 and 11, so we will get a latch
  
![image](https://user-images.githubusercontent.com/75379398/120293776-7dcb1200-c2bd-11eb-9b21-3c3a2a400152.png)
  
Functional Verilog Simulation
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog incomp_case.v tb_incomp_case.v 
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_incomp_case.vcd opened for output.
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_incomp_case.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120293884-95a29600-c2bd-11eb-81fa-8445c808f544.png)
  
### RTL Netlist Simulation
  
![image](https://user-images.githubusercontent.com/75379398/120293966-a4894880-c2bd-11eb-8d8f-96188b53393c.png)

abc, show
  
![image](https://user-images.githubusercontent.com/75379398/120295111-c20ae200-c2be-11eb-8c50-5255db3ddd8a.png)
  
Latch cell confirmed by abc and show
Output y being driven by a latch (we don’t want the latch but this is what has happened with an incomplete CASE/design)
Enable of latch being driven by sel[1]’ (we see that sel[1] is being correctly inverted)
We see sel[0] driving combinational logic

## Incomplete overlapping Case part2
comp_case.v (COMPLETE CASE example with DEFAULT behaviour specified)
```
module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
        case(sel)
                2'b00 : y = i0;
                2'b01 : y = i1;
                default : y = i2;
        endcase
end
endmodule
```

![image](https://user-images.githubusercontent.com/75379398/120295283-e961af00-c2be-11eb-8cfe-694ba0139580.png)
  
No latch inferred
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog comp_case.v tb_comp_case.v 
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_comp_case.vcd opened for output.
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_comp_case.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120295426-072f1400-c2bf-11eb-992b-cdb433e7db7b.png)


Traces confirm no latch action
Netlist generation to confirm
No latches after synthesis

![image](https://user-images.githubusercontent.com/75379398/120295534-1dd56b00-c2bf-11eb-97cb-f5daa93430cf.png)

No latches after abc/show 
(need to prove combinational logic – offline)

![image](https://user-images.githubusercontent.com/75379398/120295573-29289680-c2bf-11eb-995c-e5d8b3cd3ace.png)

<NEED TO PROVE THE LOGIC>
partial_case_assign.v
```
module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel, output reg y , output reg x);
always @ (*)
begin
        case(sel)
                2'b00 : begin
                        y = i0;
                        x = i2;
                        end
                2'b01 : y = i1;
                default : begin
                           x = i1;
                           y = i2;
                          end
        endcase
end
endmodule
```
     
![image](https://user-images.githubusercontent.com/75379398/120295647-3e052a00-c2bf-11eb-9175-deba126f7a93.png)

Redundancy theorem to simplify
## Incomplete overlapping Case part3
Synthesise to check if latch present
After synth (latch confirmed – just 1 for X)

![image](https://user-images.githubusercontent.com/75379398/120295979-93d9d200-c2bf-11eb-91ff-da21aac44412.png)
  
Y has no latches in its path
X has latch in its path

![image](https://user-images.githubusercontent.com/75379398/120296036-a227ee00-c2bf-11eb-8534-16879cd2fdeb.png)
  
<Need to check Boolean expressions in RTL with his previous logic explained some steps above with the partial_case_assign.v – log book also>
Path to enable Latch
<logbook>
Need to ensure manually CASE conditions are mutually exclusive because it allows overlap Verilog (IF block conditions are always mutually exclusive)
bad_case.v shows an example of a CASE statement that has overlapping conditions
```
module bad_case (input i0 , input i1, input i2, input i3 , input [1:0] sel, output reg y);
always @(*)
begin
        case(sel)
                2'b00: y = i0;
                2'b01: y = i1;
                2'b10: y = i2;
                2'b1?: y = i3;
                //2'b11: y = i3;
        endcase
end
endmodule
```
Has wildcard character in one of its conditions which facilitates overlap
2'b1?: y = i3;
When select becomes 11 or 10 this condition is activate which overlaps with this condition 2'b10: y = i2;
This can lead to tool “confusion” – we will simulate to see what Verilog simulation reveals about this handling
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog bad_case.v tb_bad_case.v 
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ ./a.out
VCD info: dumpfile tb_bad_case.vcd opened for output.
robert@ubuntu-m-2vcpu-16gb-sgp1-01:~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ gtkwave tb_bad_case.vcd
```

![image](https://user-images.githubusercontent.com/75379398/120296115-b53abe00-c2bf-11eb-83d0-9ec1af1bfd10.png)
  

When select goes to 11 it is not taking i3 input nor i2, it is latching to value 1
## Lab incomplete overlapping Case part4
Now try synthesis of bad_case.v in yosys (no latches inferred as expected since all combination of conditions are covered but overlapping exists)

  
![image](https://user-images.githubusercontent.com/75379398/120296173-c1bf1680-c2bf-11eb-811e-8579d1ae5979.png)

 ![image](https://user-images.githubusercontent.com/75379398/120296204-cb487e80-c2bf-11eb-93f2-feee21c56c6f.png)
 
 write_verilog to perform GLS on netlist
```
~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_case_net.v tb_bad_case.v
```
<COULD NOT CONTINUE - LAB DOWN AT 7.30PM SUNDAY ☹ - I need to work faster next time…>

 ## IF CASE Constructs part1
If/case is used to signify priorities amongst conditions
Create priority logic in the HW
Danger/Caution with if => “INFERRED LATCHES” : Bad coding style with “INCOMPLETE IF”
Occurs when there is an if/else if but then no closing “else”, so output y is “hooked” back into the incomplete MUX input via a latch (latch placed by tool to avoid combinational loop).
For counter code this could be intentional
## IF CASE Constructs part2
### Counter code
If no en on MUX then count will not increase, value will latch onto previous value of count – we can do this for SEQUENTIAL circuits, where as you cannot have inferred latches for COMBINATIONAL circuits
### Case statement
IF AND CASE are always used inside a block, realised by a multiplexor
Verilog rule : Whatever variable you are trying to assign in CASE (or IF) should be a register variable
Case statement infers a MUX
Caveats with Case
1 – Incomplete case => Inferred latches
See below, sel is 2 bit signal so four combinations so need to state behaviour for all 4 conditions not just two otherwise get inferred latches
So code case with DEFAULT (just like in ‘C’ language)
With default, inferred latches are avoided
## IF CASE Constructs part3
### Caveats with Case
2 – Partial assignments in case
Need to assign all the outputs in all the segments of case
Example, trying to control more than one output by CASE (in this example we use two outputs)

3 – No overlapping CASE statements
### Priorities IMPLIED in IF/ELSE IF/ELSE
For CASE, no priorities, so its possible that two conditions may be satisfied if wildcards (e.g. ?) are used since CASE executes sequentially where all conditions are evaluated – must not have over lapping case statements – leads to UNPREDICTABLE OUTPUTS
In IF statements, only one condition can ever be true
### For Loop and For Generate part1
Looping constructs to generate HW
In Verilog there is:
-	 “for loop” : used in “always” block – used to evaluate expressions NOT to instantiate gate instance X times
-	“generate 
for loop” : should not/cannot be used inside “always” – used to generate HW through instantiating gate X times

Imagine if defining a multiplexor block if we use case statement, we have a condition for each possible input of the mux, so:-


![image](https://user-images.githubusercontent.com/75379398/120299454-e963ae00-c2c2-11eb-8508-141f9f5fe5fe.png)

We can use BLOCKING STATEMENTS 😊 Different context in meaningful way
Take same principle for demux, means no additional HW generated, note in both cases the for loop is inside the always
## For Loop and For Generate part2
### for generate :
Want to generate multiple instances of HW, e.g. 500 instances of an and gate
e.g. Ripple Carry Adder (RCA)

## For Loop and For Generate part3
### Ripple Carry Adder (RCA)
Performs Ripple Carry Addition
Has Carry + Sum
Carry ripples through from present LSb to next MSb
Need a separate RCA instance HW per bit of the bus being added
Clearly a single HW may be instantiated multiple times -> for generate
  
  ![image](https://user-images.githubusercontent.com/75379398/120299532-fd0f1480-c2c2-11eb-976e-ae089df19998.png)






 
  
  
# Acknowledgements
- [Kunal Ghosh](https://github.com/kunalg123?tab=repositories)
- [VSD-IAT](https://vsdiat.com/)
 

  
                       
                       
                
                      

                       
 
 
  
  

  

  

  
  
  
 

               
                  
                  
                  
                  

    


    
    
    
    

   
    


 
  
