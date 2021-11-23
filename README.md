


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

The project is supported in the Pycxsimulator project, networkx and matplotlib libraries. 

To do the iteration over time we define three functions called "initialize", "observe" and "update"; so, at the end we only need to import pycxsimulator and run the realtime simulation GUI developed by Chun Won with this three functions as inputs.

We first initialize the system, with a random environment, initial cell and goal cells included.
```
def initialize():
    global G,config,WeightVector,AccessNodes, nextconfig,x1,y1, position, CurrentNode,NextNode, DinamicWeight,LastNode,Stop, NextRandomNode, Vecinos, PositionCheck,OccupiedPosition, OccupiedPositionNoEdges,OccupiedPositionNoEdgesNodes
    global Path
    config=zeros([n,n])
    CurrentNode=1
    LastNode=1
    Path=[]
    NextRandomNode=[]
    PositionCheck=[]
    OccupiedPosition=[]
    BloqueoSegundo=0
    OccupiedPositionNoEdges=[]
    OccupiedPositionNoEdgesNodes=[]
    WeightVector=[]
    AccessNodes=[]
    DinamicWeight=1 #El peso de los enlaces que cambiara conforme avabza la busqueda
    NextNode=2
    Stop=0
    goal=np.random.randint(n,size=2)
    xmember=goal[0]
    ymember=goal[1]
    start=np.random.randint(n,size=2)
    x1=start[0]
    y1=start[1]
    for x in xrange(n):
        for y in xrange(n):
            if x==0 or y==0 or x==n-1 or y==n-1:
                config[x,y]=1 #Los 1 representan paredes y obstaculos
            else:
                config[x,y]=0 if random()<p else 1
    config[start[0],start[1]]=2 #Esta es mi posicion inicial, debo construir un nodo por aqui
    config[xmember,ymember]= 3#Objetivo a encontrar
    nextconfig=zeros([n,n])
    G = nx.Graph()
    G.add_node(1,pos=(y1,x1))
    G.node[1]['state'] = 1
    G.node[1]['order']=1
    LastNode=1
	#for j in G.nodes(): #Recorremos todos los nodos de la red
	#							if (G.node[j]['pos'][0]==y1 and G.node[j]['pos'][1]==x1):
	#								LastNode=j

    for i in range(-Ra,2):
        for j in range(-Ra,2):
            if (i==0 and j==1) or (i==0 and j==-1) or (i==1 and j==0) or (i==-1 and j==0):
                if config[x1+i,y1+j]==1:
                    G.add_node(NextNode, pos=(y1+j,x1+i))
                    G.add_edge(LastNode,NextNode)
                    G.edges[NextNode,LastNode]['weight'] = 0
                    G.node[NextNode]['state'] = 0 #Nodo inaccesible es igual a 0
                    G.node[NextNode]['goal']=0
                    G.node[NextNode]['order']=NextNode
                    CurrentNode=CurrentNode+1
                    NextNode=NextNode+1
                if config[x1+i,y1+j]==0:
                    G.add_node(NextNode, pos=(y1+j,x1+i))
                    G.add_edge(LastNode,NextNode)
                    G.edges[NextNode,LastNode]['weight'] = 1#Era 1
                    G.node[NextNode]['state'] = 1 #Nodo accesible es igual a 1
                    G.node[NextNode]['goal']=0
                    G.node[NextNode]['order']=NextNode
                    CurrentNode=CurrentNode+1
                    NextNode=NextNode+1
```
![Alt text](images/Celullar5.png?raw=true "Circuit of the prototype, includes power, control and sensor stages")

