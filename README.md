# SPARQL demo  

## Wat is SPARQL? 

SPARQL is de standaard query taal en protocol voor het bevragen en consulteren van [Linked Open Data](https://www.w3.org/egov/wiki/Linked_Open_Data) (LOD) op het web of voor het raadplegen van [RDF](https://www.w3.org/RDF/) triplestores. 

In het kader van deze demo wordt gebruik gemaakt van de [Communica Web Client](http://query.linkeddatafragments.org/). Communica is een knowledge graph querying framework.

## bronnen

| instelling   |      dcat      |
|----------|:-------------:|
| personen en instellingen (alle instellingen) |  http://stad.gent/ldes/personen | 
| thesaurus (alle instellingen)| http://stad.gent/ldes/thesaurus | 
| industriemuseum |    http://stad.gent/ldes/industriemuseum   | 
| archief gent| http://stad.gent/ldes/archief |  
| design museum gent| http://stad.gent/ldes/dmg |   
| huis van alijn| http://stad.gent/ldes/hva |  
| STAM| http://stad.gent/ldes/stam |  

## SPARQL Recipes
### 1. Queries over 1 collectie

#### basis query (opvragen van titels)
deze [SPARQL query](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Fstad.gent%2Fsparql&query=PREFIX%20cidoc%3A%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2F%3E%0ASELECT%20%3Ftitle%20FROM%20%3Chttp%3A%2F%2Fstad.gent%2Fldes%2Fdmg%3E%20%0AWHERE%20%7B%20%0A%20%20%3Fobject%20cidoc%3AP102_has_title%20%3Ftitle%0A%7D%20LIMIT%20100) vraagt 100 titels op van objecten uit de LDES van het Design Museum Gent 

```sparql
PREFIX cidoc: <http://www.cidoc-crm.org/cidoc-crm/>
SELECT ?title FROM <http://stad.gent/ldes/dmg> 
WHERE { 
  ?object cidoc:P102_has_title ?title
} LIMIT 100
```
toevoegen van de beschrijving in de [query](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Fstad.gent%2Fsparql&query=PREFIX%20cidoc%3A%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2F%3E%0ASELECT%20%3Ftitle%20%20%3Fmaker%20FROM%20%3Chttp%3A%2F%2Fstad.gent%2Fldes%2Fdmg%3E%20%0AWHERE%20%7B%20%0A%20%20%3Fobject%20cidoc%3AP102_has_title%20%3Ftitle.%0A%20%20%3Fobject%20cidoc%3AP108i_was_produced_by%20%3Fmaker%0A%7D%20LIMIT%20100); 

```sparql
PREFIX cidoc: <http://www.cidoc-crm.org/cidoc-crm/>
SELECT ?title  ?maker FROM <http://stad.gent/ldes/dmg> 
WHERE { 
  ?object cidoc:P102_has_title ?title
  ?object cidoc:P108i_was_produced_by ?maker
} LIMIT 100
```

#### werken met FILTER
FILTER kan gebruikt worden voor het filteren in de opgevraagde resultaten. 
Aan de hand van de functie regex() kan gezocht worden naar een substring in een string. 
In het onderstaande [voorbeeld](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Fstad.gent%2Fsparql&query=PREFIX%20cidoc%3A%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2F%3E%0ASELECT%20%3Ftitle%20%20%3Fbeschrijving%20FROM%20%3Chttp%3A%2F%2Fstad.gent%2Fldes%2Fdmg%3E%20%0AWHERE%20%7B%20%0A%20%20%3Fobject%20cidoc%3AP102_has_title%20%3Ftitle.%0A%20%20FILTER%20(regex(%3Ftitle%2C%20%22NOVA%22%2C%20%22i%22))%0A%20%20%3Fobject%20cidoc%3AP3_has_note%20%3Fbeschrijving%0A%7D%20LIMIT%20100) wensen we enkel deze objecten te ontvangen waarbij "NOVA" voorkomt in de titel. 

```sparql
PREFIX cidoc: <http://www.cidoc-crm.org/cidoc-crm/>
SELECT ?title  ?beschrijving FROM <http://stad.gent/ldes/dmg> 
WHERE { 
  ?object cidoc:P102_has_title ?title.
  FILTER (regex(?title, "NOVA", "i"))
  ?object cidoc:P3_has_note ?beschrijving
} LIMIT 100
```

#### werken met DISTINCT 
Door gebruik te maken van DISTINCT na SELECT [deze](https://bit.ly/3BAVFcX) query heeft enkel resultaten met unieke titels en beschrijving terug. Op deze manier kunnen we het ophalen van meerdere versies van 1 object vermijden. 

```sparql 
PREFIX cidoc: <http://www.cidoc-crm.org/cidoc-crm/>
SELECT DISTINCT ?title ?beschrijving FROM <http://stad.gent/ldes/dmg> 
WHERE { 
  ?object cidoc:P102_has_title ?title.
  FILTER (regex(?title, "NOVA", "i")).
  ?object cidoc:P3_has_note ?beschrijving.
} LIMIT 100
```

op dezelfde manier kunnen we ook een lijst opvragen met alle voorkomende vervaardigers gekoppeld aan objecten waarbij "NOVA" voortkomt in de titel aan de hand van de volgende [query](shorturl.at/zCLTW);

```sparql
PREFIX cidoc: <http://www.cidoc-crm.org/cidoc-crm/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

select distinct ?maker from <http://stad.gent/ldes/dmg>
where {
	?object cidoc:P102_has_title ?title.
	FILTER (regex(?title, "NOVA", "i"))
  	?object cidoc:P108i_was_produced_by ?p.
  	?p cidoc:P14_carried_out_by ?m.
  	?m rdfs:label ?maker
}
```

### 2. Queries over verschillende collecties heen
door het toevoegen van meerdere bronnen via de FROM statement kan een query opgesteld worden die over de verschillende collecties heen zoekt. 

### 3. Queries over verschillende datasets heen (extern)

```sparql 
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>

SELECT ?maker WHERE {
  ?maker wdt:P6379 wd:Q1809071;
         wdt:P27 wd:Q31; 
         }
LIMIT 100
```

```sparql
PREFIX cidoc: <http://www.cidoc-crm.org/cidoc-crm/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX aat: <http://vocab.getty.edu/aat/>
PREFIX gvp: <http://vocab.getty.edu/ontology#>
PREFIX xl: <http://langegger.at/xlwrap/vocab#>
PREFIX spif: <http://spinrdf.org/spif#>
PREFIX uniprot: <http:xx//purl.uniprot.org/uniprot/>

select distinct ?l from <http://stad.gent/ldes/dmg>
where {
	?object cidoc:P102_has_title ?title.
  	?object cidoc:P45_consists_of ?m.
  	filter(regex(str(?m), "aat")).
  	bind (substr(str(?m), 28, 40) as ?aat_id). #fetch aat id 
} 
```

### todo 
- alle objecten met een vrouwelijke maker 
- alle objecten gerelateerd aan Gent 
- alle objecten gemaakt tijdens WOI

### enkele web interfaces:
- plotten van objecten op een kaart
- alle "portretten" van makers uit de collectie (via Wikimedia)
