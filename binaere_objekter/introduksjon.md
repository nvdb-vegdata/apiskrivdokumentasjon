---
category: 2#Binære objekter
title: Introduksjon
order: 1
permalink: /binære-objekter/introduksjon
---


# Nytt endepunkt for [NVDB api SKRIV dokumentasjon](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

NVDB api SKRIV V3 dokumentasjon er flyttet til [https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

API'et blir jo aktivt vedlikeholdt og videreutviklet, så vi anbefaler sterkt at dere legger om til den nyeste versjonen av dokumentasjonen. 


## Introduksjon til binære objekter

Enkelte vegobjekter har egenskaper som kan inneholde store binære objekter ([BLOBs](https://en.wikipedia.org/wiki/Binary_large_object))
som digitale bilder, videoer o.l. På grunn av størrelsen kan imidlertid endringssett ikke ta i mot binære objekter som
egenskapsverdier direkte. Binærobjektene må først lastes opp via et eget endepunkt. Opplastingen responderer med en ressursid
som benyttes som egenskapsverdi i endringssettet.

### Bruk av binærobjekt som egenskapsverdi

For å angi et binærobjekt som egenskapsverdi i et vegobjekt benyttes elementet `<binaer>`, f.eks.:

```xml
<egenskap typeId="7900">
  <binaer ressursId="e62a6896-ff48-474c-a5f3-bb5ccc45c9c9" format="image/png"/>
</egenskap>
```

Attributtene til ```<binaer>``` angis slik:
 
* ```ressursId``` : id'en mottatt fra [opplastingsendepunktet](api-referanse.md#laste-opp-binærdata)
* ```format``` : media-type i henhold til [IANA](http://www.iana.org/assignments/media-types/media-types.xhtml)
