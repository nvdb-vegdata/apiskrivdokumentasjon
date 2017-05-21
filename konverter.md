#Konvertering

##Konverter SOSI-GeoJSON til endringssett
Det er følgende begrensninger:

- effektDato settes til dagens dato.
- feature[n] blir til registrer.vegObjekter[n]
- registrer.vegObjekter[n].lokasjon blir ikke satt.
Mønster
```
POST /nvdb/apiskriv/rest/v2/konverter/sosiGeoJson
```
Forespørsel
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. {Content-Type} benyttes hvis ikke annet er oppgitt.|
|Content-Type|MediaType|application/json|

Kropp
Eksempel

```json
POST /nvdb/apiskriv/rest/v2/konverter/sosiGeoJson HTTP/1.1
Accept: application/json
Content-Type: application/json


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

Svar
Hode


|Navn|Type|Beskrivelse|
|-|-|-|
|Content-Type|MediaType|Angir hvilken media-type kroppen er. application/json eller application/xml.|

Kropp
Eksempel

```json
HTTP/1.1  200 OK
Content-Type: application/json


{
  "registrer": {
    "vegObjekter": [
      {
        "typeId": 95,
        "tempId": "1",
        "egenskaper": [
          {
            "typeId": 1884,
            "verdi": ["0.30"]
          },
          {
            "typeId": 1876,
            "enum": [2868]
          },
          {
            "typeId": 1877,
            "verdi": ["1"]
          },
          {
            "typeId": 1671,
            "enum": [2435]
          },
          {
            "typeId": 4794,
            "verdi": ["srid=4326;datafangstdato=2013-11-26;målemetode=96;målemetodeHøyde=96;nøyaktighet=5;nøyaktighetHøyde=5;synbarhet=0;temakode=999;POINT(10.391780132804493 63.430602994329256)"]
          }
        ]
      }
    ]
  },
  "datakatalogversjon": "2.01",
  "effektDato": "2015-02-26"
}
```

##Konverter SOSI-NVDB til endringssett
Det er følgende begrensninger:
- effektDato settes til dagens dato.
- krever KOORDSYS 23 og VERT-DATUM NN54.
- PUNKT/KURVE/FLATE blir til registrer.vegObjekter[n]
- registrer.vegObjekter.lokasjon blir ikke satt.
Mønster

```
POST /nvdb/apiskriv/rest/v2/konverter/sosi
```

Forespørsel
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. {Content-Type} benyttes hvis ikke annet er oppgitt.|
|Content-Type|MediaType|text/plain|

Kropp
Eksempel
```
POST /nvdb/apiskriv/rest/v2/konverter/sosi HTTP/1.1
Accept: application/json
Content-Type: text/plain


.HODE
..TEGNSETT ISO8859-1
..SOSI-VERSJON 8.1
..DATAKATALOGVERSJON 2.01
..SOSI-NIVÅ 1
..TRANSPAR
...KOORDSYS 23
...ORIGO-NØ  7034310 569351
...ENHET 0.010
...VERT-DATUM NN54
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
.PUNKT 2:
..OBJTYPE Skiltplate_96
..PTEMA 0999
..DATAFANGSTDATO 20131127
..KVALITET 96 5 0 96 5
..SkiltnummerHB-050_5530 7691
..Ansiktsside_1894 2717
..Størrelse_1970 4132
..Skiltform_1892 2855
..Belysning_1879 3476
..Folieklasse_1921 2849
..Klappskilt_8828 11739
..NØH
9040 10967 928
.SLUTT
```

Svar
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Content-Type|MediaType|Angir hvilken media-type kroppen er. application/json eller application/xml.|

Kropp
Eksempel
```json
HTTP/1.1  200 OK
Content-Type: application/json


{
  "registrer": {
    "vegObjekter": [
      {
        "typeId": 95,
        "tempId": "1",
        "egenskaper": [
          {
            "typeId": 1884,
            "verdi": ["0.30"]
          },
          {
            "typeId": 1876,
            "enum": [2868]
          },
          {
            "typeId": 1877,
            "verdi": ["1"]
          },
          {
            "typeId": 1671,
            "enum": [2435]
          },
          {
            "typeId": 4794,
            "verdi": ["datafangstdato=2013-11-27;målemetode=96;målemetodeHøyde=96;nøyaktighet=5;nøyaktighetHøyde=5;synbarhet=0;temakode=999;POINT(10.391780132804493 63.430602994329256)"]
          }
        ]
      }
    ]
  },
  "datakatalogversjon": "2.01",
  "effektDato": "2015-02-26"
```
