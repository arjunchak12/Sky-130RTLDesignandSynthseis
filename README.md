# Sky-130RTLDesignandSynthseis


![workshop_banner](https://user-images.githubusercontent.com/86126674/166294305-9ffb2a8e-cb59-430b-a108-7a0d075d5509.png)

## Table of contents
1. [Day 1] (https://github.com/arjunchak12/Sky-130RTLDesignandSynthseis#day-1)
2. [Day 2] (https://github.com/arjunchak12/Sky-130RTLDesignandSynthseis#day-2)
3. [Day 3] (https://github.com/arjunchak12/Sky-130RTLDesignandSynthseis#day-3)
4. [Day 4] (https://github.com/arjunchak12/Sky-130RTLDesignandSynthseis#day-4)
5. [Day 5] (https://github.com/arjunchak12/Sky-130RTLDesignandSynthseis#day-5)

## Day 1:
Day 1 started off with an introduction to iverilog, specifically the Verilog testbench. The testbench is a separate module that is used to feed inputs while monitoring output changes through time.

![WhatsApp Image 2022-05-02 at 9 40 08 PM](https://user-images.githubusercontent.com/86126674/166268400-3c9fc8c5-0689-4547-9713-1ca97d886a90.jpeg)


It is important to know that the testbench doe not have any primary inputs or outputs. We then looked at the simulator, which is GTKWave. An important feature of the simulator is that it checks for output changes whenever there is a change in input.

We then looked at the entire verilog design workflow

![WhatsApp Image 2022-05-02 at 9 40 21 PM](https://user-images.githubusercontent.com/86126674/166268427-e2b7fc5c-4a62-445a-bfab-844eebc34132.jpeg)



Then the lab environment and the required files were setup by cloning the github repository sky130RTLDesignAndSynthesisWorkshop. This contained all the behavioral files of several building blocks of digital electronics as well as well as the standard cell Verilog models.



We then focused on GTKWave, the simulator which is used for the duration of this course. The basic commands used to simulate a .vcd file (VCD stands for value change dump) are given as follows

![rtlworkshopday13](https://user-images.githubusercontent.com/86126674/166251013-83ff1635-a9dc-4e93-9d83-3cf1178d42cd.PNG)




A simulated waveform looks as follows

![rtlworkshopday14](https://user-images.githubusercontent.com/86126674/166251114-8de897eb-7b71-4c45-9286-f9e8ebcd0157.PNG)



### The Synthesizer:
Another important tool pertaining to the entire workflow is the synthesizer. Whatever is coded in Verilog is the RTL design. However, this has to be synthesized or converted into physical blocks. This conversion of an RTL into Netlist is done through synthesis and this is the job of a synthesizer. 

The synthesizing workflow looks as follows
(synthesizer workflow diagram)

The RTL code looks as follows, written in iverilog.

![rtlworkshopday15](https://user-images.githubusercontent.com/86126674/166251195-02f2b726-90c9-47a6-b9b4-c7f3baff53c1.PNG)


The synthesized module looks as follows:

![rtlworkshopday16](https://user-images.githubusercontent.com/86126674/166251233-6c627b21-eca4-463d-b9c9-69f869735a46.PNG)




### The .lib files
- The .lib files are a collection of logical modules based on the sky130 PDK
- It includes a collection of basic as well as complex gates.
- There are different flavors for each of these modules Eg: 2,3 and 4 input, slower gates, medium gates and faster gates, etc.

The need for gates of different flavours is discussed from a timing perspective. Take the example of a two flip flop combinational circuit, like the one given below.

![WhatsApp Image 2022-05-02 at 9 41 10 PM](https://user-images.githubusercontent.com/86126674/166268564-391f5a06-0ebe-4c11-be4d-a2ecbeacf6e3.jpeg)



We define two terms here, namely setup and hold time. The setup time is defined as the minimum time for which the input must be held stable before a rising/ falling edge. 

Tclock > Tcq_a + Tcomb + Tsetup_b

The faster our cells are, the lower our Tclock can be and hence we can drive the circuit at a higher frequency. Similarly, hold time can be defined as the minimum amount of time after the clock’s active edge where the data should be stable. The hold time expression is given as

Thold_b < Tcq_a + Tcompi

Hence, we need faster cells to take care of setup time issues and hold time to accommodate for the fold time issues. However, faster and slower cells come with their own drawbacks

Faster cells -> more current -> Low delay -> More area and power dissipation.
Slower cells ->less current -> High delay -> Lesser area and power consumption.

The netlist for the synthesized mux is given below.

![rtlworkshopday18](https://user-images.githubusercontent.com/86126674/166251310-4b8fba2d-d6ff-45db-9c47-eea28f1034af.PNG)





## Day 2:
Day 2 starts off by exploring the .lib file. The different components of the file name are explained as follows:
- tt- typical process
- 025C - process temperature
- 1v80 - 1.8V 
        
The parameters P,V and T are important features of any process and PDK. They influence the behaviour of the circuit. The various parameters can be viewed in the .lib file. 



![day1pic](https://user-images.githubusercontent.com/86126674/166279632-ee87bd07-1bc8-4b1a-8659-d9417ea45f57.PNG)

Different flavours of the AND gate (AND_2, AND_4 and AND_4) were discussed along with their variations in area, power and delays.

### Hierarchical vs Flat synthesis
Synthesizing can take place in two ways - Hierarchical synthesis and flat synthesis. 
Features of Hierarchical synthesis
- The hierarchy is preserved. This property is best illustrated with a module with submodules
- Instead of seeing the gates, we will see a modular level synthesis result.

![day2-3rd](https://user-images.githubusercontent.com/86126674/166251525-2b680a3a-e898-43ad-bd19-c77085c8ebe3.PNG)



Features of Flat synthesis
- The hierarchy isn’t preserved
- We see individual gates as the individual modules are split apart, thereby removing the hierarchy.
This is implemented with the flatten command.

![day2-5](https://user-images.githubusercontent.com/86126674/166251560-3be944df-8673-47f3-83fd-41a6ad9c3286.PNG)



We can also synthesize a single module from a vast amount of models in a single design. This can help in the following ways:
- We can synthesize a module once and stitch several of them together.
- We can synthesize several smaller modules from a very large module which can be difficult for the synthesizer to synthesize.

![day2_6](https://user-images.githubusercontent.com/86126674/166251597-14762f37-0150-4a67-9406-59e492e5ffc7.PNG)


### Flop coding styles
When using just a standard combinational circuit, there will be a lot of propagation delay and there will be glitching. When using a continuous combinational circuit, we will be having a lot of glitching and the circuit may never settle down. For this, we use flops between stages. These can act as a shield against glitching and outputs from one stage propagate to another in a settled down stage.

There are two methods of coding flops
The Asynchronus block
- In an asynchronous block, both reset/set and clock are independent of each other. The always block is activated no matter which changes.
- The output follows the asynchronous set. 

![day2_8](https://user-images.githubusercontent.com/86126674/166251884-fea5d911-9d70-4efd-870d-be78e4411fc3.PNG)



The synchronous block
- In a synchronous block, reset/set waits for the positive edge of the clock. 


![day2-9](https://user-images.githubusercontent.com/86126674/166251931-0c23fdfa-454b-4edf-a8bc-4c1763315705.PNG)



For synthesizing flip-flop, we use difflibmap. The final synthesized flip flop looks as follows

![day2 10](https://user-images.githubusercontent.com/86126674/166252071-ab4f47da-18bb-4d47-8245-c0eb18ce97f6.PNG)

![WhatsApp Image 2022-05-02 at 10 05 09 PM](https://user-images.githubusercontent.com/86126674/166282186-5d2b0e00-0eba-42de-890e-072ad105bb6d.jpeg)

### Some interesting synthesis.
We use a x2 multiplier and and x8 multiplier and see how we do not exactly need a multiplier circuit to synthesize these. We can look at the truth table of a x2 multiplier and see that the output is the same as that of an input but with an appended zero.

![WhatsApp Image 2022-05-02 at 10 13 32 PM](https://user-images.githubusercontent.com/86126674/166290626-352c7e18-a119-4c75-ac99-2b83d9c5bcf6.jpeg)


![someshittt](https://user-images.githubusercontent.com/86126674/166290657-3c6f8a7d-b250-41d9-b8b4-2d5e1767d0b1.PNG)


## Day 3
The main focus of day 3 was optimizations. It is the process of simplifying a circuit based on a certain set of conditions. There are optimizations for both combinational and sequential circuits.

### Combinational Circuit optimization methods
Constant Propogation: The output is dependent on only one variable.
(Insert image)
Boolean optimization: 
(Write expression and simplify)

### Sequential circuit optimization
- Basic optimization techniques
Sequential constant propogation

![WhatsApp Image 2022-05-02 at 10 29 29 PM](https://user-images.githubusercontent.com/86126674/166291629-235fb762-e7a4-47c1-9260-cd081de9aae5.jpeg)




Special case of where a tied D cannot be taken to have a constant propagation.

![WhatsApp Image 2022-05-02 at 10 29 02 PM](https://user-images.githubusercontent.com/86126674/166291902-aeddcd77-07b2-4b39-bc42-22300f7ee650.jpeg)


When set = 0, Q goes to 0 only after a clock cycle. When set is high, Q follows set. So Q is not constant and it changes for a clock cycle. Therefore, we need a flip flop.

- Advanced optimization techniques
- State optimization: State optimization involves the optimization of an unused state. It also has a condensed state matrix.
- Cloning: To reduce the effective distance between two flops to reduce propagation delay, thereby reducing setup time.
- Retiming: Changing operating frequency by attempting to reduce the maximum delay component.



### Laboratory simulations of combinational design optimizations.
We use opt_check files, which are combinational circuits which can be optimized. 

![day3-1](https://user-images.githubusercontent.com/86126674/166252436-ede213b5-881e-4c2a-9c95-690270bfaeee.PNG)



To do constant propagation, we use opt_clean -purge command. We will check this constant propagation with the file opt_check3. The file is synthesized as follows

![day3-3](https://user-images.githubusercontent.com/86126674/166266513-7ee977f6-4b8f-413e-8057-6a019374f604.PNG)



The simplified expression for the behavioral code is Y = ABC, which is exactly what is implemented.

### Laboratory simulations of sequential circuits. 
We use the dff_const verilog files to make the simulations. For dff_const1.v and dff_const3.v, flip flops are definitely synthesized as there is a change in the output, even if it is only for a single clock cycle. The waveforms below show these simulations.

![day3-4](https://user-images.githubusercontent.com/86126674/166266588-3b53363b-e335-494b-83ce-9fa921df2930.PNG)

![day3-6](https://user-images.githubusercontent.com/86126674/166266771-68eb4d7e-1935-4d1f-913c-765c6c0c2c3c.PNG)



However, df_const2, is synthesized as a single line with a high output.

![day3-5](https://user-images.githubusercontent.com/86126674/166266743-eb43cadb-72cd-4ca0-be09-17358b340865.PNG)





### Unused output optimizations.
Sometimes, there will be instances when one or more outputs will not be used in a circuit. In such cases, we do not need to synthesize the circuit pertaining to the output. Consider an 8 bit counter where only the first bit is used. Such a circuit will be synthesized with a single flip flop.

![day3-7](https://user-images.githubusercontent.com/86126674/166253213-a0cfd2d8-ac45-4a68-83c2-dac9aa361edb.PNG)


Compare this with 3 bit counter circuit where all four outputs are used. All three flip flops are used in this case.


![day3-8](https://user-images.githubusercontent.com/86126674/166253285-8e9aab9b-9964-4cbb-803c-9e680cdfe953.PNG)



## Day 4
Day 4 mainly focused on gate level simulation or GLS. In this concept, the synthesized netlist is fed inputs from the testbench and the output changes are monitored. The netlist can seamlessly fit with the testbench. 
Reasons to use GLS
- It is used to verify the logical correctness of the circuit after synthesis.
- It ensures timing constraints are met.
- Gate level verilog models are required to tell iverilog what each of the cells in the syntheis means. These models can be both functional and timing-aware. 

### Synthesis simulation mismatch.
This is the condition which occurs due to improper coding styles. These can be detrimental as our synthesis will not match what we did during simulation.
- Missing Sensitivity list: The always block is evaluated whenever one of it's parameter changes. If one or more inputs in the always block which are bound to change is not specified in the always block, then there are errors which can happen in the simulation and there will be a mismatch when synthesizing.


### Blocking and non blocking assignments. 
The two most important statement varieties in verilog are blocking an dnon bloacking statements.
- In blocking statements, the order of writing the statements is really important. Differing orders will lead to different simulations and synthesis. The statements in a blocking statement are executed one after the other and is usually used only in combinational circuits.
- In non-blocking statements, the execution takes place simultaneously within an always block. The order of statements do not matter and are predominantly used in sequential circuits. 

### Mismatch example in blocking assignments

![WhatsApp Image 2022-05-02 at 10 35 20 PM](https://user-images.githubusercontent.com/86126674/166292321-7e96317a-abe8-4001-918c-3cdcc99dee9f.jpeg)


Here, statement 1 is evaluated first, followed by statement 2. The old value of q0 is used for the present value of y instead of the present value of q0. This is undesirable in any combinational circuit and causes simulation synthesis mismatch.

### Mismatch simulations.
We start off by using the file ternary_operator_mux.v. This is a very simple way of coding a mux.

![1](https://user-images.githubusercontent.com/86126674/166213559-08d2cb01-2b5a-4b9c-b6b9-51c2611bcb95.PNG)

We then synthesize this MUX using yosys. 

![2](https://user-images.githubusercontent.com/86126674/166213887-6f0ff01e-1244-4cf2-8b3c-18aa0461fc74.PNG)


We now use the synthesized netlist with the testbench. To do this, we will need a few extram files. These are
- Primitives
- The netlist file.
- The actual verilog file.
The synthesized GLS output looks like this.

![3](https://user-images.githubusercontent.com/86126674/166213921-f798c3b4-c1e1-4d7a-9fec-70ca57626793.PNG)


As we can see, this is the example of a good_mux. Now, we take the example of a badly written mux, called the bad_mux.v. This mux is a classic case of a missing sensitivty list, which is given in an example above. When we simulate this, we get a simulation in which the output does not follow input i0 or i1, and holds on to the value present in i0 or i1 when select is low or high respectively. It basically acts as a latch.

![4](https://user-images.githubusercontent.com/86126674/166213995-2fb81446-e56e-49ca-91b5-f21d08683d0c.PNG)



But, when we synthesize it perform GLS, it acts like a correct MUX. This is a classic case of synthesis mismatch.

![5](https://user-images.githubusercontent.com/86126674/166214029-28c5bbf4-b011-4737-9fe5-02f31b4832d6.PNG)


### Blocking Caveats
The same example discussed for blocking and non-blocking assignments is used from above. 

![8](https://user-images.githubusercontent.com/86126674/166214074-106564db-359f-4955-97dc-955c7e624201.PNG)


When simulated, the circuit looks like a flip flop. Since the prvious value of D is used for the present value of y.

![6](https://user-images.githubusercontent.com/86126674/166214132-a5a69464-addf-4695-b9f9-1fd64664c34f.PNG)

But when synthesized and GLS waveform is observed, no latches are synthesized and proper waveforms are observed.

![7](https://user-images.githubusercontent.com/86126674/166214185-62108191-e272-40dc-b982-16e3ec81a5a0.PNG)

Hence, blocking statements have to be used with care. 

## Day 5
During the last day of the workshop, we dealt with if and case statements as well as for and generate statements. Several bad coding styles of these constructs and their repurcussions in synthesis were also studied

### If constructs
The if else-if else set are priority statements. They yield an array of muxes and their syntax will be as follows.

![WhatsApp Image 2022-05-02 at 10 37 41 PM](https://user-images.githubusercontent.com/86126674/166292650-c2083af6-08b7-4789-be98-0efe2dd50b70.jpeg)



There is an inherent danger when it comes to if statements called the infered latch. When if statements are incomplete, the cases which are not dealt with produce a latch to hold on to the previous values. However, such infered latches are not always bad, and are an integral part of counter design.

![WhatsApp Image 2022-05-02 at 10 39 35 PM](https://user-images.githubusercontent.com/86126674/166292909-057c0f3a-7901-4417-9728-9fe29713b2eb.jpeg)



### Case Cosnstructs.
The case statement does not hold any priority. Infact, it synthesizes a single mux when coded properly. However, when not all cases are dealt with, we again get an infered latch

To deal with this problem, we use a default statement to deal with all other cases. There is another case caveat called partial assignements. In such assignments, the default case will not be able to stop the generation of an infered latch. This arises when all the variables are not dealt with in all cases.

Another caveat is the case of an overlapping case. When two cases in a switch case are essentially the same, but evaluate different outputs, the simulator and the synthesizer get confused. This is another caveat that has to be taken care of.

### If construct labs

We see the effect of an incomplete if statement with two modules namely incomp_if.v and incomp_if2.v. These modules infer a latch during synthesis. The yosys simulation and synthesis for both the incomplete_if blocks are given below.

![day5-1](https://user-images.githubusercontent.com/86126674/166254548-1caff0ff-4630-489e-8c5f-eddfc4ff8df7.PNG)

![day5-2](https://user-images.githubusercontent.com/86126674/166254561-e093e35b-a761-419f-8bcd-9718a0fd1edc.PNG)

![day5-3](https://user-images.githubusercontent.com/86126674/166254594-2ddbd708-b0c2-4b5c-a856-f2102252331d.PNG)

![day5-4](https://user-images.githubusercontent.com/86126674/166254616-81a6236d-5e4a-4036-9970-b90c236f99ac.PNG)

![day5-5](https://user-images.githubusercontent.com/86126674/166254646-62600fef-e7ce-4747-ba87-45178cc4d79a.PNG)

### Case construct labs

We see the effect of an improper case statement. The output does not follow any of the inputs completely, which can be seen from the simulation.

![day5-6](https://user-images.githubusercontent.com/86126674/166262501-73c441a8-deb9-4cbf-8802-c9e54b701eeb.PNG)

A latch is also infered and this can be seen from the yosys synthesis.

![day5-7](https://user-images.githubusercontent.com/86126674/166263695-1cac47b3-a94e-4d0c-bd4b-4c2028821a94.PNG)

This is compared with the simulation of a good_case  where all cases are covered.

![day5-8](https://user-images.githubusercontent.com/86126674/166263937-a71a801a-18cb-4401-82dd-f8c336dd01be.PNG)


### Loop and generate statements
There are two main looping constructs in verilog. These are the for loop and the generate for. The for loop is used for evaluating expressions while the generate loop is used to obtain multiple instances of a particular module. Examples of the two are given below


![WhatsApp Image 2022-05-02 at 10 42 43 PM](https://user-images.githubusercontent.com/86126674/166293381-7870e899-e3c9-4329-aee4-fec719775ec3.jpeg)



![WhatsApp Image 2022-05-02 at 10 43 15 PM](https://user-images.githubusercontent.com/86126674/166293443-f9d0334b-5817-4f80-b475-61c2c9fa5082.jpeg)


It should be noted that the generate block is always used outside an always block.

### Lab instances for for and generate.

The for and generate statements are extremely useful when you have to synthesize larger blocks of hardware. The usage of for is illustrated with the production of a MUX and a DEMUX. The simulation for the two has been given below.

![day5-11](https://user-images.githubusercontent.com/86126674/166264739-488d8a13-4d23-498a-a57a-6ce6b5ecf7ba.PNG)

![day5-12](https://user-images.githubusercontent.com/86126674/166264778-e716631e-4f88-453c-950c-257ae0c1edfb.PNG)

The use of generate statement is shown with the generation of a ripple carry adder. It's simulation is shown below.


![day5-13](https://user-images.githubusercontent.com/86126674/166265161-0968996c-dfb2-48d1-8b9f-4be4cbabaa7b.PNG)

It's synthesis is shown below,


![day5-14](https://user-images.githubusercontent.com/86126674/166265226-de26ab0e-d490-4f7d-a5cd-048edb06750c.PNG)

## Conclusion
The workshop was extremely informative and rich in practical experience. It helped me gain first hand experience not only in from a subjective point of view, but also in terms of dcumentation, working with deadlines etc. My documentations have a lot of room for improvement and I will strive to do so in the future workshops. Huge thanks to the VSD team for putting together such a wonderful cousre.




