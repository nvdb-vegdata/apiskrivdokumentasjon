---
category: 2#Binære objekter
title: API-referanse
order: 2
permalink: /binære-objekter/api-referanse
---

## API-referanse for binære objekter

### Innhold

[Laste opp binærdata](#laste-opp-binærdata)  
[Laste opp binærdata fra nettside](#laste-opp-binærdata-fra-nettside)  
[Laste ned binærdata](#laste-ned-binærdata)  
<br/>


### Laste opp binærdata

Laster opp et binært objekt og responderer med en id som deretter kan brukes som referanse i et endringssett.

#### Mønster

```
POST /rest/v3/binaer
```
#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload, f.eks. image/png
Content-Disposition|Tekst|Hvis en ønsker å angi et filnavn for egen referanse, f.eks. filename="N808080-1.png"
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Payload

Binærdata.

##### Eksempel

```
POST /rest/v3/binaer HTTP/1.1
Content-Type: image/png
Content-Disposition: filename="N808080-1.png"
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18

.PNG........IHDR.............:~.U....IDATx.cj.......U.o....IEND.B`.
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Location|URI|Angir hvor binærobjektet kan hentes.

##### Payload

UUID for det registrerte binærobjektet formatert som JSON-streng.

##### Eksempel
```
HTTP/1.1 201 Created
Content-Type: application/json; charset=UTF-8
Location: https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/binaer/12d57307-8ba8-4866-8373-153768cfbd09

"12d57307-8ba8-4866-8373-153768cfbd09"
```
<br/>


### Laste opp binærdata fra nettside

Et binært objekt kan lastes opp via filopplasting fra en nettside med følgende HTML-fragment:

```html
<form method="post" action="/rest/v3/binaer" enctype="multipart/form-data">
  <!-- Viktig: name="content" -->
  <input type="file" name="content"/>
  <input type="submit"/>
</form>
```
<br/>


### Laste ned binærdata

Tillater nedlasting av tidligere opplastede binærdata.

#### Mønster

```
GET /rest/v3/binaer/{id}
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
GET /rest/v3/binaer/12d57307-8ba8-4866-8373-153768cfbd09 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Samme verdi som angitt ved opplasting.
Content-Disposition|Tekst|Samme verdi som angitt ved opplasting.

##### Payload

Binærdata.

##### Eksempel

```
HTTP/1.1 200 OK
Content-Type: image/png
Content-Disposition: filename="N808080-1.png"

.PNG........IHDR.............:~.U....IDATx.cj.......U.o....IEND.B`.
```
