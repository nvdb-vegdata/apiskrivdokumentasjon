---
category: 4#Stedfesting
title: Introduksjon
order: 1
permalink: /stedfesting/introduksjon
---

## Introduksjon til stedfesting [BETA]

NVDB API Skriv tilbyr et endepunkt for å generere gyldig punkt- eller strekningsstedfesting for vegobjekter.
Stedfestingen (vegnettstilknytningen) beregnes ved å "projisere" vegobjektets geometri ned på nærmeste relevante vegnett på
vegtrasénivå. Forutsetningen er derfor at vegobjektene har minst én geometriegenskap. Stedfestingsendepunktet tar høyde for
faktorer som:
 
* Vegobjektets levetid (begrenser gyldig vegnett for stedfesting)
* Om vegobjekttypen tillater stedfesting på konnekteringsveglenker
* Minimumslengde for strekningsstedfesting
* Om vegobjekttypen krever at stedfestingen er innenfor stedfestingen til morobjektet

Beregnet strekningsstedfesting kan bestå av flere stedfestingselementer (flere veglenkesekvenser). I så fall beskriver den
en sammenhengende rute langs en og samme veg. En veg i dette tilfellet er kombinasjonen av vegkategori,
vegfase og vegnummer.

Responsen fra endepunktet, dersom en stedfesting lot seg beregne, er garantert å være gyldig for det aktuelle vegobjektet
på det tidspunktet den ble levert. Vegnettet kan imidlertid endre seg over tid og gjøre en tidligere gyldig stedfesting ugyldig.
Mottatt stedfesting bør derfor brukes i et endringssett innen rimelig tid.

### Validering

Vegobjekter som sendes til stedfestingsendepunktet gjennomgår en liten valideringssekvens for å sikre at det er levert nok
og korrekt informasjon til at stedfesting kan gjennomføres. Valideringen inneholder noen av de samme grunnleggende kontrollene
som utføres for vegobjekter i endringssett.

Valideringen kontrollerer bl.a. at:

* Vegobjekttypen er kjent i datakatalogen.
* Egenskapstypene er kjent i datakatalogen og tillatt for den aktuelle vegobjekttypen.
* Vegobjekttypen krever punkt- eller strekningsstedfesting.
* Vegobjekttypen har minst én geometriegenskap
* Vegobjektet har angitt minst én geometriegenskap
* Geometriegenskaper har gyldig verdi
  
Dersom det avdekkes én eller flere uregelmessigheter vil responsen inneholde beskrivelse av disse. I tillegg til
varselkodene som er [felles med endringssett](../endringssett/behandlingsresultat.md#varselkoder), kan følgende koder forekomme: 

Varselkode|Forklaring
-|-
MANGLER_GEOMETRI|Vegobjektet har ingen gyldige geometriegenskaper.
MOROBJEKT_MANGLER_STEDFESTING|Kan ikke stedfeste datterobjektet når stedfesting av morobjektet mislyktes.
STEDFESTING_MISLYKTES|Gyldig vegnettstilknytning for vegobjektet ble ikke funnet med de angitte parametere.
UGYLDIG_VEGOBJEKTTYPE|Vegobjektet krever ikke punkt- eller strekningsstedfesting.

### Geometriegenskaper

Enkelte vegobjekttyper tillater flere geometriegenskaper. For mest mulig presis stedfesting anbefales at så mange som mulig
av geometriegenskapene leveres med i vegobjektene, også for vegobjekttyper som skal ha punktstedfesting.

Geometriegenskapen som brukes for å projisere til en punktstedfesting, velges etter følgende prioritet:

1. Punkt
2. Kurve (bruker sentroiden)
3. Flate (bruker sentroiden)

Geometriegenskapen som brukes for å projisere til en strekningsstedfesting, velges etter følgende prioritet:

1. Kurve
2. Flate
3. Punkt

### Mor- og datterrelasjoner

Vegobjektene i requesten beskrives på samme måte som i endringssett, selv om bare geometriegenskaper er obligatoriske
for dette endepunktet. Dersom mor- og datterobjekter leveres i samme request og assosisasjonen mellom dem er beskrevet, vil
stedfestingen av datterobjektet ta hensyn til om det er av en vegobjekttype som krever at stedfestingen er innenfor morobjektets
stedfesting.

Dersom morobjektet er beskrevet i requesten med stedfesting vil denne ikke bli reberegnet. Stedfestingen til datterobjektet
vil imidlertid bli plassert innenfor denne om det kreves. Dette kan være nyttig dersom klienten skal registrere et nytt datterobjekt
for et morobjekt som allerede er registrert i NVDB.
 
### BETA

Stedfestingsendepunktet er tilgjengelig i alle miljøer hos Statens vegvesen, men er fortsatt under utvikling og testing. Det kan
forekomme endringer i både request-parametere og payloads, uten at endepunktet versjoneres.
