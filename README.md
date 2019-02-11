# Dijkstras shortest path using MPI

#### Prerequisites
In order to run this program you need to
install **Open MPI** : here are [instructions](https://wiki.helsinki.fi/display/hugg/open+mpi+install+on+mac+os+x) on how to do it on a mac.


#### Program explanation
This is a parallel implementation of Dijkstra's shortest path algorithm for
a weighted directed graph given as an adjaceny matrix. Dijkstras algorithm
finds the shortest path from a source to every other vertex.

![Graph](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/graph.jpg)

Below is an example run of the shortest path from A to every other vertex in the graph above. The blue arrows in the graph is the shortest path from A to F.
In the program A, B, C, D, E and F corresponds to 0, 1, 2, 3, 4 and 5 respectively.

![terminal](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/tty.gif)

The bottom of dist 0->v shows that the computed path to F (5) is of length 20.


With the following conditions:
* n = the number of vertices
* p = the number of processes
* p evenly divides n

The work can be divided among the multiple processes as follows:

* Process 0    :  Vertices *0, 1, ..., (n / p) - 1*
* Process 1    :  Vertices *(n / p), (n / p) + 1, ..., 2 * (n / p) - 1*
*    ....             :                  ......
* Process p - 1:  Vertices *(p - 1) * (n / p), (p - 1) * (n / p) + 1, ..., p * (n / p) - 1 = n - 1*

If we consider the graph in adjacency matrix form where index *[u][v]* holds the distance from *u to v* which is ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) if there is no edge between *u and v*:


u, v | A | B | C | D | E | F
---    | - | - | - | - | - | -
A   | 0 | 4 | 2 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)
B   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 0 | 5 | 10 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)
C   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 0 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 3 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)
D   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 0 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 11
E   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 4 | 0 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)
F   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 0


then if we would like to compute the shortest path from A to F with 3 processes the adjacency matrix gets partitioned as follows:


* Process 0     : Gets column 0 and 1 (col A and col B)
* Process 1     : Gets column 2 and 3 (col C and col D)
* Process 2     : Gets column 4 and 5 (col E and col F)

By doing a column partition of the matrix then each process gets responsible for all incoming edges to their assigned vertices.

Then each process checks for their local minimum distance from the global source vertex to their assigned vertices. Now one of the processes has found a new global minimum distance from the source vertex. This vertex will not be visited again.

All processes update their local distance from the source to this new found global vertex by checking if the distance of the path: from the source to the global vertex to the assigned vertex is shorter than the distance straight to the assigned vertex from the source.

This procedure is repeated n - 1 times and after completion the minimum distance from the source vertex to all the other vertices is now computed.


Example of first iteration:
* Process 0 finds its local minimum distance 4 from 0 to (local) 1 -> (global) 1
* Process 1 finds its local minimum distance 2 from 0 to (local) 0 -> (global)  2
* Process 2 finds its local minimum distance ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) since there are no edge from 0 to any of its assigned vertices (0 to (global) 4) and (0 to (global) 5)


The algorithm determines that process 1 has found the global minimum vertex so it gets marked as visited.

* Now each process checks if the distance to their assigned vertices can be updated.
* Process 2 notices that there is a new shorter path to its assigned (global) vertex 4 (vertex E in the graph above) from the source 0.
* This new distance is calculated by the adding the distance of the path to the global minimum and adding the cost of the path from that global minimum to the assigned vertex 4.
* The distance from the global minimum to the assigned vertex 4 is 3 so the total distance from 0 to 4 is now 2 + 3 = 5.
* The distance gets stored locally at process 2 and is going to be used in later findings of global minimum distances.

Each round marks one vertex which makes the algorithm ensured to terminate.









## ----------------- Reference to image -----------------
By Artyom Kalinin - Own work, CC BY-SA 3.0, https://commons.wikimedia.org/w/index.php?curid=29980338

