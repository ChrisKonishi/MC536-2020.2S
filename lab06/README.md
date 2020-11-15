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

## Exercício

Construa um grafo ligando os medicamentos aos efeitos colaterais (com pesos associados) a partir dos registros das pessoas, ou seja, se uma pessoa usa um medicamento e ela teve um efeito colateral, o medicamento deve ser ligado ao efeito colateral.

## Resolução

```
MATCH ()-[A]-() DELETE A;
MATCH (N) DELETE N;


LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug.csv' AS line
CREATE (:Drug {code: line.code, name: line.name});
CREATE INDEX ON :Drug(code);
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/pathology.csv' AS line
CREATE (:Pathology { code: line.code, name: line.name});
CREATE INDEX ON :Pathology(code);


LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug-use.csv' AS line
MATCH (d:Drug {code: line.codedrug})
MERGE (p:pessoa {id: line.idperson})
MERGE (p)-[:usa]->(d);
CREATE INDEX ON :pessoa(id);


LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/sideeffect.csv' AS line
MATCH (pa:Pathology {code: line.` codePathology`})
MATCH (p:pessoa {id: line.idPerson})
MERGE (p)-[:teve]->(pa);


MATCH (pa:Pathology)<-[]-(p:pessoa)-[]->(d:Drug)
MERGE (d)-[r:causa]->(pa)
ON CREATE SET r.weight=1
ON MATCH SET r.weight=r.weight+1;

MATCH (pa:Pathology)<-[e]-(d:Drug)
where e.weight > 20
return d,pa 
limit 50;
```


## Exercício

Que tipo de análise interessante pode ser feita com esse grafo?

Proponha um tipo de análise e escreva uma sentença em Cypher que realize a análise.

## Resolução

Alguns efeitos colaterais podem estar associados ao uso de duas drogas, irei associair as drogas entre si, se ambas forem conectadas a um efeito colateral

```
MATCH (d1:Drug)-[c1:causa]->(p:Pathology)<-[c2:causa]-(d2:Drug)
WHERE c1.weight>20 and c2.weight>20
MERGE (d1)-[r:combina]-(d2)
ON CREATE SET r.weight=1
ON MATCH SET r.weight=r.weight+1;

MATCH (d1:Drug)-[r:combina]-(d2:Drug)
return d1, d2, r
```
