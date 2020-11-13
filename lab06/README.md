## Exercício

Escreva uma sentença em Cypher que crie o medicamento de nome Metamizole, código no DrugBank DB04817.

### Resolução

```
CREATE (:Drug {drugbank: "DB04817", name:"Metamizole"})
```

## Exercício

Considerando que a Dipyrone e Metamizole são o mesmo medicamento com nomes diferentes, crie uma aresta com o rótulo :SameAs que ligue os dois.

### Resolução

```
MATCH (p:Drug {name:"Metamizole"}) MATCH (d:Drug {name:"Dipyrone"}) CREATE (p)-[:SameAs]->(d) CREATE (d)-[:SameAs]->(p)
```

## Exercício

Use o DELETE para excluir o relacionamento que você criou (apenas ele).

### Resolução

```
MATCH((:Drug {name:"Metamizole"})-[r:SameAs]->(:Drug {name:"Dipyrone"}))
MATCH((:Drug {name:"Dipyrone"})-[s:SameAs]->(:Drug {name:"Metamizole"}))
DELETE r
DELETE s
```

## Exercício

Faça a projeção em relação a Patologia, ou seja, conecte patologias que são tratadas pela mesma droga.

### Resolução

```
MATCH (p1:Pathology)<-[a]-(d:Drug)-[b]->(p2:Pathology)
WHERE a.weight > 20 AND b.weight > 20
MERGE (p1)<-[r:PathoRelates]->(p2)
ON CREATE SET r.weight=1
ON MATCH SET r.weight=r.weight+1
```