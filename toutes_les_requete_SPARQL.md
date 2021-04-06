#Toutes tes requêtes SPARQL que l'on trouve sur le site
Date : 6 avril 2021

Le site grimoire-electronique-mogai.fr a comme spécificité d'explorer les questions LGBTQI+ dans l'univers de wikidata. Pour cela des requêtes sont nécessaires.
Afin de les retrouver plus rapidement pour les adapter à d'autres recherches, je les regroupe toutes ici.
Je ne publie ici que les requêtes, pas les interpétations.

C'est ranger par article du site.

## Bi et Pan dans la culture
article : http://grimoire-electronique-mogai.fr/bi-et-pan-dans-la-culture/
### Oeuvres
* Œuvre (Q386724) : objet physique ou virtuel résultant d’un travail anthropique, c’est-à-dire réalisé par l’humain
* les sous-classes d’œuvres : au 20/04/2020 cela correspond à plusieurs milliers de nature différente dont : algorithmes, vêtements, exposition, livres, CD… https://w.wiki/NWw
```
#Oeuvre dont le sujet ou thème principal est bisexualité ou pansexualité

SELECT DISTINCT ?oeuvre ?oeuvreLabel ?nature_de_l_elementLabel WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  VALUES ?orientation {
    wd:Q43200
    wd:Q271534
  }
  ?oeuvre wdt:P31*/wdt:P279* wd:Q386724.
  ?oeuvre  wdt:P921 ?orientation.  
  OPTIONAL { ?oeuvre wdt:P31 ?nature_de_l_element. }
}

ORDER BY ?oeuvreLabel
```
```
#Oeuvre qui dépeind est bisexualité ou pansexualité

SELECT ?oeuvre ?oeuvreLabel ?nature_de_l_elementLabel WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  VALUES ?orientation {
    wd:Q43200
    wd:Q271534
  }
  ?oeuvre (wdt:P31*/(wdt:P279*)) wd:Q386724.
  ?oeuvre  wdt:P180 ?orientation.  
  OPTIONAL { ?oeuvre wdt:P31 ?nature_de_l_element. }
}
```
```
#Oeuvre qui represente la bisexualité ou la pansexualité
SELECT ?oeuvre ?oeuvreLabel ?nature_de_l_elementLabel WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  VALUES ?orientation {
    wd:Q43200
    wd:Q271534
  }
  ?oeuvre (wdt:P31/(wdt:P279*)) wd:Q386724.
  ?oeuvre  wdt:P1268 ?orientation.  
  OPTIONAL { ?oeuvre wdt:P31 ?nature_de_l_element. }
}
```
### Personnages de fiction
Cherchons tous les éléments dont la nature et les sous-classes (P31/P279) sont personnage de fiction (Q95074) et dont l’orientation sexuelle (P91) est bisexualité (Q43200) ou pansexualité (Q271534). Regroupons dans une seule case toutes les œuvres où ils apparaissent (P1441).
```
SELECT 
 ?personnage_de_fiction ?personnage_de_fictionLabel
 (GROUP_CONCAT(DISTINCT (?present_dans_l_ouvreLabel); separator=", ") as ?personnage_deLabel) 
 (GROUP_CONCAT(DISTINCT (?present_dans_l_ouvre); separator=", ") as ?personnage_de) 

WHERE {
 SERVICE wikibase:label {
   bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en".
   ?present_dans_l_ouvre rdfs:label ?present_dans_l_ouvreLabel.
   ?personnage_de_fiction rdfs:label ?personnage_de_fictionLabel.
 }
 VALUES ?orientation {
   wd:Q43200
   wd:Q271534
 }
 ?personnage_de_fiction (wdt:P31/(wdt:P279*)) wd:Q95074;
   wdt:P91 ?orientation.
 OPTIONAL { ?personnage_de_fiction wdt:P1441 ?present_dans_l_ouvre. }
 OPTIONAL { ?personnage_de_fiction wdt:P91/wdt:P279 ?orientation_sexuelle. }
}

GROUP BY ?personnage_de_fiction ?personnage_de_fictionLabel
ORDER BY ?personnage_de_fictionLabel
```
### Liste des créataires bi ou pan
```
SELECT DISTINCT ?createur ?createurLabel WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  VALUES ?orientation {
    wd:Q43200
    wd:Q271534
  }
  ?createur (wdt:P106/wdt:P279) wd:Q2500638;
    wdt:P91 ?orientation.
}

ORDER BY ?createurLabel
```
#### Occupation des créataires
```
SELECT 
  ?occupation ?occupationLabel 
  (COUNT(?occupation) AS ?nombre_personne_par_occupation)
WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  VALUES ?orientation {
    wd:Q43200
    wd:Q271534
  }
  ?createur (wdt:P106/wdt:P279) wd:Q2500638;
    wdt:P91 ?orientation.
  OPTIONAL { ?createur wdt:P106 ?occupation. }
}
GROUP BY ?occupation ?occupationLabel 
ORDER BY DESC (?nombre_personne_par_occupation)
```
#### Nationalités
```
#defaultView:BubbleChart
SELECT 
  ?nationalite ?nationaliteLabel 
  (COUNT(?nationalite) AS ?nombre_personne_par_nationalite)
WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  VALUES ?orientation {
    wd:Q43200
    wd:Q271534
  }
  ?createur (wdt:P106/wdt:P279) wd:Q2500638;
    wdt:P91 ?orientation.
  OPTIONAL { ?createur wdt:P27 ?nationalite. }
}
GROUP BY ?nationalite ?nationaliteLabel  
ORDER BY DESC (?nombre_personne_par_nationalite)
```
#### Genre
```
SELECT 
  ?genre ?genreLabel 
  (COUNT(?genre) AS ?nombre_personne_par_genre)
WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  VALUES ?orientation {
    wd:Q43200
    wd:Q271534
  }
  ?createur (wdt:P106/wdt:P279) wd:Q2500638;
    wdt:P91 ?orientation.
  OPTIONAL { ?createur wdt:P21 ?genre. }
}
GROUP BY ?genre ?genreLabel
ORDER BY DESC (?nombre_personne_par_genre)
```