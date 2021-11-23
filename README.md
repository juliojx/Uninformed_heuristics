


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
    DinamicWeight=1 #Dynamical Weight Assignment
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
                config[x,y]=1 #The 1's represents obstacles
            else:
                config[x,y]=0 if random()<p else 1
    config[start[0],start[1]]=2 #Initial position
    config[xmember,ymember]= 3#Target
    nextconfig=zeros([n,n])
    G = nx.Graph()
    G.add_node(1,pos=(y1,x1))
    G.node[1]['state'] = 1
    G.node[1]['order']=1
    LastNode=1
    
    #In this subroutine we build the network node by node, this block is used in the other functions. A call of this function will  take the elements in the neighborhood (four elements) to detect obstacles, the border and free spaces, this information will be store in the graph in construction. 
    for i in range(-Ra,2):
        for j in range(-Ra,2):
            if (i==0 and j==1) or (i==0 and j==-1) or (i==1 and j==0) or (i==-1 and j==0):
                if config[x1+i,y1+j]==1:
                    G.add_node(NextNode, pos=(y1+j,x1+i))
                    G.add_edge(LastNode,NextNode)
                    G.edges[NextNode,LastNode]['weight'] = 0
                    G.node[NextNode]['state'] = 0 
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

The "observe" function is nedeed to show graphically how the algorithm evolve the network built by the agent
```
def observe():
	global G,config, nextconfig,x1,y1, position
	cla() #Clear axis in each step
	position=nx.get_node_attributes(G,'pos')
	imshow(config,vmin=0,vmax=3,cmap=None, interpolation='none')
	nx.draw(G,vmin = 0, vmax = 1, cmap = cm.binary,node_shape='o',pos=position,node_size=30 ,node_color = [G.node[i]['state'] for i in G.nodes()],edge_cmap =cm.Reds,edge_color = [G.edges[i,j]['weight'] for i, j in G.edges()]) #We draw the edges with colors, the more red the color, the more weighted is the edge.


```
