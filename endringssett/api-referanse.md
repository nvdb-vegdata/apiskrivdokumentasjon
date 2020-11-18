---
category: 1#Endringssett
title: API-referanse
order: 5
permalink: /endringssett/api-referanse
---

## API-referanse for endringssett

### Innhold

[Validere et endringssett](#validere-et-endringssett)  
[Registrere et endringssett](#registrere-et-endringssett)  
[Starte behandling av endringssett](#starte-behandling-av-endringssett)  
[Kansellere behandling av et endringssett](#kansellere-behandling-av-et-endringssett)  
[Restarte behandling av et endringssett](#restarte-behandling-av-et-endringssett)  
[Hente fremdriftskoden for et endringssett](#hente-fremdriftskoden-for-et-endringssett)  
[Hente fremdrifts- og årsakskoden for et endringssett](#hente-fremdrifts--og-årsakskoden-for-et-endringssett)  
[Hente status for et endringssett](#hente-status-for-et-endringssett)  
[Hente endringssett](#hente-endringssett)  
[Søke etter endringssett](#søke-etter-endringssett)  
<br/>


### Validere et endringssett

Dette kommando-endepunktet gjør det mulig å kontrollere innholdet i et endringssett syntaktisk før registrering og behandling.
Det gjøres bare validering av velformethet og om vegobjektene er beskrevet i tråd med datakatalogen.
Kontroll av relasjonelle styringsparametere og annen mer kompleks validering som krever sammenstilling med
eksisterende data i NVDB utføres ikke. Et endringssett som godkjennes av dette endepunktet vil derfor fortsatt kunne
avvises ved dypere validering under behandling.

Dette endepunktet gir synkron respons og medfører ingen endringer i NVDB.

#### Mønster

```
POST /nvdb/apiskriv/rest/v3/endringssett/validator
     ?skipLocation={JA|NEI}
     &skipAssociations={JA|NEI}
     &useObjectList={JA|NEI}
```

#### Request

##### Parametere

Navn|Type|Beskrivelse
-|-|-
skipLocation|Boolsk|Angir om stedfestingen skal valideres
skipAssociations|Boolsk|Angir om assosiasjoner skal valideres
useObjectList|Boolsk|Angir om validering skal gjøres mot objektlista i stedet for datakatalogen

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml
Accept|MediaType|Angir ønsket media-type for responsen: application/json eller application/xml. Content-Type benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Payload

Entitet av type [Endringssett](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

##### Eksempel

```xml
POST /nvdb/apiskriv/rest/v3/endringssett/validator HTTP/1.1
Content-Type: application/xml
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<endringssett xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <datakatalogversjon>2.20</datakatalogversjon> 
  <registrer>
    <vegobjekter>
      <vegobjekt typeId="581" tempId="tunnel#1">
        <gyldighetsperiode>
          <startdato>2020-01-01</startdato>
        </gyldighetsperiode>
        <egenskaper>
          <egenskap typeId="5225">
            <verdi>Grevlingtunnelen</verdi>
          </egenskap>
          <egenskap typeId="0">
            <verdi>xyz</verdi>
          </egenskap>
        </egenskaper>
        <stedfesting>
          <punkt veglenkesekvensNvdbId="1125766" posisjon="0.3"/>
        </stedfesting>
      </vegobjekt>
    </vegobjekter>
  </registrer>
</endringssett>
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml

##### Payload

Entitet av type [Status](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/status.xsd).

Dersom endringssettet ble godkjent vil subelementet ```<fremdrift>``` angi ```UTFØRT```. Dersom det ble funnet feil angis dette
med verdien ```AVVIST``` og feilene beskrives i detalj under subelementet ```<resultat>```. Uavhengig av valideringsresultat
vil HTTP-statuskode alltid være 200 OK.
 
##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<status xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <mottatt>2020-05-29T09:54:45.886</mottatt>
  <fremdrift>AVVIST</fremdrift>
  <fremdriftOppdatert>2020-05-29T09:54:45.885</fremdriftOppdatert>
  <avvistårsak>VALIDERINGSFEIL</avvistårsak>
  <etterbehandling>
    <tilgjengeligILes>false</tilgjengeligILes>
  </etterbehandling>
  <resultat>
    <feil/>
    <advarsler/>
    <notabener/>
    <vegobjekter>
      <vegobjekt tempId="tunnel#1">
        <feil>
          <feil kode="UKJENT_EGENSKAPSTYPE">
            <melding>Egenskapstypen 0 finnes ikke i datakatalogen</melding>
            <referanse>https://datakatalogen.vegdata.no/581</referanse>
            <egenskapTypeId>0</egenskapTypeId>
          </feil>
        </feil>
        <advarsler/>
        <notabener/>
      </vegobjekt>
    </vegobjekter>
  </resultat>
  <eier>exttxa</eier>
  <klient>MinKlientApplikasjon</klient>
  <apiversjon>3</apiversjon>
  <transaksjon/>
</status>
```
<br/>


### Registrere et endringssett

Registrerer et endringssett i NVDB API Skriv sin lokale database. Behandling av endringssettet starter imidlertid
ikke før eksplisitt [start-kommando](#starte-behandling-av-endringssett) sendes.

#### Mønster

```
POST /nvdb/apiskriv/rest/v3/endringssett
```

#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml.
Accept|MediaType|Angir ønsket media-type for responsen: application/json eller application/xml. Content-Type benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.
X-NVDB-DryRun|Boolsk|Angir om dette er en prøvekjøring (gjør ingen endringer i NVDB) av et endringssett.
X-NVDB-DryRun-NoLocking|Boolsk|Som over, men unnlater å etablere låser i NVDB.
X-NVDB-Delay|Heltall|Angir antall sekunder behandlingen skal vente mellom fullført validering og (eventuelt) effektuering i NVDB. Benyttes kun til testformål.

##### Payload

Ingen.

##### Eksempel

```xml
POST /nvdb/apiskriv/rest/v3/endringssett HTTP/1.1
Content-Type: application/xml
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<endringssett xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <datakatalogversjon>2.20</datakatalogversjon> 
  <registrer>
    <vegobjekter>
      <vegobjekt typeId="581" tempId="tunnel#1">
        <gyldighetsperiode>
          <startdato>2020-01-01</startdato>
        </gyldighetsperiode>
        <egenskaper>
          <egenskap typeId="5225">
            <verdi>Grevlingtunnelen</verdi>
          </egenskap>
        </egenskaper>
        <stedfesting>
          <punkt veglenkesekvensNvdbId="1125766" posisjon="0.3"/>
        </stedfesting>
      </vegobjekt>
    </vegobjekter>
  </registrer>
</endringssett>
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml
Location|URI|Angir hvor endringssettet kan hentes.

##### Payload

Entitet av type [Ressurser](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/ressurser.xsd).

Inneholder URIer for requester som den nye endringssettressursen kan anropes med.

##### Eksempel

```xml
HTTP/1.1 201 Created
Content-Type: application/xml; charset=UTF-8
Location: https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ressurser xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <ressurs rel="self" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037"/>
  <ressurs rel="start" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/start"/>
  <ressurs rel="kanseller" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/kanseller"/>
  <ressurs rel="status" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/status"/>
  <ressurs rel="fremdrift" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/fremdrift"/>
</ressurser>
```
<br/>


### Starte behandling av endringssett

Endepunktet anropes for å starte behandling av et registrert endringssett. Behandlingen utføres asynkront ved at endringssettet
legges i en arbeidskø før responsen leveres. Flere separate prosesser jobber i bakgrunnen med å hente endringssett fra køen og 
gjennomføre behandling av disse. For å monitorere fremdriften i behandlingen må klienten polle jevnlig på [fremdrift-URIen](#hente-fremdriftskoden-for-et-endringssett).
Når behandlingen er fullført, kan detaljert behandlingsresultat hentes med [status-URIen](#hente-status-for-et-endringssett).

#### Mønster

```
POST /nvdb/apiskriv/rest/v3/endringssett/{id}/start
```

#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Eksempel

```
POST /nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/start HTTP/1.1
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

Entitet av type [Ressurser](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/ressurser.xsd).

Inneholder relevante URIer for requester som endringssettressursen kan anropes med.

##### Eksempel

```xml
HTTP/1.1 202 Accepted
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ressurser xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <ressurs rel="fremdrift" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/fremdrift"/>
  <ressurs rel="status" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/status"/>
  <ressurs rel="self" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037"/>
</ressurser>
```
<br/>


### Kansellere behandling av et endringssett

Kansellerer et endringssett og markerer at det kan slettes fra NVDB API Skriv sin lokale database. Et endringssett som
er ferdigbehandlet kan ikke kanselleres. Et endringssett med fremdriftskode BEHANDLES kan kanselleres så lenge
det ikke er i er sluttfasen av behandlingen der data blir skrevet til NVDB.

#### Mønster

```
POST /nvdb/apiskriv/rest/v3/endringssett/{id}/kanseller
```

#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

#### Payload

Ingen.

##### Eksempel
```
POST /nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/kanseller HTTP/1.1
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

Entitet av type [Ressurser](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/ressurser.xsd).

Inneholder relevante URIer for requester som endringssettressursen kan anropes med.

##### Eksempel

```xml
HTTP/1.1 202 Accepted
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ressurser xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <ressurs rel="status" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/status"/>
  <ressurs rel="self" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037"/>
</ressurser>
```
<br/>


### Restarte behandling av et endringssett

Brukes for å starte nytt forsøk på behandling av et endringssett med fremdriftskoden VENTER. Normalt vil slike endringssett bli restartet
automatisk etter en rimelig pause. Denne ventetiden settes av NVDB API Skriv basert på hvilken venteårsak det er og hvor mange ganger
endringssettet har blitt forsøkt restartet tidligere. Klienter har derfor sjelden behov for å restarte eksplisitt.

#### Mønster

```
POST /nvdb/apiskriv/rest/v3/endringssett/{id}/restart
```

#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Payload

Ingen.

##### Eksempel

```
POST /nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/restart HTTP/1.1
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

Entitet av type [Ressurser](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/ressurser.xsd).

Inneholder relevante URIer for requester som endringssettressursen kan anropes med.

##### Eksempel

```xml
HTTP/1.1 202 Accepted
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ressurser xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <ressurs rel="fremdrift" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/fremdrift"/>
  <ressurs rel="status" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/status"/>
  <ressurs rel="self" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037"/>
</ressurser>
```
<br/>


### Hente fremdriftskoden for et endringssett

Gir informasjon om fremdriften av behandlingen av et endringssett i form av en fremdriftskode. Brukes til hyppig
monitorering (polling) av et endringssett under behandling. Klienter må avpasse pollefrekvensen i tråd med
[anbefalt nøkkel](introduksjon.html#hvor-ofte-skal-klienten-polle).

#### Mønster

```
GET /nvdb/apiskriv/rest/v3/endringssett/{id}/fremdrift
```

#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/fremdrift HTTP/1.1
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

Entitet av type [Fremdrift](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/fremdrift.xsd).

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<fremdrift xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">BEHANDLES</fremdrift>
```
<br/>


### Hente fremdrifts- og årsakskoden for et endringssett

Samme som over, men gir i tillegg med venteårsak dersom fremdriftskoden er VENTER og avvistårsak når fremdriftskoden er AVVIST.
Klienter må avpasse pollefrekvensen i tråd med [anbefalt nøkkel](introduksjon.html#hvor-ofte-skal-klienten-polle).

#### Mønster

```
GET /nvdb/apiskriv/rest/v3/endringssett/{id}/fremdriftOgÅrsak
```

#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v3/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/fremdriftOgÅrsak HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: alltid text/plain

##### Payload

Tekst bestående av [fremdriftskode](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/fremdrift.xsd) og dersom denne er 
VENTER eller AVVIST, kolon etterfulgt av [venteårsak](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/venteÅrsak.xsd)
eller [avvistårsak](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/avvistÅrsak.xsd)

##### Eksempel

```
HTTP/1.1 200 OK
Content-Type: text/plain

AVVIST:VALIDERINGSFEIL
```
<br/>


### Hente status for et endringssett

Gir detaljert informasjon om behandlingstatus for et endringssett, inkludert valideringsresultat. Endepunktet skal
primært brukes for ferdigbehandlede endringssett eller endringssett med fremdriftskode VENTER.

#### Mønster

```
GET /nvdb/apiskriv/rest/v3/endringssett/{id}/status
    ?inkluderResultat={JA|NEI}
    &inkluderVarsler={JA|NEI}
```

#### Request

##### Parametere

Navn|Type|Beskrivelse
-|-|-
inkluderResultat|Boolsk|Angir om ```<resultat>```-elementet skal inkluderes i responsen. Hvis NEI leveres kun metadata om endringssettet. Standardverdi: JA.
inkluderVarsler|Boolsk|Angir om valideringsvarsler (feil, advarsler og notabener) skal inkluderes i ```<resultat>```-elementet. Standardverdi: JA.

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v3/endringssett/8ff65469-2424-4ed2-8b58-a5a2a3c7a408/status HTTP/1.1
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

Entitet av type [Status](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/status.xsd).

Dersom endringssettet ble utført og effektuert i NVDB vil tildelte id'er for registrerte vegobjekter angis i
```<resultat>``` -elementet.

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<status xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <mottatt>2020-05-29T12:27:15.108</mottatt>
  <fremdrift>UTFØRT</fremdrift>
  <fremdriftOppdatert>2020-05-29T12:27:17.514</fremdriftOppdatert>
  <resultat>
    <feil/>
    <advarsler/>
    <notabener/>
    <vegobjekter>
      <vegobjekt tempId="tunnel#1" nvdbId="4587234667" versjon="1">
        <feil/>
        <advarsler/>
        <notabener/>
      </vegobjekt>
    </vegobjekter>
  </resultat>
  <eier>exttxa</eier>
  <klient>MinKlientApplikasjon</klient>
  <apiversjon>3</apiversjon>
  <transaksjon>
    <oppdragId>14534547</oppdragId>
    <tidspunkt>2020-05-29T10:27:17</tidspunkt>
  </transaksjon>
  <ressurser>
    <ressurs rel="self" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/8ff65469-2424-4ed2-8b58-a5a2a3c7a408/status"/>
    <ressurs rel="endringssett" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/8ff65469-2424-4ed2-8b58-a5a2a3c7a408"/>
    <ressurs rel="oppdrag" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v1/oppdrag/14534547"/>
  </ressurser>
</status>
```
<br/>


### Hente endringssett

Henter ut innhold og metadata for et registrert endringssett.

#### Mønster

```
GET /nvdb/apiskriv/rest/v3/endringssett/{id}
    ?inkluderResultat={JA|NEI}
    &inkluderVarsler={JA|NEI}
```

#### Request

##### Parametere

Navn|Type|Beskrivelse
-|-|-
inkluderResultat|Boolsk|Angir om subelementet ```<resultat>``` skal inkluderes under ```<status>``` i responsen. Hvis NEI leveres kun metadata om endringssettet i ```<status>```. Standardverdi: JA.
inkluderVarsler|Boolsk|Angir om valideringsvarsler (feil, advarsler og notabener) skal inkluderes i subelementet ```<resultat>``` under ```<status>```. Standardverdi: JA.

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v3/endringssett/8ff65469-2424-4ed2-8b58-a5a2a3c7a408 HTTP/1.1
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

Entitet av type [Endringssett](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<endringssett xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <datakatalogversjon>2.20</datakatalogversjon> 
  <registrer>
    <vegobjekter>
      <vegobjekt typeId="581" tempId="tunnel#1">
        <gyldighetsperiode>
          <startdato>2020-01-01</startdato>
        </gyldighetsperiode>
        <egenskaper>
          <egenskap typeId="5225">
            <verdi>Grevlingtunnelen</verdi>
          </egenskap>
        </egenskaper>
        <stedfesting>
          <punkt veglenkesekvensNvdbId="1125766" posisjon="0.3"/>
        </stedfesting>
      </vegobjekt>
    </vegobjekter>
  </registrer>
  <status xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
    <mottatt>2020-05-29T12:27:15.108</mottatt>
    <fremdrift>UTFØRT</fremdrift>
    <fremdriftOppdatert>2020-05-29T12:27:17.514</fremdriftOppdatert>
    <resultat>
      <feil/>
      <advarsler/>
      <notabener/>
      <vegobjekter>
        <vegobjekt tempId="tunnel#1" nvdbId="4587234667" versjon="1">
          <feil/>
          <advarsler/>
          <notabener/>
        </vegobjekt>
      </vegobjekter>
    </resultat>
    <eier>exttxa</eier>
    <klient>MinKlientApplikasjon</klient>
    <apiversjon>3</apiversjon>
    <transaksjon>
      <oppdragId>14534547</oppdragId>
      <tidspunkt>2020-05-29T10:27:17</tidspunkt>
    </transaksjon>
    <ressurser>
      <ressurs rel="self" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/8ff65469-2424-4ed2-8b58-a5a2a3c7a408/status"/>
      <ressurs rel="endringssett" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/8ff65469-2424-4ed2-8b58-a5a2a3c7a408"/>
      <ressurs rel="oppdrag" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v1/oppdrag/14534547"/>
    </ressurser>
  </status>
</endringssett>
```
<br/>


### Søke etter endringssett

Henter en liste av registrerte endringssett som tilfredstiller angitte søkekriterier.

Som standard leveres kun endringssett som anropende bruker eier selv. Dersom brukeren har system-admin-rollen, vil søket gjøres i samtlige
endringssett uansett eier.

#### Mønster

```
GET /nvdb/apiskriv/rest/v3/endringssett
    ?antall={tall}
    &start={tall}
    &sorterPå={feltnavn}
    &sorterStigende={JA/NEI}
    &fraEpochMs={tall}
    &tilEpochMs={tall}
    &status={kode}
    &årsak={kode}
    &brukernavnEllerKlient={navn}
```
 
#### Request

##### Parametere

Navn|Type|Beskrivelse
-|-|-
antall|Heltall|Angir ønsket antall endringssett i responsen (sidestørrelse). Maksimumsverdi er 1000. 
start|Heltall|Angir indeksverdi (1-basert) for første endringssett (sidestart).
sorterPå|Tekst|Angir sortingsnøkkel. Tillatte verdier: TID.
sorterStigende|Boolsk|Angir om endringssettene skal sorteres stigende.
fraEpochMs|Heltall|Angir første mottakstidspunkt for ønskede endringssett i form av antall millisekunder siden 1. januar 1970. 
tilEpochMs|Heltall|Angir siste mottakstidspunkt for ønskede endringssett i form av antall millisekunder siden 1. januar 1970.
status|Tekst|Angir fremdriftskoden til ønskede endringssett. Gyldige verdier defineres av typen [Fremdrift](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/fremdrift.xsd).
årsak|Tekst|Angir venteårsak eller avvistårsak til ønskede endringssett. Gyldige verdier defineres av typen [VenteÅrsak](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/venteÅrsak.xsd) og [AvvistÅrsak](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/avvistÅrsak.xsd)
brukernavnEllerKlient|Tekst|Angir brukernavn for eier eller ansvarlig, eller registrerende klient til ønskede endringssett.  

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v3/endringssett?antall=1 HTTP/1.1
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

Entitet av type [EndringssettListe](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett-liste.xsd).

Innholdet i endringssettet er ikke med i responsen, kun ```<status>``` -elementet (uten ```<resultat>```).

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<endringssettliste xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3" antallTotalt="4">
  <endringssett id="2b0f19a0-f8ac-480d-9e4e-0e6346b17fdb">
    <status>
      <mottatt>2020-05-28T10:14:01.628</mottatt>
      <fremdrift>UTFØRT</fremdrift>
      <fremdriftOppdatert>2020-05-28T10:14:07.753</fremdriftOppdatert>
      <eier>exttxa</eier>
      <klient>MinKlientApplikasjon</klient>
      <apiversjon>3</apiversjon>
      <transaksjon>
        <oppdragId>14534547</oppdragId>
        <tidspunkt>2020-05-29T10:27:17</tidspunkt>
      </transaksjon>
      <ressurser>
        <ressurs rel="self" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/2b0f19a0-f8ac-480d-9e4e-0e6346b17fdb/status"/>
        <ressurs rel="endringssett" src="https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/2b0f19a0-f8ac-480d-9e4e-0e6346b17fdb"/>
      </ressurser>
    </status>
    <datakatalogversjon>2.20</datakatalogversjon>
  </endringssett>
</endringssettliste>
```
