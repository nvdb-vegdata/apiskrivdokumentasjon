---
category: 99#Datafangst
title: Datafangst-API
order: 2
permalink: /datafangst/datafangst-api
---
 
## Datafangst-API
Datafangst har et API som støtter [geoJSON-formatet](#format).

### Forutsetninger
#### KontraktID
Nåværende API-versjon støtter kun operasjoner på eksisterende kontrakter som er opprettet i webgrensesnittet.

#### Brukernavn og passord 
Autentisering mot API skjer med brukernavn og passord. Brukeren må ha tilgang til operasjonen man ønsker å utføre på kontrakten. 
Brukere kan opprettes fritt, men for å få tilgang til en gitt kontrakt må brukeren legges til i denne kontraktens brukere. Dette gjøres 
i webgrensesnittet.

---

### Generelt for alle operasjoner
**Responser:**

Se beskrivelse per operasjon for hvilken status som angir vellykket innsending.

Ved feilsituasjoner vil du kunne få følgende status-responser:

* 404: Alle operasjoner returnerer med 404 Not found dersom objektet med den oppgitte id ikke finnes. 
* 401: Om brukeren ikke er innlogget returneres 401 unauhtorized
* 403: Om brukeren ikke har tilgang til kontrakten returneres 403 Forbidden. 
* 400: Dersom innsendt payload ikke er velformet geoJSON vil 400 Bad request bli returnert. 

**Forespørsler:**


Alle POST og PUT må ha headeren Content-Type: application/geo+json
```
Content-Type: application/geo+json
```
 
---

### Contract

#### Hent kontrakter
List opp alle kontrakter

##### Mønster
```
GET /api/v1/contract
```

##### Forespørsel
Eksempel
```
GET /api/v1/contract HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```
##### Respons
````
HTTP/1.1 200 OK
````
Payload med kontrakter med id, navn og arkiveringsstatus

---

#### Hent kontrakt
Informasjon om kontrakten

##### Mønster
```
GET /api/v1/contract/{contractId}
```

##### Forespørsel
Eksempel
```
GET /api/v1/contract/52fbcce9-ccb9-4f50-8bcd-0047f85038e8 HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```
##### Respons
````
HTTP/1.1 200 OK
````
Payload med informasjon om kontrakten

---

### Feature Collection

#### Hent feature collections for kontrakt
FeatureCollections i kontrakt
 
##### Mønster
 ```
 GET /api/v1/contract/{contractId}/featurecollection
 ```
 
##### Parameter
`destination` kan benyttes for å hente FeatureCollections fra `NVDB` eller `FKB`. Dersom parameteret ikke benyttes returneres alle FeatureCollections for kontrakten, altså både fra `NVDB` og `FKB`.
 ```
 GET /api/v1/contract/{contractId}/featurecollection?destination=FKB
 ```
 
##### Forespørsel
Eksempel
 ```
 GET /api/v1/contract/52fbcce9-ccb9-4f50-8bcd-0047f85038e8/featurecollection HTTP/1.1
 Host: datafangst.kantega.no
 Authorization: Basic *********
 ```
##### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med alle featurecollections i kontrakten
 
---

#### Hent feature collection	
Henter oppgitt feature collection
 
##### Mønster
 ```
 GET /api/v1/contract/{contractId}/featurecollection/{collectionId}
 ```
 
##### Forespørsel
Eksempel
 ```
GET /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/fc7b536b-eba2-47a3-82d4-4ae3a0f59883 HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
 ```
##### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med feature collection som  [geoJSON](#format)

---

#### Post feature collection til kontrakt
 POST en komplett «feature collection» til en kontrakt. 
 Behandling og validering tar noe tid, derfor er denne operasjonen asynkron.
  
##### Mønster
 
  ```
  POST /api/v1/contract/{contractId}/featurecollection
  ```
 
  
##### Forespørsel
 Eksempel
  ```
 POST /api/v1/contract/52fbcce9-ccb9-4f50-8bcd-0047f85038e8/featurecollection HTTP/1.1
 Host: datafangst.kantega.no
 Content-Type: application/geo+json
 Authorization: Basic *********
  ```
###### Body
 Feature collection som [geoJSON](#format)
 
##### Respons
  ````
  HTTP/1.1 202 Accepted
  ````
 Payload med URI for polling av status.
  
 
---
#### Erstatt feature collection	
Erstatt den oppgitte feature collection
 
##### Mønster
 ```
PUT /api/v1/contract/{contractId}/featurecollection/{collectionId}
 ```
 
##### Forespørsel
Eksempel
 ```
PUT /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/fc7b536b-eba2-47a3-82d4-4ae3a0f59883 HTTP/1.1
Host: datafangst.kantega.no
Content-Type: application/geo+json
Authorization: Basic *********
```
###### Body
Feature collection som [geoJSON](#format)

##### Respons
 ````
 HTTP/1.1 202 Accepted
 ````
Payload med URI for polling av status.
 
---

#### Hent status for innsendt feature collection
Hent prosesseringsstatus for innsendt feature collection. 
 
##### Mønster
 ```
GET /api/v1/contract/{contractId}/featurecollection/{collectionId}/status
 ```
 
##### Forespørsel
Eksempel
 ```
GET /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/fc7b536b-eba2-47a3-82d4-4ae3a0f59883/status HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```


##### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload. Respons er json eller xml avhengig av klientens *Accept*-header. 
 
---

### Feature

#### Hent feature
Hent et enkelt vegobjekt.
 
##### Mønster
 ```
GET /api/v1/contract/{contractId}/featurecollection/{featurecollectionid}/feature/{featureId}
 ```
 
##### Forespørsel
Eksempel
 ```
GET /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/feature/52073785-7f8f-4746-9607-6e70e6d8651e HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```

##### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med vegobjekt som geoJSON feature
 
---

#### Post feature
POST et enkelt vegobjekt som geoJSON-feature.
 
##### Mønster
 ```
POST /api/v1/contract/{contractId}/featurecollection/{featurecollectionid}/feature/
 ```
 
##### Forespørsel
Eksempel
 ```
POST /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/feature/ HTTP/1.1
Host: datafangst.kantega.no
Content-Type: application/geo+json
Authorization: Basic *********
```
###### Body
[geoJSON](#format) feature

##### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med vegobjekt som geoJSON feature
 
---
 
#### Erstatt feature
Erstatt oppgitt vegobjekt
 
##### Mønster
 ```
PUT /api/v1/contract/{contractId}/featurecollection/{featurecollectionid}/feature/{featureId}
 ```
 
##### Forespørsel
Eksempel
 ```
PUT /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/feature/52073785-7f8f-4746-9607-6e70e6d8651e HTTP/1.1
Host: datafangst.kantega.no
Content-Type: application/geo+json
Authorization: Basic *********
```
###### Body
[geoJSON](#format) feature

##### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med vegobjekt som geoJSON feature
 
---

#### Slett feature
Slett oppgitt vegobjekt
 
##### Mønster
 ```
DELETE /api/v1/contract/{contractId}/featurecollection/{featurecollectionid}/feature/{featureId}
 ```
 
##### Forespørsel
Eksempel
 ```
Delete /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/feature/52073785-7f8f-4746-9607-6e70e6d8651e HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```

##### Respons
 ````
 HTTP/1.1 204 No content
 ````
HTTP status 204 returneres om sletting var vellykket.
 
---
  
### Format
Datafangst-APIet bruker en JSON-struktur som følger 
[geoJSON](https://tools.ietf.org/html/rfc7946), med et sett med egenskaper under "properties" som er spesifikke 
til Datafangst. Se under for et enkelt eksempel. 
```json
{
"type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [ [10.39241731, 63.43053048],
            [10.39495434, 63.43043698],
            [10.39579151, 63.42898665],
            [10.39272171, 63.42909269],
            [10.39241731, 63.43053048] ]
        ]
      },
      "properties": {
        "tag": "Forsterkningslag#1",
        "dataCatalogVersion": "2.06",
        "typeId": 227,
        "comment": "Usikker på måledatoen",
        "attributes": {
          "5543": "20160802"
         }
       }
    }
  ]
}
```
Geometriseksjonen i dette objektet er standard geoJSON. *properties*-delen av objektet inneholder de attributtene som hører til
vegobjektet.

#### Eksisterende objekter i NVDB
Det er mulig å både laste opp nye vegobjekter, og å legge inn eksisterende vegobjekter i NVDB med endringer gjennom API.
Det er den samme strukturen som brukes, men det er noe variasjon i hvilke properties som må eller kan oppgis.

For eksisterende vegobjekter er det valgfritt å oppgi "geometry"-elementet. Hvis dette ikke oppgis brukes geometri fra NVDB, og hvis "geometry" er oppgitt vil den erstatte eksisterende geometri i NVDB når vegobjektet sendes inn til NVDB.

#### Geometri
Definisjon av geometri følger GeoJSON-standarden, men type geometri må være en type som er tillatt for vegobjekttypen i henhold til Datakatalogen. 

Datafangst har tidligere godtatt import av multigeometrier der Datakatalogen bare spesifiserer enkeltgeometrier, men dette vil bli avvist som valideringsfeil fra desember 2020 eller januar 2021.

#### Properties

Følgende egenskaper kan defineres under "properties" i et geoJson-objekt.

##### tag (påkrevd)
Dette er et navn på vegobjektet som er ment brukt for å gjøre det lettere å referere til objekter,
og lett identifisere dem med et lettlest navn. Navnet vises for bruker i datafangst, og vil bli brukt 
i feilmeldinger fra API.


##### dataCatalogVersion
Hvilken versjon av som er brukt som grunnlag ved opprettelse av objektet. Siste versjonsnummer
finner man på https://nvdbapiles-v3.atlas.vegvesen.no/status.json. 
Datakatalog-definisjoner er tilgjengelige på https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/{typeId}.json 
og http://labs.vegdata.no/nvdb-datakatalog.

 
##### typeId (påkrevd)
ID for vegobjekttypen dette vegobjektet er en instans av, f.eks. "96" for skiltplate.


##### nvdbId (påkrevd for eksisterende vegobjekter)
ID i NVDB for vegobjekt som skal importeres. Må være unik innenfor en kontrakt.


##### nvdbVersion (påkrevd for eksisterende vegobjekter)
Versjon av NVDB-objekt som skal importeres. Må være siste versjon, Datafangst har ingen mekanismer for å håndtere konflikt med nyere versjoner.


##### operation (påkrevd for eksisterende vegobjekter)
Operasjon i Datafangst for vegobjekt. For nye vegobjekter er denne implisitt "CREATE", og den trenger ikke å oppgis. 
Mulige verdier er "CORRECT" (rette feil i en versjon), "UPDATE" (ny versjon i NVDB) og "CLOSE" (lukk i NVDB, tidligere omtalt som "slett"). I tillegg er "DELETE" et deprekert synonym for "CLOSE".


##### verticalDatum 
Benyttet høydesystem (vertikal datum) for vegobjektet. Gjeldende versjon av Datafangst støtter _NN54_ og _NN2000_ som verdier for høydesystem. Om høydesystem ikke angis, tolkes det som _NN2000_.


##### comment
Kommentar for vegobjektet.


##### attributes (påkrevd for nye vegobjekter)
NVDB-attributtene for vegobjektet på format "attributtid" : "verdi". Datakatalogversjonen definerer hva som er påkrevde 
attributter, så dersom påkrevde attributter som mangler vil gi valideringsfeil ved Datakatalog-validering. 


For eksisterende vegobjekter er dette elementet valgfritt. Hvis det ikke oppgis vil eksisterende attributter fra NVDB brukes.
Hvis denne er oppgitt for et eksisterende vegobjekt vil objektet importeres med de attributter som er oppgitt, 
samt andre eksisterende attributter fra NVDB dersom objektet har andre attributter som ikke nevnes spesifikt i 
innsendingen. Det er altså kun endringer som skal oppgis her.


##### geometryAttributtes

En egen struktur som beskriver kvalitet og andre egenskaper for geometri. Denne bør oppgis ved ny eller endret geometri.

Alle feltene er definert i Datakatalogen med lovlige verdier, se lenker på hvert enkelt element under. Hvis ikke annet er 
oppgitt er det den numeriske verdien som skal oppgis, men som en streng. Se eksempel 
"[Nytt objekt med geometriegenskaper](#nytt-objekt-med-geometriegenskaper)".

* captureDate - Dato for måling / "datafangstdato". Dato på ISO 8601-format, f.eks. "2020-03-04"
* lenght - Lengde på geometri, vil bli beregnet ved innsending til NVDB hvis den ikke oppgis
* measurementMethod - [Målemetode for geometri](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9543.json?pretty=true)
* measurementMethodHeight - [Målemetode for høyde](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9544.json?pretty=true), hvis den er forskjellig fra grunnriss
* accuracy - [Nøyaktighet](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9551.json?pretty=true) på måling, i cm
* accuracyHeight - [Nøyaktighet for høyde](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9552.json?pretty=true) i cm, hvis den avviker fra nøyaktighet for grunnriss
* visibility - [Synbarhet](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9545.json?pretty=true) / synlighet i terrenget
* tolerance - [Maksimalt avvik](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9783.json?pretty=true)
* themeCode - [Temakode](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9784.json?pretty=true)
* medium - [Medium](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9792.json?pretty=true)
* municipality - [Kommunenummer](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9789.json?pretty=true)
* heightRef - [Referanse for høydemåling](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9546.json?pretty=true)
* referenceGeometry - Angir om geometri er [referansegeometri](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9547.json?pretty=true) , med verdiene "true" eller "false" (default)
 
#### Levering av featureCollection med FKB
For å sende inn FKB-objekter må request-parameter `destination=FKB` legges til, og for hver feature droppes `dataCatalogVersion`
og `typeName` brukes for for hver feature i stedet for `typeId`.

`POST /api/v1/contract/{contractId}/featurecollection?destination=FKB`
```json
{
"type": "FeatureCollection",
  "features": [
    {
      ... ,
      "properties": {
        "tag": "Stikkrenne#1",
        "typeName": "Stikkrenne",
        "comment": "Usikker på måledatoen",
        "attributes": {
          ...
         }
       }
    }
  ]
}
```
#### Eksempler

##### Nytt objekt med geometriegenskaper
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [ [10.39241731, 63.43053048],
            [10.39495434, 63.43043698],
            [10.39579151, 63.42898665],
            [10.39272171, 63.42909269],
            [10.39241731, 63.43053048] ]
        ]
      },
      "properties": {
        "tag": "Forsterkningslag#1",
        "dataCatalogVersion": "2.06",
        "typeId": 227,
        "comment": "Usikker på måledatoen",
        "attributes": {
          "5543": "2016-08-02",
          "1212": "3677"
        },
        "geometryAttributes": {
          "captureDate": "2016-08-02",
          "length": "12.3",
          "measurementMethod": "96",
          "measurementMethodHeight": "96",
          "accuracy": "5",
          "accuracyHeight": "6",
          "visibility": "2",
          "tolerance": "4",
          "themeCode": "9999",
          "medium": "3",
          "municipality": "1601",
          "heightRef": "0",
          "referenceGeometry": "true"
        }
      }
    }
  ]
}
```

##### Eksisterende vegobjekt uten endringer
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {
        "tag": "Skiltpunkt#1",
        "dataCatalogVersion": "2.06",
        "typeId": 95,
        "nvdbId": 667990284,
        "nvdbVersion": 1,
        "operation": "UPDATE"
      }
    }
  ]
}
``` 

##### Postman-collection

Det finnes også en [Postman-collection](../assets/Datafangst_API.postman_collection.json) for alle operasjoner,
med eksempler på data for hver operasjon.
