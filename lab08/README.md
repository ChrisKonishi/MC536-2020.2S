## Questão 1

Construa uma comando SELECT que retorne dados equivalentes a este XPath

### Resolução

```
SELECT I.NAME
FROM INDIVIDUOS I
WHERE I.IDADE>20;
```

## Questão 2

Qual a outra maneira de escrever esta query sem o where?

```
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')
 
for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
```

### Resolução

```
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')
for $i in ($fichariodoc//individuo[idade>17])
return {data($i/@nome)}
```

## Questão 3

Escreva uma consulta SQL equivalente ao XQuery:

```
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')

for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
```

### Resolução

```
SELECT I.NOME
FROM INDIVIDUO I
WHERE I.IDADE>17
```

## Questão 4

Retorne quantas publicações são posteriores ao ano de 2011.

### Resolução

```
let $pubs := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

for $p in ($pubs//publication[year>2011])
group by $pubs
return count($p)
```

## Questão 5

Retorne a categoria cujo <label> em inglês seja 'e-Science Domain'.

### Resolução

```
let $pubs := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

for $c in ($pubs//categories[@catkey='subject']/category)
where $c/label[@lang="en-US"]/text() = 'e-Science Domain'
return {data($c/@key)}
```

## Questão 6

Retorne as publicações associadas à categoria cujo <label> em inglês seja 'e-Science Domain'. A associação entre o label e a key da categoria deve ser feita na consulta.

### Resolução

```
let $pubs := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

for $p in ($pubs//publication)
where $p/key/text() =
{
for $c in ($pubs//categories[@catkey='subject']/category)
where $c/label[@lang="en-US"]/text() = 'e-Science Domain'
return {data($c/@key)}
}
return {data($p/title/text()), '&#xa;'}
```

---

# Exercícios com DRON e PubChem

## Questão 1

Liste todas as classificações que estão dois níveis abaixo da raiz

### Resolução

```
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')
let $root := $dron/drug
for $d in ($root/drug/drug)
return {data($d/@name), '&#xa;'}
```


## Questão 2

Apresente todas as classificações de um componente a sua escolha (diferente de Acetylsalicylic Acid) que estejam hierarquicamente dois níveis acima. Note que no exemplo dado em sala foi considerado um nível hierárquico acima.

### Resolução

Para a droga "CORTISONE"

```
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

for $d in ($dron//drug)
where ($d/drug/drug/@name) = "CORTISONE"
let $name := $d/@name
return {data($d/@name), '&#xa;'}
```


## Questão 3.1

Liste todos os códigos ChEBI dos componentes disponíveis.

### Resolução


```
let $pubchem := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')

let $root := $pubchem/PC-DataSet
for $info in ($root/InformationList/Information)
return {$info/Synonym[1]/text(), '&#xa;'}
```

## Questão 3.2

Liste todos os códigos ChEBI e primeiro nome (sinônimo) de cada um dos componentes disponíveis.

### Resolução


```
let $pubchem := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')

let $root := $pubchem/PC-DataSet
for $info in ($root/InformationList/Information)
return {'&#xa;', 'nome: ', $info/Synonym[2]/text(), '&#xa;', $info/Synonym[1]/text(), '&#xa;'}
```

## Questão 3.3

Para cada código ChEBI, liste os sinônimos e o nome que aparece para o mesmo componente no DRON (se existir). Para isso faça um JOIN com o DRON.

### Resolução


```
let $pubchem := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

let $root := $pubchem/PC-DataSet

for $info in ($root/InformationList/Information),
    $dron_drug in ($dron//drug)

where $dron_drug/@id = concat("http://purl.obolibrary.org/obo/CHEBI_", substring($info/Synonym[1]/text(), 7))


return {data($dron_drug/@id),'&#xa;'}
```