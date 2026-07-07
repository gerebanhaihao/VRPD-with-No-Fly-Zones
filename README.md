# VRPD-with-No-Fly-Zones
Solving the Vehicle Routing Problem with Drones (VRP-D) under no-fly zone constraints using a hybrid Tabu Search-Simulated Annealing (TS-SA) algorithm.

## Model Design

### Model Paradigm

- Multi-truck multi-drone collaborative system
- All vehicles depart from and return to the distribution center
- One drone per truck
- A drone serves only one customer per sortie
- A drone can only be recovered by the truck that launched it


### Decision Variables

| Variable | Description |
|----------|-------------|
| $x_{ij}^k$ | =1 if truck $k$ travels directly from node $i$ to node $j$; 0 otherwise |
| $x_{iwj}^{u_k}$ | =1 if drone $u_k$ leaves truck $k$ at node $i$, serves customer $w$, and returns to truck $k$ at node $j$; 0 otherwise |
| $y_{ij}^k$ | =1 if node $i$ is visited before node $j$ by truck $k$; 0 otherwise |
| $z_{ih}^{u_k}$ | =1 if drone $u_k$ arrives at or departs from node $i$ during the active period of no-fly zone $h$; 0 otherwise |
| $z_{ijh}^{u_k}$ | =1 if drone $u_k$ traverses from node $i$ to node $j$ through the active no-fly zone $h$; 0 otherwise |

### Objective Function

Minimize total system cost, including truck travel cost, drone flight cost, truck fixed cost, drone fixed cost, truck waiting cost, and drone waiting cost.

### Constraints

- Truck and drone maximum travel distance
- Truck and drone load capacity
- Flow balance constraints
- Temporal synchronization constraints


## No-Fly Zone Design

- **Dynamic** – time-varying activation windows
- **Irregular** – arbitrary convex or concave polygons


## Algorithm Design

### Two-Stage Solution Framework

#### Outer Layer: Truck Route Optimization

- Generate initial feasible truck routes using sequential insertion method
- Six neighborhood operators: Insert, Swap, 2-opt, Relocate, Exchange, and Cross
- Adaptive operator selection based on historical performance
- Tabu search with tabu list to avoid cycling
- Metropolis criterion to accept inferior solutions with a certain probability
- Multiple termination criteria to balance solution quality and computational efficiency

#### Inner Layer: Drone Task Assignment

- Given a truck route, formulate drone assignment as a shortest path problem on a weighted directed graph
- Solve optimally using Dijkstra's algorithm
- No-fly zone conflict detection using ray casting and orientation methods
- A* algorithm for detour path planning around dynamic irregular no-fly zones


## Node Sets

### Data Structure

| Column | Description |
|--------|-------------|
| x | X-coordinate |
| y | Y-coordinate |
| ID | Node identifier |
| Demand | Customer demand |

### Instance Sizes

| Set | Nodes |
|-----|-------|
| Set1 | 10 |
| Set2 | 25 |
| Set3 | 100 |
| Set4 | 250 |


## Parameter Settings


| Parameter | Value |
|-----------|-------|
| Max Iterations | 2000 |
| Tabu Length | 15 |
| No-Improvement Threshold | 200 |
| Initial Temperature | 2000 |
| Cooling Rate | 0.98 |
| Weight Update Period | 20 iterations |
| Candidate solution size | 3 for Set1 & Set2, 10 for Set3, and 20 for Set4 |
| Learning Rate | 0.3 |
| Weight Lower Bound | 0.1 |
| Weight Upper Bound | 3.0 |



## Algorithm Comparison

### vs Gurobi (10 independent runs for TS-SA)

- On all Set1 instances, TS-SA achieves exactly the same optimal solutions as Gurobi, with the average solution identical to the best solution; Solution speed is approximately 86.3% faster than Gurobi
- On all Set2 instances, the average deviation between TS-SA's best solution and Gurobi's optimal solution is only 0.82%, with the deviation between the average solution and the best solution within 15 yuan; Solution speed is approximately 174 times faster than Gurobi

### vs ALNS (10 independent runs respectively)

- On all Set3 instances, TS-SA reduces the average best solution by approximately 6.3% compared to ALNS, with computation time shortened by approximately 62.4%
- On all Set4 instances, TS-SA reduces the average best solution by approximately 14.8% compared to ALNS, with computation time shortened by approximately 86.5%
