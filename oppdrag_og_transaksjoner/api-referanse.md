---
category: 6#Oppdrag og transaksjoner
title: API-referanse
order: 2
permalink: /oppdrag-og-transaksjoner/api-referanse
---

## API-referanse for oppdrag og transaksjoner

### Innhold

[Hente oppdrag](#hente-oppdrag)  
[Søke etter oppdrag](#søke-etter-oppdrag)  
[Søke etter transaksjoner](#søke-etter-transaksjoner)  
<br/>


### Hente oppdrag

Henter ut detaljert beskrivelse av et oppdrag i NVDB.

#### Mønster

```
GET /nvdb/apiskriv/rest/v1/oppdrag/{id}
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
GET /nvdb/apiskriv/rest/v1/oppdrag/3466724 HTTP/1.1
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

Entitet av type [Oppdrag](https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oppdrag/oppdrag.xsd).

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<oppdrag xmlns="http://nvdb.vegvesen.no/apiskriv/domain/task/v1" xmlns:ns2="http://nvdb.vegvesen.no/apiskriv/domain/common/v1" id="3466724">
  <typeId>24598</typeId>
  <navn>Fylkes- og kommunale veger</navn>
  <beskrivelse>Øvrige veger: bare for kommunale veger</beskrivelse>
  <sluttidspunkt>2016-09-20T07:13:31Z</sluttidspunkt>
  <bruker id="42">
    <brukernavn>olanor</brukernavn>
    <fulltNavn>Ola Nordmann</fulltNavn>
  </bruker>
  <tjenesteregel>VEGNETTSOPPDRAG</tjenesteregel>
  <ressurser>
    <ressurs rel="self" src="https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oppdrag/3466724"/>
    <ressurs rel="transaksjoner" src="https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/transaksjon?oppdragId=3466724"/>
  </ressurser>
</oppdrag>
```
<br/>


### Søke etter oppdrag

Henter en liste av oppdrag i NVDB som tilfredsstiller angitte søkekriterier.

#### Mønster

```
GET /nvdb/apiskriv/rest/v1/oppdrag
    ?antall={tall}
    &start={tall}
    &sorterPå={feltnavn}
    &sorterStigende={JA/NEI}
    &fraDato={dato}
    &eier={navn}
    &tjenesteregel={kode}
```
 
#### Request

##### Parametere

Navn|Type|Beskrivelse
-|-|-
antall|Heltall|Angir ønsket antall oppdrag i responsen (sidestørrelse). Maksimumsverdi er 1000. 
start|Heltall|Angir indeksverdi (1-basert) for første oppdrag (sidestart).
sorterPå|Tekst|Angir sortingsnøkkel. Tillatte verdier: BRUKERNAVN og TID.
sorterStigende|Boolsk|Angir om oppdragene skal sorteres stigende.
fraDato|Dato|Angir tidligste dato for ønskede oppdrag. 
eier|Tekst|Angir brukernavn til eier av ønskede oppdrag. 
tjenesteregel|Tekst|Angir tjenesteregel til ønskede oppdrag. Gyldige verdier defineres av typen [Tjenesteregel](https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oppdrag/tjenesteregel.xsd).

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
GET /nvdb/apiskriv/rest/v1/oppdrag?antall=1&eier=olanor HTTP/1.1
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

Entitet av type [OppdragListe](https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oppdrag/oppdrag-liste.xsd).

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<oppdragliste xmlns="http://nvdb.vegvesen.no/apiskriv/domain/task/v1" xmlns:ns2="http://nvdb.vegvesen.no/apiskriv/domain/common/v1" antallTotalt="4951115">
  <oppdrag id="3466724">
    <typeId>24598</typeId>
    <navn>Fylkes- og kommunale veger</navn>
    <beskrivelse>Øvrige veger: bare for kommunale veger</beskrivelse>
    <sluttidspunkt>2016-09-20T07:13:31Z</sluttidspunkt>
    <bruker id="42">
      <brukernavn>olanor</brukernavn>
      <fulltNavn>Ola Nordmann</fulltNavn>
    </bruker>
    <tjenesteregel>VEGNETTSOPPDRAG</tjenesteregel>
    <ressurser>
      <ressurs rel="self" src="https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oppdrag/3466724"/>
      <ressurs rel="transaksjoner" src="https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/transaksjon?oppdragId=3466724"/>
    </ressurser>
  </oppdrag>
</oppdragliste>
```
<br/>


### Søke etter transaksjoner

Henter en liste av transaksjoner i NVDB som tilfredsstiller angitte søkekriterier.

#### Mønster

```
GET /nvdb/apiskriv/rest/v1/transaksjon
    ?antall={tall}
    &start={tall}
    &sorterPå={feltnavn}
    &sorterStigende={JA/NEI}
    &endringssettId={id}
    &oppdragId={id}
    &vegobjektId={id}
    &nettelementId={id}
    &fraDato={dato}
    &eier={navn}
```

#### Request

##### Parametere

Navn|Type|Beskrivelse
-|-|-
antall|Heltall|Angir ønsket antall transaksjoner i responsen (sidestørrelse). Maksimumsverdi er 1000. 
start|Heltall|Angir indeksverdi (1-basert) for første transaksjon (sidestart).
sorterPå|Tekst|Angir sortingsnøkkel. Tillatte verdier: BRUKERNAVN og TID.
sorterStigende|Boolsk|Angir om transaksjonene skal sorteres stigende.
endringssettId|UUID|Angir id for endringssettet en ønsker transaksjonen for. 
oppdragId|Heltall|Angir id for oppdraget en ønsker transaksjonene for. 
vegobjektId|Heltall|Angir id for vegobjektet en ønsker transaksjonene for. 
nettelementId|Heltall|Angir id for nettelementet en ønsker transaksjonene for. 
fraDato|Dato|Angir tidligste dato for ønskede transaksjoner. 
eier|Tekst|Angir brukernavn til eier av ønskede transaksjoner. 

Minst én av parametrene ```endringssettId```, ```oppdragId```, ```vegobjektId``` og ```nettelementId``` må angis. Men
parameter ```endringssettId``` og ```oppdragId``` kan ikke brukes samtidig. Samme begrensning gjelder for parameter
```vegobjektId``` og ```nettelementId```

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
GET /nvdb/apiskriv/rest/v1/transaksjon?antall=1&eier=olanor HTTP/1.1
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

Entitet av type [TransaksjonListe](https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/transaksjon/transaksjon-liste.xsd).

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<transaksjoner xmlns="http://nvdb.vegvesen.no/apiskriv/domain/transaction/v1" xmlns:ns2="http://nvdb.vegvesen.no/apiskriv/domain/common/v1" antallTotalt="1">
  <transaksjon>
    <oppdragId>3466724</oppdragId>
    <tidspunkt>2016-09-20T09:12:46</tidspunkt>
    <brukernavn>olanor</brukernavn>
    <objekttyper>
      <objekttype>
        <typeId>83</typeId>
        <objekttype>VEGOBJEKT</objekttype>
        <objekter>
          <objekt>
            <objektId>737948714</objektId>
            <transaksjonstype>NY</transaksjonstype>
            <objektversjon>1</objektversjon>
            <startdato>2016-09-20</startdato>
            <ressurser>
              <ressurs rel="objekt" src="https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/les/vegobjekt/83/737948714/1"/>
            </ressurser>
          </objekt>
        </objekter>
      </objekttype>
    </objekttyper>
    <ressurser>
      <ressurs rel="oppdrag" src="https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oppdrag/3466723"/>
    </ressurser>
  </transaksjon>
</transaksjoner>
```
