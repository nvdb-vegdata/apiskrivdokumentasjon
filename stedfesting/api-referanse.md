---
category: 4#Stedfesting
title: API-referanse
order: 2
permalink: /stedfesting/api-referanse
---

## API-referanse [BETA]

### Innhold

[Beregne stedfesting](#beregne-stedfesting)  
<br/>


### Beregne stedfesting

Dette kommando-endepunktet beregner gyldig stedfesting for vegobjekter med geometriegenskap(er). Responsen kan brukes
direkte på de samme vegobjektene i et endringssett.

Dersom requesten ikke angir ønsket veg (vegkategori, vegfase og vegnummer) for stedfestingen, beregnes stedfestingen til nærmeste
vegnett med vegkategori E, R, F eller K.

Dette endepunktet gir synkron respons. Responstiden er korrellert med antall vegobjekter i payloaden.

#### Mønster

```
POST /nvdb/apiskriv/rest/v3/stedfest
     ?maksimumAvstandTilVeg={tall}
     &vegkategori={Vegkategori}
     &fase={Vegfase}
     &vegnummer={tall}
     &typeVeg={liste av TypeVeg}
```

#### Request

##### Parametere

Navn|Type|Beskrivelse
-|-|-
maksimumAvstandTilVeg|Heltall|Angir hvor mange meter utenfor vegobjektgeometrien det skal søkes etter relevant vegnett (obligatorisk)
vegkategori|Vegkategori|Angir hvilken vegkategori relevant vegnett kan ha (obligatorisk når fase er angitt). For lovlige verdier se [Vegkategori](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/stedfest/vegkategori.xsd)
fase|Vegfase|Angir hvilken vegfase relevant vegnett kan ha (obligatorisk når vegnummer er angitt). For lovlige verdier se [Vegfase](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/stedfest/vegfase.xsd)
vegnummer|Heltall|Angir hvilket vegnummer relevant vegnett kan ha
typeVeg|TypeVeg-liste|Angir hvilke typeVeg-verdier (kommaseparert) relevant vegnett kan ha. For lovlige verdier se [TypeVeg](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/stedfest/typeveg.xsd)
        
##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml
Accept|MediaType|Angir ønsket media-type for responsen: application/json eller application/xml. Content-Type benyttes hvis ikke annet er oppgitt.
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken
X-Client|Tekst|Angir navnet på klientapplikasjonen

##### Payload

Entitet av type [StedviltVegobjektListe](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/stedfest/stedfest.xsd).

##### Eksempel

```xml
POST /nvdb/apiskriv/rest/v3/stedfest HTTP/1.1
Content-Type: application/xml
Cookie: iPlanetDirectoryOAM=AQIC5wM2LY4SfczXL0v42tkJK__EjrzGGl9PTJsJMYKuzLo
X-Client: MinKlientApplikasjon

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<vegobjekter xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
    <vegobjekt typeId="95" tempId="skiltpunkt#1">
        <gyldighetsperiode>
            <startdato>2020-01-01</startdato>
        </gyldighetsperiode>
        <egenskaper>
            <egenskap typeId="4794">
                <verdi>srid=5973;POINT Z(270196.99 7041858.13 15.72)</verdi>
            </egenskap>
        </egenskaper>
        <assosiasjoner/>
    </vegobjekt>
</vegobjekter>
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for payload: application/json eller application/xml

##### Payload

Entitet av type [StedfestingResultat](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/stedfest/stedfest-resultat.xsd).

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