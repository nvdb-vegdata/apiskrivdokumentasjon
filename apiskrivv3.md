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
* Operasjonen `slett` er nå omdøpt til `lukk` (se også strukturendringer). Operasjonen har aldri faktisk slettet noen ting fra NVDB, den har satt en sluttdato på objektene. Lukk er dermed et mer korrekt navn på denne operasjonen. Det vil i fremtiden også bli utviklet en `slett` operasjon som faktisk fjerner data fra NVDB.

### Strukturendringer

* `datakatalogversjon` er ikke lengre attributt til endringssett-elementet selv, men er nå et eget sub-element:
  * V2: `<endringssett ... datakatalogversjon="2.08">`
  * V3: `<datakatalogversjon>2.08</datakatalogversjon>`
  
* Effektdato representerer i V2 en felles effektdato for alle endringer i endringssettet. I V3 kan man nå manipulere start- og sluttdatoene på objektene direkte. Effektdato er derfor fjernet som attributt til endringssettet selv, og må dermed angis på hvert objekt. For operasjonene `registrer`, `korriger` og `oppdater` angis datoene under sub-elementet `gyldighetsperiode`, mens for operasjonen `lukk` angis en egen `lukkedato`:
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
