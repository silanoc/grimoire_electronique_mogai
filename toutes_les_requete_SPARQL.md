#Toutes tes requêtes SPARQL que l'on trouve sur le site
_Date de création_ : 6 avril 2021
_Dernière mise à jour_ : 29 octobre 2021

Le site grimoire-electronique-mogai.fr a comme spécificité d'explorer les questions LGBTQI+ dans l'univers de wikidata. Pour cela des requêtes sont nécessaires.
Afin de les retrouver plus rapidement pour les adapter à d'autres recherches, je les regroupe toutes ici.
Je ne publie ici que les requêtes, pas les interpétations.
Elles sont rangées par article du site.

## Où dans le monde participer à une marche des fiertés ?

### Où ? Localisation les marches sur une carte

Trouve tous les éléments dont la nature (P31) est "marche des fiertés LGBT" (Q51404). Pour ceux qui ont une "Localisation administrative" (P131) cherche en les "coordonnées géographiques" (P625).  Affiche sur la carte un point correspondant pour chacune. Affiche lorsqu'on clique sur un point une image (P18) si elle existe.
```
#defaultView:Map
#title:Carte avec toutes les marches des fiertés dans le monde. Inclus les différentes éditions et des images.
SELECT ?Marche_des_fiertes ?Marche_des_fiertesLabel ?localisation_administrativeLabel ?coordonnees_geographiques ?image WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  ?Marche_des_fiertes wdt:P31/wdt:279* wd:Q51404;
                      wdt:P131 ?localisation_administrative.
  ?localisation_administrative wdt:P625 ?coordonnees_geographiques .
  OPTIONAL {?Marche_des_fiertes wdt:P18 ?image.}
}
```

### Où ? C'est dans des grandes villes !

```
#defaultView:Map
#title:Marches des fiertés par taille de ville
SELECT ?Marche_des_fiertes ?Marche_des_fiertesLabel ?localisation_administrativeLabel ?population ?coordonnees_geographiques ?layer WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  ?Marche_des_fiertes (wdt:P31/(wdt:279*)) wd:Q51404;
                       wdt:P131 ?localisation_administrative.
  ?localisation_administrative wdt:P1082 ?population;
                               wdt:P625 ?coordonnees_geographiques.
  BIND(IF(?population < 1000 , "1000", IF(?population < 10000 , "10000", IF(?population < 100000 , "100000", IF(?population < 1000000 , "1000000", IF(?population < 10000000 , "10000000", "100000000"))))) AS ?layer)
}
```
### Où ? Cherchons les villes avec le plus de marche.
```
#defaultView:LineChart
SELECT (SAMPLE(?_villeLabel) AS ?_villeLabel) ?year (COUNT(?_ville) AS ?nombre_de_marche)  WHERE {
  ?item wdt:P31 wd:Q51404; 
        wdt:P585 ?_date;   
        wdt:P131 ?_ville.
  BIND(str(YEAR(?_ville)) AS ?year)
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en".
                          ?_ville rdfs:label ?_villeLabel.}
  FILTER(?_date >= "1950-00-00T00:00:00Z"^^xsd:dateTime)
}
GROUP BY ?year ?_villeLabel
```

### Quand ? Y-a t'il de plus en plus de marche des fiertés dans le monde ?

```
#defaultView:LineChart
SELECT ?année (COUNT(?date) AS ?nombre_de_marche ) WHERE {
?item wdt:P31 wd:Q51404;
wdt:P585 ?date;

BIND(str(YEAR(?date)) AS ?année)
SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en".}
FILTER (?date >= "1950-00-00T00:00:00Z"^^xsd:dateTime)
}
GROUP BY ?année
```
### Quand ? Les marches c'est quand il fait beau ?

```
#Compte par dd-mm le nombre de pride
#defaultView:BarChart
SELECT ?moisjour (COUNT(?pride) AS ?count) WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  ?pride wdt:P31 wd:Q51404;
    (p:P585/psv:P585) _:b40.
  _:b40 wikibase:timeValue ?date.
  BIND(CONCAT(STR(Month(?date)), "-", STR(day(?date))) AS ?moisjour)
  BIND(MONTH(?date) AS ?mois)
  BIND(DAY(?date) AS ?jour)
}
GROUP BY ?moisjour
ORDER BY (?mois) (?jour)
```
### Quoi ? Une marche des fiertés est-ce politique ?
```
#title: Mots d'ordre des marches des fiertés LGBTQI+
# le mot d'ordre étant la devise ou l'énoncé de la devise 
SELECT DISTINCT ?marche ?marcheLabel ?devise ?deviseLabel WHERE {  
  ?marche wdt:P31 wd:Q51404 ;
           wdt:P1451|wdt:P1546 ?devise .
 SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
```

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