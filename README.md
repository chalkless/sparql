# SPARQL Memo

## Taxonomy

- Taxonomy ID を入力にして scientific name を出す

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX taxon: <http://ddbj.nig.ac.jp/ontologies/taxonomy/>
PREFIX taxid: <http://identifiers.org/taxonomy/>

SELECT ?label
FROM <http://togogenome.org/graph/taxonomy>
WHERE {
    taxid:9606 rdfs:label ?label .
}
```

- Taxonomy ID を入力にして Taxonomy ID とscientific name を出す（試行錯誤中）

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX taxon: <http://ddbj.nig.ac.jp/ontologies/taxonomy/>
PREFIX taxid: <http://identifiers.org/taxonomy/>

SELECT ?taxid ?label
FROM <http://togogenome.org/graph/taxonomy>
WHERE {
    ?taxon  ?taxid ;
           rdfs:label ?label .
    filter (?taxid = 9606)
}
```

- scientific name を入れて Taxonomy IDを出す

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX taxon: <http://ddbj.nig.ac.jp/ontologies/taxonomy/>
PREFIX taxid: <http://identifiers.org/taxonomy/>

SELECT ?taxid
FROM <http://togogenome.org/graph/taxonomy>
WHERE {
    ?taxid rdfs:label "Homo sapiens" .
}
```

  - 結果

```
"taxid" "http://identifiers.org/taxonomy/9606"
```

- 1つ上の階層

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX taxon: <http://ddbj.nig.ac.jp/ontologies/taxonomy/>
PREFIX taxid: <http://identifiers.org/taxonomy/>

SELECT ?taxid ?label ?rank
FROM <http://togogenome.org/graph/taxonomy>
WHERE {
    taxid:4530 rdfs:subClassOf ?taxid .
    ?taxid rdfs:label ?label ;
           taxon:rank ?rank .
}
```

- 下全部（自分を含めない場合）

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX taxon: <http://ddbj.nig.ac.jp/ontologies/taxonomy/>
PREFIX taxid: <http://identifiers.org/taxonomy/>

SELECT ?taxid ?label ?rank
FROM <http://togogenome.org/graph/taxonomy>
WHERE {
    ?taxid rdfs:subClassOf+ taxid:4530 .
    ?taxid rdfs:label ?label ;
           taxon:rank ?rank .
}
```
rdfs:subClassOf+ と + がつくことに注目

- 下全部（自分を含める場合）

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX taxon: <http://ddbj.nig.ac.jp/ontologies/taxonomy/>
PREFIX taxid: <http://identifiers.org/taxonomy/>

SELECT ?taxid ?label ?rank
FROM <http://togogenome.org/graph/taxonomy>
WHERE {
    ?taxid rdfs:subClassOf* taxid:4530 .
    ?taxid rdfs:label ?label ;
           taxon:rank ?rank .
}
```
rdfs:subClassOf* となっている。

- まとめ：
  - rdfs:subClassOf：1階層だけ下
  - rdfs:subClassOf+：下全部
  - rdfs:subClassOf*：自分も含めて下全部

- 結果

```
"taxid"	"label"	"rank"
"http://identifiers.org/taxonomy/4530"	"Oryza sativa"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/Species"
"http://identifiers.org/taxonomy/4536"	"Oryza sativa f. spontanea"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/Forma"
"http://identifiers.org/taxonomy/39946"	"Oryza sativa Indica Group"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/39947"	"Oryza sativa Japonica Group"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1080340"	"Oryza sativa Japonica Group x Oryza sativa Indica Group"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1050722"	"Oryza sativa Indica Group x Oryza sativa Japonica Group"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1736656"	"Oryza sativa tropical japonica subgroup"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1736657"	"Oryza sativa temperate japonica subgroup"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1736658"	"Oryza sativa aromatic subgroup"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1736659"	"Oryza sativa aus subgroup"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
```


- taxidをURLの形でなく値として返す、自分も含めた下全部

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX taxon: <http://ddbj.nig.ac.jp/ontologies/taxonomy/>
PREFIX taxid: <http://identifiers.org/taxonomy/>
PREFIX dct: <http://purl.org/dc/terms/>

SELECT ?taxid ?id ?label ?rank
FROM <http://togogenome.org/graph/taxonomy>
WHERE {
    ?taxid rdfs:subClassOf* taxid:4530 .
    ?taxid rdfs:label ?label ;
           dct:identifier ?id;
           taxon:rank ?rank .
}
```

- 結果

```
"taxid"	"id"	"label"	"rank"
"http://identifiers.org/taxonomy/4530"	4530	"Oryza sativa"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/Species"
"http://identifiers.org/taxonomy/4536"	4536	"Oryza sativa f. spontanea"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/Forma"
"http://identifiers.org/taxonomy/39946"	39946	"Oryza sativa Indica Group"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/39947"	39947	"Oryza sativa Japonica Group"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1080340"	1080340	"Oryza sativa Japonica Group x Oryza sativa Indica Group"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1050722"	1050722	"Oryza sativa Indica Group x Oryza sativa Japonica Group"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1736656"	1736656	"Oryza sativa tropical japonica subgroup"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1736657"	1736657	"Oryza sativa temperate japonica subgroup"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1736658"	1736658	"Oryza sativa aromatic subgroup"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
"http://identifiers.org/taxonomy/1736659"	1736659	"Oryza sativa aus subgroup"	"http://ddbj.nig.ac.jp/ontologies/taxonomy/NoRank"
```


- 1つ上の階層をたどって下全部

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX taxon: <http://ddbj.nig.ac.jp/ontologies/taxonomy/>
PREFIX taxid: <http://identifiers.org/taxonomy/>
SELECT ?taxid ?label ?rank
FROM <http://togogenome.org/graph/taxonomy>
WHERE {
    taxid:4530 rdfs:subClassOf ?parent .
    ?taxid rdfs:subClassOf* ?parent .
    ?taxid rdfs:label ?label ;
           taxon:rank ?rank .
}
```

- scientific name を入れて、（上をたどり）目や科を出す

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX taxon: <http://ddbj.nig.ac.jp/ontologies/taxonomy/>
PREFIX taxid: <http://identifiers.org/taxonomy/>

SELECT ?taxid ?label ?rank
FROM <http://togogenome.org/graph/taxonomy>
WHERE {
    ?taxid_self rdfs:label "Oryza sativa" .
    ?taxid_self rdfs:subClassOf+ ?taxid .
    ?taxid rdfs:label ?label ;
           taxon:rank ?rank .
    VALUES ?rank {
           taxon:Superkingdom
           taxon:Phylum
           taxon:Class
           taxon:Order
           taxon:Family
           taxon:Genus
           taxon:Species
    }
}
```
