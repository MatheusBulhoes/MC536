## Tarefa de Cypher e o FDA Adverse Event Reporting System (FAERS)

## Exercício 1

Escreva uma sentença em Cypher que crie o medicamento de nome `Metamizole`, código no DrugBank `DB04817`.

### Resolução
~~~cypher
CREATE (:Drug {drugbank: "DB04817", name:"Metamizole"})
~~~

## Exercício 2

Considerando que a `Dipyrone` e `Metamizole` são o mesmo medicamento com nomes diferentes, crie uma aresta com o rótulo `:SameAs` que ligue os dois.

### Resolução
~~~cypher
MATCH (d1:Drug {name:"Dipyrone"})
MATCH (d2:Drug {name:"Metamizole"})
CREATE (d1)-[:SameAs]->(d2)
~~~

## Exercício 3

Use o `DELETE` para excluir o relacionamento que você criou (apenas ele).

### Resolução
~~~cypher
MATCH (:Drug {name:"Dipyrone"})-[s:SameAs]->(:Drug {name:"Metamizole"})
DELETE s
~~~

## Exercício 4

Faça a projeção em relação a Patologia, ou seja, conecte patologias que são tratadas pela mesma droga.

### Resolução
~~~cypher
MATCH (p1:Pathology)<-[a]-(d:Drug)-[b]->(p2:Pathology)
WHERE a.weight > 20 AND b.weight > 20 AND a<>b
MERGE (p1)<-[r:Relates]->(p2)
ON CREATE SET r.weight=1
ON MATCH SET r.weight=r.weight+1
~~~

## Exercício 5

Construa um grafo ligando os medicamentos aos efeitos colaterais (com pesos associados) a partir dos registros das pessoas, ou seja, se uma pessoa usa um medicamento e ela teve um efeito colateral, o medicamento deve ser ligado ao efeito colateral.

### Resolução
~~~cypher
(LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug-use.csv' AS line
MATCH (d:Drug {code: line.codedrug})
MERGE (p:Person {id: line.idperson})
MERGE (p)-[u:Uses]->(d)
ON CREATE SET u.weight=1
ON MATCH SET u.weight=u.weight+1

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/sideeffect.csv' AS line
MATCH (pat:Pathology {code: line.codePathology})
MERGE (per:Person {id: line.idPerson})
MERGE (per)-[s:sideEffect]->(pat)
ON CREATE SET s.weight=1
ON MATCH SET s.weight=s.weight+1

MATCH (d:Drug)<-[u:Uses]-(per:Person)-[s:sideEffect]->(pat:Pathology)
MERGE (d)-[c:Causes]->(pat)
ON CREATE SET c.weight=1
ON MATCH SET c.weight=c.weight+1
~~~

## Exercício 6

Que tipo de análise interessante pode ser feita com esse grafo?

Proponha um tipo de análise e escreva uma sentença em Cypher que realize a análise.

### Resolução
~~~cypher
(escreva aqui a resolução em Cypher)
~~~
