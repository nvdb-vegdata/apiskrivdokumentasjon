---
category: 4#Stedfesting
title: API-referanse
order: 2
permalink: /stedfesting/api-referanse
---

# Nytt endepunkt for [NVDB api SKRIV dokumentasjon](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

NVDB api SKRIV V3 dokumentasjon er flyttet til [https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

API'et blir jo aktivt vedlikeholdt og videreutviklet, så vi anbefaler sterkt at dere legger om til den nyeste versjonen av dokumentasjonen. 


## API-referanse for stedfesting [BETA]

### Innhold

[Beregne stedfesting](#beregne-stedfesting)  
<br/>


### Beregne stedfesting

Dette kommando-endepunktet beregner gyldig stedfesting for vegobjekter med geometriegenskap(er). Responsen kan brukes
direkte på de samme vegobjektene i et endringssett.

Dersom requesten ikke angir relevante veger (vegkategori, vegfase og vegnummer) for stedfestingen, beregnes stedfestingen til nærmeste
vegnett med vegkategori E, R, F eller K.

Dette endepunktet gir synkron respons. Responstiden er korrellert med antall vegobjekter i payloaden.

#### Mønster

```
POST /rest/v3/stedfest
```

#### Request

##### Parametere

Ingen.
        
##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml
Accept|MediaType|Angir ønsket media-type for responsen: application/json eller application/xml. Content-Type benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen
X-Request-ID|UUID|Angir unik korrelasjonsidentifikator for requesten.

##### Payload

Entitet av type [Stedfest](https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/stedfest/stedfest.xsd).

I subelementet ```<parametere>``` kan det angis opplysninger som avgrenser eller gir hint om ønsket stedfesting. Hvert subelement er vagfritt, med mindre noe annet er angitt:

* ```<maksimumAvstandTilVeg>``` Angir hvor mange meter utenfor vegobjektgeometrien det skal søkes etter relevant vegnett (obligatorisk).
* ```<beregnSideposisjon>``` Angir hvorvidt sideposisjon skal beregnes for stedfestingselementene. Standardverdi er ```false```.
* ```<veger>``` angir en liste av veger som det er relevant å stedfeste på. Hvert innslag i listen beskrives med et ```<veg>``` -element som har følgende subelementer:
  * ```<kategori>``` angir vegkategori for vegen (obligatorisk). For lovlige verdier se [Vegkategori](https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/stedfest/vegkategori.xsd).
  * ```<fase>``` angir vegfase for vegen. For lovlige verdier se [Vegfase](https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/stedfest/vegfase.xsd).
  * ```<nummer>``` angir vegnummer for vegen.
* ```<typeVeger>``` angir en liste over type veger som det er relevant å stedfeste på. Hvert innslag i listen beskrives med et ```<typeVeg>``` -element med lovlige verdier fra [TypeVeg](https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/stedfest/typeveg.xsd).
* ```<forankring>``` angir forankringspunkter som stedfestingen skal ta utgangspunkt i i stedet for vegobjektgeometriene. Elementet har følgende subelementer:
  * ```<srid>``` angir koordinatreferansesystem for ankerpunktene (obligatorisk).
  * ```<startWkt>``` angir geometrisk ankerpunkt (f.eks. "POINT (123 456)") for starten på ønsket stedfesting (obligatorisk). Punktet må befinne seg i rimelig nærhet til ønsket veg.
  * ```<sluttWkt>``` angir geometrisk ankerpunkt for slutten på ønsket stedfesting (obligatorisk dersom vegobjekttypen krever strekningsstedfesting). Punktet må befinne seg i rimelig nærhet til ønsket veg. 

##### Eksempel

```xml
POST /rest/v3/stedfest HTTP/1.1
Content-Type: application/xml
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<stedfest xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <parametere>
    <maksimalAvstandTilVeg>100</maksimalAvstandTilVeg>
    <veger>
      <veg>
        <kategori>F</kategori>
        <fase>V</fase>
        <nummer>6690</nummer>
      </veg>
    </veger>
    <typeVeger>
      <typeVeg>ENKEL_BILVEG</typeVeg>
      <typeVeg>KANALISERT_VEG</typeVeg>
      <typeVeg>RAMPE</typeVeg>
      <typeVeg>RUNDKJØRING</typeVeg>
    </typeVeger>
    <beregnSideposisjon>false</beregnSideposisjon>
    <forankring>
      <srid>5973</srid>srid>
      <startWkt>POINT (270195 7041859)</startWkt>
    </forankring>
  </parametere>
  <vegobjekter>
    <vegobjekt typeId="95" tempId="skiltpunkt#1">
      <gyldighetsperiode>
        <startdato>2020-01-01</startdato>
      </gyldighetsperiode>
      <egenskaper>
        <egenskap typeId="4794">
          <geometri>
            <srid>5973</srid>srid>
            <wkt>POINT Z(270196.99 7041858.13 15.72)</wkt>
          </geometri>
        </egenskap>
      </egenskaper>
      <assosiasjoner/>
    </vegobjekt>
  </vegobjekter>
</stedfest>
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml

##### Payload

Entitet av type [StedfestingResultat](https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/stedfest/stedfest-resultat.xsd).

Beregnet stedfesting for hvert vegobjekt ledsages av et ```<oversikt>``` -element med beskrivelse av veg og målt
lengde (meter) for stedfestingen. Hvert stedfestingselement vil i tillegg ha et ```<geometri>``` -subelement med geometrien
til vegnettet det er stedfestet på. Dette kan eventuelt brukes i klienter til å markere stedfestingen på digitale
kart.

Dersom vegobjektene ikke ble godkjent av valideringen vil eventuelle feil beskrives i responsen på samme måte som i 
[behandlingsresultatet](../endringssett/behandlingsresultat.md) for endringssett.

Uavhengig av om vegobjektene lot seg stedfeste eller ikke, vil HTTP-statuskode alltid være 200 OK.
 
##### Eksempel - Vellykket stedfesting

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<stedfestingResultat xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <vegobjekt tempId="skiltpunkt#1">
    <stedfesting>
      <oversikt>
        <veg>
          <kategori>F</kategori>
          <fase>V</fase>
          <nummer>6690</nummer>
        </veg>
        <lengde>0.0</lengde>
      </oversikt>
      <punkt veglenkesekvensNvdbId="2510771" posisjon="0.1429407">
        <geometri>
          <srid>5973</srid>
          <wkt>POINT (270192.0336852201 7041858.010694844)</wkt>
        </geometri>
      </punkt>
    </stedfesting>
  </vegobjekt>
</stedfestingResultat>
```

##### Eksempel - Valideringsfeil

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<stedfestingResultat xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <vegobjekt tempId="skiltpunkt#1">
    <feil>
      <feil kode="UGYLDIG_EGENSKAPSTYPE">
        <melding>Egenskapstypen Geometri, punkt (4795) er ikke del av vegobjekttypen Skiltpunkt (95)</melding>
        <referanse>https://datakatalogen.vegdata.no/95</referanse>
        <egenskapTypeId>4795</egenskapTypeId>
      </feil>
    </feil>
  </vegobjekt>
</stedfestingResultat>
```
