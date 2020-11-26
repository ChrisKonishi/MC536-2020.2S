# Lab07 - Análise de Redes

## Tarefa de análises feitas no Cypher

## Exercício 1

Calcule o Pagerank do exemplo da Wikipedia em Cypher:

```
MATCH ()-[E]-()
DELETE E;
MATCH(N)
DELETE N;

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/network/pagerank/pagerank-wikipedia.csv' AS line
MERGE (p1:Page {name:line.source})
MERGE (p2:Page {name:line.target})
MERGE (p1)-[:LINKS]->(p2);

CALL gds.graph.create(
  'prGraph',
  'Page',
  'LINKS'
);

CALL gds.pageRank.stream('prGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name, score
ORDER BY score DESC, name ASC;

CALL gds.pageRank.stream('prGraph')
YIELD nodeId, score
MATCH (p:Page {name: gds.util.asNode(nodeId).name})
SET p.pagerank = score
```

## Exercício 2

Departing from a Drug-Drug graph created in a previous lab, whose relationship determines drugs taken together, apply a community detection in it to see the results:

```
CALL gds.graph.create(
  'drugGraph',
  'Drug',
  {
    Relates: {
      orientation: 'UNDIRECTED'
    }
  }
);

CALL gds.louvain.stream('drugGraph')
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).name AS name, communityId
ORDER BY communityId ASC
```