---
title: Tilgangskontroll
order: 4
permalink: /tilgangskontroll
---

## Tilgangskontroll

Tilgangskontroll eller autorisasjon i NVDB API Skriv reguleres i to nivåer: endepunktsnivå og datanivå. En request med
godkjent autentiseringstoken autoriseres basert på de rollene brukeren har i Statens vegvesens LDAP-register og mer
finkornede datarettigheter tildelt via Kontrollpanelet til NVDB API Skriv.

### Tilgang til endepunkter og ressurser

For å kunne anrope et bestemt endepunkt må brukeren ha en rolle som gir denne tilgangsrettigheten. Det er tre
tilgangsnivåer for endepunkter og ressurser, avhengig av tildelt rolle:

Rolle|Brukertype|Tilgangsrettigheter
-|-|-
nva/0_bruker_fagdata|Personlig bruker|Registrere nye endringssett<br/>Gjøre operasjoner på egne endringssett<br/>Hente egne endringssett<br/>Hente status og fremdriftskode på egne endringssett<br/>Hente statistikk for egne endringssett<br/>Hente egne datarettigheter<br/>Hente alle låser<br/>Hente alle oppdrag og transaksjoner
TjeNVDBAPIskriv/user|Tjenestebruker|Alle over<br/>Hente status og fremdriftskode for andres endringssett<br/>Hente statistikk for andres endringssett<br/>Hente andres datarettigheter
nva/9_system_admin|API-administrator|Alle rettigheter

En tjenestebruker benyttes typisk av serverbaserte fagsystemer som utfører batchvise registreringer av endringssett uten
involvering av en fysisk bruker.

En bruker har normalt bare én av disse rollene. Enkelte brukere har imidlertid både nva/0_bruker_fagdata og nva/9_system_admin,
og får da tilgangsrettigheter tilsvarende rollen med videste fullmakter.

Requester mot endepunkter som brukeren ikke har tilgang til får respons med [HTTP-statuskode](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
403 FORBIDDEN.

### Tilgang til NVDB-data

#### Datarettigheter

Under behandling vil et endringssett bli kontrollert mot de finkornede datarettighetene som er tildelt brukeren. Slike datarettigheter
definerer hvilke vegobjekttyper (HVA) brukeren har rett til å manipulere og på hvilken del av vegnettet (HVOR) disse vegobjektene må være
stedfestet for å kunne manipulere dem. Sistnevnte defineres ved hjelp av følgende områdebegreper:

* Kommuner
* Fylker
* Vegkategorier (Europaveg, riksveg, fylkesveg osv.)
* Typer veg (Kanalisert veg, enkel bilveg, rampe, rundkjøring osv.)

Datarettigheter tildeles personlige brukere og tjenestebrukere av en API-administrator via Kontrollpanelet til NVDB API Skriv.
API-administratoren selv har automatisk alle datarettigheter.

Dersom et endringssett manipulerer vegobjekter av en type eller i et område brukeren ikke har datarettigheter til blir det avvist under
behandling med avvistårsak ```IKKE_AUTORISERT```. Endringssettets status vil inneholde et feilvarsel med varselkode ```MANGLER_TILGANG```
som beskriver hvilke rettigheter som mangler.

En klient kan om ønskelig hente datarettighetene tildelt en bruker via et eget [endepunkt](./datarettigheter/api-referanse.md).

#### Sensitive egenskapstyper

Enkelte egenskapstyper i datakatalogen anses som sensitive og kan ikke manipuleres med mindre brukeren har spesifikke
sensitivitetsroller i LDAP. Datakatalogen operer med tre sensitivitetskoder som krever hver sin rolle:

Rolle|Tilgangsrettighet
-|-
nva/0_sensitive_role1|Manipulere egenskapstyper med sensitivitetskode 1
nva/0_sensitive_role2|Manipulere egenskapstyper med sensitivitetskode 2
nva/0_sensitive_role3|Manipulere egenskapstyper med sensitivitetskode 3

Dersom et endringssett manipulerer (registrerer, legger til, oppdaterer eller fjerner) egenskaper med en sensitivitetskode
som brukeren ikke har rolle for blir det avvist under behandling med avvistårsak ```IKKE_AUTORISERT```. Endringssettets status
vil inneholde et feilvarsel med varselkode ```MANGLER_TILGANG``` som beskriver hvilken rolle som mangler.
