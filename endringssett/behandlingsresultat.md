---
category: 1#Endringssett
title: Behandlingsresultat
order: 4
permalink: /endringssett/behandlingsresultat
---

# Nytt endepunkt for [NVDB api SKRIV dokumentasjon](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

NVDB api SKRIV V3 dokumentasjon er flyttet til [https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv](https://nvdb.atlas.vegvesen.no/docs/category/nvdb-api-skriv)

API'et blir jo aktivt vedlikeholdt og videreutviklet, så vi anbefaler sterkt at dere legger om til den nyeste versjonen av dokumentasjonen. 


## Behandlingsresultat

Et endringssett er ferdigbehandlet når det har [fremdriftskoden](introduksjon.md#koder-for-behandlingsfremdrift) ```UTFØRT``` (eventuelt ```UTFØRT_OG_ETTERBEHANDLET```) eller ```AVVIST```.
Uavhengig av om endringssettet ble effektuert eller ikke, vil endringssettets behandlingsresultat inneholde relevant informasjon for
klienten. Behandlingsresultatet beskrives av ```<resultat>``` -elementet i Status-entiteten som leveres ved anrop endringssettets
[statusendepunkt](api-referanse.md#hente-status-for-et-endringssett), for eksempel:

```xml
<status xmlns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3">
  <fremdrift>UTFØRT</fremdrift>
  ...
  <resultat>
    <feil/>
    <advarsler>
      <advarsel kode="IKKE_GJELDENDE_DATAKATALOGVERSJON">
        <melding>Datakatalogversjonen i endringssettet er ulik nåværende datakatalogversjon: 2.21</melding>
      </advarsel>
    </advarsler>
    <notabener/>
    <vegobjekter>
      <vegobjekt nvdbId="45874667" versjon="1">
        <feil/>
        <advarsler>
          <advarsel kode="MANGLER_BETINGET_PÅKREVDE_EGENSKAPER">
            <melding>Objektet, av typen Tunnel (581), mangler egenskaper med viktighet 'betinget': [Særskilt brannobjekt fra tidspunkt (9507), Merknad syklende (3913), Prosjektreferanse (11148), Brutus_Id (9329), Stigning, kvalitet (11510), Eier (11984), Sikkerhetsgodkjenningsdato (11448), Vedlikeholdsansvarlig (12013)]</melding>
            <referanse>https://datakatalogen.vegdata.no/581</referanse>
          </advarsel>
        </advarsler>
        <notabener/>
      </vegobjekt>
    </vegobjekter>
  </resultat>
  ...
</status>
```

### Varsler

I behandlingsresultatet kan API Skriv legge inn varsler til klienten/brukeren. Disse opptrer i tre ulike alvorlighetsgrader:

* **Notabene** er en ren informativ melding om ekstra tiltak som ble gjort i NVDB for å ivareta integriteten i dataene
(f.eks. følgeoppdateringer).
* **Advarsel** er et varsel om en mindre uregelmessighet i endringssettet eller NVDB, uten at det stopper effektuering av endringssettet.
* **Feil** indikerer en uregelmessighet i endringssettet som er så alvorlig at det ikke kan godkjennes og effektueres.

De fleste varslene er knyttet til spesifikke vegobjekter i endringssettet. Advarselen med kode ```MANGLER_BETINGET_PÅKREVDE_EGENSKAPER``` over er et eksempel
på dette. Noen ganger genereres imidlertid varsler som ikke er relatert til vegobjekter i endringssettet. Dette kalles _globale varsler_ og kan være
varsler knyttet til vegobjekter i NVDB eller er helt uavhengig av vegobjekter. Advarselen med kode ```IKKE_GJELDENDE_DATAKATALOGVERSJON``` over er et eksempel på det siste.

Et endringssett får fremdriftskoden AVVIST dersom det ble generert minst ett feilvarsel, enten som globalt varsel eller varsel tilknyttet et vegobjekt i endringssettet.

Hvert varsel beskrives minimum med en kode og en melding og, i de fleste tilfeller, en referanse eller URL for nærmere dokumentasjon.
I varsler som kan knyttes til underordnede komponenter av et vegobjekt angis dette med egne subelementer i varselet, for eksempel:

```xml
<resultat>
  <vegobjekter>
    <vegobjekt nvdbId="45874667" versjon="1">
      <feil>
        <feil kode="UGYLDIG_FLERVERDI">
          <melding>Verdien for egenskapstype Tunnelklasse, prosjektert (9134) må være en av følgende verdier: [A (12172), B (12173), C (12174), D (12175), E (12176), F (12177)]</melding>
          <referanse>https://datakatalogen.vegdata.no/581</referanse>
          <egenskapTypeId>9134</egenskapTypeId>
        </feil>
      </feil>
      <advarsler/>
      <notabener/>
    </vegobjekt>
  </vegobjekter>
</resultat>
```

Subelementer som gir kontekst for varselet kan være:

* ```<egenskapTypeId>``` angir typeId for egenskapen som trigget varselet.
* ```<geomertrideler>``` angir den delen, f.eks. to nabopunkter, av en geometriegenskap som trigget varselet.

### Tildelt id i NVDB

Dersom endringssettet inneholder registrering av nye vegobjekter og disse behandles uten feil-varsler vil behandlingsresultatet angi hvilken id disse
vegobjektene fikk i NVDB:

```xml
<resultat>
  <vegobjekter>
    <vegobjekt tempId="tunnel#1" nvdbId="834456" versjon="1">
      <feil/>
      <advarsler/>
      <notabener/>
    </vegobjekt>
  </vegobjekter>
</resultat>
```

For hvert registrert vegobjekt under ```<resultat>``` angir attributtene ```tempId``` og ```nvdbId``` koblingen mellom midlertidig id i endringssettet og endelig id i NVDB.

### Varselkoder

De fleste varselkodene som genereres av API Skriv indikerer en uregelmessighet i forhold til datakatalogen eller andre valideringsregler.
I behandlingsresultatet angis koden som en streng, ikke en enumerert type i [XML-skjemaet](https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/endringssett/status.xsd).
Kodene kan med andre ord endre ordlyd uten at statusendepunktet eller navnerommet versjoneres. Klienter frarådes derfor å implementere logikk som er avhengig av eksakt staving av
varselkodene. 

#### Globale varselkoder

Varselkode|Forklaring
-|-
IKKE_GJELDENDE_DATAKATALOGVERSJON|NVDB bruker en annen versjon av datakatalogen enn den som er oppgitt i endringssettet.
AUTOMATISK_OPPDATERING|Notabene med beskrivelse av en følgeoppdatering trigget av endringssettet.

#### Varselkoder relatert til vegobjekter

Varselkode|Forklaring
-|-
MANGLER_TILGANG|Brukeren har ikke tilgang til vegobjekttypen eller det området som vegobjektet er stedfestet i.
UFULLSTENDIG_VEGOBJEKT|Et delvis oppdatert vegobjekt har ikke angitt nok endringer (en egenskap, en assosiasjon eller stedfestingen må endres), eller et delvis korrigert vegobjekt har ikke angitt nok endringer (gyldighetsperioden, en egenskap, en assosiasjon eller stedfestingen må endres).
UKJENT_VEGOBJEKTTYPE|Vegobjektet har en typeId som ikke finnes i datakatalogen.
VEGOBJEKTTYPE_IKKE_I_OBJEKTLISTA|Vegobjektet har en typeId som ikke er med i objektlista for ferdigvegsdata fra entreprenører.
VEGOBJEKTET_FINNES_IKKE|Vegobjektet med oppgitt nvbdId ble ikke funnet i NVDB.
VEGOBJEKTVERSJONEN_FINNES_IKKE|Vegobjektversjonen med oppgitt nvdbId og versjon ble ikke funnet i NVDB.
VEGOBJEKTVERSJON_OVERSKREVET_AV_ANDRE|Vegobjektversjonen oppgitt for korrigering/overskriving er oppdatert av en annen prosess etter at det ble lest fra NVDB.
VERSJONSKONFLIKT|Vegobjektversjonen som skal oppdateres eller lukkes er ikke siste versjon av vegobjektet.
UGYLDIG_STARTDATO|Oppgitt dato for lukking eller oppgitt startdato for oppdatert versjon er ikke etter startdato for siste versjon i NVDB.
VEGOBJEKTET_ER_LUKKET|Vegobjektversjonen for lukking har allerede sluttdato, eller vegobjektversjonen for oppdatering har sluttdato som ikke er kompatibel med oppgitt sluttdato på ny versjon.
KONFLIKT_MELLOM_VEGOBJEKTER|En følgeoppdatering kan ikke gjennomføres fordi operasjonen kommer i konflikt med en annen vegobjektoperasjon i endringssettet.
STEDFESTING_UTENFOR_MOROBJEKT|Vegobjektet har en stedfesting som ikke dekkes av morobjektets stedfesting.
STEDFESTING_INNENFOR_MOROBJEKT<br/>_MEN_ANNET_FELT|Vegobjektet har en stedfesting som dekkes av morobjektets stedfesting, men i forskjellig kjørefelt.
STEDFESTING_INNENFOR_MOROBJEKT<br/>_MEN_ANNEN_SIDEPOSISJON|Vegobjektet har en stedfesting som dekkes av morobjektets stedfesting, men i forskjellig sideposisjon.
STEDFESTING_DEKKER_IKKE_DATTEROBJEKT|Vegobjektet har en stedfesting som ikke dekker stedfestingen til et datterobjekt.
STEDFESTING_DEKKER_DATTEROBJEKT<br/>_MEN_ANNET_FELT|Vegobjektet har en stedfesting som dekker stedfestingen til et datterobjekt, men i forskjellig kjørefelt.
STEDFESTING_DEKKER_DATTEROBJEKT<br/>_MEN_ANNEN_SIDEPOSISJON|Vegobjektet har en stedfesting som dekker stedfestingen til et datterobjekt, men i forskjellig sideposisjon.
DATTEROBJEKT_FINNES_IKKE|Oppgitt datterobjekt finnes ikke.
DATTEROBJEKT_AV_FEIL_TYPE|Oppgitt datterobjekt finnes, men er av feil vegobjekttype.
DATTEROBJEKT_UTEN_MOR|Vegobjektet er av en type som krever morobjekt, men vil ikke ha det etter at endringssettet er utført. 
UGYLDIG_OVERLAPP|Vegobjektet er av en type som ikke tillater at stedfestingen overlapper med et annet vegobjekt av samme type, men vil gjøre det etter at endringssettet er utført.
OVERLAPPSAUTOMATIKK_PÅKREVD|Vegobjektet er av en heldekkende type, men overlappsautomatikk er avbestilt. Overlappsautomatikk gjennomføres alltid for heldekkende vegobjekttyper. 
OVERLAPPSAUTOMATIKK_IKKE_PÅKREVD|Vegobjektet er av en type som tillater at stedfestingen overlapper med et annet vegobjekt av samme type, men overlappsautomatikk er likevel bestilt.
MANGLER_MOROBJEKT|Vegobjektet er av en type som krever morobjekt, men har det ikke.
MER_ENN_ETT_MOROBJEKT|Vegobjektet er av en type som krever ett og bare ett morobjekt, men har flere.
VEGOBJEKTTYPE_KAN_IKKE_BEHANDLES|Vegobjektet er av en type som er blokkert for behandling i endringssett.
VEGOBJEKTTYPE_KREVER_LÅS|Vegobjektet er av en vegnettsrelatert type og kan ikke behandles uten forhåndslåsing i NVDB.
TVETYDIG_KOMMUNETILHØRIGHET|Vegobjektet er stedfestet i en kommune, men har assosiasjon til en annen kommune.
VEGOBJEKTTYPE_TILLATER_KUN_EN_VERSJON|Vegobjektet er av en type som ikke kan oppdateres (versjoneres).
VEGOBJEKTTYPE_ER_IKKE_TIDSROMRELEVANT|Vegobjektet er av en type som ikke skal ha sluttdato og kan derfor verken lukkes eller oppdateres.
INKONSISTENT_GATENAVN|Vegobjektet av type Gate har en gatenavnegenskap som ikke matcher andre Gate-objekter med samme gatekodeegenskap innenfor samme kommune.
INKONSISTENT_GATEKODE|Vegobjektet av type Gate har en gatekodeegenskap som ikke matcher andre Gate-objekter med samme gatenavnegenskap innenfor samme kommune.
USAMMENHENGENDE_HISTORIKK|Vegobjektversjonen får endret gyldighetsperiode på en slik måte at historikken til vegobjektet (versjonene) ikke lenger er sammenhengende i tid.
FJERNING_AV_USAMMENHENGENDE_VERSJONER|Vegobjektversjonen som skal fjernes skaper hull i historikken til vegobjektet. Versjoner som skal fjernes må være sammenhengende.
FJERNING_AV_HISTORISKE_VERSJONER|Vegobjektversjonene som skal fjernes inkluderer ikke siste versjon. Historikk som fjernes må begynne bakfra med nyeste versjon.

#### Varselkoder relatert til egenskaper

Varselkode|Forklaring
-|-
MANGLER_TILGANG|Brukeren er ikke tildelt den sensitivitetsrollen som er nødvendig for å endre en sensitiv egenskap.
EGENSKAPEN_FINNES_IKKE|Det er bestilt sletting av en egenskap i forbindelse med delvis korrigering eller oppdatering, men vegobjektet har ikke denne egenskapen i NVDB.
UKJENT_EGENSKAPSTYPE|Egenskapen har en typeId som ikke finnes i datakatalogen.
EGENSKAPSTYPE_IKKE_I_OBJEKTLISTA|Egenskapen har en typeId som ikke er med i objektlista for ferdigvegsdata fra entreprenører.
UKJENT_MEDLEMSTYPE|Strukturmedlemmet har en typeId som ikke finnes i datakatalogen.
UGYLDIG_EGENSKAPSTYPE|Egenskapen har en typeId som finnes i datakatalogen, men ikke for denne vegobjekttypen.
UGYLDIG_MEDLEMSTYPE|Strukturmedlemmet har en typeId som finnes i datakatalogen, men ikke for denne strukturegenskapen.
AVLEDET_EGENSKAPSTYPE|Egenskapen får verdi automatisk av NVDB. Oppgitt verdi blir derfor overskrevet.
AUTOGENERERT_EGENSKAP|Egenskapen ble lagt til vegobjektet med verdi beregnet ut fra geometrien.
IKKE_FLERVERDIEGENSKAP|Egenskapen har enumId som verdi, men er ikke en flerverdiegenskap.
MANGLER_OBLIGATORISKE_EGENSKAPER|Vegobjektet mangler èn eller flere obligatoriske egenskaper.
MANGLER_BETINGET_PÅKREVDE_EGENSKAPER|Vegobjektet mangler èn eller flere egenskaper som er obligatoriske i gitte situasjoner.
MANGLER_ANBEFALTE_EGENSKAPER|Vegobjektet mangler èn eller flere anbefalte egenskaper.
FORENKLET_STEDFESTING|Stedfestingen ble forenklet på grunn av innbyrdes overlapp i stedfestingselementene eller stedfestingselementer var butt i butt. 
STEDFESTING_MED_FOR_LITEN_UTSTREKNING|Summen av godkjent lengde for stedfestingselementene var mindre enn minimumslengden.

#### Varselkoder relatert til egenskapsverdier

Varselkode|Forklaring
-|-
UGYLDIG_FELT|Stedfestingen angir kjørefelt, men vegobjekttypen er ikke kjørefeltrelevant.
UGYLDIG_SIDEPOSISJON|Stedfestingen angir sideposisjon, men vegobjekttypen er ikke sideposisjonrelevant.
MANGLER_RETNING|Stedfestingen mangler retning og vegobjekttypen er retningsrelevant.
UKJENT_NODE|Stedfestingen av type sving refererer til en node som ikke finnes.
NODE_UGYLDIG_PÅ_TIDSPUNKT|Stedfestingen av type sving refererer til en node som finnes, men er ikke gyldig i hele vegobjektversjonens levetid.
UKJENT_VEGLENKESEKVENS|Stedfestingen refererer til en veglenkesekvens som ikke finnes.
VEGLENKESEKVENS_UGYLDIG_PÅ_TIDSPUNKT|Stedfestingen refererer til en veglenkesekvens som finnes, men posisjonen/posisjonsintervallet er ikke gyldig i hele vegobjektversjonens levetid.
STEDFESTET_PÅ_KONNEKTERINGSVEGLENKE|Stedfestingen har en posisjon/posisjonsintervall inne på en konnekteringsveglenke, men vegobjekttypen tillater ikke dette.
STEDFESTET_PÅ_UGYLDIG_TOPOLOGINIVÅ|Stedfestingen refererer til en veglenkesekvens som representerer et topologinivå som vegobjekttypen ikke kan stedfestes på. De fleste vegobjekttype skal stedfestes på vegtrasénivå.
FORENKLET_GEOMETRI|Geometriegenskapen ble forenklet ved at parvise nabopunkter som var for tette ble redusert til ett punkt.
NORMALISERT_GEOMETRI|Geometriegenskapen ble angitt med enkelgeometri, men skal ha multigeometri. Verdien ble konvertert til multigeometri med ett element.
IRREGULÆR_GEOMETRI|Geometriegenskaper er ikke topologisk velformet, f.eks. for få punkter eller polygon som krysser seg selv.
GEOMETRI_MED_INKONSEKVENT_HØYDE|Geometriegenskapen har høyde-koordinat bare i noen punkter. Høyde, eller z-verdi, må angis i alle er ingen punkter.
UGYLDIG_KVALITETSPARAMETER|Geometriegenskapen har en kvalitetsparameter med ugyldig verdi.
UGYLDIG_FLERVERDI|Egenskapen er en flerverdiegenskap, men verdien er ikke blant de godkjente verdiene i datakatalogen.
FLERVERDI_IKKE_I_OBJEKTLISTA|Egenskapen er en flerverdiegenskap, men verdien er ikke blant de godkjente verdiene i objektlista for ferdigvegsdata fra entreprenører.
BINÆRVERDI_FINNES_IKKE|Egenskapen refererer til et binærobjekt i NVDB som ikke finnes.
STØRRE_ENN_ABSOLUTT_MAKSIMUM|Egenskapen har en verdi som er større enn den absolutte maksimumsverdien i datakatalogen.
MINDRE_ENN_ABSOLUTT_MINIMUM|-Egenskapen har en verdi som er mindre enn den absolutte minimumsverdien i datakatalogen.
STØRRE_ENN_ANBEFALT_MAKSIMUM|Egenskapen har en verdi som er større enn den anbefalte maksimumsverdien i datakatalogen.
MINDRE_ENN_ANBEFALT_MINIMUM|Egenskapen har en verdi som er mindre enn den anbefalte minimumsverdien i datakatalogen.
MANGLER_OBLIGATORISKE_MEDLEMMER|Strukturegenskapen mangler èn eller flere obligatoriske medlemmer.
MANGLER_ANBEFALTE_MEDLEMMER|Strukturegenskapen mangler èn eller flere anbefalte medlemmer.
FLERE_ENN_MAKSIMUM_ANTALL|Egenskapen tillater multiple verdier, men det er angitt flere enn maksimumsantallet i datakatalogen.
FÆRRE_ENN_MINIMUM_ANTALL|Egenskapen tillater multiple verdier, men det er angitt færre enn minimumsantallet i datakatalogen.
OVERSKRIDER_FELTLENGDE|Egenskapen har en verdi med feltbredde større en maksimal feltbredde i datakatalogen. For heltalls- og desimaltallsverdi er feltbredden antall siffere pluss eventuelt komma. For tekstverdi er feltbredden antall tegn.
FOR_MANGE_DESIMALER|Egenskapen har en desimaltallsverdi med flere desimaler en maksimalt antall i datakatalogen.
UGYLDIG_TYPE|Egenskapen har en verdi av en annen datatype enn det datakatalogen krever. Kan også indikere at feil geometritype er brukt i en geometriegenskap.
UGYLDIG_DATOFORMAT|Egenskapen har en dato- eller kortdatoverdi som ikke er korrekt formatert.
UGYLDIG_GEOMETRIFORMAT|Egenskapen har en geometriverdi som ikke er korrekt formatert.
UGYLDIG_GEOMETRI_SRID|Egenskapen har en geometriverdi som refererer til en SRID som ikke støttes av NVDB API Skriv.
UGYLDIG_GEOMETRITRANSFORMASJON|Egenskapen har en geometriverdi som ikke kunne transformeres til NVDBs koordinatreferansesystem EUREF89/UTM33.
GEOMETRI_UTENFOR_OMRISSET_AV<br/>_EUREF89_UTM33N|Egenskapen har en geometriverdi der ett eller flere punkter er utenfor gyldighetsområdet til sone 33 av UTM-båndet.
UGYLDIG_HELTALLFORMAT|Egenskapen har en heltallsverdi som ikke er korrekt formatert.
UGYLDIG_FLYTTALLFORMAT|Egenskapen har en desimaltallsverdi som ikke er korrekt formatert.
UGYLDIG_KORTDATOFORMAT|Egenskapen har en kortdatoverdi som ikke er korrekt formatert.
UGYLDIG_TIDFORMAT|Egenskapen har en klokkeslettverdi som ikke er korrekt formatert.
UGYLDIG_TEGNFORMAT|Egenskapen har en tegnverdi som ikke er korrekt formatert.
UGYLDIG_BOOLSK_FORMAT|Egenskapen har en boolsk verdi som ikke er korrekt formatert.
EGENSKAPSVERDI_FINNES_ALLEREDE|Egenskapen tillater multiple verdier, men har fått en ny verdi (via delvis oppdatering) som allerede finnes i NVDB.
EGENSKAPSVERDI_FINNES_IKKE|Egenskapen tillater multiple verdier, men har fått slettet en verdi (via delvis oppdatering) som ikke finnes i NVDB.
EGENSKAPSVERDI_OVERLAPPER|Stedfestingen har fått en ny verdi (via delvis oppdatering) som overlapper med et eksisterende stedfestingselement i NVDB. 
EGENSKAPSTYPE_STØTTER_IKKE<br/>_MULTIPLE_VERDIER|Egenskapen tillater ikke multiple verdier, men har delvis oppdatering av verdier.
