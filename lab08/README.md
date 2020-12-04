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

for $p in ($pubs//publication/title)
return {data($p), '&#xa;'}
```

## Questão 5

Retorne a categoria cujo <label> em inglês seja 'e-Science Domain'.

### Resolução

```
let $pubs := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

for $c in ($pubs//categories/category)
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
for $c in ($pubs//categories/category)
where $c/label[@lang="en-US"]/text() = 'e-Science Domain'
return {data($c/@key)}
}
return {data($p/title/text()), '&#xa;'}
```
