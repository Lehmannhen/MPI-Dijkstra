# Dijkstras shortest path using MPI

This is a parallel implementation of Dijkstras shortest path algorithm for
a weighted directed graph given as an adjaceny matrix. Dijkstras algorithm
finds the shortest path from a source to every other vertex. With the following conditions:
* n = the number of vertices
* p = the number of processes
* p evenly divides n

The work can be divided among the multiple processors as follows:

* Process 0    :  Vertices *0, 1, ..., (n / p) - 1*
* Process 1    :  Vertices *(n / p), (n / p) + 1, ..., 2 * (n / p) - 1*
*                     :      ...
* Process p - 1:  Vertices *(p - 1) * (n / p), (p - 1) * (n / p) + 1, ..., (p ) * (n / p) - 1 = n - 1*

![Graph](https://raw.githubusercontent.com/Lehmannhen/MPI-Dijkstra/master/images/graph.jpg)






## ----------------- Reference to image -----------------
By Artyom Kalinin - Own work, CC BY-SA 3.0, https://commons.wikimedia.org/w/index.php?curid=29980338

