# Datafangst

Datafangst er et web-basert system for innsending, kontroll, redigering, og på sikt direkte registrering i [NVDB](http://www.vegvesen.no/fag/Teknologi/Nasjonal+vegdatabank).
Systemet skal ta i mot - GPS-stedfestede data - for alle vegobjekter konstruert eller endret
 under et vegbyggingsprosjekt.

Entrepenører er pålagt å levere data fra vegprosjekter til Statens Vegvesen for registrering i NVDB. Data fra entrepenørene 
 må kvalitetssikret og etterbehandles før de kan skrives til NVDB.

I Datafangst kan entrepenører laste opp SOSI-filer og få dem validert mot [Datakatalogen](http://www.vegvesen.no/fag/Teknologi/Nasjonal+vegdatabank/Datakatalogen), 
men den endelige registreringen av dataene til NVDB må gjøres av dataforvaltere hos Statens Vegvesen.

Release notes for datafangst oppdateres sporadisk [her](https://www.vegdata.no/category/nvdb-api/release-notes/).

Tradisjonelt har data blitt levert på [SOSI-format](http://www.kartverket.no/sosi/). 
I tillegg til å støtte opplasting av SOSI-filer til NVDB og [FKB](http://www.kartverket.no/kart/kartdata/vektorkart/fkb/),
har Datafangst også et API for å sende inn vegobjekter som  [geoJSON](http://geojson.org) - dette APIet vil bli beskrevet i dette dokumentet.

## Definisjoner
Før vi beskriver normal arbeidsflyt i Datafangst og APIet definerer vi noen termer som er brukt i NVDB-domenet. Engelsk oversettelse i parantes.
* Kontrakt (contract) - Alle data hører til en kontrakt som representerer den faktiske kontrakten de innsamlede data tilhører. En kontrakt har 
 et navn, en [objektliste](http://www.vegvesen.no/fag/Teknologi/Nasjonal+vegdatabank/Objektliste), og flere valgfri felter for å stedfeste og beskrive den.
* Vegobjekt-type (featuretype) - Alle vegobjekter tilhører en vegobjekt-type. Denne er definert i Datakatalogen, og definerer alle 
 attributter objektet har, påkrevd-nivå, og relasjoner til andre vegobjekt-typer. Et eksempel på en vegobjekt-type er «Fartsgrense».
* Vegobjekt (feature) - en instans av en vegobjekt-type, for eksempel en enkelt fartsgrense.

## Roller og tilganger i Datafangst

### Dataforvalter
* Kun Vegvesenbrukere
* Kan opprette og konfigurere kontrakter
* Kan være kontraktseier
* Kan legge til andre brukere på en kontrakt
* Kan godkjenne og underkjenne innsendte data

### Medlem på kontrakt
* Både Vegvesenbrukere og eksterne brukere
* Kan sende inn data på eksisterende kontrakt
* Kan se innsendte data
* Kan redigere innsendte data
* Kan kommentere på innsendte data
* Kan se oversiktsstatus for innsendte data

Oppretting av datafangst bruker kan gjøres fritt, men for eksterne å bruke datafangst trenger en å være invitert på en kontrakt. Dersom
det er ønskelig å teste ut datafangst uten registrering kan dette gjøres med docker. Repository for datafangst og skrive-API finnes på 
https://hub.docker.com/u/nvdbapnevegdata/. Det er også mulig å få tildelt en test kontrakt, kontakt 
[Terje Brasethvik](mailto:terje.brasethvik@vegvesen.no) for dette. 


## Dataflyt i Datafangst
![Dataflyt i Datafangst](bilder/workflow.png)

Innsending av data og eventuell registrering av «Ferdigvegsdata» følger en definert arbeidsflyt som nå vil bli beskrevet.

1. Dataforvalter oppretter en kontrakt i webgrensesnittet.
2. Dataforvalter konfigurerer kontrakten ved å definere dens objektliste og hvilke brukere som skal ha tilgang til den.
3. Data lastes opp til kontrakten via nettleser eller API.
4. Innsendte data blir validert synkront. Webgrensesnittet poller etter endringer og viser en spinner, ved bruk at API 
 må en selv håndtere polling.
 De valideringer som blir gjort er blant andre attributt- og geometrivalidering mot Datakatalogen. 
  Det blir også utført automatisk stedfesting på vegnettet, men denne informasjonen er bare tilgjengelig for dataforvaltere 
  i webgrensesnittet,
5. Gjennomgang av innsendte data, dataforvalter går gjennom data i webgrensesnittet. Entrepenører er ikke påkrevd å levere alle
 attributter som er påkrevd for registrering i NVDB, disse attributtene må Dataforvalter legge til etter innsending. 
 Dataforvalter og andre prosjektdeltakere har mulighet til å legge til kommentarer på alle vegobjekter og vegobjekttyper, 
 samt overordnede kommentarer for kontrakten. Dataforvalter har mulighet til å markere kommentarer som feil som innsender 
 må ordne og deretter sende inn de aktuelle vegobjektene på nytt.
6. Om den automatiske stedfestingen av objekter ikke er konklusiv må dataforvalter utføre denne manuelt. Objekter som hører sammen 
(f eks en skiltplate sitter alltid på et skiltpunkt) må kobles sammen av dataforvalter. 
7. Registrering til NVDB. Når alle data er godkjent og eventuelle valideringsfeil er rettet kan de sendes til NVDB. 
 Denne opersjonen er ikke enda fullt støttet i Datafangst per oktober 2017, men den vil komme på neste release, som er godt på vei.
 
# Datafangst API
Datafangst har et API som støtter [geoJSON-formatet](#format).

## Forutsetninger
### KontraktID
Nåværende API-versjon støtter kun operasjoner på eksisterende kontrakter som er opprettet i webgrensesnittet.

### Brukernavn og passord 
Autentisering mot API skjer med brukernavn og passord. Brukeren må ha tilgang til operasjonen man ønsker å utføre på kontrakten. 
Brukere kan opprettes fritt, men for å få tilgang til en gitt kontrakt må brukeren legges til i denne kontraktens brukere. Dette gjøres 
i webgrensesnittet.

---

## Generelt for alle operasjoner
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

## Contract

### Hent kontrakter
List opp alle kontrakter

#### Mønster
```
GET /api/v1/contract
```

#### Forespørsel
Eksempel
```
GET /api/v1/contract HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```
#### Respons
````
HTTP/1.1 200 OK
````
Payload med kontrakter med id og navn

---

### Hent kontrakt
Informasjon om kontrakten

#### Mønster
```
GET /api/v1/contract/{contractId}
```

#### Forespørsel
Eksempel
```
GET /api/v1/contract/52fbcce9-ccb9-4f50-8bcd-0047f85038e8 HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```
#### Respons
````
HTTP/1.1 200 OK
````
Payload med informasjon om kontrakten

---

## Feature Collection

### Hent feature collections for kontrakt
FeatureCollections i kontrakt
 
#### Mønster
 ```
 GET /api/v1/contract/{contractId}/featurecollection
 ```
 
#### Forespørsel
Eksempel
 ```
 GET /api/v1/contract/52fbcce9-ccb9-4f50-8bcd-0047f85038e8/featurecollection HTTP/1.1
 Host: datafangst.kantega.no
 Authorization: Basic *********
 ```
#### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med alle featurecollections i kontrakten
 
---

### Hent feature collection	
Henter oppgitt feature collection
 
#### Mønster
 ```
 GET /api/v1/contract/{contractId}/featurecollection/{collectionId}
 ```
 
#### Forespørsel
Eksempel
 ```
GET /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/fc7b536b-eba2-47a3-82d4-4ae3a0f59883 HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
 ```
#### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med feature collection som  [geoJSON](#format)

---

### Post feature collection til kontrakt
 POST en komplett «feature collection» til en kontrakt. 
 Behandling og validering tar noe tid, derfor er denne operasjonen asynkron.
  
#### Mønster
 
  ```
  POST /api/v1/contract/{contractId}/featurecollection
  ```
 
  
#### Forespørsel
 Eksempel
  ```
 POST /api/v1/contract/52fbcce9-ccb9-4f50-8bcd-0047f85038e8/featurecollection HTTP/1.1
 Host: datafangst.kantega.no
 Content-Type: application/geo+json
 Authorization: Basic *********
  ```
##### Body
 Feature collection som [geoJSON](#format)
 
#### Respons
  ````
  HTTP/1.1 202 Accepted
  ````
 Payload med URI for polling av status.
  
 
---
### Erstatt feature collection	
Erstatt den oppgitte feature collection
 
#### Mønster
 ```
PUT /api/v1/contract/{contractId}/featurecollection/{collectionId}
 ```
 
#### Forespørsel
Eksempel
 ```
PUT /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/fc7b536b-eba2-47a3-82d4-4ae3a0f59883 HTTP/1.1
Host: datafangst.kantega.no
Content-Type: application/geo+json
Authorization: Basic *********
```
##### Body
Feature collection som [geoJSON](#format)

#### Respons
 ````
 HTTP/1.1 202 Accepted
 ````
Payload med URI for polling av status.
 
---

### Hent status for innsendt feature collection
Hent prosesseringsstatus for innsendt feature collection. 
 
#### Mønster
 ```
GET /api/v1/contract/{contractId}/featurecollection/{collectionId}/status
 ```
 
#### Forespørsel
Eksempel
 ```
GET /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/fc7b536b-eba2-47a3-82d4-4ae3a0f59883/status HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```


#### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload. Respons er json eller xml avhengig av klientens *Accept*-header. 
 
---

## Feature

### Hent feature
Hent et enkelt vegobjekt.
 
#### Mønster
 ```
GET /api/v1/contract/{contractId}/featurecollection/{featurecollectionid}/feature/{featureId}
 ```
 
#### Forespørsel
Eksempel
 ```
GET /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/feature/52073785-7f8f-4746-9607-6e70e6d8651e HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```

#### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med vegobjekt som geoJSON feature
 
---

### Post feature
POST et enkelt vegobjekt som geoJSON-feature.
 
#### Mønster
 ```
POST /api/v1/contract/{contractId}/featurecollection/{featurecollectionid}/feature/
 ```
 
#### Forespørsel
Eksempel
 ```
POST /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/feature/ HTTP/1.1
Host: datafangst.kantega.no
Content-Type: application/geo+json
Authorization: Basic *********
```
##### Body
[geoJSON](#format) feature

#### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med vegobjekt som geoJSON feature
 
---
 
### Erstatt feature
Erstatt oppgitt vegobjekt
 
#### Mønster
 ```
PUT /api/v1/contract/{contractId}/featurecollection/{featurecollectionid}/feature/{featureId}
 ```
 
#### Forespørsel
Eksempel
 ```
PUT /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/feature/52073785-7f8f-4746-9607-6e70e6d8651e HTTP/1.1
Host: datafangst.kantega.no
Content-Type: application/geo+json
Authorization: Basic *********
```
##### Body
[geoJSON](#format) feature

#### Respons
 ````
 HTTP/1.1 200 OK
 ````
Payload med vegobjekt som geoJSON feature
 
---

### Slett feature
Slett oppgitt vegobjekt
 
#### Mønster
 ```
DELETE /api/v1/contract/{contractId}/featurecollection/{featurecollectionid}/feature/{featureId}
 ```
 
#### Forespørsel
Eksempel
 ```
Delete /api/v1/contract/e853091c-5eef-4879-9352-a384c1ea68f4/featurecollection/feature/52073785-7f8f-4746-9607-6e70e6d8651e HTTP/1.1
Host: datafangst.kantega.no
Authorization: Basic *********
```

#### Respons
 ````
 HTTP/1.1 204 No content
 ````
HTTP status 204 returneres om sletting var vellykket.
 
---
  
## Format
Datafangst-APIet bruker [geoJSON](https://tools.ietf.org/html/rfc7946) som payload-format. 
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
Geometriseksjonen i dette objektet er standard geoJSON. *properties* delen av objektet inneholder de attributtene som hører til
vegobjektet.
### Properties
* tag (påkrevd)- Dette er et navn på vegobjektet som er ment brukt for å gjøre det lettere å referere til objekter, og lett identifisere 
dem med et lettlest navn. 
* dataCatalogVersion - Hvilken versjon av Datakatalogen som er brukt som grunnlag ved opprettelse av objektet. Siste versjonsnummer
 finner man på https://www.vegvesen.no/nvdb/api/v2/status.json. Datakatalogdefinisjoner er tilgjengelige på https://www.vegvesen.no/nvdb/api/v2/vegobjekttyper/{FeatureTypeId}.json og http://labs.vegdata.no/nvdb-datakatalog
* typeId (påkrevd) - Id for vegobjekttypen dette vegobjektet er en instans av
* comment - kommentar for vegobjektet
* attributes (påkrevd) - NVDB-attributtene for vegobjektet på format "attributtid" : "verdi". Datakatalogversjonen definerer hva som er påkrevde 
attributter, så dersom påkrevde attributter som mangler vil gi valideringsfeil ved Datakatalog-validering.
 
### Levering av featureCollection med FKB
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
 
[Postman collection for API-operasjoner](https://www.getpostman.com/collections/ef3fc73342f94df0585d)
