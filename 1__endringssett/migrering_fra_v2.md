---
title: Migrering fra V2
order: 2
---

## Migrering fra v2/endringssett

Versjon 3 av endringssettendepunktene ble utviklet som en del av regionreformen under prosjektet *Nytt nasjonalt referansessystem*.
Hovedmålet med versjon 3 er å kunne ta i mot endringer i vegnettet, men denne funksjonaliteten er kun forbeholdt spesifikke vegnettsklienter.
Den nye versjonen av NVDB API Skriv representerer også en vesentlig oppgradering av behandlingsmotoren for endringssett og inneholder mange forbedringer
både i meldingstrukturen og i valideringslogikken. Endringssett kan fortsatt leveres i V2-format, men disse behandles av den samme forbedrede behandlingsmotoren som V3. 

### Nytt i V3

* Registrering og oppdatering av vegnett (forbeholdt vegnettsklienter).
* Mulighet for korrigering av historiske versjoner av et objekt. Operasjonen `oppdater` (og `delvisOppdater`) med
flagget `overskriv='JA'` erstatter dagens `korriger`. Korrigering vil nå skje uten følgeoppdateringer, men med alle
valideringer. Oppdater med overskriv=JA trigger følgeoppdateringer som før. 
* Mulighet for å fjerne vegobjekter eller enkeltversjoner av et vegobjekt fra NVDB.
* Mulighet for å legge til eller fjerne enkeltverdier fra stedfestingen eller assosiasjoner.
* Korrigering av en vegobjektversjon vil ikke lenger overskrive korrigeringer utført av andre på samme objekt.  
* Automatisk overlappshåndtering for vegobjekttyper som ikke tillater overlapp.
* Automatisk forenkling av geometrier med for tette nabopunkter.
* Mulighet for å inkludere klientspesifikk informasjon ('kontekst') i endringssettet.
* Effektdato kan angis individuelt per vegobjekt. 

[Skjemaet for endringssett](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd) har en ny og utvidet struktur.
Endringene omfatter både navngivning, struktur og nye elementer. 

#### Navneendringer

* Endringssettets `effektDato` er nå borte og erstattet med  `gyldighetsperiode` og `lukkedato` som må angis for hvert enkelt objekt (se [Strukturendringer](#strukturendringer))
* `vegObjekt` skrives nå `vegobjekt`
* `lokasjon`er omdøpt til `stedfesting`
* Stedfesting: `lenkeId` er omdøpt til `veglenkesekvensNvdbId`
* Stedfesting/Sving: `nodeId`er omdøpt til `nodeNvdbId`
* Stedfesting/Felt: `felt`er omdøpt til `kjørefelt` (som nå kan ta imot en liste av felt, se [Strukturendringer](#strukturendringer))
* Operasjonen `slett` er nå omdøpt til `lukk` (se også [Strukturendringer](#strukturendringer)). Operasjonen har aldri slettet/fjernet vegobjekter fra NVDB,
den har kun satt sluttdato på vegobjektene. Lukk er dermed et mer korrekt navn på denne operasjonen. For å fysiske fjerne
vegobjekter fra NVDB kan den nye operasjonen `fjern` benyttes (se [Nye elementer](#nye-elementer)).

#### Strukturendringer

##### Datakatalogversjon
`datakatalogversjon` er ikke lengre attributt til endringssett-elementet selv, men er nå et eget sub-element:
* V2: `<endringssett ... datakatalogversjon="2.18">`
* V3: `<endringssett><datakatalogversjon>2.18</datakatalogversjon>...</endringssett>`
  
##### Effektdato
Effektdato representerer i V2 en felles effektdato for alle operasjoner og vegobjekter i endringssettet. I V3 kan man
manipulere start- og sluttdatoene på vegobjektene individuelt. Effektdato er derfor fjernet som attributt til endringssettet
selv, og må angis på hvert vegobjekt. For operasjonene `registrer`, `korriger`, `delvisKorriger`, `oppdater` og `delvisOppdater`
angis datoene under sub-elementet `gyldighetsperiode`, mens for operasjonen `lukk` angis en egen `lukkedato`:

* V2: `<endringssett ... effektDato="2013-10-29" ... >`
* V3: Gyldighetsperiode må angis per vegobjekt for `registrer`, `korriger` og `oppdater`:
  ```xml
  <vegobjekt>
    ...
    <gyldighetsperiode>
      <startdato>2013-10-29</startdato>
    </gyldighetsperiode>
  </vegobjekt>
  ```
* Gyldighetsperiode kan angis med både `startdato` og `sluttdato`. `startdato` er påkrevd for operasjonene `registrer` og `oppdater`
* For operasjon `lukk` - angi `lukkedato`:
  ```xml
  <vegobjekt typeId="3" nvdbId="91610862" versjon="1">
    <lukkedato>2013-10-29</lukkedato>        
  </vegobjekt>
  ```

##### Versjonskontroll ved korrigering
For å kontrollere at andre klienters endringer på samme vegobjektversjon ikke skrives over ved korrigering (eller oppdatering med overskriv=JA),
må man for slike vegobjekter oppgi tidspunktet når vegobjektversjonen ble lest fra NVDB. Dette tidspunktet må angis som "NVDB-tid", ikke klient-tid.
Dette gjøres enklest ved å hente ut tidspunkt for siste indekserte transaksjon i NVDB via NVDB API Les sitt statusendepunkt,
[https://www.vegvesen.no/nvdb/api/v3/status](https://www.vegvesen.no/nvdb/api/v3/status). Tidspunktet må etableres umiddelbart
etter uthenting av vegobjekter fra NVDB API Les og angis slik i endringssett:  

```xml
<endringssett>
  <oppdater>
    <vegobjekter>
      <vegobjekt typeId="581" nvdbId="551800127" versjon="1" overskriv="JA">
        <validering>
          <lestFraNvdb>2019-10-29T12:23:56</lestFraNvdb>
        </validering>
        ...
      </vegobjekt>
    </vegobjekter>
  </oppdater>
  <korriger>
    <vegobjekter>
      <vegobjekt typeId="95" nvdbId="34345656" versjon="2">
        <validering>
          <lestFraNvdb>2019-10-29T12:23:56</lestFraNvdb>
        </validering>
        ...
      </vegobjekt>
    </vegobjekter>
  </korriger>
</endringssett>
```

##### Oppdatering med overskriving
Operasjon `oppdater` med attributten `overskriv` erstatter dagens `korriger`, både helt og delvis:

* V2: Korriger oppdaterer ikke versjon, men overskriver hele objektet:
  ```xml
  <korriger>
    <vegObjekter>
      <vegObjekt typeId="581" nvdbId="551800127" versjon="1">
        <egenskaper>
          <egenskap typeId="5225">
            <verdi>Bevertunnelen</verdi>
          </egenskap>
          ...
        </egenskaper>
        <lokasjon>
          <punkt lenkeId="1125766" posisjon="0.3"/>
        </lokasjon>
      </vegObjekt>
    </vegObjekter>
  </korriger>
  ```
* V3: Overskriving angis som attributt til operasjon `oppdater`:
  ```xml
  <oppdater>
    <vegobjekter>
      <vegobjekt typeId="581" nvdbId="551800127" versjon="1" overskriv="JA">
        <validering>
          <lestFraNvdb>2019-10-29T12:23:56</lestFraNvdb>
        </validering>
        <gyldighetsperiode>
          <startdato>2013-10-29</startdato>
        </gyldighetsperiode>
        <egenskaper>
          <egenskap typeId="5225">
            <verdi>Bevertunnelen</verdi>
          </egenskap>
          ...
        </egenskaper>
        <stedfesting>
          <punkt veglenkesekvensNvdbId="1125766" posisjon="0.3"/>
        </stedfesting>
      </vegobjekt>
    </vegobjekter>
  </oppdater>
  ```

##### Korrigering av historiske versjoner    
Operasjon `korriger` tillater nå også korrigering av historiske vegobjektversjoner. Det vil ikke lenger bli gjort følgeoppdateringer
for korreksjon, men alle korreksjoner blir validert mot eksisterende data i NVDB. Det er klientens ansvar å supplere endringssettet
med nødvendige operasjoner for å opprettholde integriteten i NVDB. Eksempel:
 
```xml
<korriger>
  <vegobjekter>
    <vegobjekt typeId="538" nvdbId="2099994" versjon="1">
      <validering>
        <lestFraNvdb>2019-10-29T12:23:56</lestFraNvdb>
      </validering>
      <gyldighetsperiode>
        <startdato>1950-01-01</startdato>
        <sluttdato>2011-01-01</sluttdato>
      </gyldighetsperiode>
      <egenskaper>
        <egenskap typeId="4588">
          <verdi>1004</verdi>
        </egenskap>
        <egenskap typeId="4589">
          <verdi>Granevegen</verdi>
        </egenskap>
      </egenskaper>
      <stedfesting>
        <linje veglenkesekvensNvdbId="1004667" fra="0.0" til="1.0"/>
      </stedfesting>
    </vegobjekt>
    <vegobjekt typeId="538" nvdbId="2099994" versjon="2">
      <validering>
        <lestFraNvdb>2019-10-29T12:23:56</lestFraNvdb>
      </validering>
      <gyldighetsperiode>
        <startdato>2011-01-01</startdato>
      </gyldighetsperiode>
      <egenskaper>
        <egenskap typeId="4588">
          <verdi>1004</verdi>
        </egenskap>
        <egenskap typeId="4589">
          <verdi>Furuvegen</verdi>
        </egenskap>
      </egenskaper>
      <stedfesting>
        <linje veglenkesekvensNvdbId="1004667" fra="0.0" til="0.875867839535678"/>
      </stedfesting>
    </vegobjekt>
  </vegobjekter>
</korriger>
```

##### Strukturert geometribeskrivelse
Angivelse av geometri med kvalitetsparametre og tilleggsegenskaper har fått ny struktur. I V2 ble alle tilleggsegenskaper til en geometri angitt som verdier i EWKT syntaks, i V3 må tilleggsegenskaper angis som egne subelementer:

* Geometri med egenskaper i V2:
  ```xml
  <egenskap typeId="8883">
    <verdi>datafangstdato=2016-09-09;målemetode=96;målemetodeHøyde=96;nøyaktighet=1;nøyaktighetHøyde=1;synbarhet=0;maksimaltAvvik=1;temakode=9001;medium=1;sosinavn=FLATE;kommune=1601;verifiseringsdato=2016-09-10;oppdateringsdato=2016-09-11;høydereferanse=2;referansegeometri=1;srid=32633;POLYGON Z ((278220 7034016 123.45, 279220 7035016 246.78, 270220 7036016 226.78, 278220 7034016 123.45))</verdi>
  </egenskap>
  ```
* Geometri med egenskaper i V3:
  ```xml
  <egenskap typeId="8883">
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

##### Lukking
Operasjon `slett` heter nå `lukk` og har fått ny struktur. V2-attributten `kaskadeSletting` er nå et subelement: `kaskadelukking`. I tillegg må en eksplisitt `lukkedato` angis, også i et eget subelement. Begge deler må angis (påkrevd):

* V2 - operasjon slett: 
  ```xml
  <slett>
    <vegObjekter>
      <vegObjekt typeId="3" nvdbId="91610862" versjon="1" kaskadeSletting="JA"/>
    </vegObjekter>
  </slett>
  ```
* V3 - operasjon lukk:
  ```xml
  <lukk>
    <vegobjekter>
      <vegobjekt typeId="3" nvdbId="91610862" versjon="1">
        <lukkedato>2013-10-29</lukkedato>
        <kaskadelukking>JA</kaskadelukking>
      </vegobjekt>
    </vegobjekter>
  </lukk>
  ```

##### Delvis endring av stedfestingsverdier
Operasjon `delvisKorriger` og `delvisOppdater` tillater nå delvis manipulering av verdiene for `stedfesting`. I stedet for å angi komplett liste med stedfestingsverdier kan man oppgi bare
nye eller fjernede verdier via attributten `operasjon` på `<punkt>` og `<linje>`. Dersom man angir `operasjon="ny"` legges verdien til de eksisterende stedfestingsverdiene i NVDB. Dersom man angir `operasjon="slett"`
fjernes verdien fra den eksisterende stedfestingen i NVDB. Eksempel:
 
```xml
<delvisOppdater>
  <vegobjekter>
    <vegobjekt typeId="538" nvdbId="2099994" versjon="1">
      <gyldighetsperiode>
        <startdato>2020-01-01</startdato>
      </gyldighetsperiode>
      <stedfesting operasjon="oppdater">
        <linje veglenkesekvensNvdbId="1004667" fra="0.0" til="1.0" operasjon="ny"/>
      </stedfesting>
    </vegobjekt>
  </vegobjekter>
</delvisOppdater>
```

Attributten må angis på *alle* eller *ingen* verdier. Dersom attributten ikke er angitt på noen verdier, erstattes alle gjeldende verdier i NVDB med
de angitte verdiene (slik det har vært fram til nå). Det er ikke tillatt å fjerne alle gjeldende verdier fra stedfestingen (uten å legge til minst én ny).
Det er heller ikke tillatt å legge til en verdi som er identisk med eller overlapper en gjeldende verdi.

##### Delvis endring av assosiasjoner
Operasjon `delvisKorriger` og `delvisOppdater` tillater nå delvis manipulering av verdiene for `assosiasjon`. I stedet for å angi komplett liste med datterobjektreferanser kan man oppgi bare
nye eller fjernede verdier via attributten `operasjon` på `<nvdbId>` og `<tempId>`. Dersom man angir `operasjon="ny"` legges verdien til de eksisterende datterobjektreferansene i NVDB. Dersom man angir `operasjon="slett"`
fjernes verdien fra den eksisterende assosiasjonen i NVDB. Eksempel:
 
```xml
<delvisOppdater>
  <vegobjekter>
    <vegobjekt typeId="95" nvdbId="2454566" versjon="1" operasjon="oppdater">
      <gyldighetsperiode>
        <startdato>2020-01-01</startdato>
      </gyldighetsperiode>
      <assosiasjoner>
        <assosiasjon typeId="220004">
          <nvdbId operasjon="slett">37583</nvdbId>
        </assosiasjon>
      </assosiasjoner>
    </vegobjekt>
  </vegobjekter>
</delvisOppdater>
```

Attributten må angis på *alle* eller *ingen* verdier. Dersom attributten ikke er angitt på noen verdier, erstattes alle gjeldende verdier i NVDB med
de angitte verdiene (slik det har vært fram til nå). Dersom alle gjeldende verdier fjernes fra assosiasjonen, fjernes assosiasjonsegenskapen i sin helhet fra NVDB.
Det er ikke tillatt å legge til en verdi som er identisk med en gjeldende verdi.
    
    
#### Nye elementer

##### Kontekstinformasjon
Klienten kan angi egen, valgfri kontekstinformasjon som fritekst i endringssettet:
```xml
<kontekst><![CDATA[Dette er min kontekstinformasjon]]></kontekst>
```

##### Fjerning av vegobjektversjoner
Det er innført en ny hovedoperasjon for å kunne fysisk fjerne en vegobjektversjon. Merk at dette *ikke* er samme operasjon som `slett` i V2, som bare lukker vegobjektet.
```xml
<fjern>
  <vegobjekter>
    <vegobjekt typeId="3" nvdbId="91610862" versjon="2">
      <kaskadefjerning>JA</kaskadefjerning>
    </vegobjekt>
  </vegobjekter>
</fjern>
```
Når én eller flere vegobjektversjoner fjernes vil siste gjenværende versjon "arve" sluttdato fra siste fjernede versjon.
Når kaskadefjerning bestilles vil API Skriv sørge for at eventuelle datterobjekter også fjernes. Dersom man ikke bestiller kaskadefjerning og den fjernede
vegobjektversjonen har datterobjekter som krever mor, vil endringssettet avvises med valideringsfeil.
Dersom man utelater attributten `versjon` vil hele vegobjektet med all historikk fjernes.

##### Endringssettstatus
Status har fått nye elementer og utvidet innhold:
  * apiversjon - Angir hvilken versjon av NVDB API Skriv endringssettet ble sendt inn med
  * transaksjon - Identifiserer den resulterende transaksjonen i NVDB som endringssettet førte til
  * fremdriftOppdatert - Tidspunkt når endringssettet fikk gjeldende fremdriftsstatus
  * blokkerendeLåser - Liste med låsid'er for låser som blokkerer behandling av endringssettet
  * Resultat (subelement til Status) inneholder nå tilbakemeldinger av type `notabene` i tillegg til `feil` og `advarsel`. Dette er rene informative meldinger til klient/bruker om forhold rundt behandlingen av endringssettet.
  * Utførte følgeoppdateringer beskrives som notabene-meldinger under `<resultat>` 

