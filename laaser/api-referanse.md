---
category: 5#Låser
title: API-referanse
order: 2
permalink: /laaser/api-referanse
---

# Nytt endepunkt for [NVDB api SKRIV dokumentasjon](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

NVDB api SKRIV V3 dokumentasjon er flyttet til [https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

API'et blir jo aktivt vedlikeholdt og videreutviklet, så vi anbefaler sterkt at dere legger om til den nyeste versjonen av dokumentasjonen. 


## API-referanse for låser

### Innhold

[Hente lås](#hente-lås)  
[Søke etter låser](#søke-etter-låser)  
<br/>


### Hente lås

Henter ut detaljert beskrivelse av en lås i NVDB.

#### Mønster

```
GET /nvdb/apiskriv/rest/v1/lås/{id}
```

#### Request

##### Parametere

Ingen.

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|UUID|Angir unik korrelasjonsidentifikator for requesten.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v1/lås/17900713 HTTP/1.1
Accept: application/xml
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml

##### Payload

Entitet av type [Lås](https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/lås/lås.xsd).

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<lås xmlns="http://nvdb.vegvesen.no/apiskriv/domain/lock/v1" id="17900713">
  <oppdragId>3466724</oppdragId>
  <eier>
    <brukernavn>olanor</brukernavn>
    <fulltNavn>Ola Nordmann</fulltNavn>
  </eier>
  <opprettet>2020-07-14T08:32:07Z</opprettet>
  <opphav>KLASSISK_API</opphav>
  <vegnettlås>false</vegnettlås>
  <vegobjekttyper>
    <vegobjekttype typeId="852">
      <låstype>NORMAL</låstype>
      <lokasjonrelevans>VEGLENKESEKVENS</lokasjonrelevans>
    </vegobjekttype>
    <vegobjekttype typeId="446">
      <låstype>SEKUNDÆR</låstype>
      <lokasjonrelevans>VEGLENKESEKVENS</lokasjonrelevans>
    </vegobjekttype>
  </vegobjekttyper>
  <lokasjon>
    <veglenkesekvens id="921808" fra="0.16808652" til="1.0"/>
    <veglenkesekvens id="921809" fra="0.0" til="1.0"/>
    <veglenkesekvens id="921810" fra="0.0" til="1.0"/>
  </lokasjon>
</lås>
```
<br/>


### Søke etter låser

Henter en liste av låser i NVDB som tilfredsstiller angitte søkekriterier.

#### Mønster

```
GET /nvdb/apiskriv/rest/v1/lås
    ?antall={tall}
    &start={tall}
    &sorterPå={feltnavn}
    &sorterStigende={JA/NEI}
    &eier={navn}
    &oppdragId={tall}
    &blokkererEndringssettId={id}
```
 
#### Request

##### Parametere

Navn|Type|Beskrivelse
-|-|-
antall|Heltall|Angir ønsket antall låser i responsen (sidestørrelse). Maksimumsverdi er 1000. 
start|Heltall|Angir indeksverdi (1-basert) for første lås (sidestart).
sorterPå|Tekst|Angir sortingsnøkkel. Tillatte verdier: BRUKERNAVN og TID.
sorterStigende|Boolsk|Angir om låsene skal sorteres stigende.
eier|Tekst|Angir brukernavn til eier av ønskede låser. 
oppdragId|Heltall|Angir id til oppdraget som ønskede låser er knyttet til.
blokkererEndringssettId|UUID|Angir id til endringssettet som ønskede låser blokkerer.

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|UUID|Angir unik korrelasjonsidentifikator for requesten.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v1/lås?antall=1&eier=olanor HTTP/1.1
Accept: application/xml
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml

##### Payload

Entitet av type [LåsListe](https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/lås/lås-liste.xsd).

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<låsliste xmlns="http://nvdb.vegvesen.no/apiskriv/domain/lock/v1" antallTotalt="43">
  <lås id="17900713">
    <oppdragId>3466724</oppdragId>
    <eier>
      <brukernavn>olanor</brukernavn>
      <fulltNavn>Ola Nordmann</fulltNavn>
    </eier>
    <opprettet>2020-07-14T08:32:07Z</opprettet>
    <opphav>KLASSISK_API</opphav>
    <vegnettlås>false</vegnettlås>
    <vegobjekttyper>
      <vegobjekttype typeId="852">
        <låstype>NORMAL</låstype>
        <lokasjonrelevans>VEGLENKESEKVENS</lokasjonrelevans>
      </vegobjekttype>
      <vegobjekttype typeId="446">
        <låstype>SEKUNDÆR</låstype>
        <lokasjonrelevans>VEGLENKESEKVENS</lokasjonrelevans>
      </vegobjekttype>
    </vegobjekttyper>
    <lokasjon>
      <veglenkesekvens id="921808" fra="0.16808652" til="1.0"/>
      <veglenkesekvens id="921809" fra="0.0" til="1.0"/>
      <veglenkesekvens id="921810" fra="0.0" til="1.0"/>
    </lokasjon>
  </lås>
</låsliste>
```
