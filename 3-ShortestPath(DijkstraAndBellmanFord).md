
#PAA
#Professor Fabrício

## Single-Source Shortest Path

Seja um grafo direcionado com pesos. Encontre o
menor caminho (menor peso) do vertice S para o V.

### Shortest Path Properties

1. Optimal substructure:
   O melhor caminho ótimo é composto de subcaminhos ótimos.

2. O menor caminho satisfaz a inegualdade de triangulo:  
   Se tenho o caminho mínimo de (a,b) ele deve ser <=
   a soma do caminho mínimo de (a,c) + (c,b) para um vertice c qualquer.

### Grafos com pesos negativos:

Grafos com pesos negativos podem causar problemas. Se houver um ciclo
cujo peso total é negativo, isso significa que caminhar 2 vezes pelo
ciclo é `mais perto` do que caminhar uma vez. Assim em grafos
onde haja ciclos negativos não é possível obter o caminho mínimo.

Por outro lado se não houverem ciclos negativos não há problema
em haverem pesos negativos nas arestas.

### Relaxamento:
 
  A idéia do relaxamento consiste em armazenar o caminho
  (a,b) = infinito a princípio e para cada novo caminho
  melhor que o anterior reduzir esse caminho. Para isso
  basta fazer o teste:
  
```python
  # d = distancia da origem
  # w = peso da aresta
  if d[b] >= d[c] + w(c,d):
    d[b] = d[c] + w(c,b)
```

### Algoritmos:

#### Dijkstra:

Este algoritmo utiliza dois conjuntos de nós:

- Visitados
- Adjacentes

O conjunto de visitados vai incluir todos os nós para os quais nós
já temos a distancia ótima. O conjunto de não visitados vai incluir
apenas os nós que fazem aresta com os já visitados (e não necessariamente
todos os nós não visitados do grafo).

O algoritmo funciona usando um conceito interessante que consiste
em escolhar de forma cuidadosa um item do grupo de não visitados
e inclui-lo no conjunto de nós visitados.
E a cada nova inclusão recalcular as estimativas de distancia dos
nós adjacentes ao que acabou de ser incluido.

Esse mecanismo de escolha por sí só garante que a estimativa
de distancia desse nó recentemente incluido será a menor distancia
possível da raiz, e não é nada muito complexo:
Escolhe-se sempre o nó do conjunto de adjacentes com a menor
distancia estimada até então.

Uma última observação antes de mostrar o algoritmo.
A atualização das distancias é feita sempre considerando a menor
distancia:

```python
nova_dist = distancia_de(P) + peso_da_aresta(P -> NovoNó)
if (nova_dist < dist_atual) dist_atual = nova_dist
```

O que significa que se um nó adjacente puder ser alcançado por 2 ou mais nós
visitados, nós escolheremos sempre o menor caminho até ele.

A figura abaixo ilustra bem o funcionamento do algoritmo, cada nó vermelho
está no conjunto de nós visitados:

![Demonstração Dijkstra](images/dijkstra.gif "animação representando a execução")

Agora o algoritmo em pseucódigo:

```python
def Dijkstra(G, root):
  # Distance list:
  d = []
  # Ancestor list:
  # (Esse vetor vai ser usado para recuperar os caminhos mínimos
  #  e não apenas as distancias mínimas)
  a = []
  
  for v in G.V:
    # Set the distance to infinity:
    d[v] = <infinity>
    # Set the ancestor to None:
    a[v] = None

  # The root vertex start with distance 0:
  d[root] = 0
  
  # Creates 2 sorted sets where the sorting criteria
  # is the distance from the root node:
  visitados = novo ConjuntoOrdenado()
  adjacentes = novo ConjuntoOrdenado()
  
  # Add root to the adjacents set:
  adjacentes.add(root)
  
  while len(adjacentes) > 0:
    v = adjacentes.extractCloserNode()
    
    # For each node adjacent to v:
    for u in v.adjacents():
      if d[u] > d[v] + w(v,u):
        d[u] = d[v] + w(v,u)
        a[u] = v
        
      # Now manage the adjacentes set:
      if u in adjacents:
        adjacents.update_position(u)
      else:
        adjacents.add(u)
```

Ao fim da execução desse algoritmo, o vetor `d`, vai conter as distancias
para cada nó, e com o vetor `a` será possível reconstruir o caminho ótimo
de cada nó voltando para o nó raiz.

Uma observação importante é que normalmente se utiliza um `Heap` para representar
o conjunto ordenado, e isso influencia diretamente na complexidade do algoritmo.

Complexidade no tempo:

- O( E log(V) ) com heap binário.
- O( V log(V) + E ) com heap de fibonacci.

Se o grafo não tiver pesos nas arestas voce pode utilizar
**Busca em Largura (BFS)** para encontrar a menor distancia
com complexidade: O(V+E)

#### Bellman-Ford:

O Bellman-Ford tem uma vantagem sobre o Dikstra:
  ele detecta ciclos negativos.

O algoritmo é menos eficiente, porém mais seguro.  
A idéia do algoritmo consiste em relaxar todos as E
arestas V-1 vezes onde V é o número de vertices, ou seja
O(V * E) relaxamentos. Esse é o número de iterações que garante
que todas as distancias já foram calculadas, e todas já estão
no mínimo mesmo no pior caso para um grafo sem ciclos negativos.

Após isto o algoritmo executa mais uma vez o relaxamento para cada aresta
se ainda houver uma forma de melhorar algum caminho mínimo significa
que há ao menos um ciclo negativo no grafo.

A figura abaixo apresenta um grafo degenerado (o pior caso nesse problema), neste
exemplo `len(V) = 5` e  logo o número de loops do algoritmo será `V-1 = 4`.
Note que após 4 iterações mesmo nesse pior caso todos as arestas já terão encontrado
o melhor caminho:

![worst-case Bellman-Ford](images/bellman-ford.png)

```python
BellmanFord(G, root)
  # Distance list:
  d = []
  # Ancestor list:
  a = []
  for v in G.V
    d[v] = infinito
    a[v] = None
  
  # Root distance to root is 0:
  d[root] = 0
  
  # Run once for each node of G -1:
  for i from 1 to len(G.V)-1:
    # Run once for each edge of G: (run G.V * G.E times)
    for edge (u,v) in G.E:
      if(d[v] > d[u] + w(u,v))
        d[v] = d[u] + w(u,v)
        a[v] = u

  # Run again, if there is a better path
  # it means we have a negative cicle:
  for edge(u,v) in G.E:
    if(d[v] > d[u] + w(u,v)):
      return 'No solution!'
```

Complexidade no tempo: O(V * E)

---

# Fim da Aula!
