---
category: 2#Binære objekter
title: API-referanse
order: 2
permalink: /binære-objekter/api-referanse
---

## API-referanse

### Innhold

[Laste opp binærdata](#laste-opp-binærdata)  
[Laste opp binærdata fra nettside](#laste-opp-binærdata-fra-nettside)  
[Laste ned binærdata](#laste-ned-binærdata)  
<br/>


### Laste opp binærdata

Laster opp et binært objekt og responderer med en id som deretter kan brukes som referanse i et endringssett.

#### Mønster

```
POST /nvdb/apiskriv/rest/v3/binaer
```
#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload, f.eks. image/png
Content-Disposition|Tekst|Hvis en ønsker å angi et filnavn for egen referanse, f.eks. filename="N808080-1.png"
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken
X-Client|Tekst|Angir navnet på klientapplikasjonen.

##### Payload

Binærdata.

##### Eksempel

```
POST /nvdb/apiskriv/rest/v3/binaer HTTP/1.1
Content-Type: image/png
Content-Disposition: filename="N808080-1.png"
Cookie: iPlanetDirectoryOAM=AQIC5wM2LY4SfczXL0v42tkJK__EjrzGGl9PTJsJMYKuzLo
X-Client: MinKlientApplikasjon

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
Location: https://www.vegvesen.no/nvdb/apiskriv/rest/v3/binaer/12d57307-8ba8-4866-8373-153768cfbd09

"12d57307-8ba8-4866-8373-153768cfbd09"
```
<br/>


### Laste opp binærdata fra nettside

Et binært objekt kan lastes opp via filopplasting fra en nettside med følgende HTML-fragment:

```html
<form method="post" action="/nvdb/apiskriv/rest/v3/binaer" enctype="multipart/form-data">
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
GET /nvdb/apiskriv/rest/v3/binaer/{id}
```

#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken
X-Client|Tekst|Angir navnet på klientapplikasjonen.

##### Eksempel

```
GET /nvdb/apiskriv/rest/v3/binaer/12d57307-8ba8-4866-8373-153768cfbd09 HTTP/1.1
Cookie: iPlanetDirectoryOAM=AQIC5wM2LY4SfczXL0v42tkJK__EjrzGGl9PTJsJMYKuzLo
X-Client: MinKlientApplikasjon
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
