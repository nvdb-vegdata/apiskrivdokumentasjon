# APISKRIV versjon 3
NVDB API SKRIV versjon 3 er utviklet som en del av regionreformen under prosjektet Nytt Nasjonalt Referansessystem. Hovedmålet med versjon 3 er å kunne ta i mot endringer i vegnettet, men denne funksjonaliteten er forbeholdt klienter som redigerer vegnett. Versjon 3 representerer også en vesentlig oppgradering av behandlingsmotoren i apiet og inneholder en god del forbedringer både funksjonelt og under panseret. Versjon 2 vil fremdeles fungere som før frem til 1.8.2021, men versjon 2 benytter nå bak fasaden den samme prosesseringspipeline som V3, slik at også V2-brukere får gleden av den nye motoren. 

## Nytt i V3

* Registrering og endring av vegnett (forbeholdt vegnettsklienter)
* Mulighet for korrigering av historiske versjoner av et objekt. Operasjonen `oppdater` (og `delvisOppdater`) med flagget `overskriv='JA'` erstatter dagens `korriger`. Korrigering vil nå skje uten følgeoppdateringer, men med alle valideringer. Oppdater med overskriv=JA trigger følgeoppdateringer som før. 
* Mulighet for å inkludere klient-spesifikk informasjon ('kontekst') i endringssettet


Skjemaet for endringssett har nå en ny og utvidet struktur. [Ny XSD er tilgjengelig her](https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett/endringssett.xsd). Endringene omfatter både navneendringer, strukturendringer og nye elementer. 

### Navneendringer

* Endringssettets `effektdato` er nå borte og erstattet med  `gyldighetsperiode` og `lukkedato` som nå må angis for hvert enkelt objekt (se strukturendringer)
* `vegObjekt` skrives nå `vegobjekt`
* `lokasjon`er omdøpt til `stedfesting`
* Stedfesting: `lenkeid` er omdøpt til `veglenkesekvensNvdbId`
* Stedfesting -> Sving: `nodeid`er omdøpt til `nodeNvdbId`
* Stedfesting -> Felt: `felt`er omdøpt til `kjørefelt` (som nå kan ta imot en liste av felt, se strukturendringer)
* Operasjonen `slett` er nå omdøpt til `lukk` (se også strukturendringer). Operasjonen har aldri faktisk slettet noen ting fra NVDB, den har alltid satt en sluttdato på objektene. Lukk er dermed et mer korrekt navn på denne operasjonen. Det vil i fremtiden også bli utviklet en `slett` operasjon som faktisk fjerner data fra NVDB.

### Strukturendringer

* `datakatalogversjon` er ikke lengre attributt til endringssett-elementet selv, men er nå et eget sub-element:
  * V2: `<endringssett ... datakatalogversjon="2.08">`
  * V3: `<datakatalogversjon>2.08</datakatalogversjon>`
  
* Effektdato representerer i V2 en felles effektdato for alle endringer i endringssettet. I V3 kan man nå manipulere start- og sluttdatoene på objektene direkte. Effektdato er derfor fjernet som attributt til endringssettet selv, og må angis på hvert objekt. For operasjonene `registrer`, `korriger` og `oppdater` angis datoene under sub-elementet `gyldighetsperiode`, mens for operasjonen `lukk` angis en egen `lukkedato`:
  * V2: `<endringssett ... effektDato="2013-10-29" ... >`
  * V3: Gyldighetsperiode angis per objekt for `registrer`, `korriger` og `oppdater`:
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

* Operasjon `oppdater` med atributten `overskriv` erstatter dagens `korriger`, både helt og delvis:
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
  * V3: Overskriving angis som attributt til operasjon `Oppdater`:
    ```xml  
      <oppdater>
        <vegobjekter>
          <vegobjekt typeId="581" nvdbId="551800127" versjon="1" overskriv="JA">
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
      <datakatalogversjon>2.16</datakatalogversjon>
    </endringssett>
    ```
    
 * Operasjon `korriger` tillater nå også korrigering av historikk. Det vil ikke bli gjort følgeoppdateringer for korreksjon, men alle korreksjoner blir validert. Eksempel:
 
    ```xml
      <korriger>
          <vegobjekter>
            <vegobjekt typeId="538" nvdbId="2099994" versjon="1">
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
              <gyldighetsperiode>
                <startdato>1950-01-01</startdato>
                <sluttdato>2011-01-01</sluttdato>
              </gyldighetsperiode>
            </vegobjekt>
            <vegobjekt typeId="538" nvdbId="2099994" versjon="2">
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
              <gyldighetsperiode>
                <startdato>2011-01-01</startdato>
              </gyldighetsperiode>
            </vegobjekt>
          </vegobjekter>
        </korriger>
      ```

* Angivelse av Geometri med kvalitetsparametre og tillegsegenskaper har fått ny struktur. I V2 ble alle tilleggsegenskaper til en geometri angitt som verdier i EWKT syntaks, i V3 må tilleggsegenskaper angis som egne sub-element:
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

* Operasjon `slett` heter nå `lukk` og har fått ny struktur. V2-attributten `kaskadeSletting` er nå et sub-element: `kaskadelukking`. I tillegg må en eksplisitt `lukkedato` angis, også i et eget sub-element. Begge deler må angis (påkrevd):
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


### Nye elementer i V3
* Klienten kan angi egen, valgri kontekst-informasjon som tekst i endringssettet:
  ```xml
    <kontekst><![CDATA[<HEI></HEI>]]></kontekst>
  ```
  
* Status og Resultat har fått nye element og utvidet innhold
 * Apiversjon - Angir hvilken versjon av APISKRIV endringssettet ble sendt inn med
 * OppdragsID - Identifiserer den resulterende transaksjonen i NVDB som endringssettet førte til
 * Nye venteårsaker og avvist-årsaker
 * Oppdatert liste av tilbakemeldingene: `feil`, `advarsel` og `notabene`
 

 


