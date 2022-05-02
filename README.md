# Sky-130RTLDesignandSynthseis
## Day 1:
Day 1 started off with an introduction to iverilog, specifically the Verilog testbench. The testbench is a separate module that is used to feed inputs while monitoring output changes through time.

(Stimulus Generator diagram)

It is important to know that the testbench doe not have any primary inputs or outputs. We then looked at the simulator, which is GTKWave. An important feature of the simulator is that it checks for output changes whenever there is a change in input.

We then looked at the entire verilog design workflow

(Workflow diagram)

Then the lab environment and the required files were setup by cloning the github repository sky130RTLDesignAndSynthesisWorkshop. This contained all the behavioral files of several building blocks of digital electronics as well as well as the standard cell Verilog models.



We then focused on GTKWave, the simulator which is used for the duration of this course. The basic commands used to simulate a .vcd file (VCD stands for value change dump) are given as follows

![rtlworkshopday13](https://user-images.githubusercontent.com/86126674/166251013-83ff1635-a9dc-4e93-9d83-3cf1178d42cd.PNG)


(day13)


A simulated waveform looks as follows

![rtlworkshopday14](https://user-images.githubusercontent.com/86126674/166251114-8de897eb-7b71-4c45-9286-f9e8ebcd0157.PNG)

(day14)

### The Synthesizer:
Another important tool pertaining to the entire workflow is the synthesizer. Whatever is coded in Verilog is the RTL design. However, this has to be synthesized or converted into physical blocks. This conversion of an RTL into Netlist is done through synthesis and this is the job of a synthesizer. 

The synthesizing workflow looks as follows
(synthesizer workflow diagram)

The RTL code looks as follows, written in iverilog.

![rtlworkshopday15](https://user-images.githubusercontent.com/86126674/166251195-02f2b726-90c9-47a6-b9b4-c7f3baff53c1.PNG)

(day15)
The synthesized module looks as follows:

![rtlworkshopday16](https://user-images.githubusercontent.com/86126674/166251233-6c627b21-eca4-463d-b9c9-69f869735a46.PNG)

(day16)

Hence, the final workflow for the entire design will look as follows
(our own image)

### The .lib files
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

![rtlworkshopday18](https://user-images.githubusercontent.com/86126674/166251310-4b8fba2d-d6ff-45db-9c47-eea28f1034af.PNG)

(day18)



## Day 2:
Day 2 starts off by exploring the .lib file. The different components of the file name are explained as follows:
- tt- typical process
- 025C - process temperature
- 1v80 - 1.8V 
        
The parameters P,V and T are important features of any process and PDK. They influence the behaviour of the circuit. The various parameters can be viewed in the .lib file. 

(The .lib file)

Different flavours of the AND gate (AND_2, AND_4 and AND_4) were discussed along with their variations in area, power and delays.

### Hierarchical vs Flat synthesis
Synthesizing can take place in two ways - Hierarchical synthesis and flat synthesis. 
Features of Hierarchical synthesis
- The hierarchy is preserved. This property is best illustrated with a module with submodules
- Instead of seeing the gates, we will see a modular level synthesis result.

![day2-3rd](https://user-images.githubusercontent.com/86126674/166251525-2b680a3a-e898-43ad-bd19-c77085c8ebe3.PNG)

(Day23)

Features of Flat synthesis
- The hierarchy isn’t preserved
- We see individual gates as the individual modules are split apart, thereby removing the hierarchy.
This is implemented with the flatten command.

![day2-5](https://user-images.githubusercontent.com/86126674/166251560-3be944df-8673-47f3-83fd-41a6ad9c3286.PNG)

(day25)

We can also synthesize a single module from a vast amount of models in a single design. This can help in the following ways:
- We can synthesize a module once and stitch several of them together.
- We can synthesize several smaller modules from a very large module which can be difficult for the synthesizer to synthesize.

![day2_6](https://user-images.githubusercontent.com/86126674/166251597-14762f37-0150-4a67-9406-59e492e5ffc7.PNG)

(day26)

### Flop coding styles
When using just a standard combinational circuit, there will be a lot of propagation delay and there will be glitching. When using a continuous combinational circuit, we will be having a lot of glitching and the circuit may never settle down. For this, we use flops between stages. These can act as a shield against glitching and outputs from one stage propagate to another in a settled down stage.

There are two methods of coding flops
The Asynchronus block
- In an asynchronous block, both reset/set and clock are independent of each other. The always block is activated no matter which changes.
- The output follows the asynchronous set. 

![day2_8](https://user-images.githubusercontent.com/86126674/166251884-fea5d911-9d70-4efd-870d-be78e4411fc3.PNG)

(Simulation)

The synchronous block
- In a synchronous block, reset/set waits for the positive edge of the clock. 
(Diagram)

![day2-9](https://user-images.githubusercontent.com/86126674/166251931-0c23fdfa-454b-4edf-a8bc-4c1763315705.PNG)

(Simulation)

For synthesizing flip-flop, we use difflibmap. The final synthesized flip flop looks as follows

![day2 10](https://user-images.githubusercontent.com/86126674/166252071-ab4f47da-18bb-4d47-8245-c0eb18ce97f6.PNG)

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

![day3-1](https://user-images.githubusercontent.com/86126674/166252436-ede213b5-881e-4c2a-9c95-690270bfaeee.PNG)

![day3-2](https://user-images.githubusercontent.com/86126674/166252532-a5cca6f1-bbda-45a8-8f5e-4ab700801684.PNG)

![day3-3](https://user-images.githubusercontent.com/86126674/166252623-317c840d-2166-4987-ae41-f93d65d8900d.PNG)


To do constant propagation, we use opt_clean -purge command. We will check this constant propagation with the file opt_check3. The file is synthesized as follows





The simplified expression for the behavioral code is Y = ABC, which is exactly what is implemented.

Laboratory simulations of sequential circuits. 
We use the dff_const verilog files to make the simulations. For dff_const1.v and dff_const3.v, flip flops are definitely synthesized as there is a change in the output, even if it is only for a single clock cycle. The waveforms below show these simulations.




However, df_const2, is synthesized as a single line with a high output.

Unused output optimizations.
Sometimes, there will be instances when one or more outputs will not be used in a circuit. In such cases, we do not need to synthesize the circuit pertaining to the output. Consider an 8 bit counter where only the first bit is used. Such a circuit will be synthesized with a single flip flop.


Compare this with 3 bit counter circuit where all four outputs are used. All three flip flops are used in this case.



## Day 4
Day 4 mainly focused on gate level simulation or GLS. In this concept, the synthesized netlist is fed inputs from the testbench and the output changes are monitored. The netlist can seamlessly fit with the testbench. 
Reasons to use GLS
- It is used to verify the logical correctness of the circuit after synthesis.
- It ensures timing constraints are met.
- Gate level verilog models are required to tell iverilog what each of the cells in the syntheis means. These models can be both functional and timing-aware. 

### Synthesis simulation mismatch.
This is the condition which occurs due to improper coding styles. These can be detrimental as our synthesis will not match what we did during simulation.
- Missing Sensitivity list: The always block is evaluated whenever one of it's parameter changes. If one or more inputs in the always block which are bound to change is not specified in the always block, then there are errors which can happen in the simulation and there will be a mismatch when synthesizing
(Example of a MUX)

### Blocking and non blocking assignments. 
The two most important statement varieties in verilog are blocking an dnon bloacking statements.
- In blocking statements, the order of writing the statements is really important. Differing orders will lead to different simulations and synthesis. The statements in a blocking statement are executed one after the other and is usually used only in combinational circuits.
- In non-blocking statements, the execution takes place simultaneously within an always block. The order of statements do not matter and are predominantly used in sequential circuits. 

### Mismatch example in blocking assignments
(The blocking image).

Here, statement 1 is evaluated first, followed by statement 2. The old value of q0 is used for the present value of y instead of the present value of q0. This is undesirable in any combinational circuit and causes simulation synthesis mismatch.

### Mismatch simulations.
We start off by using the file ternary_operator_mux.v. This is a very simple way of coding a mux.

![1](https://user-images.githubusercontent.com/86126674/166213559-08d2cb01-2b5a-4b9c-b6b9-51c2611bcb95.PNG)

We then synthesize this MUX using yosys. 

![2](https://user-images.githubusercontent.com/86126674/166213887-6f0ff01e-1244-4cf2-8b3c-18aa0461fc74.PNG)

(day4_2).
We now use the synthesized netlist with the testbench. To do this, we will need a few extram files. These are
- Primitives
- The netlist file.
- The actual verilog file.
The synthesized GLS output looks like this.

![3](https://user-images.githubusercontent.com/86126674/166213921-f798c3b4-c1e1-4d7a-9fec-70ca57626793.PNG)

(day4_3).

As we can see, this is the example of a good_mux. Now, we take the example of a badly written mux, called the bad_mux.v. This mux is a classic case of a missing sensitivty list, which is given in an example above. When we simulate this, we get a simulation in which the output does not follow input i0 or i1, and holds on to the value present in i0 or i1 when select is low or high respectively. It basically acts as a latch.

![4](https://user-images.githubusercontent.com/86126674/166213995-2fb81446-e56e-49ca-91b5-f21d08683d0c.PNG)

(day4_4).

But, when we synthesize it perform GLS, it acts like a correct MUX. This is a classic case of synthesis mismatch.

![5](https://user-images.githubusercontent.com/86126674/166214029-28c5bbf4-b011-4737-9fe5-02f31b4832d6.PNG)

(day4_5)

### Blocking Caveats
The same example discussed for blocking and non-blocking assignments is used from above. 

![8](https://user-images.githubusercontent.com/86126674/166214074-106564db-359f-4955-97dc-955c7e624201.PNG)

(day4_8)
When simulated, the circuit looks like a flip flop. Since the prvious value of D is used for the present value of y.

![6](https://user-images.githubusercontent.com/86126674/166214132-a5a69464-addf-4695-b9f9-1fd64664c34f.PNG)

(day4_6)
But when synthesized and GLS waveform is observed, no latches are synthesized and proper waveforms are observed.

![7](https://user-images.githubusercontent.com/86126674/166214185-62108191-e272-40dc-b982-16e3ec81a5a0.PNG)

(day4_7).
Hence, blocking statements have to be used with care. 

## Day 5
During the last day of the workshop, we dealt with if and case statements as well as for and generate statements. Several bad coding styles of these constructs and their repurcussions in synthesis were also studied

### If constructs
The if else-if else set are priority statements. They yield an array of muxes and their syntax will be as follows.
(Syntax picture)
There is an inherent danger when it comes to if statements called the infered latch. When if statements are incomplete, the cases which are not dealt with produce a latch to hold on to the previous values. However, such infered latches are not always bad, and are an integral part of counter design
(counter picture)

### Case Cosnstructs.
The case statement does not hold any priority. Infact, it synthesizes a single mux when coded properly. However, when not all cases are dealt with, we again get an infered latch
(Case caveat 1)
To deal with this problem, we use a default statement to deal with all other cases. There is another case caveat called partial assignements. In such assignments, the default case will not be able to stop the generation of an infered latch. This arises when all the variables are not dealt with in all cases.
(case caveat2)
Another caveat is the case of an overlapping case. When two cases in a switch case are essentially the same, but evaluate different outputs, the simulator and the synthesizer get confused. This is another caveat that has to be taken care of.

### If construct labs

### Case construct labs

### Loop and generate statements
There are two main looping constructs in verilog. These are the for loop and the generate for. The for loop is used for evaluating expressions while the generate loop is used to obtain multiple instances of a particular module. Examples of the two are given below
(for loop)
(generate loop)

It should be noted that the generate block is always used outside an always block.

### Lab instances for for and generate.



