---
layout: single
title:  "Technical debt as dynamic indicators: VAN and TIR"
date:   2016-06-19 16:19:48 +0100
lang: es
categories: pmp
customjs:
 - http://fred-wang.github.io/mathml.css/mspace.js
---

<script src="http://fred-wang.github.io/mathml.css/mspace.js">
</script>

Last studies show us that the software's average lifetime is around 10 years. Does it mean that software is like a engine of an old car? Is there a time when you cannot fix it more and the best option is to buy a new one?
Well, there are many influential factors but this entry is about the **technical debt** and how you can manage it as a Project Manager.

{% include toc %}

Let's assume that you are a PM in a long time project (4 or 5 years) and obviously, you will need a initial investment and some financing during the project. Investors will ask whether that project is a good investment, what and when will be the ROI. I mean, they could need to know the project's **NVP** and **IRR**.

In software development world, we usually ++estimate the cost of each new feature++, trying to know what will be the cost associated. However, do we actually implement what we estimated before?. No, we don't. Normally new issues arise, the complexity is increased or requirements do not match with the client's needed.

> This entry introduces an indicator to evaluate software refactoring process and **it could be extremely useful when you have to explain to your stakeholders** the cost of avoiding a refactoring. During the planning, those indicators will **help you to prioritize each refactoring** process to get the best performance.

Let's come back to our financial example: each **un-planned** change in the project is like a *request for funding*. It means that we do not have enough cash and we need to use external financial services. As you know, this loan is not free. Any administrator would think to request a loan if the interest rate is unknown. If someone did that, we could think that he/she is incompetent. Well, this situation occurs everyday in software development.

# What is ...  #
In this section I'm going to introduce some concepts. If you are already familiar with those concepts, please move to the next section.

## ... the technical debt? (developer skill) ##

Basically, **technical debt** is everything that allows us to be **flexible ** meanwhile we are implementing changes in our project which we already know that will be *fixed in future*. For instance, this happens when we discover new requirements which affect the software architecture, or when we need to use a *workaround* to fix quickly something.

When we have found new requirements, it is similar to request a loan, and the final technical debt interests will be increased. We usually try to add those new requirements with no modifications into our primary base lines (time, cost, and scope). When our contingency reserve is empty, and our quality base line absorbs the impact, this is also part of the **technical debt**. For instance, if we reduce the amount of test, then we increase the probability of errors.

> New requirements on the scope usually are similar to a new loan request on financial management. Team could be flexible and agile, but at the end we will pay our "debt interests".

When we make a quick *workaround*, we could likely avoid a delay on the deployment but we are also increasing our **technical debt**. That means that we have ++reduced the **time to market**++ but we have left pending a future fix. If these *workaround* is isolated, it won't probably affect us. However components are usually interconnected, and that pending change will increase the development time of new features on the future.

A good approach is grouping related **technical debts** and fix them together (like multiple loan example): It is called **re-factoring**.

## ... NPV and IRR? (PMP skill) ##
First of all, don't worry about the following formulas because your SpreadSheet (Microsoft Excel or LibreOffice) has ++simple functions to manage them++: functions NVP and IRR.

**NPV** is the acronym of **Net Present Value**, and it is *the difference between the present value of cash inflows and the present value of cash outflows*. In PMP, we use that to analyse the profitability of a project investment. As a financial dynamic indicator, the NPV formula is:

<math xmlns="http://www.w3.org/1998/Math/MathML" display="block">
 <semantics>
  <mrow>
   <msub>
    <mi mathvariant="italic">NVP</mi>
    <mi>N</mi>
   </msub>
   <mo stretchy="false">=</mo>
   <mrow>
    <mrow>
     <munderover>
      <mo stretchy="false">∑</mo>
      <mrow>
       <mi>i</mi>
       <mo stretchy="false">=</mo>
       <mn>1</mn>
      </mrow>
      <mi>N</mi>
     </munderover>
     <mrow>
      <mo fence="true" stretchy="false">(</mo>
      <mrow>
       <mfrac>
        <msub>
         <mi>C</mi>
         <mi>t</mi>
        </msub>
        <msup>
         <mrow>
          <mo fence="true" stretchy="false">(</mo>
          <mrow>
           <mrow>
            <mn>1</mn>
            <mo stretchy="false">+</mo>
            <mi>i</mi>
           </mrow>
          </mrow>
          <mo fence="true" stretchy="false">)</mo>
         </mrow>
         <mi>t</mi>
        </msup>
       </mfrac>
      </mrow>
      <mo fence="true" stretchy="false">)</mo>
     </mrow>
    </mrow>
    <mo stretchy="false">−</mo>
    <msub>
     <mi>C</mi>
     <mn>0</mn>
    </msub>
   </mrow>
  </mrow>
  <annotation encoding="StarMath 5.0">NVP_N = sum from{i=1} to{N} ( C_t over (1+i)^t)- C_0</annotation>
 </semantics>
</math>
<!--object data="../_includes/mml/nvp_financial_formula.mml" type="text/xml"></object-->

Where:
 - **C~t~** is the ++net cash flow++, i.e. inflow - outflow, at time *t*.
 - **C~0~** is the net cash flow at the beginning. It means the ++initial investment++.
 - **i** is the ++discount rate++. We could use a fixed rate investment or any other similar risk rate investment.

We could classify the projects using the following table based on NVP result:

| NVP Result| It means | Then |
|:---------:| -------- | ---- |
|    > 0    | Project **adds value** to the company | Project may be **accepted** |
|    < 0    | Project would subtract value from the company | Project may be rejected |
|    = 0    | Project would neither add or subtract value | No information, we should use other criteria |

**IRR** is the **Internal Rate of Return**: a discount rate that *makes the net present value (NPV) of all cash flows from a particular project equal to ++zero++*.

<embed data="../_includes/mml/irr_financial_formula.mml" type="text/xml"/>

However ++we need to transform each financial concept to our **technical debt** templates++( see **Technical Debt Calculation** section).

Using **NVP**, we could evaluate the viability and profitability of a project overtime. Let's see the example below to clarify both concepts.

### NVP and IRR simple example ###

Let's assume that we have three projects, their cash-flow estimations (CF) and a ++discount rate++ equal to **5%** .

| Project | Init Investment (C~0~) | CF 1st year (C~1~) | CF 2nd year (C~2~) | CF 3rd year (C~3~) |
|:-------:|:--------------------:|:-----------------:|:-----------------:|:-----------------:|
| A       | -200                 | 50                | 100               | 200               |
| B       | -300                 | 70                | 70                | 170     	         |
| C       | -400				 | 100               | 200               | 380               |

Calculating NVP and IRR for each project:

| Project | SUM(C~i~)|  NVP   |   IRR   |
|:-------:|:--------:|:------:|:-------:|
| A       | 150      | 111.09  | 26.73%  |
| B       | 10       | -22.99  | 1.42%   |
| C       | 280      | 204.90  | 25.35%  |

#### Brief analysis ####
Using the *NVP* value, we can see that project *B* will subtract value from the company. Projects *A* and *C* are profitable and *C* has bigger NVP than *A*, so we should select *C* project. But sometimes, we are looking for a project with a low initial investment but a good performance. In that case, IRR of *A* is 1.38% bigger than the *B*'s one.
Now that we understand NVP and IRR concepts, we could apply them to our refactoring process in our development world.

# Refactoring: pay your debt #

Refactoring can be classified as:

- **Pro-active Refactoring**. It the best one because is *optional* (at the moment) and it allows us to plan when and how achieve it (or even leave it). Other big advantage is that it gives us information about the *team status*. Whether we have a lot of change requests coming from the team (pro-activity) that means that the team engagement is high. Otherwise, we likely invest some resources to get that engagement. A demotivated team is one of the worst things in a project.

- **Mandatory Refactoring**. We have no alternatives and we have to pay right now to get the new feature. In our financial example, it means that we have been into a court process and we have lost it. In that case, we have lost the flexibility.

# Manage the technical debt #

In the same way that any administrator keeps under control the financial aspect, we also have to manage our *technical debt*. Our suggestion is to *manage each technical debt as a new risk* and link it to a module. As another risk, we have to calculate the probability and the impact (using the relation index of that module). The estimation cost should be provided by the technical team.

After that initial classification, we need to evaluate the over-cost of the technical debt. We can use the **NVP** concept but, instead of time, we will use the amount of **interconnected modules**. It means that a debt affects 5 first components should be more expensive that one only affects just a module.

## Technical debt qualification and quantification##

As risk management, we have got a *Probability and Impact Matrix*. However, the *impact* concept is replaced by the number of related modules that could be affected by this debt. *Probability* should be calculate for each software deployment. It means that if the next deploy has no affected modules, then its probability is less than 0.1. 
This matrix allows us to prioritise the payment of our technical debt along development iterations and get better contingency reserve.

<table>
    <tr>
        <th>Probability<br/>(Iteration)</th> <th colspan="5">Technical Debt Threat</th>
    </tr>
    <tr>
        <td>0.90</td> <td>0.05</td> <td>0.09</td> <td>0.18</td> <td>0.36</td> <td>0.72</td>
    </tr>
    <tr>
        <td>0.70</td> <td>0.04</td> <td>0.07</td> <td>0.14</td> <td>0.28</td> <td>0.56</td>
    </tr>
    <tr>
        <td>0.50</td> <td>0.03</td> <td>0.05</td> <td>0.10</td> <td>0.20</td> <td>0.40</td>
    </tr>
    <tr>
        <td>0.30</td> <td>0.02</td> <td>0.03</td> <td>0.06</td> <td>0.12</td> <td>0.24</td>
    </tr>
    <tr>
        <td>0.10</td> <td>0.01</td> <td>0.01</td> <td>0.02</td> <td>0.04</td> <td>0.08</td>
    </tr>
    <tr>
        <td/> <td>0.05<br/>Very Low</td> <td>0.10<br/>Low</td> <td>0.20<br/>Moderate</td> <td>0.40<br/>High</td> <td>0.80<br/>Very Hight</td>
    </tr>
</table>

An template

<table>
    <tr>
        <td><b>Technical debt ID</b></td> <td><i>rtd-001</i></td> <td><b>Iteration</b></td> <td><i>2</i></td>
    </tr>
    <tr>
        <td colspan="4">
            <table width="100%">
                <tr>
                    <td><b>Probability</b></td> <td>0.50</td> <td><b>Impact</b></td> <td>0.40</td>
                </tr>
                <tr>
                    <td colspan="2"><b>PI index (PxI)</b></td> <td colspan="2">0.20</td>
                </tr>
            </table>
        </td>
    </tr>
    <tr>
        <td colspan="3"><b>Affected Modules Count</b></td> <td>3</td>
    </tr>
    <tr>
        <td colspan="3"><b>Estimation</b></td> <td>45 h</td>
    </tr>
    <tr>
        <td colspan="4">
            <table width="100%">
                <tr><td colspan="2"><b>Description</b></td></tr>
                <tr>
                    <td>2016-9-20</td> <td>Sort description and technical notes</td>
                </tr>
                <tr>
                    <td>2016-10-1</td> <td>More technical detail</td>
                </tr>
            </table>
        </td>
    </tr>
    <tr>
        <td colspan="4">
            <table width="100%">
                <tr><th>Modules</th></tr>
                <tr><td>Mod 1</td></tr>
                <tr><td>Mod 2</td></tr>
                <tr><td>Mod 3</td></tr>
            </table>
        </td>
    </tr>
</table>

## Technical Debt Calculation ##

We only need a little modification in NVP formula to apply to our **technical debt** calculation:
1. Instead of use years or any period time (t), we will use the released feature.
2. The **cash flow** ++is the difference between the cost of doing nothing and the cost of applying the refactor++.

Let's start an example. We have to develop and deploy three new features in our product/project. Team has identified two possible refactors, and modules are affected by each of those refactor. Next step is to generate the feature associated estimations, using different scenarios (no refactor, refactor A and refactor B). The following table represents those estimations:

<table>
	<tr>
    	<th colspan="10">Hours</th>
    </tr>
    <tr>
    	<th></th> <th colspan="3">Feature 1</th> <th colspan="3">Feature 2</th> <th colspan="3">Feature 3</th>
    </tr>
    <tr>
    	<th></th> <th>No Refactor</th> <th>Refactor A</th> <th>Refactor B</th> <th>No Refactor</th> <th>Refactor A</th> <th>Refactor B</th> <th>No Refactor</th> <th>Refactor A</th> <th>Refactor B</th>
    </tr>
    <tr>
    	<td>Refactor</td> <td>0</td> <td>40</td> <td>30</td> <td>0</td> <td>0</td> <td>0</td> <td>0</td> <td>0</td> <td>0</td>
    </tr>
    <tr>
    	<td>Mod 1</td> <td>40</td> <td>10</td> <td>40</td> <td>100</td> <td>50</td> <td>100</td> <td>50</td> <td>0</td> <td>0</td>
    </tr>
    <tr>
    	<td>Mod 2</td> <td>20</td> <td>10</td> <td>20</td> <td>30</td> <td>10</td> <td>30</td> <td>0</td> <td>20</td> <td>50</td>
    </tr>
    <tr>
    	<td>Mod 3</td> <td>80</td> <td>80</td> <td>40</td> <td>0</td> <td>0</td> <td>0</td> <td>40</td> <td>0</td> <td>0</td>
    </tr>
    <tr>
    	<td>Mod 4</td> <td>0</td> <td>0</td> <td>0</td> <td>0</td> <td>0</td> <td>0</td> <td>90</td> <td>40</td> <td>20</td>
    </tr>
    <tr>
    	<td>**Subtotals**</td> <td>**140**</td> <td>**140**</td> <td>**130**</td> <td>**130**</td> <td>**60**</td> <td>**130**</td> <td>**180**</td> <td>**60**</td> <td>**70**</td>
    </tr>
</table>


Let's translate this information to cost:

<table>
	<tr>
    	<th colspan="10">Costs</th>
    </tr>
    <tr>
    	<td colspan="7"/> <td colspan="2">Hour cost</td><td>$50</td>
    </tr>
    <tr>
    	<th></th> <th colspan="3">Feature 1</th> <th colspan="3">Feature 2</th> <th colspan="3">Feature 3</th>
    </tr>
    <tr>
    	<th></th> <th>No Refactor</th> <th>Refactor A</th> <th>Refactor B</th> <th>No Refactor</th> <th>Refactor A</th> <th>Refactor B</th> <th>No Refactor</th> <th>Refactor A</th> <th>Refactor B</th>
    </tr>
    <tr>
    	<td>Refactor</td> <td>$0</td> <td>$2,000</td> <td>$1,500</td> <td>$0</td> <td>$0</td> <td>$0</td> <td>$0</td> <td>$0</td> <td>$0</td>
    </tr>
    <tr>
    	<td>Mod 1</td> <td>$2,000</td> <td>$500</td> <td>$2,000</td> <td>$5,000</td> <td>$2,500</td> <td>$5,000</td> <td>$2,500</td> <td>$0</td> <td>$0</td>
    </tr>
    <tr>
    	<td>Mod 2</td> <td>$1,000</td> <td>$500</td> <td>$1,000</td> <td>$1,500</td> <td>$500</td> <td>$1,500</td> <td>$0</td> <td>$1,000</td> <td>$2,500</td>
    </tr>
    <tr>
    	<td>Mod 3</td> <td>$4,000</td> <td>$4,000</td> <td>$2,000</td> <td>$0</td> <td>$0</td> <td>$0</td> <td>$2,000</td> <td>$0</td> <td>$0</td>
    </tr>
    <tr>
    	<td>Mod 4</td> <td>$0</td> <td>$0</td> <td>$0</td> <td>$0</td> <td>$0</td> <td>$0</td> <td>$4,500</td> <td>$2,000</td> <td>$1,000</td>
    </tr>
    <tr>
    	<td>**Subtotals**</td> <td>$7,000</td> <td>$7,000</td> <td>$6,500</td> <td>$6,500</td> <td>$3,000</td> <td>$6,500</td> <td>$9,000</td> <td>$3,000</td> <td>$3,500</td>
    </tr>
    <tr>
    	<th colspan="2"/> <th>CashFlow A</th> <th>CashFlow B</th> <th/> <th>CashFlow A</th> <th>CashFlow B</th> <th/> <th>CashFlow A</th> <th>CashFlow B</th> <th/>
    </tr>
    <tr>
    	<td colspan="2">CashFlow = Base - Refactor</td> <td>$0</td> <td>**$500**</td> <td/> <td>**$3,500**</td> <td>$0</td> <td/> <td>**$6,500**</td> <td>**$5,500**</td> <td/>
    </tr>
</table>

Now, using this data, we will calculate the **NVP** and **IRR**, using a 5.00% of discount rate.

<table>
	<tr>
    	<th/> <th>Refactor A</th> <th>Refactor B</th>
    </tr>
    <tr>
    	<td>**Initial Cost**</td> <td>$ -2,000</td> <td>$ -1,500</td>
    </tr>
    <tr>
    	<td>Feature 1</td> <td>$0</td> <td>$500</td>
    </tr>
    <tr>
    	<td>Feature 2</td> <td>$3,500</td> <td>$0</td>
    </tr>
    <tr>
    	<td>Feature 3</td> <td>$6,000</td> <td>$5,500</td>
    </tr>
    <tr>
    	<td>**NVP**</td> <td>$6,357.63</td> <td>$3,727.30</td>
    </tr>
    <tr>
    	<td>**IRR**</td> <td>83.89%</td> <td>66.15%</td>
    </tr>
</table>

> Using this method, it is easy to **put some real numbers** on reports and **persuade your stakeholders** to allow a code refactor process. 
> You can also use it to **evaluate several refactors**, and **choose the best one** for your current portfolio.

# Don't do #

Thinking that a little **technical debt** will not affect the project is an error that we must avoid as a PM. We have to keep risks under control using templates or techniques (like this one) but this requires that our project is perfectly split into functional packages, working very close to the architectural team.

> We should **never ignore** the issue behind a refactor requested by developers.

Other option, less flexible but simpler, is to allocate bandwidth in every iteration to solve some refactors. However this only works whether your stakeholders engagement is high.

# Hypes:  Chronicle of a Death Foretold #

In this business, from time to time, new hypes are coming up and they become the "silver bullet" for every technical issue. Maybe you can hear that they solve each of your problems or they are extremely easy to use, or (the best of one) they work well on other companies. Generally, these hypes are really good for specific situations but general environments. So pay attention to the  constraints of the hype.

> Your R&D team could investigate and test those new *hypes*, but you should be careful to limit the investment (time, resources and money).

Some of those last hypes are:
 - **Agile**, please read [Agile dead](https://www.linkedin.com/pulse/agile-dead-matthew-kern) or read about hybrid agile (agile + waterfall).
 - Or **Big Data** where just the [27% of executives describe their Big Data initiatives as  successful](https://www.capgemini-consulting.com/resource-file-access/resource/pdf/cracking_the_data_conundrum-big_data_pov_13-1-15_v2.pdf)).

The big advantage of those hypes is that projects have hardly any **technical debt and they could still be high efficient projects**. Nevertheless, over time the weight of **technical debt** is increasing. I have faced to projects where the cost of a new development from scratch was cheaper than fix the current one.

# Conclusion #
I have been fortunate to work in long-life projects (more than 10 years when I arrived to the team) and they had a huge **technical debt** fed over the years. The big issue is that **technical debt** is hidden to management layers until it has threatening level.
In this sense, it is impossible to get high performance team (Agile team) when they are spending more that 40% of the development time fighting against the **technical debt**. How do you tell your stakeholders that the final product is 40% more expensive or the team velocity is just 60%? And this is the best case because next year will be likely worse. Those things end up with team's motivation, increase the **time to market**, and you could loose competency or whole business.

> Keep risks under control, keep your **technical debt** under control

You could think that the cost of evaluate the **technical debt** on a long-life project is high but let's see from the financial point of view: Does someone dare to buy a house without checking if it is free of charge?
