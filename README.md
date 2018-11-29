# Dijkstras shortest path using MPI

This is a parallel implementation of Dijkstras shortest path algorithm for
a weighted directed graph given as an adjaceny matrix. Dijkstras algorithm
finds the shortest path from a source to every other vertex. With the following conditions:
* n = the number of vertices
* p = the number of processes
* p evenly divides n

The work can be divided among the multiple processes as follows:

* Process 0    :  Vertices *0, 1, ..., (n / p) - 1*
* Process 1    :  Vertices *(n / p), (n / p) + 1, ..., 2 * (n / p) - 1*
*    ....             :                  ......
* Process p - 1:  Vertices *(p - 1) * (n / p), (p - 1) * (n / p) + 1, ..., p * (n / p) - 1 = n - 1*

If we consider the graph below and we would like to comput the shortest path from A to F
So when the program is run with p = 3 (3 processes), the vertices get distributed as follows:

* Process 0     : Vertices A, B
* Process 1     : Vertices C, D
* Process 2     : Vertices E, F

Then each process checks for their local minimum distance from their local source vertex
to all vertices assigned to that process:

* Process 0 has local source A and will check the distance to B which is 4
* Process 1 has local source C and will check the distance to D which is ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)
* Process 2 has local source E and will check the distance to F which is ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)

The adjacency matrix *mat [u][v]* contains the cost of the edge from *u* to *v*, if there is no
edge between the vertices the cost is set to ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg).

So in the example the matrix mat looks like :

u, v | A | B | C | D | E | F
---    | - | - | - | - | - | -
A   | 0 | 4 | 2 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)
B   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 0 | 5 | 10 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)
C   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 0 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 3 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)
D   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 0 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 11
E   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 4 | 0 | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg)
F   | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | ![inf](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/inf.jpg) | 0

## More to come..

![Graph](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/graph.jpg)






## ----------------- Reference to image -----------------
By Artyom Kalinin - Own work, CC BY-SA 3.0, https://commons.wikimedia.org/w/index.php?curid=29980338

