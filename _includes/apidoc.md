NVDBs API SKRIV lar deg oppdatere eksisterende, registrere nye eller slette vegobjekter i NVDB.

## Endringssett

Systemet mottar beskrivelser av vegobjekter (_endringssett_) for _registrering_, _oppdatering_ eller _sletting_ i NVDB.
En rekke validerings- og kontrolltrinn gjennomføres før data er godkjent og  sendes til NVDB. Et endringssett under 
behandling omtales som en jobb. Flere av trinnene i en jobb krever oppslag i NVDB og vil derfor kunne ta en del tid, 
spesielt for klienter som registrerer mange vegobjekter samtidig. I tillegg vil selve skrivingen til NVDB også ta tid. 
Videre vil noen oppdateringer ikke kunne skje umiddelbart fordi data er låst av andre klienter (eller av en annen jobb 
i dette APIet). En konsekvens av dette er at jobber må eksekveres asynkront. Det vil si at klienten sender forespørsler 
for å definere og starte jobber, men siden svaret vil ta noe tid å utarbeide kan ikke et endelig svar leveres med en gang. 
Jobben blir i stedet behandlet i bakgrunnen, i en separat prosess som ikke blokkerer klienten.


NVDB API Skriv setter strenge krav til mottatte data:

* Data valideres strengt ihht siste versjon av datakatalogen [https://datakatalogen.vegdata.no](Datakatalogen)
* Kun data som er komplette og korrekte ihht definisjonene i datakatalogen vil bli lagret i NVDB
* Data valideres også opp mot allerede lagrede data
* Alle data som skrives vha API Skriv må være stedfestet med veglenkeposisjon. Klienter er selv ansvarlig for å sende inn korrekte veglenkeposisjoner for sine objekter.  



Endringer i NVDB gjennomføres ved hjelp av endringssett. Et endringssett er en liste med vegobjekter som skal registreres,
oppdateres, korrigeres eller slettes i NVDB. Et endringssett har en id, effektdato, datakatalogversjon og ansvarlig, og 
består av registrer, oppdater, delvisOppdater, korriger, delvisKorriger, slett, status og etterbehandling. Registrer, 
oppdater, delvisOppdater, korriger, delvisKorriger, slett består igjen av vegobjekter.


## Tilgjengelige ressurser

|VERB|URI|Beskrivelse|Dokumentasjon|
|:---|:---|:---|:---:|
|POST|/nvdb/apiskriv/rest/v2/endringssett/validator|Valider et endringssett.|dokumentasjon|
|POST|/nvdb/apiskriv/rest/v2/endringssett|Registrer et endringssett.|dokumentasjon|
|GET|/nvdb/apiskriv/rest/v2/endringssett|Se oversikt over endringssett.|dokumentasjon|
|POST|/nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/start|Start behandling av et endringssett.|dokumentasjon|
|POST|/nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/kanseller|Kanseller behandling av et endringssett.|dokumentasjon|
|POST|/nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/restart|	Restart behandling av et endringssett.|dokumentasjon|
|GET|/nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/fremdrift|	Se fremdrift på et endringssett.|dokumentasjon|
|GET|/nvdb/apiskriv/rest/v2/endringssett/{endringssettId}/status|	Se status for et endringssett.|dokumentasjon|
|GET|/nvdb/apiskriv/rest/v2/endringssett/{endringssettId}|	Se innholdet i et endringssett.|dokumentasjon|
|POST|/nvdb/apiskriv/rest/v2/konverter/sosiGeoJson|	Konverter SOSI-GeoJSON til endringssett-format.|dokumentasjon|
|POST|/nvdb/apiskriv/rest/v2/konverter/sosi|Konverter SOSI-NVDB til endringssett-format.|dokumentasjon|
|POST|/nvdb/apiskriv/rest/v2/binaer|Last opp binærdata.|dokumentasjon|
|POST|/nvdb/apiskriv/rest/v2/binaer|Last opp multipart/form-data|dokumentasjon|
|GET|/nvdb/apiskriv/rest/v2/binaer/{ressursId}|Last ned binærdata.|dokumentasjon|


## Retningslinjer for klientutvikling
Alle klienter som sender forespørsler til APIet skal identifisere seg via header-parameteren X-Client.

For å undersøke behandlingsfremdriften på et endringssett, er det anbefalt å benytte Se fremdrift fremfor Se status.

Se status bør kun benyttes for å hente nvdbId på registrerte objekter, eller for å se eventuelle feilmeldinger etter at behandlingen har stanset.

## Livssyklus for endringssett
Oppdatering av vegobjekter i NVDB skjer ved å sende inn et endringssett som beskriver endringene. Klienten mottar umiddelbart en URI til endringssettet.

Når et endringssett er registrert kan klienten starte asynkron behandling av endringssettet ved å kalle endringssettets start-URI.

Klienter kan kontrollere forløpet for behandlingen av et endringssett ved å gjøre et kall til endringssettets fremdrift-URI og status-URI.


## Endringssettet kan avvises dersom:

En automatisk validering feiler. [Les mer...]
Ett eller flere av vegobjektene som skal endres er allerede blitt endret av andre og har fått høyere versjonsnummer enn i dette endringssettet.
Hvis endringssettet feiler må klienten korrigere feilene og registrere et nytt endringssett.

## Mediatyper
APIet støtter både XML og JSON.

Klienter angir ønsket MediaType-representasjon fra serveren ved å sette "Content-Type"/"Accept"-header i request.

|Type|MediaType|
|-|-|
|JSON|application/json|
|XML|application/xml|

## Feilhåndtering
Feil kan oppstå på to stadier:

Det kan oppstå feil når klienten registrerer eller henter et endringssett.
Det kan oppstå feil når serveren behandler et endringssett asynkront.
Ved feil som oppstår ved registrering eller henting av et endringssett vil serveren returnere en standard HTTP statuskode. I tillegg får man en beskrivelse av feilen i form av en feil entitet.

Eksempel

```xml
HTTP/1.1 400 Bad Request

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<fault xmlns="http://nvdb.vegvesen.no/apiskriv/fault/v1">
    <message>Må være unik: tempId</message>
</fault>
```

Ved feil som oppstår ved asynkron behandling av et endringssett vil endringssettet få en feil-status.