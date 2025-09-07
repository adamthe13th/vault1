# Rucksack

![[Rucksack.pdf]]


- Rucksack
- Mengenüberdeckung
- SAT & n-KNF-SAT for n>2
- Clique
- Bin packing: partition < Bin packing (by using 2 bins with capacity exactly half the value of the entire set)
- partition
- Knapsack : Rucksack is about just finding the elements that sum up to the const b => EnstscheidungsProblem
	- Knapsack however, is an optimierungsProblem where all elements have weight and a value and you want to optimize the value of the elements that can fit within a bag (aka their weight is under a certain const b limit)
	- ofc reducing Rucksack < knapsack is very easy where you give all the elements the same value (what about fitting under a certain limit / = const b limit)
- TSP travelling salesman: I think HamiltonKreis < TSP
- HamiltonKreis: 3-KNF < HamiltonKreis (beweis is 30 minutes long so not done in the VL)
# CLIQUE
**Gegeben:** Ungerichteter Graph G = (V, E) und Zahl k ∈ N.
**Problem:** Besitzt G eine „Clique“ der Größe mindestens k?
Dh eine Teilmenge V 0 ⊆ V mit |V 0 | ≥ k
und alle u, v ∈ V 0 mit u 6= v sind benachbart.

# Mengenüberdeckung
**Gegeben:** Teilmengen T1 , . . . , Tn ⊆ M einer endlichen Menge M
und eine Zahl k ≤ n.
**Problem:** Gibt es i1 , . . . , ik ∈ {1, . . . , n} mit M = Ti1 ∪ · · · ∪ Tik ?

#