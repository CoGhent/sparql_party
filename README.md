# SPARQL party  

## Wat is SPARQL? 

SPARQL is de standaard query taal en protocol voor het bevragen en consulteren van [Linked Open Data](https://www.w3.org/egov/wiki/Linked_Open_Data) (LOD) op het web of voor het raadplegen van [RDF](https://www.w3.org/RDF/) triplestores. 

## SPARQL Recipes

### return all titles
deze SPARQL query vraagt 100 titels op van objecten uit de LDES van het Design Museum Gent 

```sparql
SELECT ?title FROM <http://stad.gent/ldes/dmg> 
WHERE { 
  ?object <http://www.cidoc-crm.org/cidoc-crm/P102_has_title> ?title
} LIMIT 100
```


http://query.linkeddatafragments.org/#datasources=https%3A%2F%2Fstad.gent%2Fsparql&query=SELECT%20*%20FROM%20%3Chttp%3A%2F%2Fstad.gent%2Fldes%2Fdmg%3E%20WHERE%20%7B%20%3Fs%20%3Fp%20%3Fo%20%7D%20LIMIT%20100


