---
category: 5#Låser
title: API-referanse
order: 2
permalink: /laaser/api-referanse
---

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
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken
X-Client|Tekst|Angir navnet på klientapplikasjonen.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v1/lås/17900713 HTTP/1.1
Accept: application/xml
Cookie: iPlanetDirectoryOAM=AQIC5wM2LY4SfczXL0v42tkJK__EjrzGGl9PTJsJMYKuzLo
X-Client: MinKlientApplikasjon
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml

##### Payload

Entitet av type [Lås](https://www.vegvesen.no/nvdb/apiskriv/rest/v1/lås/lås.xsd).

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

Henter en liste av låser i NVDB som tilfredstiller angitte søkekriterier.

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
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken
X-Client|Tekst|Angir navnet på klientapplikasjonen.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v1/lås?antall=1&eier=olanor HTTP/1.1
Accept: application/xml
Cookie: iPlanetDirectoryOAM=AQIC5wM2LY4SfczXL0v42tkJK__EjrzGGl9PTJsJMYKuzLo
X-Client: MinKlientApplikasjon
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml

##### Payload

Entitet av type [LåsListe](https://www.vegvesen.no/nvdb/apiskriv/rest/v1/lås/lås-liste.xsd).

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
