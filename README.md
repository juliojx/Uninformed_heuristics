


# Uninformed_heuristics
A heuristic for uninformed search on a grid, project developed for a National Congress of Physics.

___

# Table of Contents
1. [Theory](#Theory)
2. [Implementation on Python](#Implementation-on-python)



## Theory
A model of cellular Automata consists of identical cells ordered uniformly over a grid in a discrete space D-dimensional. Each cell is a dynamical variable, an its temporal change is given by a transition function, the next figure ilustrates how works this cellular automata:

![Alt text](images/Celullar1.png?raw=true "Circuit of the prototype, includes power, control and sensor stages")

The type of neighborhood used in this project is a von Neumann neighborhood, imitating the restriction of a robot which only moves in four different directions

![Alt text](images/Celullar2.png?raw=true "Circuit of the prototype, includes power, control and sensor stages")

And the boundary is of fixed type, where the border cells are obstacles that delimit the searching environment

![Alt text](images/Celullar3.png?raw=true "Circuit of the prototype, includes power, control and sensor stages")


As the mobile agent does not have any information about the environment and just knows how to detect the goal and obstacles, it can build a map reflecting the topology of the environment to steer the searching towards undiscovered areas.

![Alt text](images/Celullar4.png?raw=true "Circuit of the prototype, includes power, control and sensor stages")


## Implementation on Python

The project is supported in the Pycxsimulator project developed by Chun Won, networkx and matplotlib libraries.
