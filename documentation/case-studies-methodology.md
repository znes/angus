---
geometry:
- margin=2cm
header-includes:
- \usepackage{mathtools}
- \usepackage{tikz}
- \usetikzlibrary{shapes}
numbersections: true
---

# Introduction

For the determination of plant and storage operation in the ANGUS case studies,
an economic parameter from the fundamental energy system model is used. The model
is based on the Open Energy Modelling Framework (oemof). It models the german electricity 
with its electrical neighbours on an hourly basis. The linear programming model is a 
market simulation model using a perfect foresight approach. 
The economic parameter is derived from the shadow price which is equal to the dual variable associated 
with the electricity balancing constraint of the model. The dual variable / shadow price indicates 
the change in the primal objective when relaxing the primal balancing constraint:

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

For further background on this economic parameter, see also Poster "Price theory and storage operation in electricity systems",
Angus 2 Project Meeting, 12 June 2019 in Leipzig.


# Case Study CAES

The first case study is the assement of a compressed air energy storage (CAES). In contrast to existing CAES applications with caverns as storage, 
poros strucutre are investigated. These structure are vastly available for storing energy. Hence, the potential for these (poros) CAES storages is 
large compared to rather limited cavern potential. To analyse the technical and economic performance of such storage the case study is applied. Results
are used to assess the role to (p)-CAES storage in the context of a german energy transition with pathyways towards 100% Renewable energy supply.

\begin{tikzpicture}
\draw
 (0,0) node [draw](storage){storage}
 (3,0) node [draw, ellipse](bus){electric bus};
\draw[->] (storage) -- (bus);
\draw[->] (bus) -- (storage);
\end{tikzpicture}

For the development of the interface between the fundamental energy system model
and the detailed CAES model of WG Tuschy, an iterative process for the model
formulation was applied.

## First Iteration

 In the first step, a linear formulation for a generic storage
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

\begin{tikzpicture}[line width=2, scale=1.6]
\draw[line width=10] (-.5,-.1) -- (10.5,-.1);
\draw[line width=10] (-.5,4.1) -- (10.5,4.1);
\path (10.8,4.1) node {elbus};
\path (10.8,-.1) node {htbus};
\path (1.5,3) node [draw] (gassrc) {gassrc*};
\path (1.5,2) node [draw, ellipse] (gasbus) {gasbus};
\draw[->] (gassrc) -- (gasbus);
\path (0,2) node [draw] (extrb) {extrb};
\draw[->] (gasbus) -- (extrb);
\draw[->] (extrb) -- (0,4);
\draw[->] (extrb) -- (0,0);
\path (3,2) node [draw] (mchp) {mchp};
\draw[->] (gasbus) -- (mchp);
\draw[->] (mchp) -- (3,4);
\draw[->] (mchp) -- (3,0);
\path (4,2) node [draw] (htpmp) {htpmp};
\draw[->] (4,4) -- (htpmp);
\draw[->] (htpmp) -- (4,0);
\path (5,2) node [draw] (elboil) {elboil};
\draw[->] (5,4) -- (elboil);
\draw[->] (elboil) -- (5,0);
\path (6.5,1) node [draw] (htstor) {htstor};
\path (6.5,2) node [draw,ellipse] (lhtbus) {lhtbus};
\path (8,2) node [draw] (storhtpmp) {storhtpmp};
\draw[->] (6.5,0) -- (htstor);
\draw[->] (htstor) -- (lhtbus);
\draw[->] (lhtbus) -- (storhtpmp);
\draw[->] (8,4) -- (storhtpmp);
\draw[->] (storhtpmp) -- (8,0);
\path (9,1) node [draw] (htsrc) {htsrc};
\draw[->] (htsrc) -- (9,0);
\path (10,1) node [draw] (htsnk) {htsnk};
\draw[->] (10,0) -- (htsnk);
\path (9,3) node [draw] (elsrc) {elsrc*};
\draw[->] (elsrc) -- (9,4);
\path (10,3) node [draw] (elsnk) {elsnk*};
\draw[->] (10,4) -- (elsnk);
\path (-.5,-.5) node [label=right:{* marks slack variables with positive costs for gassrc and
 elsrc and negative costs for elsnk, no other costs}] {};
\end{tikzpicture}


## First iteration

\begin{alignat*}{2}
\text{min}& \ &&\sum_t (c^{\text{cost}}_{\text{gas}} \cdot x_{\text{gassrc,gasbus}}(t)
 + c^{\text{cost}}_{\text{el}} \cdot (x_{\text{elsrc,elbus}}(t) - x_{\text{elbus,elsnk}}(t)))
\\
\text{subject to:}&&&\forall \ t \in T
\\
\end{alignat*}

\begin{alignat*}{3}
& x_{\text{extrb,elbus}}(t)
&& = \quad &&c^{\text{eta,el,noex}}_{\text{extrb}} \cdot x_{\text{gasbus,extrb}}(t)
 -  \frac{c^{\text{eta,el,noex}}_{\text{extrb}}-c^{\text{eta,el,maxex}}_{\text{extrb}}}
 {c^{\text{eta,ht,maxex}}_{\text{extrb}}} \cdot x_{\text{extrb,htbus}}(t)
\\
& x_{\text{extrb,elbus}}(t)
&& \geq &&\frac{c^{\text{eta,el,maxex}}_{\text{extrb}}}{c^{\text{eta,ht,maxex}}_{\text{extrb}}}
 \cdot x_{\text{extrb,htbus}}(t)
\\
&x_{\text{mchp,elbus}}(t)
&& = && (c^{\text{eta,el}}_{\text{mchp}} + c^{\text{eta,ht}}_{\text{mchp}})
 \cdot x_{\text{gasbus,mchp}}(t) - x_{\text{mchp,htbus}}(t)
\\
&x_{\text{mchp,elbus}}(t)
&& = &&\frac{c^{\text{eta,el}}_{\text{mchp}}}{c^{\text{eta,ht}}_{\text{mchp}}}
 \cdot x_{\text{mchp,htbus}}(t)
\\
&x_{\text{htpmp,htbus}}(t)
&& = &&c^{\text{cop}}_{\text{htpmp}} \cdot x_{\text{elbus,htpmp}}(t)
\\
&x_{\text{elboil,htbus}}(t)
&& = &&c^{\text{eta}}_{\text{elboil}} \cdot x_{\text{elbus,elboil}}(t)
\\
&x^{\text{level}}_{\text{htstor}}(t)
&& = && x^{\text{level}}_{\text{htstor}}(t-1)
 + x_{\text{htbus,htstor}}(t)
 - x_{\text{htstor,lhtbus}}(t)
\\
&x_{\text{storhtpmp,htbus}}(t)
&& = && c^{\text{cop}}_{\text{storhtpmp}} \cdot x_{\text{elbus,storhtpmp}}(t)
\\
&x_{\text{storhtpmp,htbus}}(t)
&& = && x_{\text{lhtbus,storhtpmp}}(t) + x_{\text{elbus,storhtpmp}}(t)
\\
&x_{\text{htsrc,htbus}}(t) && \leq && c^{\text{capacity}}_{\text{htsrc}}
\\
&x_{\text{htbus,htsnk}}(t) && = && c^{\text{capacity}}_{\text{htsnk}}(t)
\\
&\sum_\text{input} x_{\text{input(b),b}}(t) && = && \sum_\text{output} x_{\text{b,output(b)}}(t)
 \quad \forall \ b \in B
\\
\end{alignat*}

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
