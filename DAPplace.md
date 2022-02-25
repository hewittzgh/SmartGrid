# README
> The markdown document is a reproduction description of the paper "Data Aggregation Point Placement for
Smart Meters in the Smart Grid"
***
#### Problem Formulation
##### **Variables defintions**
|Symbol|Description|
|----|----|
|$N_{SM}$|Total number of SMs|
|$N_{poles}$|Total number of poles|
|$s$|Index of SMs, $\forall s \in \{1,...,N_{SM}\}$|
|$p$|Index of poles, $\forall p \in \{1,...,N_{P}\}$|
|$i$|Index of starting devices of the data link, $\forall i \in \{1,...,N_{SM}\}$|
|$j$|Index of ending devices of the data link, $\forall j \in \{1,...,N_{P}+N_{SM}\}$. Device $j \in \{1,...,N_{P}\}$ represents a pole, while device $j \in \{N_{P}+1,...,N_{P}+N_{SM}\}$ represents a SM|
|$x_{p}$|$\left\{\begin{matrix} 1, if\ pole\ p\ is\ selected\ to\ place\ a\ DAP \\ 0, otherwise \end{matrix}\right.$|
|$y^{sp}_{ij}$|$\left\{\begin{matrix} 1, if\ devive\ i\ sends\ data\ to\ device\ j\ belonging\ to\ the\ routing\ with\ start\ point\ SM\ s\ and\ end\ point\ pole\ p \\ 0, otherwise \end{matrix}\right.$|
|$z^{sp}$|$\left\{\begin{matrix} 1, if\ pole\ p\ is\ selected\ to\ place\ a\ DAP\ and\ SM\ s\ sends\ data\ to\ DAP\ p \\ 0, otherwise \end{matrix}\right.$|
|$C_{inst}$|Installation cost|
|$C_{trans}$|Transmission cost|
|$C_{dly}$|Delay cost|
|$N^{max}_{SP}$|Maximal number of SMs per DAP|
|$N^{max}_{SS}$|Maximal number of SMs per relay SM|
|$d_{ij}$|Distance between SM i and relay SM j or DAP j|
|$d_{max}$|Maximal transmission range|
###### **Problem Formulation**
* view
![](view_pic/view_pic1.png)
* Objective Function  
The objective function to minimize is the sum of these costs:
<center>$C_{total}=C_{inst}+C_{trans}+C_{dly} \tag{1}$</center>
&nbsp;

  * $C_{inst}=a\sum_{p}x_{p} \tag{2}$
  $a$ is the purchasing and mounting cost for each DAP  
  the total installation cost is proportional to the number of DAPs
    
  * $C_{trans}=b\sum_{s,p,i,j} y^{sp}_{ij}*PL(d_{ij}) \tag{3}$  
  $b=g8M \gamma E_{b} \kappa T_{m}/T_{I}$, the transmission cost for one SM per unit path loss
    * $T_{I}$, the time interval between the transmissions  
    * $T_{m}$, the assumed lifetime of the network  
    * $g$, the energy price  
    * $\gamma$, the ratio of the total power consumption in the model and the power consumed for transmission  
    * $E_{b}, the required received energy per bit$  
    * $\kappa$, the fading margin  
    * $M$, the packet size in bytes  
    * $PL(d_{ij})$, the expected path loss over distance $d_{ij}$  
      * given in real values: $PL(d_{ij})=PL_{d_{0}}*(d_{ij}/d_{0})^\alpha \tag{4}$
      * given in dB: $PL(d_{ij})_{dB}=PL(d_{0})_{dB}+10 \alpha log_{10}(d_{ij}/d_{0}) \tag{5}$
      * $\alpha$: the path-loss exponent
      * subscript $_{dB}$ indicating a value in dB
      * $PL(d_{0})_{dB}$: the mean path loss at the reference distance $d_{0}$, given as $PL(d_{0})_{dB}=20log_{10}(4\pi d_{0} f/c) \tag{6}$
        * $f$: the transmission frequency
        * $c$: the speed of light
  
  * $C_{dly}=c \sum^{N_{SM}}_{s=1} \sum^{N_{P}}_{p=1} \sum^{N_{SM}}_{i=1} \sum^{N_{P}}_{j=1,j\not=p} y^{sp}_{ij} \tag{7}$
    > In order to get a low latency network, minimize the delay related  to 1.the routing operation, 2.data queuing at a wireless node
    * To minimize the delay related to the routing operation:  
      * define an appropriate delay cost
      * the delay cost is a **penalty** on the number of hops of the path that connects the sender SM to its corresponding DAP, which includes connections between SMs and a direct connection from an SM to a DAP
    * To minimize the delay related to data queuing at a wireless node:
      * impose constraints on the maximal number of SMs that can connect to a relay SM
    * $c$:
      * a weight parameter, translate the communication delay into a monetary loss
      * set $c$ 500 or 1000 -> hard constraint -> no connection between two SMs
    * The delay loss caused by the direct connection of SMs to DAPs is not considered
* Constraints
  ![](view_pic/view_pic2.png)
  <center>In Fig, $s \in \{1, 2\}, p \in \{1\}, i \in \{1,2\}, j \in \{1,2,3\}$</center>

  * $z^{sp} \leq x_{p},\forall s,p \tag{8}$
  &nbsp;
  * $\sum^{N_{P}}_{p=1}z^{sp}=1,\forall s \tag{9}$
  &nbsp;
  * $y^{sp}_{ij} \leq z^{sp},\forall s,p,i,j \tag{10}$
  &nbsp;
  * $\sum^{N_{p}+N_{SM}}_{j=1}y^{sp}_{sj}=z^{sp},\forall s,p \tag{11}$
  &nbsp;
  * $\sum^{N_{SM}}_{i=1}y^{sp}_{ip}=z^{sp},\forall s,p \tag{12}$
  &nbsp;
  * $\sum^{N_{P}+N_{SM}}_{j=1}y^{sp}_{ij}=\sum^{N_{SM}}_{j=1}y^{sp}_{j(i+N_{P})},\forall s,\forall p,\forall i,i \not=s \tag{13}$
  &nbsp;
  * $d_{ij}y^{sp}_{ij} \leq d_{max},\forall s,p,i,j \tag{14}$
  &nbsp;
  * $\sum^{N_{SM}}_{s=1}z^{sp} \leq N^{max}_{SP},\forall p \tag{15}$
  &nbsp;
  * $\sum^{N_{SM}}_{s=1}\sum^{N_{P}}_{p=1}\sum^{N_{SM}}_{i=1}y^{sp}_{ij} \leq N^{max}_{SS},\forall j=N_{p}+\ 1,...,N_{SM} \tag{16}$
  &nbsp;
  * $x_{p},y^{sp}_{ij},z^{sp} \in \{0,1\}, \forall s,p,i,j \tag{17}$
  &nbsp;
* Summary
$\underset {x_{p},y^{sp}_{ij},z^{sp}}{min}c_{total}, s.t. (1)-(16) \tag{*}$
***
#### Heuristic approach
###### **1. Clustering Approximation**
$$
\begin{array}{|c|c|c|}
\hline
{Distance\ metrics} & {Define} & {Remarks} \\
\hline
{euclidean\ distance} & {d_{euclidean}(i,j)=d_{ij}} & {distance\ between\ SM\ i\ and\ relay\ SM\ j\ or\ DAP\ j} \\
\hline
{cutoff\ distance} & {
d_{cutoff}(i,j)=
\left\{
\begin{aligned}
d_{ij},if d_{ij} \leq d_{max} \\
M*d_{ij}, otherwise 
\end{aligned}
\right.
} & {clusters\ are\ formed\ such\ that\ it\ directly\ satisfies\ the\ maximal\
transmission\ range\ constraint}\\
\hline
{energy-based distance} & {d_{energy}(i,j)}=b*PL(d_{ij}) & {favor\ clusters\ that\ directly\ minimize\ the\ transmission\ cost\ b*PL(d_{ij})}\\
\hline
{energy-based cutoff distance} & {
d_{cutoff-energy}(i,j)=
\left\{
\begin{aligned}
b*PL(d_{ij}),if d_{ij} \leq d_{max} \\
M*b*PL(d_{ij}),otherwise
\end{aligned}
\right.
} & {a\ combination\ of\ the\ last\ two\ distances \\ favor\ clusters\ minimizing\ the\ transmission\ cost\ while\
including\ SMs\ that\ are\ closer\ than\ d_{max}}\\
\hline
\end{array}
$$
> $M$ is a very large number
###### **2. Pole Assignment**
Suppose that the **cluster centers** lie in the set $c \in C$, and the **candidate pole positions** lie in the set $p \in P$. The cost of assigning $c$ to $p$ is defined as the weight $w_{cp} = d_{cp}$.  
Define a **bipartite graph** $G^{'}=(C,P,E^{'})$, the edges $e^{'}=(c,p) \in E^{'}$ connect every pair $(c,p)$ and have a weight $w_{cp}$.
###### **3. Optimal Routing**
The optimal routing is determined with the goal to minimize the costs $c_{trans}$ and $c_{dly}$.

