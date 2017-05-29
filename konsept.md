# Konsept

NVDBs API SKRIV lar deg oppdatere eksisterende, registrere nye eller slette vegobjekter i NVDB.

## Endringssett
Endringer i NVDB gjennomføres ved hjelp av endringssett. Et endringssett er en liste med vegobjekter som skal registreres, oppdateres, korrigeres eller slettes i NVDB. Et endringssett har en id, effektdato, datakatalogversjon og ansvarlig, og består av underelementene registrer, oppdater, delvisOppdater, korriger, delvisKorriger, slett, status og etterbehandling. Registrer, oppdater, delvisOppdater, korriger, delvisKorriger, slett består igjen av vegobjekter.

## Id
Id er en unik identifikator for endringssettet, og tildeles av systemet ved registrering av endringssettet.

## Effektdato
Effektdato angir gyldigheten til vegobjektene i endringssettet. For vegobjekter i registrer, oppdater, delvisOppdater betyr dette startdato. For vegobjekter i slett betyr dette sluttdato.

## Datakatalogversjon
Angir hvilken datakatalogversjon klienten har benyttet for å definere endringssettet. Det gis en advarsel hvis klienten har benyttet en annen versjon enn gjeldende versjon.

## Ansvarlig
Ansvarlig benyttes av fagsystemer som registrerer et endringssett på vegne av en annen bruker. Feltet er kun informativt og bare obligatorisk for systembrukere.

## Registrer
Registrer består av nye, ikke tidligere registrerte vegobjekter. Et vegobjekt i registrer må ha en tempId og kan også ha en sluttDato. StartDato angis av effektDato.

## Oppdater
Oppdater forårsaker en ny versjon av vegobjekter. Oppdater består av eksisterende, allerede registrerte vegobjekter. Et vegobjekt i oppdater må ha nvdbId og versjon og kan også ha en sluttDato. StartDato for den nye versjonen av vegobjektet angis av effektDato.

## Delvis oppdater
Fungerer på samme måte som oppdater. Men her kan en velge å oppdatere, slette eller beholde spesifikke egenskaper, assosiasjoner og lokasjon, uten å beskrive et fullstendig vegobjekt. Oppdatering av en egenskap angis ved å sette <egenskap operasjon="oppdater">. Sletting av en egenskap angis ved å sette <egenskap operasjon="slett">. Egenskaper som ikke er angitt beholdes fra forrige versjon av objektet.

## Korriger
Korriger forårsaker IKKE ny versjon av vegobjekter. Korriger består av eksisterende, allerede registrerte vegobjekter. Et vegobjekt i korriger må ha nvdbId og versjon.

## Delvis korriger
Fungerer på samme måte som korriger, men med samme forenkling som Delvis oppdater.

## Slett
Slett forårsaker at vegobjektet blir lukket, altså sluttDato blir satt i angitte versjon. Slett består av eksisterende, allerede registrerte vegobjekter. Et vegobjekt må ha nvdbId og versjon. SluttDato angis av effektDato.

##Vegobjekter
Et vegobjekt må ha en typeId og består av egenskaper, assosiasjoner og lokasjon.

## Egenskaper
En egenskap må ha en typeId og består av verdi, enum, struktur eller binaer. Kun en av verdi, enum, struktur og binaer kan angis på en egenskap.

### Verdi

Benyttes av egenskaper med datatype boolsk, tegn, dato, heltall, desimaltall, kortdato, geometri, tekst og tid.

|Datatype|Eksempel|
|-|-|
|Boolsk|ja, nei, true og false|
|Tegn|A|
|Dato|2015-02-26 (eller 20150226)|
|Heltall|123456789|
|Desimaltall|	123456.789|
|Kort dato	02-26
|Geometri|srid=4326;datafangstdato=2013-11-26Z;lengde=0;målemetode=96;målemetodeHøyde=96;nøyaktighet=5;nøyaktighetHøyde=5;synbarhet=0;maksimaltAvvik=0;temakode=999;medium=0;kommune=1601;POINT(63.430602994329256 10.391780132804493)|
|Tekst|Grevlingtunnelen|
|Tid|09:12|


### Enum

Enum angir identifikator for en verdi i et kontrollert vokabular.

###Struktur

Benyttes for egenskaper av type struktur.

Eksempel:
```xml
<egenskap typeId="6084">
    <struktur>
        <medlem typeId="6085">
            <verdi>0.1</verdi>
        </medlem>
        <medlem typeId="6086">
            <verdi>10</verdi>
        </medlem>
        <medlem typeId="6087">
            <verdi>1</verdi>
        </medlem>
    </struktur>
</egenskap>
```

###Binaer

Benyttes for egenskaper av datatype binærobjekt.

Eksempel

```xml
...
  <assosiasjoner>
    <assosiasjon typeId="220373">
      <tempId>-2</tempId>
      <nvdbId>123789456</nvdbId>
    </assosiasjon>
  </assosiasjoner>
...
```

## Lokasjon
Lokasjon er bygd opp på samme måte som en enkelt egenskap (typeId angis ikke), og punkt, linje og sving er analogt med verdi, enum, struktur og binaer.

Eksempel - punkt
```xml
...
  <lokasjon>
    <punkt lenkeid="123456789" posisjon="0.5">
  </egenskap>
...
```
Eksempel - linje
```xml
...
  <lokasjon>
    <linje lenkeid="123456789" fra="0.4" til="0.6">
  </egenskap>
...
```
Eksempel - sving
```xml
...
  <lokasjon>
    <sving nodeid="789456123">
      <punkt lenkeid="456123789" posisjon="0.99">
      <punkt lenkeid="123456789" posisjon="0.01">
    </sving>
  </egenskap>
...
```
