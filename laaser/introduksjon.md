---
category: 5#Låser
title: Introduksjon
order: 1
permalink: /laaser/introduksjon
---

## Introduksjon til låser

En _lås_ er et objekt i NVDB som indikerer at en prosess jobber med spesifikke vegobjekttyper og/eller deler av vegnettet.
Låser brukes for å hindre [race conditions](https://en.wikipedia.org/wiki/Race_condition) når flere klienter skal oppdatere eller registrere vegobjekter eller vegnett i NVDB samtidig.
Siden data i NVDB er komplekse og sammensatte holder det ikke med transaksjonsisolering og tradisjonelle låsemekanismer som tabell- og radlåser i databasen.

### Pessimistisk og optimistisk låsing

Klienter som bruker klassisk API og vegnettsredigeringsklienten setter opp låser selv med egne APIer, og dette er potensielt langlevde låser, gjerne med levetid på mange dager.
Dette kalles _forhåndslåsing_ eller _pessimistisk låsing_. Disse klientene fjerner låsene selv når innsjekk av data til NVDB er fullført. Moderne fagdataklienter som benytter
NVDB API Skriv, trenger ikke tenke på låsing, da systemet selv vil etablere nødvendige låser underveis i behandlingen og fjerne dem umiddelbart etter at endringssettet er
ferdigbehandlet. Dette er svært kortlevde låser, ofte med levetid på bare noen få sekunder. Dette kalles _optimistisk låsing_.

### Blokkerende låser

Når det skal etableres låser underveis i behandlingen av et endringssett, kan det i noen tilfeller allerede finnes låser i NVDB som kommer i konflikt med de låsene som ønskes satt.
Behandlingen kan da ikke fortsette før de blokkerende låsene er borte. I denne situasjonen havner endringssettet midlertidig i status VENTER med venteårsak VENTER_PÅ_LÅS.
NVDB API Skriv vil automatisk gjenoppta behandlingen etter en liten pause. Dersom de blokkerende låsene ennå ikke har forsvunnet, gjentas denne syklusen med stadig lengre ventetid
mellom hvert forsøk. Dersom man opplever at et endringssett blokkeres urimelig lengde, det vil si en uke eller mer, kan brukeren finne ut hvem som eier de blokkerende låsene ved å gå
inn på detaljvisningen for endringssettet i [kontrollpanelet](kontrollpanel.md). Eieren bør da kontaktes og vedkommende vil kunne avgjøre om låsene er "avglemt" og kan fjernes, eller om de må leve videre inntil han/hun er
ferdig med databearbeidingen og kan sjekke dem inn i NVDB. Langlevde låser har som nevnt oftest opphav i gamle klienter som bruker klassisk API. Når disse etter hvert migreres over
til NVDB API Skriv, vil blokkerende låser bli et mindre problem.

### Normallås og sekundærlås

Låsing av spesifikke vegobjekttyper kan gjøres i to ulike grader/styrker. Ved _normallåsing_ indikeres at man ønsker å endre på vegobjekter av
den angitte typen og at ingen andre prosesser eller klienter kan skrive til de samme dataene. De bør heller ikke lese inn de samme dataene fra NVDB,
siden de må regnes som midlertidige eller flyktige. Dette er den vanligste måten å låse på i forkant av innsjekk av nye og oppdaterte vegobjekter til NVDB.

I noen tilfeller ønsker man ikke å endre på data i NVDB, bare lese de inn for nærmere analyse eller sammenstilling med data som skal skrives til NVDB.
For å sikre at ingen andre prosesser endrer på disse mens man jobber med dem, kan man etablere _sekundærlås_ på disse vegobjekttypene.
NVDB API Skriv henter inn mange vegobjekter som del av valideringen av endringssett og etablerer da nødvendige sekundærlåser på disse. En sekundærlås
kan ikke etableres hvis det allerede finnes en normallås på samme vegobjekttype på samme del av vegnettet. Det går imidlertid fint å etablere en
ny sekundærlås som bare overlapper eksisterende sekundærlåser i NVDB. 