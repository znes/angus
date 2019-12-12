---
geometry:
- margin=2cm
header-includes:
- \usepackage{mathtools}
numbersections: true
---

# Introduction

For the determination of the plant and storage operation in the case studies,
an economic parameter from the fundamental energy system model is used. The model
is based on the Open Energy Modelling Framework (oemof). The parameter
is derived from the shadow price as the change in the primal objective when
relaxing the primal balancing constraint or as value of the dual variable
associated to the primal balancing constraint:

With $n$ producers producing a total amount of $b^*$, each producing amount $x_i$ with specific cost $c_i$:

\begin{align*}
\text{Primal:}\qquad\\
\text{min} \quad & c_1 x_1 + ... + c_n x_n \\
\text{s.t.} \quad & ...\\
&\begin{aligned}
x_1  + ... + x_n & \geq b^*  \quad \text{(primal balancing constraint)} \\
x_1, ..., x_n & \geq 0\\
\end{aligned}\\
\\
\text{Dual:}\qquad\\
\text{max} \quad & b_1 y_1 + ... + b^* \overbrace{y^*}^{\mathclap{\text{(shadow price)}}} + ... + b_m y_m \\
\text{s.t.} \quad & ...\\
& y_1, ..., y_m \geq  0\\
\end{align*}

See also Poster "Price theory and storage operation in electricity systems",
Angus 2 Project Meeting, 12 June 2019 in Leipzig.


# Case Study CAES

## First Iteration

For the development of the interface from the fundamental energy system model
to the detailed CAES model of WG Tuschy, an iterative process for the model
formulation was used. In the first step, a linear formulation for a generic storage
was used for the simulation of the operation of the CAES.

### Parameter

\begin{tabular}{lc}
Efficiency charge & $c^{eta,in}$ \\
Efficiency discharge & $c^{eta,out}$ \\
Efficiency due to self-discharge & $c^{eta,self}$ \\
Lower bounds of variables & $c^{out,min}, c^{in,min}, c^{level,min}$ \\
Upper bounds of variables & $c^{out,max}, c^{in,max}, c^{level,max}$
\end{tabular}

### Variables

\begin{tabular}{lcl}
Storage Discharge &
$c^{out,min} \leq x^{out}(t) \leq c^{out,max}$ & $\forall \ t \in T$ \\
Storage Charge &
$c^{in,min} \leq x^{in}(t) \leq c^{in,max}$ & $\forall \ t \in T$ \\
Storage Level &
$c^{level,min} \leq x^{level}(t) \leq c^{level,max}$ & $\forall \ t \in T$
\end{tabular}

### Constraints

Storage balance
$$x^{level}(t) = c^{eta,self} \cdot x^{level}(t-1)
- \frac{1}{c^{eta,out}} \cdot x^{out}(t)
+ c^{eta,in} \cdot x^{in}(t) \qquad \forall \ t \in T_{>0}
$$
$$
x^{level}(0) = 0.5 \cdot c^{level,max}
$$

### Objective function

The objective function is maximising the profit of operation:

$$
\text{max} \quad \sum_t c^{price}(t) \cdot x^{out}(t)  - c^{price}(t) \cdot x^{in}(t)
$$

### Results

The simulated operation of the CAES based on the model described above was then
analysed and compared with a more detailed component model of WG Tuschy. Based on
the significant deviations a more suitable model is being formulated by WG Tuschy.


# Case Study HEAT

## First iteration

### Parameters

$c^{marginal\_cost}$ for all flows

### Variables

$x^{flow}$

### Constraints

**Buses**

For the case study four buses are modeled:

1. *Electricity Bus* which represents the electricty grid
2. *Heat Bus* which represents the district heating system (grid)
3. *Heat Bus Low* which is used to model the low temperature heat that comes from the storage
4. *Gas Bus* for the commodity used by fossil fuel based components

With the set of all Buses $B$ all inputs $x^{flow}_{i(b),b}$ to a bus $b$ must equal all its outputs $x^{flow}_{b,o(b)}$

$$\sum_i x^{flow}_{i(b), b}(t) - \sum_o x^{flow}_{b, o(b)}(t) = 0 \qquad \forall \ t \in T, \forall \ b \in B$$


**Heat Load at Heat Bus**

The heat load $x_{l}$ at timestep t equals the exogenously defined  profile value $c^{profile}_l$ multiplied by the annual amount of this load $c^{amount}_l$

$$ x^{flow}_{l}(t) = c^{profile}_{l}(t) \cdot c^{amount}_{l} \qquad \forall \ t \in T$$

**Electricity bus**

The export (selling) of electricity to the grid (electricty bus) is
not constrained.

$$ x^{flow}_{e}(t) \leq \inf \qquad \forall t \in T, \forall e \in E$$

The assigned marginal costs are added with a negative sign, as all produced
electricity represents revenues for the heating system.

### Objective Function

$$ \text{min} \quad \sum_g \sum_t c^{marginal\_cost}_g \cdot x^{flow}_{g}(t) \\
$$

**Note**: In this model the selling of electricity to the market is represented through negativ marginal cost of the excess component.
