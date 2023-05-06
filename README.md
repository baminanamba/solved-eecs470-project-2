Download Link: https://assignmentchef.com/product/solved-eecs470-project-2
<br>
<h1>1        Introduction</h1>

We’ve mentioned synthesis several times already in the lab and lecture. Synthesis is an exponentially complex process, and so it can take a very long time on larger projects, like the one you’ll be doing at the end of the semester. To mitigate this, we can synthesize submodules and then include them as black boxes in the final project, which simplifies the synthesis problem. This is particularly useful for synthesizing the caches in your final project, which can reduce the synthesis time by half or more.

We will use a pipelined multiplier as an example of this process. This multiplier is actually the one you’ll be using in your final project, so you should use this as a chance to get very familiar with it.

Finally, we will be using this multiplier as part of a finite state machine design problem, which is intended to give you additional practice writing Verilog in reasonable style. The style we recommend for finite state machine design will be presented in the lab.

<h1>2        Hierarchical Synthesis</h1>

<h2>2.1       Concept</h2>

Synthesis takes a behavioral level design and turns it into a structural level design. Specifically, the synthesis tool replaces larger operations, i.e. multiplication or addition with standard designs for modules, and attempts to build larger logic components out of a set of standard cells (our standard cells are in the lec25dscc25.v file included by your Makefile). Doing this optimally is an NP-hard problem, so it is often impossible in practice. In this class, it is generally merely very time consuming, meaning that your final project will take anywhere between six hours and one day to synthesize.

To mitigate this problem, we can synthesize large submodules in a design individually and then include these syntheses as black boxes in the final design synthesis. Given that the original problem was NP-hard, we know that it would have been exponentially complex, where the quantity of interest is the size of a design, measured in something like the number of logic elements (standard cells, LUTs, transistors, etc.) That means that simplifying to only somewhat fewer elements still shortens the time to find a solution significantly.

<h2>2.2       Pipelined Multiplier</h2>

We have provided you with a pipelined multiplier, found in pipe mult.v, to which we will apply this concept. The multiplier does multiplication in stages, somewhat like you would have learned to carry out multiplication in elementary school. It multiplies the first 8 bits of the multiplier with the whole multiplicand in one clock cycle, then the next 8 bits of the multiplier against a shifted multiplicand, and so on to get 8 partial products. Summing those gives us the desired multiplication. This means that each multiplication will take 8 clock cycles. Each partial product is created by a separate multiplier stage, which can be found in the mult stage.v file.

Your first assignment will be to find a reasonable clock period (within 2ns of the lowest possible) at which the 8 stage multiplier we’ve provided you can be synthesized with slack met. For example, if you find that the design does not synthesize at a clock period of 4 ns, but it does synthesize with a clock period of 5.5 ns, then 5.5 ns must be within 1.5 ns of the shortest possible clock period. We’ve already setup the .tcl scripts to synthesize the two modules in this design for you. Note the set dont touch command in the mult.tcl script. This command tells the synthesis tool to treat the module as a black box and to not optimize it further.

To find the clock period, you will need to change the clock period in the mult stage.tcl file until you reach the best clock period that meets all slack. Once you’ve found that, the total clock period of the multiplier will be that stage clock period plus the additional combinational delay from the interconnects in the higher level mult module. This means that the clock period in the mult stage.tcl file will be different from the one found in the mult.tcl file.

Your second assignment is to modify the pipelined multiplier we’ve provided to work as both a 4 stage multiplier and as a 2 stage multiplier. You can either do this by copying the files and having separate 2, 4 and 8 stage multipliers or by figuring out a combination of preprocessor macros or parameters that set pipeline depth. Whichever method you choose is fine for this project, but having the parameterized pipeline depth will be extremely helpful for the final project. Once you have the other two multipliers, you will need to find the best clock periods that they can each achieve. How do all three clock periods compare? Does this conform to your expectations?

<h1>3        Integer Square Root</h1>

Now, you will need to create a module that uses the multiplier that we supplied, the 8 stage multiplier. You will be writing a module to compute the integer square root of a 64-bit number. It will generate a 32-bit number that is the largest integer that is not larger than the square root of the number provided. For example, the integer square root of 24 is 4. The module declaration is as follows:

<table width="244">

 <tbody>

  <tr>

   <td width="139">module ISR(</td>

   <td width="105"> </td>

  </tr>

  <tr>

   <td width="139">input</td>

   <td width="105">reset,</td>

  </tr>

  <tr>

   <td width="139">input</td>

   <td width="105">[63:0] value,</td>

  </tr>

  <tr>

   <td width="139">input</td>

   <td width="105">clock,</td>

  </tr>

  <tr>

   <td width="139">output logic</td>

   <td width="105">[31:0] result,</td>

  </tr>

  <tr>

   <td width="139">output logic</td>

   <td width="105">done</td>

  </tr>

 </tbody>

</table>

);

It should operate as follows:

<ul>

 <li>If reset is asserted during a rising clock edge (synchronous reset), the value signal is to be stored.</li>

 <li>If reset is asserted part way through a computation, the result of that computation is discarded and a new value is latched into the module.</li>

 <li>When the module has finished computing the answer, the output is placed on the result line and done line is raised on the same cycle.</li>

 <li>It must not take more than 600 clock cycles to compute a result (from the last clock that reset is asserted to the first clock that done is asserted.)</li>

</ul>

We do not suggest that you pipeline this module. You will likely need to perform something like a binary search to find the result a simple algorithm is as follows:

<strong>Algorithm 1 </strong>Integer Square Root

1: <strong>procedure </strong>ISR(value)

2:               <strong>for </strong><em>i </em>← 31 to 0 <strong>do</strong>

3:                         proposed solution[<em>i</em>]←1

4:                           <strong>if </strong>proposed solution<sup>2 </sup><em>&gt; </em>value <strong>then</strong>

5:                                 proposed solution[<em>i</em>]←0

6:                     <strong>end if</strong>

7:             <strong>end for</strong>

8: <strong>end procedure</strong>

Note that loops do not have a direct hardware equivalent. What hardware design technique lets us implement a procedure like this?

In addition to writing this module, you will need to write a testbench for it. This testbench should probably test specific corner cases, random testing and the short loops. Your testbench should print either

@@@Passed or @@@Failed.

Once you have the module written and tested, synthesize it. This will probably take several minutes at least.

<h1>4        Comprehension Questions</h1>

Answer the following questions, and submit the answers in a plaintext file called answers.txt along with the rest of your project.

<ol>

 <li>Consider the multiplier supplied with this project.

  <ul>

   <li>What is the cycle time achieved when you synthesized our multiplier?</li>

   <li>What does this mean the total latency is for a multiplication?</li>

  </ul></li>

 <li>Answer question 1 (parts a and b) for the two multipliers you created.</li>

 <li>Consider the relative values of the answers you found to questions 1 and 2. Do these seem reasonable? Why or why not?</li>

 <li>What is the clock period achievable with the module you wrote in section 3?</li>

 <li>How long would it take for your module to compute the square root of 1001 given the cycle time of question 4? Would you expect a performance gain or penalty if you used your 2 stage multiplier?</li>

</ol>