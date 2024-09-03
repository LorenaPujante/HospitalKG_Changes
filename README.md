# HospitalKG_Changes
In this repository, we present a slight modification to the knowledge graph (KG) with the domain model used as a basis in [10.1109/JBHI.2024.3417224](https://ieeexplore.ieee.org/document/10568325).

The changes over the KG relate to the spatial dimension used to describe a hospital layout. Specifically, we have changed the edges that link the _Area_ nodes with their coordinates (_Unit_ [row] and _Block_ [column] nodes) over its _Floor_.


## 1. Proposal
The following edges:
- **_placedInUnit_** between an _Area_ and an _Unit_
- **_placedInBlock_** between an _Area_ and a _Block_
- **_placedInFloor_** between a _Block_ or _Unit_ and a _Floor_
- **_nextTo_** between two _Units_ or two _Blocks_

should not have a weight. Therefore, they couldn't be used to calculate the _semantic distance_ between two _Location_ nodes.


## 2. Cause
In the following image, we present a floor layout with 6 _Areas_ organized in 2 rows (_Units_) and 3 columns (_Blocks_). _Units_ are named with letters and _Blocks_ with numbers. Each _Area_ is named with the union of its _Block_ and _Unit_.

<p align="center">
  <img src="https://github.com/user-attachments/assets/2e45d13a-e8c8-4311-b9f9-674cc28e4038" alt="Floor layout with 2 rows and 3 columns">
</p>

In this floor layout, the _Area **0A**_ should only have two neighbouring _Areas_:
- **_1A_**: Same _Unit_ (**A**), consecutive _Block_
- **_0B_**: Consecutive _Unit_, same _Block_ (**0**)

However, if we represent the floor layout as a graph, we can trace several paths that drive to "fake neighbours":
- **0A** -_placedInUnit_ → A -_nextTo_ → B ← _placedIn_- **2B**
- **0A** -_placedInBlock_ → 0 -_nextTo_ → 1 ← _placedIn_- **1B**
- **0A** -_placedInUnit_ → A ← _placedIn_- **2A**  `(We can access to any _Area_ that is linked to the same _Block_/_Unit_ of another one)`

<p align="center">
  <img src="https://github.com/user-attachments/assets/a83608c3-0d70-4ab8-ae66-cce067352edb" alt="Floor layout represented as a graph">
</p>


## 3. Solution
As we can see in the previous section, when a _shortest path_ algorithm is applied to search the path with the lowest weight between two _Location_ nodes from the same _Area_, we might get a path that relates both nodes as "fake neighbours" and, therefore, the result distance between them would be lower than it should be.

Hence, we will add a new relation _nextTo_ between _Area_ nodes to define when two _Area_ nodes are neighbours explicitly. For the _shortest path_ algorithm between _Location_ nodes, we will only use those edges with a _weight_ property, so the edges between _Area_/_Floor_ and _Unit_/_Block_ won't be traversed.

We don't think that _Unit_ and _Block_ nodes have to be turned into attributes of _Area_ nodes. As nodes, it is easier to retrieve all the _Areas_ from the same _Unit_/_Block_, or get the number of columns or rows in which a _Floor_ is organised. 

In the following figure, we represent how these changes are implemented in the kG:

<p align="center">
  <img src="https://github.com/user-attachments/assets/1b58466f-9002-4ddb-8640-a3e45ef46ccb" alt="Changes of the Area section in the KG">
</p>


## 4. Result
In the following figure, we represent the new KG that represents the domain model of a hospital (its layout and activity):
<p align="center">
  <img src="https://github.com/user-attachments/assets/6feb4ac3-559e-48d9-99a6-d3c0cdcebc53" alt="New KG to represent the domain model">
</p>

