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

Last statistics show us that the software's average lifetime is around 10 years. Does it mean that software is like a engine of an old car? Is there a moment when you can not fix it more and the best way is to buy a new one?
Well, there are many elements but this entry is about the **Technical Debt** and how you can manage it as a Project Manager.

{% include toc %}

Let's assume that you are a PM in a long time project (4 or 5 years). Obviously, you will need a initial investment and some financing during the project. Investors will ask whether that project is a good investment, how much and when will be the ROI. I mean, they could need to know the project's **NVP** and **IRR**.

In software development world, we usually ++estimate the cost of each new feature++, trying to know what will be the cost associated. However, do we actually implement what we have estimated before?. No, we don't. Normally new issues arise, the complexity is increased o requirements does not match with the client's needed.

> This entry introduces an indicator to evaluate software refactoring process and **it could be extreamly usefull when you need to explain to your stackeholders** the cost of avoid a refactoring. Internally to the planning team, those indicator will **help you to prioritaze each refactoring** process to get the best performance.

Let's come back to our financial example: each **un-planned** change in the project is like a *request for funding*. It means that we do not have enough cash and we need to use external financial services. As you know, this loan is not free. No administrator would think to request a loan if the interest rate is unknown. If someone would do that we could think that he/she is incompetent. Well, this situation occurs everyday in software development.

# What is ...  #
In this section I'm going to introduce some concepts. If you are already familiar with those concepts, please move the next section.

## ... the technical debt? (developer skill) ##

Basically, **technical debt** is everything that allows us to be **flexible ** meanwhile we are development, to manage changes in our project but we know that we will need to *fix it on the future*. In example, this happens when we discover new requirements which affect the software architecture, or when we need to use a *workaround* to fix something quickly.

When we have found new requirements, it is similar to request a loan, and the final technical debt interests will be increased. We usually try to add those new requirements with no modifications into our base primary lines (time, cost, or scope). If our contingency reserve is empty, and our quality base line absorbs the impact, this is also part of the **technical debt**. For instance, if we reduce the amount of test then we increase the probability of errors, especially expensive on production time.
> New requirements on the scope usually are like a new loan request on finantial management. Team could be flexible and agile, but we end up paying the "debt interests".

When we make a quick *workaround*, we could likely avoid a delay on the deployment but we are also increasing our **technical debt**. That means that we have ++reduced the **time to market**++ but we have left pending a future fix. If these *workaround* is isolated, it won't probably affect us. However components are usually interconnected, and that pending change will increase the development time of new features on the future.

A good approach is to group related **technical debts** and fix them together (like multiple loan example): It is called **re-factoring**.

## ... NPV and IRR? (PMP skill) ##
First of all, don't worry about the following formulas because your SpreadSheet (Microsoft Excel or LibreOffice) has ++simple functions to manage that++: functions NVP and IRR.

**NPV** is the achronim of **Net Present Value**, and it is *the difference between the present value of cash inflows and the present value of cash outflows*. In PMP, we use that to analyze the profitability of a project investment. As a finantial dynamic indicator, the NPV formula is:

<--object data="../_includes/mml/nvp_finantial_formula.mml" type="text/xml"></object-->
> **FORMULA NVP_finantial_formula.mml**

Where:
 - **C~t~** is the ++net cash flow++, i.e. inflow - outflow, at time *t*.
 - **C~0~** is the net cash flow at the begining. It means the ++initial investment++.
 - **i** is the ++discount rate++. We could use a fixed rate investment or any other similar risk rate investment.

We could classify the projects using the following table based on NVP result:

| NVP Result| It means | Then |
|:---------:| -------- | ---- |
|    > 0    | Project **adds value** to the company | Project may be **accepted** |
|    < 0    | Project would substract value from the company | Project may be rejected |
|    = 0    | Project would neither add or substract value | No information, we should use other criteria |

**IRR** is the **Internal Rate of Return**: a discount rate that *makes the net present value (NPV) of all cash flows from a particular project equal to ++zero++*.

<!-- object data="../_includes/mml/irr_finantial_formula.mml" type="text/xml"> ></object-->
> **FORMULA IRR_finantial_formula.mml**

However ++we need to tranform each finantial concept to our **technical debt** templates++( see **Technical Debt Calculation** section).

Using **NVP**, we could evaluated the viability and profitability of a project overtime. Let's see the example below to clarify both concepts.

### NVP and IRR simple example ###

Let's assume that we have three projects, their cashflow estimations (CF) and a ++discount rate++ equal to **5%** .

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

#### Little analysis ####
Using the *NVP* value, we can see that project *B* will substract value from the company. Projects *A* and *C* are profitable and *C* has a greater NVP than *A*, so we should select *C* project. But sometimes we are looking for a project with a low initial investment but a good perfomance. in that case, IRR of *A* is 1.38% grater than the one of *B*.
Now that we undestand NVP and IRR concepts, we could apply them to our refactoring process on our development world.

# Refactoring: pay your debt #

Refactoring can be classified as:

- **Pro-active Refactoring**. It the best one because is *optional* (at the moment) and it allows us to plan when and how achieve it (or still leave it until later). Other big advantage is that it gives us information about the *team status*. Whether we have a lot of change requests coming from the team (pro-activity) that means that the team engagement is high. Otherwise we likely invest some resources to get that engagement. A demotivated team is the worst thing into a project.

- **Mandatory Refactoring**. We have no alternative and we have to pay right now to get the new feature. In our financial example, it means that we have been into a court process and we have lost it. In this case, we have lost the flexibility.

# Manage the technical debt #

In the same way that each administrator keeps under control the financial aspect, we also have to manage our *technical debt*. Our suggestion is to *manage each technical debt as a new risk* and link it to a module. Like other risk, we have to calculate the probability and the impact (using the relation index of that module). The estimation cost should be proportionate by the technical team.

After that initial classification, we need to evaluate the technical debt over-cost. We use the **NVP** concept but instead of time we will use the amount of **interconnected modules**. It means that a debt affects 5 first components should be more expensive that one only affects a module used by just one client.

## Technical debt cualification and cuantification ##

As risk management, we have got a *Probability and Impact Matrix*. However, the *impact* concept is replaced by the number of related modules that could be affected by this debt. *Probability* should be calculate for each software deployment. It means that if the next deploy has no modules affected by the specific debt, then its probability is less than 0.1. 
This matrix allows us to prioritise the payment of our technical debt along development iterations and get better contingency reserve.

<!--div style="overflow-x:auto;"-->
<table class="PIM">
    <tr>
        <th>Probability<br/>(Iteration)</th>
        <th colspan="5">Technical Debt Threat</th>
    </tr>
    <tr>
        <td class="PIM_prob">0.90</td>
        <td class="PIM_val_low">0.05</td>
        <td class="PIM_val_med">0.09</td>
        <td class="PIM_val_hi">0.18</td>
        <td class="PIM_val_hi">0.36</td>
        <td class="PIM_val_hi">0.72</td>
    </tr>
    <tr>
        <td class="PIM_prob">0.70</td>
        <td class="PIM_val_low">0.04</td>
        <td class="PIM_val_med">0.07</td>
        <td class="PIM_val_med">0.14</td>
        <td class="PIM_val_hi">0.28</td>
        <td class="PIM_val_hi">0.56</td>
    </tr>
    <tr>
        <td class="PIM_prob">0.50</td>
        <td class="PIM_val_low">0.03</td>
        <td class="PIM_val_low">0.05</td>
        <td class="PIM_val_med">0.10</td>
        <td class="PIM_val_hi">0.20</td>
        <td class="PIM_val_hi">0.40</td>
    </tr>
    <tr>
        <td class="PIM_prob">0.30</td>
        <td class="PIM_val_low">0.02</td>
        <td class="PIM_val_low">0.03</td>
        <td class="PIM_val_med">0.06</td>
        <td class="PIM_val_med">0.12</td>
        <td class="PIM_val_hi">0.24</td>
    </tr>
    <tr>
        <td class="PIM_prob">0.10</td>
        <td class="PIM_val_low">0.01</td>
        <td class="PIM_val_low">0.01</td>
        <td class="PIM_val_low">0.02</td>
        <td class="PIM_val_low">0.04</td>
        <td class="PIM_val_med">0.08</td>
    </tr>
    <tr>
        <td class="PMI_Imp"/>
        <td class="PMI_Imp">0.05<br/>Very Low</td>
        <td class="PMI_Imp">0.10<br/>Low</td>
        <td class="PMI_Imp">0.20<br/>Moderate</td>
        <td class="PMI_Imp">0.40<br/>High</td>
        <td class="PMI_Imp">0.80<br/>Very Hight</td>
    </tr>
</table>

An template

<table>
    <tr>
        <td><b>Technical debt ID</b></td>
        <td><i>rtd-001</i></td>
        <td><b>Iteration</b></td>
        <td><i>2</i></td>
    </tr>
    <tr>
        <td colspan="4">
            <table class="inner" width="100%">
                <tr>
                    <td><b>Probability</b></td>
                    <td>0.50</td>
                    <td><b>Impact</b></td>
                    <td>0.40</td>
                </tr>
                <tr>
                    <td colspan="2"><b>PI index (PxI)</b></td>
                    <td colspan="2">0.20</td>
                </tr>
            </table>
        </td>
    </tr>
    <tr>
        <td colspan="3"><b>Affected Modules Count</b></td>
        <td>3</td>
    </tr>
    <tr>
        <td colspan="3"><b>Estimation</b></td>
        <td>45 h</td>
    </tr>
    <tr>
        <td colspan="4">
            <table width="100%">
                <tr><td colspan="2"><b>Description</b></td></tr>
                <tr>
                    <td>2016-9-20</td>
                    <td>Sort description and technical notes</td>
                </tr>
                <tr>
                    <td>2016-10-1</td>
                    <td>More technical detail</td>
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

## Technical Debt Caculation ##

We only need little modification into NVP formulta to apply to our **technical debt** calculation:
1. Instead of use years or any period time (t), we will use the number of modules affected by the component that we need to refactorize.
2. 

# Don't do #

There is only one error that we 

Pensar que esa pequeña deuda no afectara es el único error que no podemos permitirnos como PMP. Podemos mantener los riesgos bajo control con las plantillas, pero esto exige que el proyecto esté perfectamente dividio en paquetes funcionales y un trabajo bastante cercano con el equipo arquitectónico de software y, por supuesto, muy alineado con el portfolio de la empresa.
Otra opción, mucho más sencilla, aunque menos flexible, es reservar un ancho de banda entre cada iteración para resolver todos esos problemas o cambios en el diseño. Siempre al principio de la nueva iteración y limitados en el tiempo, para afectar lo mínimo posible la entraga de las nuevas funcionalidades. 

# hypes y su muerte anunciada #

En este negocio, cada cierto tiempo, surgen nuevos hyppes a los que todo el mundo parece venir bien. Esas nuevas tendencias suelen presentarse como una *bala de plata*: resuleven todos los problemas, son muy fáciles de usar y todo el mundo está feliz con los resultados. Algunos de estos últimos hyppes son el **Agile** (ver [Agile está muerto](https://www.linkedin.com/pulse/agile-dead-matthew-kern)) o el omnipresente **Big Data** donde sólo el [27% de los ejecutivos describen sus iniciativas en Big Data como existosas](https://www.capgemini-consulting.com/resource-file-access/resource/pdf/cracking_the_data_conundrum-big_data_pov_13-1-15_v2.pdf)).

La gran ventaja de estos hypes es que los proyectos que los usan no sufren apenas deuda técnica. Sin embargo, a lo largo del tiempo y su evolución, el peso de esa deuda se va incrementando. Existen incluso casos donde se vuelve imposible la continuidad del desarrollo.

# Conclusion #

He tenido la suerte de trabajar en bastantes projectos de larga duración (más de 10 años de desarrollo cuando me incorporé a ellos), y por supuesto con una gran deuda técnica heredada. El gran problema es que esa deuda no es visible a la capa de gestión hasta que no llega a niveles muy alarmantes. Es imposible pretender tener equipos de alto rendimiento(equipos Agile) cuando estos gastan más del 40% de su tiempo de desarrollo en lidiar con esa deuda. ¿Cómo le dices a tus stackeholders que el producto es un 40% más caro o que la velocidad del equipo es de sólo el 60%? y además, si no actuamos ya, el año que viene puede que se incrmente hasta un 42%. Esto acaba con la desmotivación de los equipos, el incremento del **time to market**, y como resultado la perdida de competencia y de negocio.

Es comprensible pensar que en un proyecto que lleva años de desarrollo y evolución, el coste de evaluar esa deuda técnica, puede ser notable. Pero veámoslo desde el punto de vista financiero, ¿alguien se atrevería a comprar una casa y empezar a hacer reformas sin comprobar previamente si tiene cargas previas? Muchas veces, portar la idea del mundo abstracto del software a un ejemplo más habitual, puede ayudar a hacer comprender a los stackeholders de la necesidad de este proceso.
