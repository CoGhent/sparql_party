# SPARQL party  

## Wat is SPARQL? 

SPARQL is de standaard query taal en protocol voor het bevragen en consulteren van [Linked Open Data](https://www.w3.org/egov/wiki/Linked_Open_Data) (LOD) op het web of voor het raadplegen van [RDF](https://www.w3.org/RDF/) triplestores. 

## SPARQL Recipes

### return all titles
deze [SPARQL query](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Fstad.gent%2Fsparql&query=SELECT%20%3Ftitle%20FROM%20%3Chttp%3A%2F%2Fstad.gent%2Fldes%2Fdmg%3E%20%0AWHERE%20%7B%20%0A%20%20%3Fobject%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2FP102_has_title%3E%20%3Ftitle%0A%7D%20LIMIT%20100) vraagt 100 titels op van objecten uit de LDES van het Design Museum Gent 

```sparql
SELECT ?title FROM <http://stad.gent/ldes/dmg> 
WHERE { 
  ?object <http://www.cidoc-crm.org/cidoc-crm/P102_has_title> ?title
} LIMIT 100
```
toevoegen van de beschrijving in de [query](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Fstad.gent%2Fsparql&query=SELECT%20%3Ftitle%20%20%3Fbeschrijving%20FROM%20%3Chttp%3A%2F%2Fstad.gent%2Fldes%2Fdmg%3E%20%0AWHERE%20%7B%20%0A%20%20%3Fobject%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2FP102_has_title%3E%20%3Ftitle.%0A%20%20%3Fobject%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2FP3_has_note%3E%20%3Fbeschrijving%0A%7D%20LIMIT%20100); 

```sparql
SELECT ?title  ?maker FROM <http://stad.gent/ldes/dmg> 
WHERE { 
  ?object <http://www.cidoc-crm.org/cidoc-crm/P102_has_title> ?title
  ?object <http://www.cidoc-crm.org/cidoc-crm/P108i_was_produced_by> ?maker
} LIMIT 100
```

### werken met FILTER
FILTER kan gebruikt worden voor het filteren in de opgevraagde resultaten. 
Aan de hand van de functie regex() kan gezocht worden naar een substring in een string. 
In het onderstaande [voorbeeld](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Fstad.gent%2Fsparql&query=SELECT%20%3Ftitle%20%20%3Fbeschrijving%20FROM%20%3Chttp%3A%2F%2Fstad.gent%2Fldes%2Fdmg%3E%20%0AWHERE%20%7B%20%0A%20%20%3Fobject%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2FP102_has_title%3E%20%3Ftitle.%0A%20%20FILTER%20(regex(%3Ftitle%2C%20%22NOVA%22%2C%20%22i%22))%0A%20%20%3Fobject%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2FP3_has_note%3E%20%3Fbeschrijving%0A%7D%20LIMIT%20100) wensen we enkel deze objecten te ontvangen waarbij "NOVA" voorkomt in de titel. 

```sparql
SELECT ?title  ?beschrijving FROM <http://stad.gent/ldes/dmg> 
WHERE { 
  ?object <http://www.cidoc-crm.org/cidoc-crm/P102_has_title> ?title.
  FILTER (regex(?title, "NOVA", "i"))
  ?object <http://www.cidoc-crm.org/cidoc-crm/P3_has_note> ?beschrijving
} LIMIT 100
```

### werken met DISTINCT 
Door gebruik te maken van DISTINCT na SELECT [deze](http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Fstad.gent%2Fsparql;sparql%40http%3A%2F%2Fvocab.getty.edu%2Fsparql.json&query=SELECT%20DISTINCT%20%3Ftitle%20%3Fbeschrijving%20FROM%20%3Chttp%3A%2F%2Fstad.gent%2Fldes%2Fdmg%3E%20%0AWHERE%20%7B%20%0A%20%20%3Fobject%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2FP102_has_title%3E%20%3Ftitle.%0A%20%20FILTER%20(regex(%3Ftitle%2C%20%22NOVA%22%2C%20%22i%22)).%0A%20%20%3Fobject%20%3Chttp%3A%2F%2Fwww.cidoc-crm.org%2Fcidoc-crm%2FP3_has_note%3E%20%3Fbeschrijving.%0A%7D%20LIMIT%20100%0A) query heeft enkel resultaten met unieke titels en beschrijving terug. Op deze manier kunnen we het ophalen van meerdere versies van 1 object vermijden. 

```sparql 
SELECT DISTINCT ?title ?beschrijving FROM <http://stad.gent/ldes/dmg> 
WHERE { 
  ?object <http://www.cidoc-crm.org/cidoc-crm/P102_has_title> ?title.
  FILTER (regex(?title, "NOVA", "i")).
  ?object <http://www.cidoc-crm.org/cidoc-crm/P3_has_note> ?beschrijving.
} LIMIT 100
```
