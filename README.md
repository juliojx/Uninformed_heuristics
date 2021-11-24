


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

The "update" function is required to updating the movement of the agent, including to compute which node is the next according to a probability dependent on the weights of neighbors. To resume, here is the step where de discrimination of discovered areas is performed.

```
def update():
    global G,WeightVector,config, nextconfig,x1,y1,xrand,yrand, position,CurrentNode,NextNode,LastNode,AccessNodes,DinamicWeight, Stop, NextRandomNode, PositionCheck,OccupiedPosition, OccupiedPositionNoEdges,OccupiedPositionNoEdgesNodes
    global Path
    if Stop==0:
        j=0
        i=0
        #Here we get the weight vector of the current free cell, defined as a one dimensional array that contains the weights of neighbors.
        for k in G.edges(LastNode):
            if G.get_edge_data(k[0],k[1])["weight"]!=0:
                WeightVector.append(tuple([G.get_edge_data(k[0],k[1])["weight"],k[1]]))
                i=i+1
		
        #We sort the weighted vector (using the predefined sorting function of Python) 
        WeightVectorSorted=(sorted(WeightVector, key=lambda tup: (tup[0],tup[1])))
        print(WeightVectorSorted)
        #print(len(WeightVectorSorted))
	
	#We looking for the reachable nodes from the current node
        while j!=len(WeightVectorSorted) and WeightVectorSorted[j][0]==WeightVectorSorted[0][0]:
            AccessNodes.append(tuple([WeightVectorSorted[j][0],WeightVectorSorted[j][1]]))
            j=j+1
        #print(AccessNodes)

        RandomIndex=np.random.randint(0,len(AccessNodes))#We choose the next node randomly (between the reachable nodes)
        NextRandomNode=AccessNodes[RandomIndex]
        print("Incrementamos en 1 el enlace entre", NextRandomNode[1],"y", LastNode)
        G.edges[NextRandomNode[1],LastNode]['weight']=G.edges[NextRandomNode[1],LastNode]['weight']+1
        print("La nueva posicion es",G.node[LastNode]['pos'])
        x1=G.node[NextRandomNode[1]]['pos'][1]
        y1=G.node[NextRandomNode[1]]['pos'][0]

        
	for i in range(-Ra,2):
            for j in range(-Ra,2):
                if ((i==0 and j==1) or (i==0 and j==-1) or (i==1 and j==0) or (i==-1 and j==0)):
                    PositionCheck.append(tuple([y1+j,x1+i]))

        #print(position)
        #print(PositionCheck)

        #We set the missing nodes for the new free positions
        for k in G.nodes():
            if position[k] in PositionCheck:
                #print("El nodo",k,"esta ocupando una nueva posicion")
                OccupiedPosition.append(tuple([position[k][0],position[k][1]]))
        


        #We set the edges for the new nodes and current nodes

        for k in OccupiedPosition:
            if k!=G.node[LastNode]["pos"]:
                OccupiedPositionNoEdges.append(k)
        print("Positions with edges to verify",OccupiedPositionNoEdges)
	
        for k in OccupiedPositionNoEdges:
            print(config[k[1],k[0]])
            for j in G.nodes(): #Recorremos todos los nodos de la red
                if (G.node[j]['pos'][0]==k[0] and G.node[j]['pos'][1]==k[1]) and(config[k[1],k[0]]==0):
                    OccupiedPositionNoEdgesNodes.append(j)
        print("Nodos sobre los que verificaremos enlaces nuevos", OccupiedPositionNoEdgesNodes)
        Gcopy=G.copy()
        #Aqui asignamos pesos sobre los enlaces adyacentes sin enlaces
        for l in OccupiedPositionNoEdgesNodes:
            if not Gcopy.has_edge(l,NextRandomNode[1]):
                print("No hay enlaces entre",l, "y",NextRandomNode[1])
                G.add_edge(NextRandomNode[1],l)
                G.edges[NextRandomNode[1],l]['weight'] = 2
	
	#In this last step we check the nodes, the agent will detect obstacles, unreachable positions and target cells.
        for i in OccupiedPosition:
            PositionCheck.remove(i)
        #print("Los nodos disponibles para nodos nuevos son", PositionCheck)
        for j in PositionCheck:
            if config[j[1],j[0]]==1:
                #print("Pondremos un nodo nuevo en un obstaculo")
                G.add_node(NextNode, pos=(j[0],j[1]))
                G.add_edge(NextRandomNode[1],NextNode)
                G.edges[NextNode,NextRandomNode[1]]['weight'] = 0
                G.node[NextNode]['state'] = 0 #Nodo inaccesible es igual a 0
                G.node[NextNode]['goal']=0
                CurrentNode=CurrentNode+1
                NextNode=NextNode+1
            if config[j[1],j[0]]==0 or config[j[1],j[0]]==2:
                #print("Pondremos un nodo nuevo en un obstaculo")
                G.add_node(NextNode, pos=(j[0],j[1]))
                G.add_edge(NextRandomNode[1],NextNode)
                G.edges[NextNode,NextRandomNode[1]]['weight'] = 1
                G.node[NextNode]['state'] = 1 #Nodo inaccesible es igual a 0
                G.node[NextNode]['goal']=0
                CurrentNode=CurrentNode+1
                NextNode=NextNode+1
            if config[j[1],j[0]]==3:
                Stop=1
                #print("Hemos encontrado el objetivo")
                G.add_node(NextNode, pos=(j[0],j[1]))
                G.add_edge(NextRandomNode[1],NextNode)
                G.edges[NextNode,NextRandomNode[1]]['weight'] = 1#era 1
                G.node[NextNode]['state'] = 1 #Nodo inaccesible es igual a 0
                G.node[NextNode]['goal']=1
                CurrentNode=CurrentNode+1
                NextNode=NextNode+1
		
		
		#As a extra step we can run a efficient informed pathfinding algorithm to draw a optimal path from the initial state to the target
                OptimizedPath=nx.shortest_path(G,source=1,target=NextRandomNode[1])

                for k in range(0,len(OptimizedPath)-1):
                    print(OptimizedPath[k])
                    G.edges[OptimizedPath[k],OptimizedPath[k+1]]['weight'] = 255






                #print(OptimizedPath)

        #We reset all our vectors and auxiliar variables to iterate again
        #print(G.edges(data='weight'))
        LastNode=NextRandomNode[1]
        del WeightVector[:]
        del WeightVectorSorted[:]
        del PositionCheck[:]
        del AccessNodes[:]
        del Path[:]
        del OccupiedPosition[:]
        del OccupiedPositionNoEdges[:]
        del OccupiedPositionNoEdgesNodes[:]
```

Finally, we need to iterate the algorithm until convergence. For that, we use the GUI for pycxsimulator:

```
import pycxsimulator
pycxsimulator.GUI().start(func=[initialize,observe,update])
```
In the 
