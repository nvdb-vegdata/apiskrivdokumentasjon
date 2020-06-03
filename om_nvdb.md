---
title: Om NVDB
order: 4
---

## Om NVDB

For å kunne ta i bruk NVDB API Skriv på en effektiv måte, er det en fordel å ha kunnskap om hvordan NVDBs datamodell er bygd opp.

### Innhold i NVDB

NVDB inneholder Norges offisielle digitale vegnett, med både geometri og topologi. Vegen kan plasseres på kart, og det er mulig å finne en rute mellom to punkter på vegnettet.

Vegobjekter, som stedfestes til vegnettet, er den andre viktige delen av NVDB. Det finnes mellom 300 og 400 ulike typer vegobjekter, som inneholder verdifull informasjon om vegen.

Eksempel på vegobjekttyper:

*   Fartsgrense
*   Trafikkmengde
*   Trafikkulykke
*   Skiltplate
*   Rekkverk
*   Belysningspunkt
*   Tunnel
*   Bomstasjon

Vegobjekttypene kan deles inn i to kategorier:

*   **Punktobjekttyper** – Fagdata som normalt er koblet til vegnettet gjennom ett punkt
*   **Strekningsobjekttyper** – Fagdata som er koblet til vegnettet over en strekning

### Datakatalogen

Hvilke typer vegobjekter som kan registreres i NVDB, defineres i en egen metadatabase som kalles datakatalogen.

Datakatalogen inneholder vegobjekttyper, med tilhørende egenskapstyper, tillatte verdier og relasjoner. Det finnes også styringsparametere, som detaljert angir hvordan et objekt kan, eller ikke kan, registreres.

Alle vegobjekttyper, egenskapstyper og tillatte verdier har en unik id. Vegobjekttypen Bomstasjon har for eksempel id lik 45.

I NVDB API skal det brukes id, og ikke navn, når det skal angis en spesifikk vegobjekttype.

#### Mer informasjon

*   [http://datakatalogen.vegdata.no](http://datakatalogen.vegdata.no) – Nettsted som inneholder de viktigste detaljene fra datakatalogen
*   [http://tfprod1.sintef.no/datakatalog](http://tfprod1.sintef.no/datakatalog) – Nedlastbar javaklient for komplett innsyn i datakatalogen

### Eksempel på vegobjekt

Et vegobjekt har en unik id, en unik url, og er av en bestemt vegobjekttype.

```json
{
    "id": 79632559,
    "href": "https://www.vegvesen.no/nvdb/api/v2/45/79632559",
    "type": {
        "id": 45,
        "navn": "Bomstasjon"
    }
}
```



#### Egenskaper

Et vegobjekt har egenskaper med tilhørende verdier. Hver egenskapstype er angitt med navn og id, og verdi:

```json
{
    "egenskaper": [
        {
            "id": 9409,
            "navn": "Tidsdifferensiert takst",
            "verdi": "Nei"
        },
        {
            "id": 9412,
            "navn": "Timesregel",
            "verdi": "Ja"
        },
        {
            "id": 9413,
            "navn": "Vedtatt til år",
            "verdi": 2030
        }
    ]
}
```

Datakatalogen inneholder informasjon om blant annet egenskapstypene datatype, for eksempel _tekst_, _tall_ eller _enum_. For egenskapstyper av type enum er det definert et sett av tillatte verdier.

#### Koordinater

Et vegobjekt er koordinatfestet, som gjør at det kan vises på kart. Enten punkt-, linje- eller flategeometri. Koordinatene er lagret i projeksjonen UTM33 i NVDB-databasen, men det er også mulig å hente WGS84-koordinater gjennom NVDB API.

De fleste vegobjekter har en geometri som er utledet fra vegnettet, og er derfor plassert langs vegens senterlinje. Egengeometri blir mer og mer utbredt. Om et objekt har egengeometri eller ikke, er eksplisitt angitt i API-responsen.

```json
{ "geometri": "POINT Z (271441.3500267718 7039309.464531345, 0)" }
```


#### Stedfesting til vegnettet

Et vegobjekt er stedfestet til vegnettets lenke-node-struktur. Et punktobjekt er enthydig stedfestet ved en veglenke-id + en relativ posisjon på veglenkenlenken mellom 0 og 1\. Strekningsobjekter stedfestes ved en eller flere veglenkesegmenter, ved lenke-id og fra- og til-posisjon. Det kan også angis egenskaper for stedfestingen: Sideposisjon, kjørefelt og retning.

Veglenkeposisjonen er først og fremst nyttig for de som har bruk for objektets posisjon på det topologiske vegnettet.

```json
{
    "stedfesting": {
        "veglenkeid": 320689,
        "posisjon": 0.25899,
        "sideposisjon": "H",
        "felt": "NULL",
        "retning": "MED",
        "kortform": "0.25899@320689"
    }
}
```

#### Vegreferanse

Et vegobjekt har vegreferanse, for eksempel Europaveg 6, E6, som er en administrativ betegnelse på vegen.

```json
{
    "vegreferanse": {
        "fylke": 16,
        "kommune": 0,
        "kategori": "E",
        "status": "V",
        "nummer": 6,
        "hp": 1,
        "meter": 2024,
        "kortform": "5000 Ev6 hp1 m2024"
    }
}
```


#### Områder

Et vegobjekt har beliggenhet innenfor administrative områder.

```json
{
    "lokasjon": {
        "kommuner": [
            1103
        ],
        "fylker": [
            11
        ],
        "regioner": [
            3
        ],
        "vegavdelinger": [
            11
        ],
        "kontraktsomrader": [
            {
                "nummer": 1153,
                "navn": "1153 ELEKTRO"
            }
        ]
    }
}
```



#### Relasjoner

Et objekt kan ha en assosiasjon til andre objekter, oftest i form av en komposisjon. Et ordinært vegskilt består for eksempel av ett skiltpunkt, som er komponert av flere skiltplater. I NVDB kalles denne type relasjoner ofte for mor-datter-koblinger.

#### Historikk

Objekter i NVDB-databasen slettes som hovedregel ikke. De settes i stedet historiske, ved at det angis en sluttdato. Dersom et objekt får en ny verdi på en egenskap, slettes ikke den eksisterende egenskapsverdien fra databasen. Det opprettes i stedet en ny versjon av objektet. Den eksisterende versjonen får en sluttdato, mens den nye versjonen får en tilsvarende startdato.

Per dagens dato er det kun gyldige objektversjoner som eksponeres i NVDB API. Det vil snart®™ utvikles støtte for historiske data.

#### Vegnett

For mer informasjon, les [Håndbok V830 Nasjonalt vegreferansesystem](http://www.vegvesen.no/_attachment/61505/binary/1000471?fast_title=H%C3%A5ndbok+V830+Nasjonalt+vegreferansesystem.pdf).