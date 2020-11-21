## Tarefa de análises feitas no Cypher

## Exercício 1

Calcule o Pagerank do exemplo da Wikipedia em Cypher:

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/network/pagerank/pagerank-wikipedia.csv' AS line
MERGE (p1:Page {name:line.source})
MERGE (p2:Page {name:line.target})
CREATE (p1)-[:LINKS]->(p2)

CALL gds.graph.create('prGraph','Page','LINKS')

CALL gds.pageRank.stream('prGraph')
YIELD nodeId, score
MATCH (p:Page {name: gds.util.asNode(nodeId).name})
SET p.pagerank = score
~~~

## Exercício 2

Departing from a Drug-Drug graph created in a previous lab, whose relationship determines drugs taken together, apply a community detection in it to see the results:

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug.csv' AS line
CREATE (:Drug {code: line.code, name: line.name})

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/pathology.csv' AS line
CREATE (:Pathology { code: line.code, name: line.name})

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug-use.csv' AS line
MATCH (d:Drug {code: line.codedrug})
MATCH (p:Pathology {code: line.codepathology})
MERGE (d)-[t:Treats]->(p)
ON CREATE SET t.weight=1
ON MATCH SET t.weight=t.weight+1

MATCH (d1:Drug)-[a]->(p:Pathology)<-[b]-(d2:Drug)
WHERE a.weight > 20 AND b.weight > 20
MERGE (d1)<-[r:Relates]->(d2)
ON CREATE SET r.weight=1
ON MATCH SET r.weight=r.weight+1

CALL gds.graph.create('prGraph','Drug','Relates')

CALL gds.pageRank.stream('prGraph')
YIELD nodeId, score
MATCH (d:Drug {name: gds.util.asNode(nodeId).name})
SET d.pagerank = score
~~~
