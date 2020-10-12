---
category: 6#Oppdrag og transaksjoner
title: Introduksjon
order: 1
permalink: /oppdrag-og-transaksjoner/introduksjon
---

## Introduksjon til oppdrag og transaksjoner

Et _oppdrag_ (også kjent som _task_) er et objekt i NVDB som beskriver et innsjekk av vegobjekter og/eller vegnett til NVDB. Når dette
innsjekket gjøres av NVDB API Skriv vil hvert godkjente og ferdigbehandlede endringssett representeres ved ett enkelt oppdrag. Et oppdrag identifiseres via en oppdragId, som er et unikt heltall.

Et oppdrag består av én eller flere transaksjoner. Hver transaksjon beskriver et sett av operasjoner som er gjort på bestemte vegobjektversjoner eller nettelementer i NVDB som del av et innsjekk.
Operasjoner kan være sletting, registrering og oppdatering. I NVDB API Skriv vil alle operasjoner i et endringssett beskrives i en og samme transaksjon.
En enkelt transaksjon identifiseres ikke via en egen id, men gjennom kombinasjonen av oppdragId og datoen/tidspunktet (med sekundnøyaktighet) for transaksjonen.

Informasjonen i oppdrags- og transaksjonsobjektene representerer en komplett revisjonslogg over endringene som er gjort på vegobjekter og nettelementer i NVDB over tid. I dagligtale kalles dette gjerne
bare "transaksjonsloggen i NVDB".