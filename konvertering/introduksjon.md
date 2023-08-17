---
category: 3#Konvertering
title: Introduksjon
order: 1
permalink: /konvertering/introduksjon
---

# Nytt endepunkt for [NVDB api SKRIV dokumentasjon](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

NVDB api SKRIV V3 dokumentasjon er flyttet til [https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

API'et blir jo aktivt vedlikeholdt og videreutviklet, så vi anbefaler sterkt at dere legger om til den nyeste versjonen av dokumentasjonen. 


## Introduksjon til konvertering

NVDB API Skriv tilbyr endepunkter som kan konvertere ulike filformater til endringssett:

* **SOSI-[GeoJSON](https://en.wikipedia.org/wiki/GeoJSON)**  
  Dette er en spesialisering av GeoJSON-formatet der "properties"-objektet under "feature" inneholder vegobjektets
  egenskaper med SOSI-navn fra datakatalogen.
  
* **[SOSI](https://no.wikipedia.org/wiki/SOSI-formatet)-NVDB**  
  SOSI er et generelt, tekstbasert, hierarkisk filformat for utveksling av digitale geodata, utviklet av Statens kartverk
  på slutten av 1980-tallet. Varianten SOSI-NVDB følger grunnstrukturen til SOSI, men benytter NVDBs objektkatalog i
  beskrivelsen av objektene. 

