## Tarefas com Publicações

## Questão 1
Construa uma comando SELECT que retorne dados equivalentes a este XPath
~~~xquery
//individuo[idade>20]/@nome
~~~

### Resolução
~~~sql
SELECT nome
FROM individuo
WHERE idade > 20
~~~

## Questão 2
Qual a outra maneira de escrever esta query sem o where?

~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')
 
for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
~~~
### Resolução
~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')

return data($fichariodoc//individuo[idade>17]/@nome)
~~~

## Questão 3
Escreva uma consulta SQL equivalente ao XQuery:
~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')

for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
~~~

### Resolução
~~~sql
SELECT nome
FROM individuo
WHERE idade > 17
~~~

## Questão 4
Retorne quantas publicações são posteriores ao ano de 2011.

### Resolução
~~~xquery
let $publicacoes := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

for $i in ($publicacoes/publications/publication)
let $ano := $i/@year
group by $ano
return data(count($i))
~~~

## Questão 5
Retorne a categoria cujo `<label>` em inglês seja 'e-Science Domain'.

### Resolução
~~~xquery
let $publicacoes := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

for $i in ($publicacoes/publications/categories/category)
where $i/label/@lang = "en-US" and $i/label = 'e-Science Domain'
return data($i)
~~~

## Questão 6
Retorne as publicações associadas à categoria cujo `<label>` em inglês seja 'e-Science Domain'. A associação entre o label e a key da categoria deve ser feita na consulta.

### Resolução
~~~xquery
let $publicacoes := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

for $c in ($publicacoes/publications/categories/category), $p in ($publicacoes/publications/publication)
where $c/label/@lang = "en-US" and $c/label = 'e-Science Domain' and $p/key = $c/@key
return data($p)
~~~

## Tarefas com DRON e PubChem

## Questão 1

Liste o nome de todas as classificações que estão apenas dois níveis imediatamente abaixo da raiz.

### Resolução
~~~xquery
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')
return {data($dron/*/*/*/@name)}
~~~

## Questão 2

Apresente todas as classificações de um componente a sua escolha (diferente de `Acetylsalicylic Acid`) que estejam hierarquicamente dois níveis acima. Note que no exemplo dado em sala foi considerado um nível hierárquico acima.

### Resolução
~~~xquery
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')
return {data($dron//drug[drug/drug/@name='COCAINE']/@name)}
~~~

## Questão 3

### Questão 3.1

Liste todos os códigos ChEBI dos componentes disponíveis.

#### Resolução
~~~xquery
let $pubchem := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')
for $i in ($pubchem//Synonym[substring(text(),0,6)="CHEBI"]/substring(text(),7))
return $i
~~~

### Questão 3.2

Liste todos os códigos ChEBI e primeiro nome (sinônimo) de cada um dos componentes disponíveis.

#### Resolução
~~~xquery
let $pubchem := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')
for $i in ($pubchem//Information/Synonym[1]), $j in ($pubchem//Information/Synonym[2])
return {data($i), data($j), '&#xa;'}
~~~

### Questão 3.3

Para cada código ChEBI, liste os sinônimos e o nome que aparece para o mesmo componente no DRON (se existir). Para isso faça um JOIN com o DRON.

#### Resolução
~~~xquery
let $pubchem := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

for $p in ($pubchem//Information),
    $s in ($pubchem//Synonym),
    $d in ($dron//drug)
where concat('http://purl.obolibrary.org/obo/CHEBI_', substring($s/text(), 7)) = $d/@id and $p/Synonym = $s
group by $s
return {data($s), data($p/Synonym), data($d/@name), '&#xa;'}
~~~
