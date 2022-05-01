# Sky-130RTLDesignandSynthseis
## Day 1:
Day 1 started off with an introduction to iverilog, specifically the Verilog testbench. The testbench is a separate module that is used to feed inputs while monitoring output changes through time.

(Stimulus Generator diagram)

It is important to know that the testbench doe not have any primary inputs or outputs. We then looked at the simulator, which is GTKWave. An important feature of the simulator is that it checks for output changes whenever there is a change in input.

We then looked at the entire verilog design workflow

(Workflow diagram)

Then the lab environment and the required files were setup by cloning the github repository sky130RTLDesignAndSynthesisWorkshop. This contained all the behavioral files of several building blocks of digital electronics as well as well as the standard cell Verilog models.



We then focused on GTKWave, the simulator which is used for the duration of this course. The basic commands used to simulate a .vcd file (VCD stands for value change dump) are given as follows

(day13)

A simulated waveform looks as follows

(day14)

The Synthesizer:
Another important tool pertaining to the entire workflow is the synthesizer. Whatever is coded in Verilog is the RTL design. However, this has to be synthesized or converted into physical blocks. This conversion of an RTL into Netlist is done through synthesis and this is the job of a synthesizer. 

The synthesizing workflow looks as follows
(synthesizer workflow diagram)

The RTL code looks as follows, written in iverilog.
(day15)
The synthesized module looks as follows:
(day16)

Hence, the final workflow for the entire design will look as follows
(our own image)

The .lib files
- The .lib files are a collection of logical modules based on the sky130 PDK
- It includes a collection of basic as well as complex gates.
- There are different flavors for each of these modules Eg: 2,3 and 4 input, slower gates, medium gates and faster gates, etc.

The need for gates of different flavours is discussed from a timing perspective. Take the example of a two flip flop combinational circuit, like the one given below. 

(FF image).

We define two terms here, namely setup and hold time. The setup time is defined as the minimum time for which the input must be held stable before a rising/ falling edge. 

Tclock > Tcq_a + Tcomb + Tsetup_b

The faster our cells are, the lower our Tclock can be and hence we can drive the circuit at a higher frequency. Similarly, hold time can be defined as the minimum amount of time after the clock’s active edge where the data should be stable. The hold time expression is given as

Thold_b < Tcq_a + Tcompi

Hence, we need faster cells to take care of setup time issues and hold time to accommodate for the fold time issues. However, faster and slower cells come with their own drawbacks

Faster cells -> more current -> Low delay -> More area and power dissipation.
Slower cells ->less current -> High delay -> Lesser area and power consumption.

The netlist for the synthesized mux is given below.
(day18)



## Day 2:
Day 2 starts off by exploring the .lib file. The different components of the file name are explained as follows:
- tt- typical process
- 025C - process temperature
- 1v80 - 1.8V 
        
The parameters P,V and T are important features of any process and PDK. They influence the behaviour of the circuit. The various parameters can be viewed in the .lib file. 

(The .lib file)

Different flavours of the AND gate (AND_2, AND_4 and AND_4) were discussed along with their variations in area, power and delays.

Hierarchical vs Flat synthesis
Synthesizing can take place in two ways - Hierarchical synthesis and flat synthesis. 
Features of Hierarchical synthesis
- The hierarchy is preserved. This property is best illustrated with a module with submodules
- Instead of seeing the gates, we will see a modular level synthesis result.
(Day23)

Features of Flat synthesis
- The hierarchy isn’t preserved
- We see individual gates as the individual modules are split apart, thereby removing the hierarchy.
This is implemented with the flatten command.
(day25)

We can also synthesize a single module from a vast amount of models in a single design. This can help in the following ways:
- We can synthesize a module once and stitch several of them together.
- We can synthesize several smaller modules from a very large module which can be difficult for the synthesizer to synthesize.

(day26)

Flop coding styles
When using just a standard combinational circuit, there will be a lot of propagation delay and there will be glitching. When using a continuous combinational circuit, we will be having a lot of glitching and the circuit may never settle down. For this, we use flops between stages. These can act as a shield against glitching and outputs from one stage propagate to another in a settled down stage.

There are two methods of coding flops
The Asynchronus block
- In an asynchronous block, both reset/set and clock are independent of each other. The always block is activated no matter which changes.
- The output follows the asynchronous set. 
(Simulation)

The synchronous block
- In a synchronous block, reset/set waits for the positive edge of the clock. 
(Diagram)
(Simulation)

For synthesizing flip-flop, we use difflibmap.

Some interesting synthesis.
(should take)


## Day 3
The main focus of day 3 was optimizations. It is the process of simplifying a circuit based on a certain set of conditions. There are optimizations for both combinational and sequential circuits.

Combinational Circuit optimization methods
Constant Propogation: The output is dependent on only one variable.
(Insert image)
Boolean optimization: 
(Write expression and simplify)

Sequential circuit optimization
- Basic optimization techniques
Sequential constant propogation
(Draw diagram and show how Y = 1)

Special case of where a tied D cannot be taken to have a constant propagation.
(Draw diagram, clock and explain).

When set = 0, Q goes to 0 only after a clock cycle. When set is high, Q follows set. So Q is not constant and it changes for a clock cycle. Therefore, we need a flip flop.

- Advanced optimization techniques
- State optimization: State optimization involves the optimization of an unused state. It also has a condensed state matrix.
- Cloning: To reduce the effective distance between two flops to reduce propagation delay, thereby reducing setup time.
- Retiming: Changing operating frequency by attempting to reduce the maximum delay component.
(Enter diagram)


Laboratory simulations of combinational design optimizations.
We use opt_check files, which are combinational circuits which can be optimized. 






 



To do constant propagation, we use opt_clean -purge command. We will check this constant propagation with the file opt_check3. The file is synthesized as follows



The simplified expression for the behavioral code is Y = ABC, which is exactly what is implemented.

Laboratory simulations of sequential circuits. 
We use the dff_const verilog files to make the simulations. For dff_const1.v and dff_const3.v, flip flops are definitely synthesized as there is a change in the output, even if it is only for a single clock cycle. The waveforms below show these simulations.




However, df_const2, is synthesized as a single line with a high output.

Unused output optimizations.
Sometimes, there will be instances when one or more outputs will not be used in a circuit. In such cases, we do not need to synthesize the circuit pertaining to the output. Consider an 8 bit counter where only the first bit is used. Such a circuit will be synthesized with a single flip flop.


Compare this with 3 bit counter circuit where all four outputs are used. All three flip flops are used in this case.



## Day 4

