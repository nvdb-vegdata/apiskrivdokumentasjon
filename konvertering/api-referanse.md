---
category: 3#Konvertering
title: API-referanse
order: 2
permalink: /konvertering/api-referanse
---

## API-referanse for konvertering

### Innhold

[Konvertere SOSI-GeoJSON til endringssett](#konvertere-sosi-geojson-til-endringssett)  
[Konvertere SOSI-NVDB til endringssett](#konvertere-sosi-nvdb-til-endringssett)  
<br/>


### Konvertere SOSI-GeoJSON til endringssett

Konverterer et dokument på SOSI-[GeoJSON](https://en.wikipedia.org/wiki/GeoJSON)-format til endringssett.

Konverteringen har følgende egenskaper:

* Alle feature-objekter i kilden blir til ```<vegobjekt>``` -elementer innfor en ```<registrer>``` -blokk i endringssettet.
* Startdato for alle vegobjekter settes til dagens dato.
* SOSI-GeoJSON mangler stedfestingsbegrep, slik at vegobjektene i endringssettet må kompletteres med dette i etterkant.

#### Mønster

```
POST /nvdb/apiskriv/rest/v3/konverter/sosiGeoJson
```

#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. Content-Type benyttes hvis ikke annet er oppgitt.
Content-Type|MediaType|Må være application/json
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Payload

Dokument på SOSI-GeoJSON-format

##### Eksempel

```
POST /nvdb/apiskriv/rest/v3/konverter/sosiGeoJson HTTP/1.1
Accept: application/xml
Content-Type: application/json
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18

{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "id": 1,
      "properties": {
        "objekttypenavn": "Skiltpunkt_95",
        "PTEMA": "0999",
        "datafangstdato": "2013-11-26T23:00:00.000Z",
        "kvalitet": {
          "målemetode": 96,
          "nøyaktighet": 5,
          "synbarhet": 0,
          "målemetodeHøyde": 96,
          "nøyaktighetHøyde": 5
        },
        "AvstandFraDekkekant_1884": "0,30",
        "Oppsettingsutstyr_1876": "2868",
        "AntallOppsettingsutstyr_1877": "1",
        "Fundamentering_1671": "2435"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [
          10.391780132804493,
          63.430602994329256
        ]
      }
    }
  ],
  "crs": {
    "type": "name",
    "properties": {
      "name": "EPSG:4326"
    }
  }
}
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir media-type for payloaden: application/json eller application/xml.

##### Payload

Entitet av type [Endringssett](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

##### Eksempel

```xml
HTTP/1.1  200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<endringssett xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <registrer>
    <vegobjekter>
      <vegobjekt typeId="95" tempId="1">
        <egenskaper>
          <egenskap typeId="1884">
            <verdi>0.30</verdi>
          </egenskap>
          <egenskap typeId="1876">
            <enum>2868</enum>
          </egenskap>
          <egenskap typeId="1877">
            <verdi>1</verdi>
          </egenskap>
          <egenskap typeId="1671">
            <enum>2435</enum>
          </egenskap>
          <egenskap typeId="4794">
            <geometri>
              <srid>4326</srid>
              <wkt>POINT(10.391780132804493 63.430602994329256)</wkt>
              <datafangstdato>2013-11-26</datafangstdato>
              <temakode>999</temakode>
              <kvalitet>
                <målemetode>96</målemetode>
                <målemetodeHøyde>96</målemetodeHøyde>
                <nøyaktighet>5</nøyaktighet>
                <nøyaktighetHøyde>5</nøyaktighetHøyde>
                <synbarhet>0</synbarhet>
              </kvalitet>
            </geometri>
          </egenskap>
        </egenskaper>
        <gyldighetsperiode>
          <startdato>2020-06-08</startdato>
        </gyldighetsperiode>
      </vegobjekt>
    </vegobjekter>
  </registrer>
  <datakatalogversjon>2.20</datakatalogversjon>
</endringssett>
```
<br/>


### Konvertere SOSI-NVDB til endringssett

Konverterer et dokument på SOSI-NVDB-format til endringssett.

Konverteringen har følgende egenskaper:

* Alle PUNKT-, KURVE- og FLATE-objekter i kilden blir til ```<vegobjekt>``` -elementer innfor en ```<registrer>``` -blokk i endringssettet.
* Startdato for alle vegobjekter settes til dagens dato.
* SOSI mangler stedfestingsbegrep, slik at vegobjektene i endringssettet må kompletteres med dette i etterkant.
* Krever at KOORDSYS er satt til 22 (UTM32) eller 23 (UTM-33) og VERT-DATUM er satt til NN2000. Sistnevnte er NVDBs høydekoordinatreferansesystem.

#### Mønster

```
POST /nvdb/apiskriv/rest/v3/konverter/sosi
```

#### Request

##### Hode

Navn|Type|Beskrivelse
-|-|-
Accept|MediaType|Angir ønsket [media-type](https://www.iana.org/assignments/media-types/media-types.xhtml) for responsen: application/json eller application/xml. application/xml benyttes hvis ikke annet er oppgitt.
Content-Type|MediaType|Må være text/plain
Cookie|Informasjonskapsler|Angir gyldig autentiseringstoken. NB! Under avvikling.
Authorization|Autentiseringstoken|Bearer med id-token fra OpenId Connect.
X-Client|Tekst|Angir navnet på klientapplikasjonen.
X-Request-ID|Tekst|Angir unik korrelasjonsidentifikator (UUID) for requesten.

##### Payload

Dokument på SOSI-NVDB-format

##### Eksempel

```
POST /nvdb/apiskriv/rest/v3/konverter/sosi HTTP/1.1
Accept: application/xml
Content-Type: text/plain
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...
X-Client: MinKlientApplikasjon
X-Request-ID: edf1f9eb-38dd-46e3-a250-52b810277b18

.HODE
..TEGNSETT UTF-8
..SOSI-VERSJON 8.1
..DATAKATALOGVERSJON 2.01
..SOSI-NIVÅ 1
..TRANSPAR
...KOORDSYS 23
...ORIGO-NØ 7034310 569351
...ENHET 0.010
...VERT-DATUM NN2000
..OMRÅDE
...MIN-NØ 7034310 569351
...MAX-NØ 7034406 569492
..EIER "Entreprenøren AS"
..PRODUSENT "Ola Nordmann"
.PUNKT 1:
..OBJTYPE Skiltplate_96
..PTEMA 0999
..DATAFANGSTDATO 20131127
..KVALITET 96 5 0 96 5
..SkiltnummerHB-050_5530 7691
..Ansiktsside_1894 2714
..Størrelse_1970 4132
..Skiltform_1892 2855
..Belysning_1879 3476
..Folieklasse_1921 2849
..Klappskilt_8828 11739
..NØH
2980 11668 995
.SLUTT
```

#### Respons

##### Hode

Navn|Type|Beskrivelse
-|-|-
Content-Type|MediaType|Angir media-type for payloaden: application/json eller application/xml.

##### Payload

Entitet av type [Endringssett](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

##### Eksempel

```xml
HTTP/1.1 200 OK
Content-Type: application/xml; charset=UTF-8

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<endringssett xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <registrer>
    <vegobjekter>
      <vegobjekt typeId="96" tempId="PUNKT_1_16">
        <egenskaper>
          <egenskap typeId="5530">
            <enum>7691</enum>
          </egenskap>
          <egenskap typeId="1894">
            <enum>2714</enum>
          </egenskap>
          <egenskap typeId="1970">
            <enum>4132</enum>
          </egenskap>
          <egenskap typeId="1892">
            <enum>2855</enum>
          </egenskap>
          <egenskap typeId="1879">
            <enum>3476</enum>
          </egenskap>
          <egenskap typeId="1921">
            <enum>2849</enum>
          </egenskap>
          <egenskap typeId="8828">
            <enum>11739</enum>
          </egenskap>
          <egenskap typeId="4795">
            <geometri>
              <srid>5973</srid>
              <wkt>POINT(7034339.8 569467.68 9.95)</wkt>
              <datafangstdato>2013-11-27</datafangstdato>
              <temakode>999</temakode>
              <kvalitet>
                <målemetode>96</målemetode>
                <målemetodeHøyde>96</målemetodeHøyde>
                <nøyaktighet>5</nøyaktighet>
                <nøyaktighetHøyde>5</nøyaktighetHøyde>
                <synbarhet>0</synbarhet>
              </kvalitet>
            </geometri>
          </egenskap>
        </egenskaper>
        <gyldighetsperiode>
          <startdato>2020-05-29</startdato>
        </gyldighetsperiode>
      </vegobjekt>
    </vegobjekter>
  </registrer>
  <datakatalogversjon>2.20</datakatalogversjon>
</endringssett>
```
