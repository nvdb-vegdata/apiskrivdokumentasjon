---
title: Oppbygging
order: 3
---

## Oppbygging av endringssett

### Innhold

[Grunnstruktur](#grunnstruktur)  
[Registrering](#registrering)  
[Oppdatering](#oppdatering)  
[Delvis oppdatering](#delvis-oppdatering)  
[Lukking](#lukking)  
[Korrigering](#korrigering)  
[Delvis korrigering](#delvis-korrigering)  
[Fjerning](#fjerning)
  
[Om egenskapsverdier](#om-egenskapsverdier)  
[Om assosiasjoner](#om-assosiasjoner)  
[Om stedfesting](#om-stedfesting)  
<br/>


### Grunnstruktur

For å få utført endringer i NVDB må disse beskrives i et endringssett. Et endringssett er et XML- eller JSON-dokument som detaljerer
de operasjonene som skal utføres. Klienten kan bruke flere operasjoner i samme endringssett, men det kan bare utføres én
operasjon per vegobjektversjon. 

NVDB API Skriv støtter følgende operasjoner:

Operasjon|Beskrivelse|Typisk bruksområde
-|-|
Registrer|Etablerer et nytt vegobjekt|Nyregistrering
Oppdater|Lager ny versjon av eksisterende vegobjekt|Når det har skjedd observerbare endringer på et vegobjekt langs vegen
Lukk|Setter sluttdato på et eksisterende vegobjekt|Når et vegobjekt er fjernet fra vegen
Korriger|Endrer egenskapene til en eksisterende vegobjektversjon|Når det er feilregistrerte egenskapsverdier
Fjern|Fjerner et eksisterende vegobjekt eller en vegobjektversjon fra NVDB|Når et vegobjekt er feilregistrert

Et endringssett med alle operasjoner vil kunne se slik ut:

```xml
<endringssett>
  <datakatalogversjon>2.20</datakatalogversjon>
  <ansvarlig>exttxa</ansvarlig>  
  <eksternRef>ABC123</eksternRef>
  <kontekst><![CDATA[<HEI></HEI>]]></kontekst>
  <registrer>
    ...
  </registrer>
  <oppdater>
    ...
  </oppdater>
  <lukk>
    ...
  </lukk>
  <korriger>
    ...
  </korriger>
  <fjern>
    ...
  </fjern>
</endringssett>
```

En formell beskrivelse av endringssett-strukturen kan lastes ned i form av et [XML-skjema](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

Elementet ```<datakatalogversjon>``` er obligatorisk og skal angi den datakatalogversjonen som klienten la til grunn når endringssettet
ble dannet. Dersom denne avviker fra den gjeldende versjonen i NVDB vil behandlingsresultatet inneholde en advarsel om dette. Hvis det
inntreffer uventede valideringsfeil kan det skyldes at endringssettet ikke matcher siste datakatalog og første trinn i feilsøkingen
bør derfor være å se etter den nevnte advarselen.

Elementet ```<ansvarlig>``` må angis dersom endringssettet registreres av en tjenestebruker/systembruker. Dette er et vanlig scenario i
headless serversystemer som registrerer og starter endringssett i nattlige batcher. For å knytte endringssettet til en reell eier/opphavsperson
i slike tilfeller må ```<ansvarlig>``` -elementet inneholde brukernavnet til en personlig bruker i Statens vegvesen.

Elementet ```<eksternRef>``` er valgfritt og kan brukes til å legge på en referanse til et objekt, prosjekt e.l. i klienten.

Elementet ```<kontekst>``` er også valgfritt og gjør det mulig å legge inn en større datastruktur med klientspesifikk kontekstinformasjon.
Verdien rammes inn av en CDATA-klausul slik at alle typer dataformater kan brukes, også XML og JSON.

### Registrering

```xml
<registrer>
   <vegobjekter>
      <vegobjekt typeId="581" tempId="tunnel#1">
         <gyldighetsperiode>
           ...
         </gyldighetsperiode>
         <egenskaper>
            ...
         </egenskaper>
         <assosiasjoner>
            ...
         </assosiasjoner>
         <stedfesting>
            ...
         </stedfesting>
      </vegobjekt>
      ...
   </vegobjekter>
</registrer>
```

```<registrer>``` -elementet definerer én eller flere nye vegobjekter for registrering i NVDB. Et nytt vegobjekt får en unik
id i NVDB og gis automatisk versjonsnummer 1. For komplett XML-skjema se complexType ```NyttVegobjekt```
i [endringssett.xsd](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

```<vegobjekt>``` -elementet har to obligatoriske attributter:

* ```typeId``` angir vegobjekttypens id i datakatalogen
* ```tempId``` angir en unik, temporær id eller markør for vegobjektet som brukes når det er behov for å referere til det
  f.eks. andre steder i endringssettet (assosiasjoner) og valideringsvarsler.  

```<vegobjekt>``` -elementet har fire subelementer:

* ```<gyldighetsperiode>``` angir start- og eventuelt sluttdato for vegobjektets levetid
* ```<egenskaper>``` angir én eller flere egenskaper med verdi/verdier
* ```<assosiasjoner>``` angir tilkoblede datterobjekter, dersom det er relevant for vegobjekttypen
* ```<stedfesting>``` angir vegobjektets vegnettstilknytning

#### Gyldighetsperiode

```xml
<gyldighetsperiode>
  <startdato>2020-01-01</startdato>
  <sluttdato>2050-12-31</sluttdato>
</gyldighetsperiode>
```

Dette elementet angir levetiden for vegobjektet. For de fleste vegobjekttyper betyr dette at ```<startdato>``` angir når
vegobjektet ble montert og observerbar langs vegen. ```<sluttdato>``` er valgfri og angir når vegobjektet skal fjernes fra vegen,
om dette er planlagt på forhånd.

#### Egenskaper

```xml
<egenskaper>
  <egenskap typeId="5225">
    <verdi>Grevlingtunnelen</verdi>
  </egenskap>
  ...
</egenskaper>
```

Egenskaper brukes til å angi detaljerte metadata eller opplysninger om vegobjektet. For hver egenskap må den korresponderende
egenskapstypen sin id i datakatalogen angis med ```typeId``` -attributten. Ulike egenskapstyper forventer verdier av forskjellige
datatyper. Hvordan disse angis er beskrevet i en egen seksjon om [egenskapsverdier](#om-egenskapsverdier) under.

#### Assosiasjoner

```xml
<assosiasjoner>
  <assosiasjon typeId="220710">
    <tempId>tunnelløp#1</tempId>
    ...
  </assosiasjon>
  ...
</assosiasjoner>
```

Assosiasjoner brukes til å koble sammen mor- og datterobjekter i hierarkier. I fragmentet over angis et datterobjekt
(som også registreres i sammme endringssett) av type Tunnelløp. Attributten ```typeId``` hentes fra datakatalogen og angir 
entydig hvilken morobjekttype (Tunnel) og datterobjekttype (Tunnelløp) som inngår i sammenkoblingen. Se flere detaljer i egen
seksjon om [assosiasjoner](#om-assosiasjoner).

#### Stedfesting

```xml
<stedfesting>
  <punkt veglenkesekvensNvdbId="1125766" posisjon="0.3"/>
</stedfesting>
```

Stedfestingen angir hvor vegobjektet er tilknyttet vegnettets lenke-/nodestruktur. I fragmentet over angis at tunnelen er
tilknyttet et punkt, altså en enkelt posisjon, på en bestemt veglenkesekvens. Ulike vegobjekttyper krever ulike former for
stedfesting. For flere detaljer se egen seksjon om [stedfesting](#om-stedfesting).

### Oppdatering

```xml
<oppdater>
  <vegobjekter>
    <vegobjekt typeId="581" nvdbId="551800127" versjon="1">
      <gyldighetsperiode>
        ...
      </gyldighetsperiode>
      <egenskaper>
        ...
      </egenskaper>
      <assosiasjoner>
        ...
      </assosiasjoner>
      <stedfesting>
        ...
      </stedfesting>
    </vegobjekt>
    ...
  </vegobjekter>
</oppdater>
```

```<oppdater>``` -elementet definerer én eller flere vegobjekter for oppdatering (versjonering) i NVDB.
En oppdatering endrer et eksisterende vegobjekt i NVDB ved at det etableres en ny versjon av vegobjektet med de angitte
egenskapene, assosiasjonene og stedfestingen. Oppdatering utføres for å ta vare på historikken til vegobjekter, det vil si hvilke
observerbare endringer et vegobjekt har gjennomgått i sin levetid.

Selv om det bare er snakk om små endringer, f.eks. en egenskap som får ny verdi, må alle andre egenskaper,
assosiasjonene og stedfestingen angis. Den nye versjon blir etablert nøyaktig slik den er angitt i endringssettet.
Ved små endringer er det som regel mer hensiktsmessig å bruke [delvis oppdatering](#delvis-oppdatering). For komplett XML-skjema
se complexType ```OppdatertVegobjekt``` i [endringssett.xsd](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

```<vegobjekt>``` -elementet har tre obligatoriske attributter:

* ```typeId``` angir vegobjekttypens id i datakatalogen
* ```nvdbId``` angir en vegobjektets id i NVDB  
* ```versjon``` angir det klienten mener er siste versjon av vegobjektet. NVDB API Skriv kontrollerer dette under behandlingen og avviser
endringssettet dersom feil "sisteversjon" er angitt. Den nye versjonen som etableres får versjonsnummer tilsvarende verdien til 
denne attributten + 1.

```<vegobjekt>``` -elementet har fire subelementer:

* ```<gyldighetsperiode>``` angir start- og eventuelt sluttdato for den nye vegobjektversjonen.
* ```<egenskaper>``` angir én eller flere egenskaper med verdi/verdier
* ```<assosiasjoner>``` angir tilkoblede datterobjekter, dersom det er relevant for vegobjekttypen
* ```<stedfesting>``` angir vegobjektets vegnettstilknytning

Innholdet i disse elementene er tilsvarende som for [Registrering](#registrering).

Under behandling sørger NVDB API Skriv for å lukke siste versjon ved å sette sluttdato til samme dato som
startdatoen for den nye versjonen.

#### Oppdatering med overskriving

I spesielle situasjoner, for enkelte vegobjekttyper, er det ikke relevant å ta vare på historikk. I slike tilfeller kan
man oppdatere med overskriving, ved å legge til attributten ```overskriv=JA``` på ```<vegobjekt>``` -elementet. Dette fører til
at de angitte egenskapene, assosiasjonene og stedfestingen erstatter tilsvarende elementer på siste versjon av vegobjektet.
Det lages altså ikke en ny versjon. Når overskrivingsvarianten skal brukes må avtales med relevante fagmiljø hos Statens vegvesen.

### Delvis oppdatering

```xml
<delvisOppdater>
  <vegobjekter>
    <vegobjekt typeId="581" nvdbId="551800127" versjon="1">
      <gyldighetsperiode>
        ...
      </gyldighetsperiode>
      <egenskaper>
        ...
      </egenskaper>
      <assosiasjoner>
        ...
      </assosiasjoner>
      <stedfesting>
        ...
      </stedfesting>
    </vegobjekt>
    ...
  </vegobjekter>
</delvisOppdater>
```

Delvis oppdatering har konseptuelt samme bruksformål som (full) [oppdatering](#oppdatering), men tillater klienten å bare angi de
elementene som faktisk endres. Dette gjelder enten endringen er i en egenskap, en assosiasjon eller stedfestingen.

For komplett XML-skjema se complexType ```DelvisOppdatertVegobjekt``` i [endringssett.xsd](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

```<vegobjekt>``` har de samme fire subelementene som full oppdatering, men kun ```<gyldighetsperiode>``` er obligatorisk. I tillegg må det angis
minst én endring, under ```<egenskaper>```, ```<assosiasjoner>``` eller ```<stedfesting>```.

#### Delvise egenskaper

```xml
<egenskaper>
  <egenskap typeId="5225" operasjon="oppdater">
    <verdi>Grevlingtunnelen</verdi>
  </egenskap>
  ...
</egenskaper>
```

For angi at en bestemt egenskap skal legges til vegobjektet eller, når den allerede finnes, skal få ny verdi angis dette
med attributten ```operasjon="OPPDATER"```.

Dersom man ønsker å fjerne en egenskap fra vegobjektet angis det med ```operasjon="slett"```.

#### Delvise assosiasjoner

```xml
<assosiasjoner>
  <assosiasjon typeId="220710" operasjon="oppdater">
    <nvdbId>745645</nvdbId>
    <nvdbId>57876787</nvdbId>
    <tempId>tunnelløp#1</tempId>
  </assosiasjon>
  ...
</assosiasjoner>
```

For angi at en bestemt assosiasjon skal legges til vegobjektet eller, når den allerede finnes, skal få ny(e) verdi(er) angis dette
med attributten ```operasjon="oppdater"```. Man må da angi komplett liste med datterobjektid'er som skal være med i den oppdaterte assosiasjonen.

Dersom man ønsker å fjerne en assosiasjon fra vegobjektet angis det med ```operasjon="slett"```.

Assosiasjoner har lister med potensielt mange datterobjektid'er og delvis oppdatering der man bare ønsker å legge til eller ta bort
enkeltverdier kan ofte bli komplisert. For å forenkle dette kan man gjøre inkrementelle endringer på disse id-listene i stedet:

```xml
<assosiasjoner>
  <assosiasjon typeId="220710" operasjon="oppdater">
    <tempId operasjon="ny">tunnelløp#1</tempId>
  </assosiasjon>
  ...
</assosiasjoner>
```

I fragmentet over angis det at det skal legges til et nytt datterobjekt i en assosiasjon, som (kanskje) allerede har mange slike, ved
å sette på attributten ```operasjon="ny"``` på ```<tempId>```. Attributten kan også brukes på ```<nvdbId>```.

Dersom man ønsker å fjerne en enkelt id fra listen av datterobjektid'er brukes attributten ```operasjon="slett"```. 

#### Delvis stedfesting

```xml
<stedfesting operasjon="oppdater">
  <linje veglenkesekvensNvdbId="1125766" fra="0.3" til="1.0"/>
  <linje veglenkesekvensNvdbId="1125767" fra="0.0" til="0.5"/>
</stedfesting>
```

For angi en oppdatert stedfesting i vegobjektet angis dette med attributten ```operasjon="oppdater"```.
Man må da angi komplett liste med stedfestingselementer som skal utgjøre den nye stedfestingen.

Som for assosiasjoner kan man angi at (hele) stedfestingen skal fjernes fra vegobjektet med ```operasjon="slett"```.
Dette vil imidlertid i de aller fleste tilfeller ikke bli godkjent av NVDB API Skriv, siden så og si alle
vegobjekttyper i datakatalogen har obligatorisk stedfesting. 

Strekningsstedfesting består ofte av en liste med potensielt mange stedfestingselementer og delvis oppdatering der man bare
ønsker å legge til eller ta bort enkeltelementer kan ofte bli komplisert. For å forenkle dette kan man gjøre inkrementelle
endringer på disse stedfestingselementlisten i stedet:

```xml
<stedfesting operasjon="oppdater">
  <linje veglenkesekvensNvdbId="1125767" fra="0.0" til="0.5" operasjon="ny"/>
</stedfesting>
```

I fragmentet over angis det at det skal legges til et nytt ```<linje>``` -element i stedfestingen, som (kanskje) allerede
har mange slike, ved å sette på attributten ```operasjon="ny"```. 

Dersom man ønsker å fjerne et enkelt stedfestingselement fra listen brukes attributten ```operasjon="slett"```. 

### Lukking

```xml
<lukk>
  <vegobjekter>
    <vegobjekt typeId="234" nvdbId="91610862" versjon="1">
      <lukkadato>2020-12-31</lukkadato>
      <kaskadelukking>JA</kaskadelukking>
    </vegobjekt>
    ...
  </vegobjekter>
</lukk>
```

Lukking innebærer at gjeldende (siste) versjon av et vegobjekt settes historisk, det vil si at sluttdato settes. Dette markerer
typisk at vegobjektet ble demontert eller fjernet fra vegen.

For komplett XML-skjema se complexType ```LukketVegobjekt``` i [endringssett.xsd](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

```<vegobjekt>``` -elementet har tre obligatoriske attributter:

* ```typeId``` angir vegobjekttypens id i datakatalogen
* ```nvdbId``` angir en vegobjektets id i NVDB  
* ```versjon``` angir det klienten mener er siste versjon av vegobjektet. NVDB API Skriv kontrollerer dette under behandlingen og avviser
endringssettet dersom feil "sisteversjon" er angitt

```<vegobjekt>``` -elementet har to obligatoriske subelementer:

* ```<lukkedato>``` angir sluttdatoen som skal settes på vegobjektversjonen
* ```<kaskadelukking>``` angir hvorvidt dattervegobjekter også skal lukkes nedover i hierarkiet. Dersom denne settes til ```NEI``` og det
finnes datterobjekter som er sterkt koblet (assosiasjonstype _komposisjon_) vil endringssettet avvises med valideringsfeil, fordi slike
datterobjekter må ha morobjekt.

### Korrigering

```xml
<korriger>
  <vegobjekter>
    <vegobjekt typeId="581" nvdbId="551800127" versjon="1">
      <validering>
        <lestFraNvdb>2020-05-30T15:34:22</lestFraNvdb>
      </validering>
      <gyldighetsperiode>
        ...
      </gyldighetsperiode>
      <egenskaper>
        ...
      </egenskaper>
      <assosiasjoner>
        ...
      </assosiasjoner>
      <stedfesting>
        ...
      </stedfesting>
    </vegobjekt>
    ...
  </vegobjekter>
</korriger>
```

```<oppdater>``` -elementet definerer én eller flere vegobjekter for korrigering i NVDB. Korrigering innebærer at man endrer
en eksisterende vegobjektversjon (uten å lage en ny versjon). Alle versjoner av et vegobjekt kan korrigeres, også
historiske versjoner. Hensikten med operasjonen er å muliggjøre oppretting av feilregistrerte data i NVDB. Andre typer
endringer skal utføres med [oppdatering](#oppdatering).

Selv om det bare er snakk om små endringer, f.eks. en egenskap som får ny verdi, må alle andre egenskaper,
assosiasjonene og stedfestingen angis. Den korrigerte versjonen blir overskrevet med de elementene slik de er angitt i endringssettet.
Ved små endringer er det som regel mer hensiktsmessig å bruke [delvis korrigering](#delvis-korrigering). For komplett XML-skjema
se complexType ```KorrigertVegobjekt``` i [endringssett.xsd](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

```<vegobjekt>``` -elementet har tre obligatoriske attributter:

* ```typeId``` angir vegobjekttypens id i datakatalogen
* ```nvdbId``` angir vegobjektets id i NVDB  
* ```versjon``` angir versjonen av vegobjektet som skal korrigeres

```<vegobjekt>``` -elementet har fem subelementer:

* ```<validering>``` angir valideringsparametere for korrigeringen.
* ```<gyldighetsperiode>``` angir korrigert start- og sluttdato for vegobjektversjonen.
* ```<egenskaper>``` angir én eller flere egenskaper med verdi/verdier
* ```<assosiasjoner>``` angir tilkoblede datterobjekter, dersom det er relevant for vegobjekttypen
* ```<stedfesting>``` angir vegobjektets vegnettstilknytning

Innholdet i de fire siste elementene er tilsvarende som for [Oppdatering](#oppdatering).

Ved behandling av korrigeringer vil ikke NVDB API Skriv utføre følgeoppdateringer, slik som for oppdatering og andre operasjoner. Dette fordi
korrigering av historiske vegobjekter kan trigge svært komplekse følgeoppdateringer spredt over en lang tidsperiode. I noen tilfeller
er det heller ikke mulig å automatisere alle scenarier, siden intensjonen til ulike endringer over tid ikke alltid er kjent. NVDB API Skriv 
vil imidlertid validere alle korrigeringer, også mot eksisterende data NVDB. Dersom korrigeringen ikke er kompatibel med andre
vegobjekter i NVDB, blir endringssettet avvist med valideringsfeil. Det er klienten sitt ansvar å supplere endringssettet
med nødvendige operasjoner for å opprettholde integriteten i NVDB.

#### Validering

I ```<validering>``` -elementet må klienten angi tidspunktet for når vegobjektversjon opprinnelig ble innlest fra NVDB API Les.
Under behandling av korrigeringer vil NVDB API Skriv kontrollere at det ikke er andre som har korrigerte (eller overskrevet)
vegobjektversjonen etter dette tidspunktet. I så fall avvises endringssettet med valideringsfeil. Klienten må da utføre en
ny innlesing fra NVDB API Les, påføre sine endringer og sende inn et nytt endringssett.

Innlesingstidspunktet må angis som "NVDB-tid", ikke klient-tid. Dette gjøres enklest ved å hente ut tidspunkt for
siste indekserte transaksjon i NVDB via NVDB API Les sitt statusendepunkt, [https://www.vegvesen.no/nvdb/api/v3/status](https://www.vegvesen.no/nvdb/api/v3/status).
Tidspunktet må etableres umiddelbart etter uthenting av vegobjekter fra NVDB API Les.  

### Delvis korrigering

```xml
<delvisKorriger>
  <vegobjekter>
    <vegobjekt typeId="581" nvdbId="551800127" versjon="1">
      <validering>
        <lestFraNvdb>2020-05-30T15:34:22</lestFraNvdb>
      </validering>
      <gyldighetsperiode>
        ...
      </gyldighetsperiode>
      <egenskaper>
        ...
      </egenskaper>
      <assosiasjoner>
        ...
      </assosiasjoner>
      <stedfesting>
        ...
      </stedfesting>
    </vegobjekt>
    ...
  </vegobjekter>
</delvisKorriger>
```

Delvis korrigering har konseptuelt samme bruksformål som (full) [korrigering](#korrigering), men tillater klienten å bare angi de
elementene som faktisk endres. Dette gjelder enten endringen er i en egenskap, en assosiasjon eller stedfestingen.

For komplett XML-skjema se complexType ```DelvisKorrigertVegobjekt``` i [endringssett.xsd](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

Innholdet i ```<egenskaper>```, ```<assosiasjoner>``` og ```<stedfesting>``` er det samme som for [delvis oppdatering](#delvis-oppdatering), inkludert muligheten
for å angi inkrementell oppdatering av datterobjekt-id'er i en assosiasjon og stedfestingselementer i stedfestingen.

### Fjerning

```xml
<fjern>
  <vegobjekter>
    <vegobjekt typeId="234" nvdbId="91610862" versjon="1">
      <kaskadefjerning>JA</kaskadefjerning>
    </vegobjekt>
    ...
  </vegobjekter>
</fjern>
```

Vegobjekter skal normalt ikke fjernes fra NVDB. Historikk om hvilke vegobjekter som har eksistert langs vegen er et viktig aspekt
ved NVDB som datakilde. Når et vegobjekt er demontert eller fysisk fjernet fra vegen, skal derfor operasjonen _lukk_ brukes.

Fjerning kan brukes til å slette data i NVDB som ikke skulle vært der i utgangspunktet, eller til å slette data som det ikke lenger
er hensiktsmessig å ta vare på. Merk at fjerning er en ikke-reversibel operasjon og at fjerning også kan påvirke andre vegobjekter
gjennom mor-datter-hierarkier. 

For komplett XML-skjema se complexType ```FjernetVegobjekt``` i [endringssett.xsd](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).

```<vegobjekt>``` -elementet har to obligatoriske attributter:

* ```typeId``` angir vegobjekttypens id i datakatalogen
* ```nvdbId``` angir vegobjektets id i NVDB  

```<vegobjekt>``` -elementet har èn valgfri attributt:
* ```versjon``` angir versjonen som skal fjernes. Om denne ikke spesifiseres fjernes **alle** versjoner, altså i praksis hele vegobjektet.

```<vegobjekt>``` -elementet har ett obligatorisk subelement:

* ```<kaskadefjerning>``` angir hvorvidt dattervegobjekter også skal få redusert historikk nedover i hierarkiet. Dersom denne settes til ```NEI``` og det
finnes datterobjekter som er sterkt koblet (assosiasjonstype _komposisjon_) vil endringssettet avvises med valideringsfeil, fordi slike
datterobjekter må ha morobjekt.

Merk at det ikke er tillatt å fjerne en vegobjektversjon innimellom to andre. Ved fjerning av enkeltversjoner må disse utgjøre
en sammenhengende rekke med versjonsnummere fra og med siste versjon og bakover i tid.

NVDB API Skriv vil kopiere inn sluttdato på siste versjon av vegobjektet _før_ fjerning til siste versjon _etter_ fjerning.

### Om egenskapsverdier

Ulike egenskaper til vegobjekt forventer verdier med forskjellige datatyper. Det er datakatalogen som styrer dette og NVDB API Skriv validerer
at korrekt datatype og eventuelt verdiområde er brukt for hver egenskapstype.

#### Heltall

Egenskaper av heltallstype skal ha verdier angitt med et ```<verdi>``` -element:

```xml
<egenskap typeId="1234">
  <verdi>543</verdi>
</egenskap>
```

Noen heltallsegenskaper i datakatalogen er såkalte _flerverdiegenskaper_. Dette betyr at det er definert et begrenset antall
lovlige verdier for egenskapen. Hver av disse lovlige verdiene har en unik id i datakatalogen og om ønskelig kan klienten
referere til disse id'ene i stedet for den konkrete verdien via et ```<enum>``` -element:

```xml
<egenskap typeId="1234">
  <enum>766455</enum>
</egenskap>
```

Datakatalogen definerer en maksimal feltbredde og minimums- og maksimumsverdi for hver heltallsegenskap. NVDB API Skriv validerer at
heltallsegenskaper i endringssettet er i tråd med disse kravene.
                         
#### Flyttall

Egenskaper av flyttallstype skal ha verdier angitt med et ```<verdi>``` -element:

```xml
<egenskap typeId="1234">
  <verdi>3.1415927</verdi>
</egenskap>
```

Noen flyttallsegenskaper i datakatalogen er såkalte _flerverdiegenskaper_. Dette betyr at det er definert et begrenset antall
lovlige verdier for egenskapen. Hver av disse lovlige verdiene har en unik id i datakatalogen og om ønskelig kan klienten
referere til disse id'ene i stedet for den konkrete verdien via et ```<enum>``` -element:

```xml
<egenskap typeId="1234">
  <enum>56455</enum>
</egenskap>
```

Datakatalogen definerer en maksimal feltbredde, antall desimaler, samt minimums- og maksimumsverdi for hver flyttallsegensap.
NVDB API Skriv validerer at flyttallsegenskaper i endringssettet er i tråd med disse kravene.

#### Streng (tekst)

Egenskaper av strengtype skal ha verdier angitt med et ```<verdi>``` -element:

```xml
<egenskap typeId="1234">
  <verdi>Grevlingtunnellen</verdi>
</egenskap>
```

Noen strengegenskaper i datakatalogen er såkalte _flerverdiegenskaper_. Dette betyr at det er definert et begrenset antall
lovlige verdier for egenskapen. Hver av disse lovlige verdiene har en unik id i datakatalogen og om ønskelig kan klienten
referere til disse id'ene i stedet for den konkrete verdien via et ```<enum>``` -element:

```xml
<egenskap typeId="1234">
  <enum>994455</enum>
</egenskap>
```

Datakatalogen definerer en maksimal lengde for strenger. NVDB API Skriv validerer at
strengegenskaper i endringssettet er i tråd med dette kravet.

#### Tegn

Egenskaper av tegntype skal ha verdier angitt med et ```<verdi>``` -element:

```xml
<egenskap typeId="1234">
  <verdi>A</verdi>
</egenskap>
```

#### Boolsk

Egenskaper av boolsk type skal ha verdier angitt med et ```<verdi>``` -element:

```xml
<egenskap typeId="1234">
  <verdi>JA</verdi>
</egenskap>
```
 
En positiv boolsk verdi angis med ```JA```, ```ja``` eller ```true```.

En negativ boolsk verdi angis med ```NEI```, ```nei``` eller ```false```.

Verdien er ikke case-sensitiv.

#### Dato

Egenskaper av datotype skal ha verdier angitt med et ```<verdi>``` -element:

```xml
<egenskap typeId="1234">
  <verdi>2020-01-01</verdi>
</egenskap>
```
 
Datoen skal ha formatering i tråd med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-standarden, eller ha kompakt form
etter mønsteret "ååååmmdd".

Datakatalogen definerer en minimums- og maksimumsverdi for hver datoegenskap. NVDB API Skriv validerer at
datoegenskaper i endringssettet er i tråd med dette kravet.

#### Kortdato

Egenskaper av kortdatotype skal ha verdier angitt med et ```<verdi>``` -element:

```xml
<egenskap typeId="1234">
  <verdi>12-31</verdi>
</egenskap>
```

Verdien består av måned og dag og formatteres etter mønsteret "mmdd" eller "mm-dd".  

#### Klokkeslett

Egenskaper av klokkeslettype skal ha verdier angitt med et ```<verdi>``` -element:

```xml
<egenskap typeId="1234">
  <verdi>15:30:00</verdi>
</egenskap>
```

Klokkeslettet skal ha formatering i tråd med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-standarden, eller ha kompakt form
etter mønsteret "ttmm", det vil si uten sekunder.

Datakatalogen definerer en minimums- og maksimumsverdi for hver klokkeslettegenskap. NVDB API Skriv validerer at
klokkeslettegenskaper i endringssettet er i tråd med dette kravet.

#### Binærobjekt

Egenskaper av binærobjekttype ([BLOB](https://en.wikipedia.org/wiki/Binary_large_object)) skal ha verdier angitt med et ```<binaer>``` -element:

```xml
<egenskap typeId="1234">
  <binaer ressursId="e62a6896-ff48-474c-a5f3-bb5ccc45c9c9" format="image/png"/>
</egenskap>
```

Attributten ```ressursId``` skal angi unik id for et binært objekt som er lastet opp til NVDB API Skriv via endepunktet for binære objekter.

Attributten ```format``` skal angi [media-type](http://www.iana.org/assignments/media-types/media-types.xhtml) for binærobjektet.

Opplasting av binære objekter er beskrevet i [egen seksjon](../2__binaere_objekter/introduksjon.md).

#### Geometri

Vegobjektets egengeometri beskrives som regulære egenskaper i et endringssett. Geometriverdien angis med et ```<geometri>``` -element:

```xml
<egenskap typeId="1234">
  <geometri>
    <srid>32633</srid>
    <wkt>POLYGON Z ((278220 7034016 123.45, 279220 7035016 246.78, 270220 7036016 226.78, 278220 7034016 123.45))</wkt>
    <lengde>4000.0</lengde>
    <datafangstdato>2016-09-09</datafangstdato>
    <temakode>9001</temakode>
    <medium>1</medium>
    <kommune>1601</kommune>
    <høydereferanse>2</høydereferanse>
    <sosinavn>FLATE</sosinavn>
    <referansegeometri>true</referansegeometri>
    <verifiseringsdato>2016-09-10</verifiseringsdato>
    <oppdateringsdato>2016-09-11</oppdateringsdato>
    <prosesshistorikk>gurba</prosesshistorikk>
    <kvalitet>
      <målemetode>96</målemetode>
      <målemetodeHøyde>96</målemetodeHøyde>
      <nøyaktighet>1</nøyaktighet>
      <nøyaktighetHøyde>1</nøyaktighetHøyde>
      <synbarhet>0</synbarhet>
      <toleranse>1</toleranse>
    </kvalitet>
  </geometri>
</egenskap>
```

Kun subelementene ```<srid>``` og ```<wkt>``` er obligatoriske.

##### srid

Subelementet ```<srid>``` angir id for [Spatial Reference System](https://en.wikipedia.org/wiki/Spatial_reference_system), eller koordinatreferansesystemet til geometrien.
Denne angir koordinatreferansesystem både for høyde (vertikaldatum) og grunnriss. I NVDB lagres alle geometrier med [NN2000](https://no.wikipedia.org/wiki/NN2000)
som vertikaldatum og [EUREF89/UTM33N](https://no.wikipedia.org/wiki/UTM-koordinater) for grunnrisset. Dette tilsvarer srid 5973.

Det er valgfritt å angi høydekoordinater i geometriene, men om den angis skal den alltid bruke NN2000 som nullreferanse.

Koordinater i grunnrisset kan bruke de fleste anerkjente koordinatreferansesystemer som er i bruk i Norge, både geosentriske og
geodetiske. NVDB API Skriv vil automatisk konvertere disse koordinatene til EUREF89/UTM33N.

##### wkt

Subelementet ```<wkt>``` angir geometriens utforming som en [WKT](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry)-streng.
NVDB aksepterer tre typer geometrier: Punkt (POINT), kurve (LINESTRING) og flate (POLYGON). Datakatalogen styrer hvilken geometriform
en bestemt geometriegenskap skal ha. Dersom det leveres høydekoordinater angis det med Z i navnet: POINT Z, LINESTRING Z eller POLYGON Z.

##### lengde

Subelementet ```<lengde>``` angir geometriens lengde i meter. Dersom geometrien har høydekoordinater, skal verdien representere lengden i rommet (3D-lengde).

Lengden beregnes automatisk av NVDB API Skriv før geometrien lagres til NVDB, hvis den ikke er angitt i endringssettet.

##### datafangstdato

Subelementet ```<datafangstdato>``` angir dato for måleopptak og formateres i tråd med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-standarden.

##### temakode

Subelementet ```<temakode>``` angir en kategorisering av geometrien brukt i utvekslingsformatet [SOSI](https://kartverket.no/standard/sosi/pdf3/del5_3/temakode.pdf).

For datatype og verdiområde se [definisjon i datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9784.json?pretty=true).

##### medium

Subelementet ```<medium>``` angir vegobjektets beliggenhet i forhold til jordoverflaten. Verdien er en tallkode og hentes
fra "kortnavn" i [verdidefinisjonene i datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9792.json?pretty=true).

##### kommune

Subelementet ```<kommune>``` angir det firesifrede nummeret til kommunen der vegobjektet befinner seg. Kommunenummeret skal alltid ha
fire siffere, det vil si eventuelt med ledende null.

##### høydereferanse

Subelementet ```<høydereferanse>``` angir om koordinatregistering er utført på topp eller bunn av vegobjektet. Verdien er en tallkode og hentes
fra "kortnavn" i [verdidefinisjonene i datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9546.json?pretty=true).

##### sosinavn

Subelementet ```<sosinavn>``` angir navnet på geometriobjektet i SOSI-standarden, typisk "PUNKT", "KURVE" eller "FLATE".

##### referansegeometri

Subelementet ```<referansegeometri>``` angir om geometrien er avledet fra vegnettstilknytningen og dermed ikke en fullverdig egengeometri.
Lovlige verdier er "true" og "false".

##### verifiseringsdato

Subelementet ```<verifiseringsdato>``` angir dato for når geometrien ble fastslått å være i samsvar med virkeligheten. Datoen 
formateres i tråd med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-standarden.

##### oppdateringsdato

Subelementet ```<oppdateringsdato>``` angir dato for siste endring av geometri eller geometriattributter. Denne kan være forskjellig fra datafangstdato
fordi data som er registrert kan mellomlagres en kortere eller lengre periode før disse legges inn i NVDB. Datoen formateres
i tråd med [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-standarden.

##### prosesshistorikk

Subelementet ```<prosesshistorikk>``` angir en beskrivelse i fritekst av de prosesser som koordinatene har gått gjennom som kan ha betydning
for kvaliteten og bruken av dem. Prosesshistorikken vil blant annet kunne inneholde informasjon om transformasjoner. Hva
slags informasjon som angis er ofte gitt i andre standarder, f.eks kvalitet og kvalitetsikring.

##### kvalitet

Subelementet ```<kvalitet>``` beskriver ulike kvalitetsparametere for geometrien.
 
###### målemetode

```<målemetode>``` angir metode for måling i grunnriss (x, y) - og høyde (z) når metoden er den samme som ved måling i grunnriss.
Verdien er en tallkode og hentes fra "kortnavn" i [verdidefinisjonene i datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9543.json?pretty=true).

###### målemetodeHøyde

```<målemetodeHøyde>``` angir metode for måling av høyde. Målemetode for høyden angis hvis det er brukt annen målemetode enn for grunnriss.
Verdien er en tallkode og hentes fra "kortnavn" i [verdidefinisjonene i datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9544.json?pretty=true).

###### nøyaktighet

```<nøyaktighet>``` angir punktstandardavviket i grunnriss for punkter samt tverravvik for linjer. Verdien angis i cm.
For verdiområde se [definisjon i datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9551.json?pretty=true).

###### nøyaktighetHøyde

```<nøyaktighetHøyde>``` angir nøyaktighet for høyden i cm. Angis bare hvis annen nøyaktighet enn for grunnriss.
For verdiområde se [definisjon i datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9552.json?pretty=true).

###### synbarhet

```<synbarhet>``` angir hvor godt vegobjektet var synbart ved innmålingen. Hvis synbarheten var god/normal
kan dette subelementet utelates. Verdien er en tallkode og hentes fra "kortnavn" i [verdidefinisjonene i datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9545.json?pretty=true).

###### toleranse

```<toleranse>``` angir numerisk toleranse (maksimalt avvik) på geometri-innmålingen. Verdien angis i cm.
For verdiområde se [definisjon i datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/9783.json?pretty=true).

#### Struktur

Egenskaper av strukturtype har verdier sammensatt av flere delverdier, kalt medlemmer, og angis med et ```<struktur>``` -element:
                                                                                       
```xml
<egenskap typeId="1234">
  <struktur>
    <medlem typeId="6085">
      <verdi>0.1</verdi>
    </medlem>
    <medlem typeId="6086">
      <verdi>JA</verdi>
    </medlem>
    <medlem typeId="6087">
      <verdi>Navn</verdi>
    </medlem>
  </struktur>
</egenskap>
```

Hvert medlem i en stuktur er definert som en separat egenskapstype i datakatalogen og må følge datatype og verdiområde for denne.

### Om assosiasjoner

Assosiasjoner brukes til å koble sammen mor- og datterobjekter i hierarkier. Konvensjonen er at morobjektet angir sine datterobjekter, ikke omvendt.

```xml
<assosiasjoner>
  <assosiasjon typeId="220710">
    <nvdbId>45576767</nvdbId>
    ...
    <tempId>tunnelløp#1</tempId>
    ...
  </assosiasjon>
  <assosiasjon typeId="220711">
    <tempId>kommentar#1</tempId>
  </assosiasjon>
  ...
</assosiasjoner>
```

Subelementet ```<tempId>``` indikerer at et vegobjekt som registreres i samme endringssett skal kobles til som datterobjekt.
Dersom man ønsker å koble til et allerede eksisterende vegobjekt i NVDB, angis dennes id med subelementet ```<nvdbId>```.

En assosiasjon/sammenkobling kan inneholde flere referanser til datterobjekter og dette kan også være en blanding av ```<tempId>``` og ```<nvdbId>```.
Disse må i så fall angis samlet, med alle forekomster av ```<nvdbId>``` øverst.

Et morobjekt kan ha assosisasjon til flere vegobjekttyper. Dette angis med separate ```<assosiasjon>``` -elementer for hver datterobjekttype.

Det er flere ulike nummerserier ute og går for assosiasjoners typeId, både med basetall 220000 og 200000, og uten basetall, avhengig av hvilken
kilde man bruker. NVDB API Skriv godtar de to første seriene. I fragmentet over er altså 220710 og 200710 likeverdige typeIder for sammenkoblingen
mellom Tunnel og Tunnelløp. TypeId 710 godtas imidlertid ikke.
   
### Om stedfesting

Stedfestingen angir hvor vegobjektet er tilknyttet vegnettets lenke-/nodestruktur. Hovedhensikten med dette er å definere
eierskap til vegobjektet, siden ulike veglenkesekvenser tilhører ulike administrative nivåer: Stat, fylke og kommune. I motsetning
til det klassiske klient-APIet må stedfestingen bruke det lineære referansesystemet i NVDB, ikke vegreferanser eller vegsystemreferanser.
Det finnes imidlertid omregningsfunksjoner i NVDB API Les om man bruker slike referanser internt i klienten.

De ulike elementene av vegnettet har gyldighetsperiode/levetid akkurat som vegobjektene. De vegnettselementene som et vegobjekt
er stedfestet på må ha sammenfallende levetid med vegobjektet. NVDB API Skriv validerer dette under behandling av endringssettet.

Datakatalogen avgjør hva slags type stedfesting en vegobjekttype skal ha. Det finnes tre typer: _punkt_, _linje_ og _sving_.

#### Punktstedfesting

Punkttilknytning angis slik:

```xml
<stedfesting>
  <punkt veglenkesekvensNvdbId="1125766" posisjon="0.3"/>
</stedfesting>
```

I ```<punkt>``` -elementet angis id til veglenkesekvensen vegobjektet skal knyttes til med ```veglenkesekvensNvdbId``` -attributten. I tilegg 
angis den relative posisjonen for tilknytningen inne på veglenkesekvensen med attributten ```posisjon```. Posisjon har verdi mellom 0.0, som indikerer starten 
på veglenkesekvensen og 1.0, som er slutten på veglenkesekvensen.
 
#### Strekningsstedfesting

Strekningstilknytning angis slik:

```xml
<stedfesting>
  <linje veglenkesekvensNvdbId="676776" fra="0.0" til="0.34"/>
</stedfesting>
```

I ```<linje>``` -elementet angis id til veglenkesekvensen vegobjektet skal knyttes til med ```veglenkesekvensNvdbId``` -attributten. I tilegg 
angis den relative start- og sluttposisjonen for tilknytningen inne på veglenkesekvensen med attributtene ```fra``` og ```til```. Start- og sluttposisjonene
har verdi mellom 0.0, som indikerer starten på veglenkesekvensen, og 1.0, som er slutten på veglenkesekvensen.

Dersom et vegobjekt dekker flere veglenkesekvenser, kan flere ```<linje>``` -elementer angis. Dette reguleres av
datakatalogen. Dersom det angis flere, skal disse beskrive en sammenhengende rute langs vegnettet.

#### Svingstedfesting

Svingtilknytning angis slik:

```xml
<stedfesting>
  <sving nodeNvdbId="4656776">
    <fra veglenkesekvensNvdbId="8976873" posisjon="0.798"/>
    <til veglenkesekvensNvdbId="5776873" posisjon="0.348"/>
  </sving>
</stedfesting>
```

En svingtilknytning beskriver en sving på vegnettet, det vil si en node som representerer et vegkryss, en veglenkesekvensposisjon
som indikerer startpunkt for svingen rett **før** krysset og en veglenkesekvensposisjon som indikerer et sluttpunkt for svingen
rett **etter** krysset.

Kryssnoden sin id angis med attributten ```nodeNvdbId```, mens start- og sluttposisjonene angis som to punktstedfestinger med subelementene
```<fra>``` og ```<til>```. 

Denne stedfestingstypen benyttes kun av vegobjekttypen Svingerestriksjon.

#### Øvrige stedfestingsdetaljer

```xml
<stedfesting>
  <punkt veglenkesekvensNvdbId="1125766" posisjon="0.3">
    <retning>MED</retning>
    <kjørefelt>
      <felt>1</felt>    
      <felt>2</felt>    
    </kjørefelt>
  </punkt>
</stedfesting>
```

Både ```<punkt>``` og ```<linje>``` -elementer kan ha ytterligere subelementer som beskriver stedfestingen om datakatalogen
krever eller tillater det:

* ```<retning>``` angir om vegobjektet er orientert i en bestemt retning og må i så fall oppgis relativt til veglenkesekvensretningen. Lovlige verdier er ```MED``` og ```MOT```.
* ```<sideposisjon>``` angir plassering av vegobjektet på tvers av vegen. Lovlige verdier er definert av typen ```RelativRetning``` i [XML-skjemaet](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd).
* ```<kjørefelt>``` angir hvilke kjørefelt vegobjektet er plassert i. Se [datakatalogen](https://nvdbapiles-v3.atlas.vegvesen.no/vegobjekttyper/793/11431.json?pretty=true) for lovlige feltkoder.
