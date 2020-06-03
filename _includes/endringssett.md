# Endringssett
Et endringssett er en liste med vegobjekter som skal registreres, oppdateres, korrigeres eller slettes i NVDB.

## Valider et endringssett
Gjør det mulig å kontrollere innholdet i et endringssett uten å måtte registrere det og deretter starte behandling. Det gjøres bare validering mot datakatalogen. Kontroll av sammenhengregler og annen mer kompleks validering utføres ikke. Denne ressursen gir synkron respons og gjøre ingen endringer i NVDB.

Mønster
```
POST /nvdb/apiskriv/rest/v2/endringssett/validator
```

**Forespørsel**

Parametre

|Navn|Type|Beskrivelse|
|-|-|-|
|skipLocation|Boolean|Angir om stedfesting (lokasjon) skal valideres|
|skipAssociations|Boolean|Angir om assosiasjoner skal valideres|
|useObjectList|Boolean|Gjør validering mot objektlista i stedet for datakatalogen.|

Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Content-Type|MediaType	Angir hvilken media-type kroppen er. application/json eller application/xml.|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. {Content-Type} benyttes hvis ikke annet er oppgitt.|
|x-client|Tekst|Angir navnet på klientapplikasjonen.|

Kropp
Eksempel

```
POST /nvdb/apiskriv/rest/v2/endringssett/validator HTTP/1.1
Content-Type: application/xml
X-Client: MinKlientApplikasjon
```

```xml
<endringssett xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2" effektDato="2014-02-26" datakatalogversjon="2.01">
  <registrer>
    <vegObjekter>
      <vegObjekt typeId="581" tempId="-1">
        <egenskaper>
          <egenskap typeId="5225">
            <verdi>Grevlingtunnelen</verdi>
          </egenskap>
          ...
        <lokasjon>
          <punkt lenkeId="1125766" posisjon="0.3"/>
        </lokasjon>
      </vegObjekt>
    </vegObjekter>
  </registrer>
</endringssett>
```
Svar
Kropp
Eksempel

```xml
HTTP/1.1 200 OK


<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<status xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">
    <mottatt>2015-02-26T12:05:24.854</mottatt>
    <fremdrift>AVVIST</fremdrift>
    <avvistårsak>VALIDERINGSFEIL</avvistårsak>
    <resultat>
        <vegObjekter>
            <vegObjekt tempId="-1"/>
                <feil>
                    <feil kode="UKJENT_EGENSKAPSTYPE">
                        <melding>Egenskapstypen 0 finnes ikke i datakatalogen</melding>
                        <referanse>http://labs.vegdata.no/nvdb-datakatalog/581</referanse>
                        <egenskapTypeId>0</egenskapTypeId>
                    </feil>
                </feil>
            </vegObjekt>
        </vegObjekter>
        <feil/>
        <advarsel/>
    </resultat>
    <eier>krimyr</eier>
</status>
```

## Registrer et endringssett
Etter at endringssettet er registrert må start-URI benyttes for å starte behandling.
Mønster
```
POST /nvdb/apiskriv/rest/v2/endringssett
```

**Forespørsel**

Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Content-Type|MediaType|Angir hvilken media-type kroppen er. application/json eller application/xml.|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. {Content-Type} benyttes hvis ikke annet er oppgitt.|
|x-client|Tekst|Angir navnet på klientapplikasjonen.|
|x-nvdb-dryrun|Boolean|	Angir om dette er en prøvekjøring (gjør ingen endringer i NVDB) av et endringssett.|
|x-nvdb-delay|Integer|Benyttes for å simulere at behandlingen av et endringssett tar lang tid. Angis i antall sekunder.|

Kropp
Eksempel

```xml
POST /nvdb/apiskriv/rest/v2/endringssett HTTP/1.1
Content-Type: application/xml
X-Client: MinKlientApplikasjon


<endringssett xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2" effektDato="2014-02-26" datakatalogversjon="2.01">
  <registrer>
    <vegObjekter>
      <vegObjekt typeId="581" tempId="-1">
        <egenskaper>
          <egenskap typeId="5225">
            <verdi>Grevlingtunnelen</verdi>
          </egenskap>
          ...
        <lokasjon>
          <punkt lenkeId="1125766" posisjon="0.3"/>
        </lokasjon>
      </vegObjekt>
    </vegObjekter>
  </registrer>
</endringssett>
```
**Svar**
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Location|URI|Angir hvor en kan lese ressursen.|

Kropp
Eksempel

```xml
HTTP/1.1  201 Created
Location: http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037


<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ressurser xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">
    <ressurs rel="self"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037"/>
    <ressurs rel="start"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/start"/>
    <ressurs rel="kanseller"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/kanseller"/>
    <ressurs rel="status"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/status"/>
    <ressurs rel="fremdrift"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/fremdrift"/>
</ressurser>
```

##Start behandling av endringssett
Behandling av endringssettet starter straks en utførerprosess blir ledig. Bruk fremdrift-URIen til å monitorere behandlingens forløp og status-URI for å hente resultatet når behandlingen har stanset.
Mønster
POST /nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/start

Forespørsel
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. application/json benyttes hvis ikke annet er oppgitt.|
|x-client|Tekst|Angir navnet på klientapplikasjonen.|

Eksempel
```
POST /nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/start HTTP/1.1
Content-Type: application/xml
X-Client: MinKlientApplikasjon
```
Svar
Kropp
Eksempel

```xml
HTTP/1.1 200 OK

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ressurser xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">
    <ressurs rel="self"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037"/>
    <ressurs rel="status"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/status"/>
    <ressurs rel="fremdrift"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/fremdrift"/>
</ressurser>
```

##Kanseller behandling av et endringssett
Kansellering kan ikke gjøres når behandling er startet.
Mønster
```
POST /nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/kanseller
```

Forespørsel
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. application/json benyttes hvis ikke annet er oppgitt.|
|x-client|Tekst|Angir navnet på klientapplikasjonen.|

Eksempel
```
POST /nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/kanseller HTTP/1.1
Content-Type: application/xml
X-Client: MinKlientApplikasjon
```
Svar
Kropp
Eksempel

```xml
HTTP/1.1 200 OK


<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ressurser xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">
    <ressurs rel="self"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037"/>
    <ressurs rel="status"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/status"/>
</ressurser>
```

##Restart behandling av et endringssett
Brukes for å starte ny behandling av et endringssett som har VENTER-status.
Mønster
```
POST /nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/restart
```
Forespørsel
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. application/json benyttes hvis ikke annet er oppgitt.|
|x-client|Tekst|Angir navnet på klientapplikasjonen.|

Eksempel
```
POST /nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/restart HTTP/1.1
Content-Type: application/xml
X-Client: MinKlientApplikasjon
```
Svar
Kropp
Eksempel
```xml
HTTP/1.1 200 OK


<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ressurser xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">
    <ressurs rel="self"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037"/>
    <ressurs rel="status"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/status"/>
    <ressurs rel="fremdrift"
             src="http://localhost:8080/nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/fremdrift"/>
</ressurser>
```

##Se fremdrift for et endringssett
Gir kortfattet informasjon om fremdriften av behandlingen av et endringssett. Brukes til hyppig monitorering av et endringssett der behandling er startet.
Mønster
```
GET /nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/fremdrift
```

Forespørsel
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. application/json benyttes hvis ikke annet er oppgitt.|
|x-client|Tekst|Angir navnet på klientapplikasjonen.|

Eksempel
```
GET /nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/fremdrift HTTP/1.1
Content-Type: application/xml
X-Client: MinKlientApplikasjon
```
Svar
Kropp
Eksempel

```xml
HTTP/1.1 200 OK

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<fremdrift xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">UTFØRT</fremdrift>
```
##Se status for et endringssett
Gir detaljert informasjon om behandlingstatus for et endringssett. Brukes primært når behandling er sluttført eller har kommet i VENTER-status.
Mønster
```
GET /nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/status
```
Forespørsel
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. application/json benyttes hvis ikke annet er oppgitt.|
|x-client|Tekst|Angir navnet på klientapplikasjonen.|

Eksempel
```
GET /nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037/status HTTP/1.1
Content-Type: application/xml
X-Client: MinKlientApplikasjon
```
Svar
Kropp
Eksempel
```xml
HTTP/1.1 200 OK


<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<status xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">
    <mottatt>2015-02-26T12:05:24.854</mottatt>
    <fremdrift>UTFØRT</fremdrift>
    <resultat>
        <vegObjekter>
            <vegObjekt tempId="-1" nvdbId="123456"/>
        </vegObjekter>
        <feil/>
        <advarsel/>
    </resultat>
    <eier>krimyr</eier>
</status>
```
##Se endringssett
Henter ut innholdet i et registrert endringssett.
Mønster
```
GET /nvdb/apiskriv/rest/v2/endringssett/{endringssettId}
```

Forespørsel
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. application/json benyttes hvis ikke annet er oppgitt.|
|x-client|Tekst|Angir navnet på klientapplikasjonen.|

Eksempel
```
GET /nvdb/apiskriv/rest/v2/endringssett/63de0209-18b3-43d7-9944-69a3bc6d4037 HTTP/1.1
Content-Type: application/xml
X-Client: MinKlientApplikasjon
```
Svar
Kropp
Eksempel

```xml
HTTP/1.1 200 OK

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<endringssett xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2" effektDato="2015-02-26"
      datakatalogversjon="2.01">
    <id>314957b6-061f-4f44-b052-296f3117ced3</id>
    <registrer>
        <vegObjekter>
            <vegObjekt typeId="581" tempId="-1">
                <egenskaper>
                    <egenskap typeId="5225">
                        <verdi>Grevlingtunnelen</verdi>
                    </egenskap>
                    ...
                </egenskaper>
                <assosiasjoner/>
                <lokasjon>
                    <punkt lenkeId="1125766" posisjon="0.3" retning="MED"/>
                </lokasjon>
            </vegObjekt>
        </vegObjekter>
    </registrer>
    <oppdater>
        ...
    </oppdater>
    <korriger>
        ...
    </korriger>
    <slett>
        ...
    </slett>
    <delvisOppdater>
        ...
    </delvisOppdater>
    <delvisKorriger>
        ...
    </delvisKorriger>
    <status>
        ...
    </status>
</endringssett>
```
##Se alle endringssett
Henter ut en liste av registrerte endringssett, sortert på registreringstidspunkt.
Mønster
```
GET /nvdb/apiskriv/rest/v2/endringssett
```

Forespørsel
Parametere

|Navn|Type|Beskrivelse|
|-|-|-|
|max|Long|Maksimum antall endringssett i svaret.|
|skip|Long|Hopp over de {skip} første endringssettene.|
|reverse|Boolean|Les fra bunnen.|
|fra|Long|Les kun endringssett innenfor et tidsrom. Angir tidsrommets begynnelse. Angis i antall millisekunder fra 1. januar 1970|
|til|Long|Les kun endringssett innenfor et tidsrom. Angir tidsrommets slutt. Angis i antall millisekunder fra 1. januar 1970|
|status|Status|Les kun endringssett med gitt {status}.|
|årsak|VenteÅrsak eller AvvistÅrsak|Les kun endringssett som er stanset eller venter med gitt {årsak}.|

Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Accept|MediaType|Angir på hvilken media-type en ønsker svar. application/json eller application/xml. application/json benyttes hvis ikke annet er oppgitt.|
|x-client|Tekst|Angir navnet på klientapplikasjonen.|

Eksempel
```
GET /nvdb/apiskriv/rest/v2/endringssett?max=1 HTTP/1.1
Content-Type: application/xml
X-Client: MinKlientApplikasjon
```
Svar
Kropp
Eksempel
```xml
HTTP/1.1 200 OK

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<endringssettliste xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2" totalt="2">
    <endringssett>
        <endringssett effektDato="2015-02-26" datakatalogversjon="2.01">
            <id>63de0209-18b3-43d7-9944-69a3bc6d4037</id>
            <registrer>
                ...
            </registrer>
            <oppdater>
                ...
            </oppdater>
            <korriger>
                ...
            </korriger>
            <slett>
                ...
            </slett>
            <delvisOppdater>
                ...
            </delvisOppdater>
            <delvisKorriger>
                ...
            </delvisKorriger>
            <status>
                ...
            </status>
        </endringssett>
    </endringssett>
</endringssettliste>
```
