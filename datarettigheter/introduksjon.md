---
category: 8#Datarettigheter
title: Introduksjon
order: 1
permalink: /datarettigheter/introduksjon
---

# Nytt endepunkt for [NVDB api SKRIV dokumentasjon](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

NVDB api SKRIV V3 dokumentasjon er flyttet til [https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

API'et blir jo aktivt vedlikeholdt og videreutviklet, så vi anbefaler sterkt at dere legger om til den nyeste versjonen av dokumentasjonen. 


## Introduksjon til datarettigheter

Datarettigheter definerer hvilke vegobjekttyper (HVA) en bruker har rett til å manipulere og på hvilken del av vegnettet
(HVOR) disse vegobjektene må være stedfestet for å kunne manipulere dem. Datarettigheter tildeles personlige brukere og
tjenestebrukere av en API-administrator via [kontrollpanelet](../kontrollpanel.md) til NVDB API Skriv. API-administratorer har automatisk
alle datarettigheter.

NVDB API Skriv tilbyr et endepunkt for å hente datarettighetene en bruker er tildelt. Endepunktet kan f.eks. brukes
til å avgrense handlingsrommet til brukeren i klientens GUI for å forhindre at det bygges opp et endringssett med
operasjoner som vil bli avvist under behandling.
