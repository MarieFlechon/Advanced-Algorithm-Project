# Advanced Algorithm Project
Algorithmic resolution of a VRP and TSP problem in Python

# <h1><center>Context</center></h1>
The transportation of goods is a major contributor to global pollution, posing a significant environmental challenge as consumers increasingly purchase goods from distant locations, leading to reduced local consumption. This pollution affects countries worldwide, prompting them to establish long-term goals and commitments for reducing carbon emissions.

To promote the implementation of solutions and experiments for both people and goods mobility, the French Environment and Energy Management Agency (ADEME) has issued a call for general interest. Our organization, CesiCDP, specializes in intelligent multimodal mobility and is actively engaged in responding to this call. Our focus for this study is on leveraging new optimization technologies for goods transportation, offering both economic and ecological benefits. The complexity of logistics in transportation poses significant challenges for the future, with numerous potential applications and a profound impact on the environment.

The primary objective of this study is to develop an optimal delivery route by employing an algorithm implemented in a programming language. The solution will identify various delivery points in a predefined order, while adhering to specific constraints and optimizing the total duration of daily deliveries.

# <h1><center>Problems</center></h1>
## <h2><center>Vehicle Routing Problem (VRP)</center></h2>

In the Vehicle Routing Problem (VRP), the objective is to find optimal routes for multiple vehicles visiting a set of locations. When there is only one vehicle, it reduces to the Traveling Salesman Problem (TSP).

But what do we mean by "optimal routes" for a VRP? It refers to the route with the smallest total distance, considering that a truck can only visit each location once. However, if there are no other constraints, the optimal solution is to assign a single vehicle to visit all locations and find the shortest route for that vehicle. Essentially, this is the same problem as the TSP.

In our case, we have an additional constraint, which is the capacity of the trucks. A second objective is then added, which is the minimization of the number of trucks required to deliver all the packages.

## <h2><center>Complexity</center></h2>
### <h3><center>What is a Complexity Class ?</center></h3>
The complexity of an algorithm describes the order of magnitude of its execution speed. Here, we are not talking about the actual execution time, but only about an order of magnitude that allows us to compare the efficiency of different algorithms.

A complexity class is a set of problems that share similar complexities. By determining the complexity class of our problem, we can target the most suitable solution methods for it.

### <h3><center>Known Problems</center></h3>
We will use several known problems to determine the complexity class of our problem through polynomial reductions. The principle of polynomial reduction is to start from a known problem and transform it through one or more steps to another problem. Thus, the reduced problem is in the same complexity class as the original problem.

If problem A reduces to problem B, then B is at least as difficult as A.

**Here are the known problems that we will use in our case:**

* **<u>Hamiltonian Cycle</u>**

   A Hamiltonian cycle is a closed loop in a graph where each node (vertex) is visited exactly once. From this statement, we can deduce a decision problem: has each vertex of the graph been visited exactly once?

   This problem belongs to the NP-complete complexity class.

* **<u>Traveling Salesman Problem (TSP)</u>**

   In this problem, a traveling salesman must visit several cities, passing through each city exactly once, and minimizing the total distance traveled. Formally, a TSP is modeled as a graph where the vertices represent the cities to be visited and the edges represent the connections between these cities. The weight or cost associated with each edge represents the cost of the connection between the two cities, usually corresponding to the distance between them. The objective is to find a Hamiltonian cycle, i.e., a cycle that passes through all vertices of the graph exactly once, with minimum length.

   The difference between this problem and the Hamiltonian cycle is that the edges are weighted. Therefore, an optimization problem is added to the decision problem: is the sum of the traveled edges less than k? (where k is an optimization objective)

   As an optimization problem, the TSP is NP-hard. However, as a decision problem, it is in the NP-complete complexity class.

* **<u>Capacitated Vehicle Routing Problem (CVRP)</u>**

   In its most basic form, known as Capacitated VRP (CVRP), a fleet of vehicles with fixed capacity, based at a depot, must serve routes between several cities, each with a certain demand for goods. The set of customers visited by a vehicle defines its route. Each customer must be served exactly once, and each route starts and ends at the depot. The objective of the CVRP is to minimize the total cost, i.e., the sum of distances or travel times of the routes, while respecting the vehicle capacity constraint:

   Decision problem -> Has the route been served while respecting the truck capacity constraint?

   Optimization problem -> This is the same as the previous problem (TSP).

## <h2><center>Mathematical Modeling</center></h2>


CesiCDP has made the strategic decision to focus its research on the optimization of delivery route management. The primary algorithmic challenge involves determining the most efficient route on a complex road network, connecting a carefully selected set of cities and ultimately returning to the initial starting point. The overarching objectives of this study encompass the following key aspects: ensuring the seamless return to the starting location, minimizing the overall duration of the entire route, taking into meticulous consideration the dynamic traffic conditions prevailing on each individual road segment, effectively accommodating diverse time intervals, meticulously visiting each city exactly once while avoiding unnecessary revisits, making precise departures from each city once without any redundant backtracking, effectively leveraging the availability of multiple trucks to simultaneously execute the deliveries, skillfully assigning objects (alongside their respective delivery points) to the available trucks with the primary aim of minimizing the return date of the very last truck to the base instead of focusing solely on the cumulative time consumed, intelligently managing the capacity constraints  applicable to the trucks, meticulously accounting for the varying sizes of the objects to be transported, and lastly, tactfully restricting certain objects to be exclusively delivered by specific trucks designated for this purpose.

<u>Input Data</u>
* $n$ the number of cities
* $N$ the set of cities, with $N = \{1,2,...,n\}$
* $k$ the number of trucks
* $K$ the set of assigned trucks, with $K = \{1,2,...,k\}$
* $V$ the set of nodes, i.e., $\{0\} \cup N$ (where $\{0\}$ represents the depot)
* $a$ an edge
* $A$ the set of edges, with $A = \{(i,j) \in V^2 : i \ne j\}$
* $d$ the demands of a city
* $t_{ij}$ is the travel time between cities $i \in V$ and $j \in A$
* $C$ the capacity (3D) of the trucks in $m^2$
* $l$ the set of package volume categories
* $L$ a dictionary associating volume categories with the number of packages to be delivered, with $S = \{ l_{p}$ : number of packages 1, ...$ \} \forall p \in l$

<u>Decision Variables</u>
* $x_{ij}^k$ = $\left\{\begin{array}{ll} 
        1  & \text{if route } a_{ij} \text{ is taken by truck } k \\
        0 & \text{otherwise} \\
    \end{array}
\right.
$ : indicates if truck $k$ travels through route $a_{ij}$


<u>Objective Functions</u>

1. First, we aim to minimize the number of trucks required to deliver all packages: $min(k)$
2. Second, we want to minimize the sum of distances traveled by all vehicles: $min(\sum_{ij\in A} t_{ij}x_{ij}^k)$

<u>Constraints</u>

* Each truck starts from the depot: $\sum_{j \in V}x_{oj}^k = 1, \forall k \in K$
* Each truck returns to the depot: $\sum_{i \in V}x_{io}^k = 1, \forall k \in K$
* Only one visit per city: $\sum_{k \in K} \sum_{i \in V, i \ne j} x_{ij}^k = 1, \forall j \in V /\ \{0\}$
* The sum of all the demands of each city visited (regarding the packages) must not exceed the capacity of the truck : $ \sum_{i \in l}  \sum_{j \in l} x_{ij}^k d_{j} \le C_{k}, \forall k \in K$

# <h1><center>Conclusion</center></h1>

In conclusion, the task at hand has revealed the intricate nature of the problem, leading us to construct a model of the linear problem. The insights gained from our model enable us to make well-informed decisions. Furthermore, we have observed that the computation of paths in path networks introduces complex combinatorial optimization problems that are embedded within NP-hard problems, indicating their significant computational complexity.

Addressing this problem offers multiple avenues for resolution. Heuristics present a valuable approach, employing predefined rules and methods to generate initial solutions and optimize local search strategies. In the context of the CVRP, heuristics encompass fundamental design searches and development heuristics, integrating strategies that yield favorable outcomes through customer reconfiguration or visit plan adjustments.

Alternatively, metaheuristics provide powerful global search techniques that delve deep into the solution space. Techniques such as tabu search, simulated annealing, genetic algorithms, and ant colony optimization offer novel approaches to identify potentially effective solutions when dealing with intricate CVRPs.

Leveraging the potential of both heuristics and metaheuristics, one can swiftly obtain reasonable optimal solutions for real-world instances of the CVRP. While these methods do not guarantee optimal solutions, they provide efficient and effective means to navigate the complexities of the problem, making substantial progress towards finding practical solutions within reasonable time frames.

Employing heuristics and metaheuristics as valuable tools and techniques for solving CVRPs empowers organizations to optimize their distribution strategies, reduce costs, and enhance overall system efficiency. By leveraging these approaches, organizations can make significant strides in improving mobility and achieving higher operational effectiveness.
