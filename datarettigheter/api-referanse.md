---
category: 8#Datarettigheter
title: API-referanse
order: 2
permalink: /datarettigheter/api-referanse
---

# Nytt endepunkt for [NVDB api SKRIV dokumentasjon](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

NVDB api SKRIV V3 dokumentasjon er flyttet til [https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

API'et blir jo aktivt vedlikeholdt og videreutviklet, så vi anbefaler sterkt at dere legger om til den nyeste versjonen av dokumentasjonen. 


## API-referanse for datarettigheter

### Innhold

[Hente datarettigheter for en bruker](#hente-datarettigheter-for-en-bruker)  
<br/>


### Hente datarettigheter for en bruker

Henter datarettighetene som er tildelt en spesifikk bruker.

#### Mønster

```
GET /rest/v1/autorisasjon/{brukernavn}
```

#### Request

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
GET /rest/v1/autorisasjon/exttxa HTTP/1.1
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

Entitet av type [Autorisasjon](https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/autorisasjon/autorisasjon.xsd).

##### Eksempel

```xml
HTTP/1.1  200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<autorisasjon xmlns="http://nvdb.vegvesen.no/apiskriv/domain/authorization/v1">
  <hva>
    <vegobjekttyper>
      <vegobjekttype>95</vegobjekttype>
      <vegobjekttype>96</vegobjekttype>
      <vegobjekttype>538</vegobjekttype>
    </vegobjekttyper>
    <vegnett>NEI</vegnett>
  </hva>
  <hvor>
    <vegkategorier>
      <vegkategori>E</vegkategori>
      <vegkategori>R</vegkategori>
      <vegkategori>F</vegkategori>
    </vegkategorier>
    <typerVeg>
      <typeVeg>RUNDKJØRING</typeVeg>
      <typeVeg>ENKEL_BILVEG</typeVeg>
      <typeVeg>KANALISERT_VEG</typeVeg>
      <typeVeg>GANG_OG_SYKKELVEG</typeVeg>
      <typeVeg>RAMPE</typeVeg>
    </typerVeg>
    <kommuner/>
    <fylker>
      <fylke>18</fylke>
      <fylke>34</fylke>
      <fylke>50</fylke>
      <fylke>3</fylke>
      <fylke>38</fylke>
      <fylke>54</fylke>
      <fylke>42</fylke>
      <fylke>11</fylke>
      <fylke>30</fylke>
      <fylke>46</fylke>
      <fylke>15</fylke>
    </fylker>
  </hvor>
  <kanDeaktivereValideringsregler>NEI</kanDeaktivereValideringsregler>
</autorisasjon>
```
<br/>


