

# NVDB API SKRIV


*   [Valideringsfeil](#valideringsfeil)
*   [Feilkoder](#feilkoder)


Hoveddelen av behandlingsforløpet for et endringssett dreier seg om validering. Det gjennomføres validering mot datakatalogen og mot eksisterende vegobjekter i NVDB.


## <a name="valideringsfeil">Valideringsfeil</a>



Eventuelle valideringsfeil angis som en del av responsen til [Se status](endringssett.html#GET-endringssett-status) etter at endringssettet er ferdigbehandlet. Opphavet til en feil (eller advarsel) indikeres ved at feil- og advarsel-elementer legges til som underlelementer til det ugyldig vegobjektet. Feil som ikke har opphav i et spesifikt vegobjekt samles nederst i resultat-elementet. Dersom feilen/advarselen kan knyttes til en spesifikk egenskap angis dette med et egenskapTypeId-element.

```xml
HTTP/1.1 200 OK


<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<status xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">
    <mottatt>2015-02-26T12:05:24.854</mottatt>
    <fremdrift>AVVIST</fremdrift>
    <avvistårsak>VALIDERINGSFEIL</avvistårsak>
    <resultat>
        <vegObjekter>
            <vegObjekt tempId="-1"/>
                <feil>
                    <feil kode="UKJENT_EGENSKAPSTYPE">
                        <melding>Egenskapstypen 0 finnes ikke i datakatalogen</melding>
                        <referanse>http://labs.vegdata.no/nvdb-datakatalog/581</referanse>
                        <egenskapTypeId>0</egenskapTypeId>
                    </feil>
                </feil>
            </vegObjekt>
        </vegObjekter>
        <feil/>
        <advarsel/>
    </resultat>
    <eier>krimyr</eier>
</status>
```


## <a name="feilkoder">Feilkoder</a>


Følgende koder benyttes i forbindelse med feil og advarsler:

|Kode|Type|Beskrivelse|
|=|=|=|
|IKKE_GJELDENDE_DATAKATALOGVERSJON|Advarsel|Datakatalogversjonen i endringssettet er ulik nåværende datakatalogversjon.|
|MANGLER_TILGANG|Feil|Brukeren mangler rettigheter til å utføre endringer på en vegobjekttype, eller mangler rettigheter til å endre vegobjekter som er stedfestet på en vegkategori, eller innenfor en kommune.|
|UFULLSTENDIG_VEGOBJEKT|Feil|Det er benyttet delvis oppdatering eller korrigering uten å angi minst én egenskap eller assosiasjon.|
|UKJENT_VEGOBJEKTTYPE|Feil|Angitt vegobjekttype finnes ikke i datakatalogen.|
|VEGOBJEKTTYPE_IKKE_I_OBJEKTLISTA|Feil|Angitt vegobjekttype finnes ikke i objektlista.|
|VEGOBJEKTET_FINNES_IKKE|Feil|Angitt vegobjekt (id) finnes ikke i NVDB. Kan inntreffe i forbindelse med korrigering.|
|VEGOBJEKTVERSJONEN_FINNES_IKKE|Feil|Angitt vegobjekt (id) finnes ikke eller har ingen aktiv versjon i NVDB. Kan inntreffe i forbindelse med oppdatering eller sletting.|
|EGENSKAPEN_FINNES_IKKE|Feil|Angitt vegobjekt (id) har ikke den angitte egenskapen i NVDB. Kan inntreffe i forbindelse med delvis oppdatering eller delvis korrigering.|
|VERSJONSKONFLIKT|Feil|Angitt versjon av et vegobjekt er ikke aktiv/siste versjon. Ved sletting og oppdatering må aktiv versjon av vegobjektet angis. Ved korrigering skal siste versjon (enten den er aktiv eller ikke) angis.|
|UGYLDIG_STARTDATO|Feil|Angitt versjon av et vegobjekt har startdato lik/etter effektdato for endringssettet. Kan inntreffe i forbindelse med oppdatering eller sletting.|
|VEGOBJEKTET_ER_LUKKET|Feil|Angitt versjon av et vegobjekt er allerede lukket med sluttdato fram i tid. Kan inntreffe i forbindelse med oppdatering eller sletting.|
|KONFLIKT_MELLOM_VEGOBJEKTER|Feil|Det er avdekket en følgeoppdatering (oppdatering av eksisterende vegobjekter for å opprettholde dataintegritet i NVDB) som kommer i konflikt med et vegobjekt i endringssettet eller en annen følgeoppdatering. Vegobjektet som skaper konflikten må tas ut og registreres i et separat endringssett.|
|UKJENT_EGENSKAPSTYPE|Feil|Angitt egenskapstype eller assosiasjonstype finnes ikke i datakatalogen.|
|EGENSKAPSTYPE_IKKE_I_OBJEKTLISTA|Feil|Angitt egenskapstype eller assosiasjonstype finnes ikke i objektlista.|
|UKJENT_MEDLEMSTYPE|Feil|Angitt egenskapstype i strukturen finnes ikke i datakatalogen.|
|UGYLDIG_EGENSKAPSTYPE|Feil|Angitt egenskapstype eller assosiasjonstype tilhører ikke vegobjekttypen i datakatalogen.|
|UGYLDIG_MEDLEMSTYPE|Feil|Angitt egenskapstype tilhører ikke strukturen i datakatalogen.|
|AVLEDET_EGENSKAPSTYPE|Advarsel|Angitt egenskapstype håndteres automatisk av NVDB. Verdien vil da bli overskrevet.|
|BEREGNET_EGENSKAP|Advarsel|Angitt egenskapstype ble lagt til vegobjektet med automatisk beregnet verdi.|
|IRREGULÆR_GEOMETRI|Advarsel|Angitt egenskapstype kan ikke benyttes som grunnlag for beregning av en geometrisk avledet egenskap.|
|UGYLDIG_FLERVERDI|Feil|Verdien for angitt flerverdiegenskap er ikke blant de tillatte verdiene.|
|FLERVERDI_IKKE_I_OBJEKTLISTA|Feil|Verdien for angitt flerverdiegenskap er ikke blant de tillatte verdiene i objektlista.|
|IKKE_FLERVERDIEGENSKAP|Feil|Det er angitt en flerverdiid (enum) for en egenskapstype som ikke er en flerverdiegenskap.|
|BINÆRVERDI_FINNES_IKKE|Feil|Det er angitt en binærverdiid som ikke finnes i registeret over opplastede binærobjekter.|
|MANGLER_OBLIGATORISKE_MEDLEMMER|Feil|En struktur mangler én eller flere egenskaper med viktighet 'påkrevd, absolutt krav'.|
|MANGLER_ANBEFALTE_MEDLEMMER|Advarsel|En struktur mangler én eller flere egenskaper med viktighet 'påkrevd, ikke absolutt'.|
|FLERE_ENN_MAKSIMUM_ANTALL|Feil|En listeegenskap har flere verdier enn maksimum antall.|
|FÆRRE_ENN_MINIMUM_ANTALL|Feil|En listeegenskap har færre verdier enn minimum antall.|
|MANGLER_OBLIGATORISKE_EGENSKAPER|Feil|Et vegobjekt mangler én eller flere egenskaper med viktighet 'påkrevd, absolutt krav'.|
|MANGLER_BETINGET_PÅKREVDE_EGENSKAPER|Advarsel|Et vegobjekt mangler én eller flere egenskaper med viktighet 'betinget'.|
|MANGLER_ANBEFALTE_EGENSKAPER|Advarsel|Et vegobjekt mangler én eller flere egenskaper med viktighet 'påkrevd, ikke absolutt'.|
|STEDFESTING_UTENFOR_MOROBJEKT|Feil|Et vegobjekt er stedfestet helt eller delvis utenfor stedfestingen til sitt morobjekt.|
|STEDFESTING_INNENFOR_MOROBJEKT_MEN_ANNET_FELT|Feil|Et vegobjekt er stedfestet korrekt innenfor sitt morobjekt, men i feil kjørefelt.|
|STEDFESTING_INNENFOR_MOROBJEKT_MEN_ANNEN_SIDEPOSISJON|Feil|Et vegobjekt er stedfestet korrekt innenfor sitt morobjekt, men med feil sideposisjon.|
|STEDFESTING_DEKKER_IKKE_DATTEROBJEKT|Feil|Et vegobjekt har stedfesting som ikke fullstendig dekker stedfestingen til et datterobjekt.|
|STEDFESTING_DEKKER_DATTEROBJEKT_MEN_ANNET_FELT|Feil|Et vegobjekt er stedfestet korrekt innenfor sitt morobjekt, men i feil kjørefelt.|
|STEDFESTING_DEKKER_DATTEROBJEKT_MEN_ANNEN_SIDEPOSISJON|Feil|Et vegobjekt er stedfestet korrekt innenfor sitt morobjekt, men med feil sideposisjon.|
|DATTEROBJEKT_FINNES_IKKE|Feil|Et vegobjekt har en assosiasjon til et datterobjekt som ikke finnes i endringssettet eller i NVDB.|
|DATTEROBJEKT_AV_FEIL_TYPE|Feil|Et vegobjekt har en assosiasjon til et datterobjekt som ikke er av forventet vegobjekttype.|
|DATTEROBJEKT_UTEN_MOR|Feil|Et vegobjekt som slettes etterlater et eller flere datterobjekter som må ha mor.|
|MANGLER_MOROBJEKT|Feil|Et vegobjekt av en type som krever et assosiert morobjekt, mangler dette i endringssettet.|
|MER_ENN_ETT_MOROBJEKT|Feil|Et vegobjekt har angitt eller får mer enn ett morobjektet gjennom behandling av endringssettet.|
|UKJENT_NODE|Feil|Et vegobjekt har ugyldig stedfesting. Angitt node finnes ikke i NVDB.|
|NODE_UGYLDIG_PÅ_TIDSPUNKT|Feil|Et vegobjekt har ugyldig stedfesting. Angitt node er ikke gyldig på effektdato.|
|UKJENT_LENKE|Feil|Et vegobjekt har ugyldig stedfesting. Angitt lenke finnes ikke i NVDB.|
|LENKE_UGYLDIG_PÅ_TIDSPUNKT|Feil|Et vegobjekt har ugyldig stedfesting. Angitt lenke er ikke gyldig på effektdato.|
|STEDFESTET_PÅ_KONNEKTERINGSLENKE|Feil|Et vegobjekt er stedfestet på en konnekteringslenke, men vegobjekttype tillater ikke dette.|
|STEDFESTET_PÅ_UGYLDIG_TOPOLOGINIVÅ|Feil|Et vegobjekt har ugyldig stedfesting. Angitt lenke eller node er ikke tilknyttet korrekt topologinivå for denne vegobjekttypen.|
|UGYLDIG_FELT|Feil|Et vegobjekt har ugyldig stedfesting. Kjørefelt er angitt for en vegobjekttype som ikke er kjørefeltrelevant, eller kjørefelt mangler for en vegobjekttype som er kjørefeltrelevant.|
|UGYLDIG_SIDEPOSISJON|Feil|Et vegobjekt har ugyldig stedfesting. Sideposisjon er angitt for en vegobjekttype som ikke er sideposisjonsrelevant, eller sideposisjon mangler for en vegobjekttype som er sideposisjonsrelevant.|
|UGYLDIG_OVERLAPP|Feil|Et vegobjekt av en type som ikke tillater overlapp, har helt eller delvis samme stedfesting som et annet vegobjekt av samme type, enten i endringssettet eller i NVDB.|
|STØRRE_ENN_ABSOLUTT_MAKSIMUM|Feil|Et vegobjekt har en verdi som er større enn absolutt maksimumsverdi.|
|MINDRE_ENN_ABSOLUTT_MINIMUM|Feil|Et vegobjekt har en verdi som er mindre enn absolutt minimumsverdi.|
|STØRRE_ENN_ANBEFALT_MAKSIMUM|Advarsel|Et vegobjekt har en verdi som er større enn anbefalt maksimumsverdi.|
|MINDRE_ENN_ANBEFALT_MINIMUM|Advarsel|Et vegobjekt har en verdi som er mindre enn anbefalt minimumsverdi.|
|OVERSKRIDER_FELTLENGDE|Feil|Et vegobjekt har en verdi med feltlengde som er større enn tillatt.|
|FOR_MANGE_DESIMALER|Feil|Et vegobjekt har en verdi med flere desimaler enn tillatt.|
|UGYLDIG_KVALITETSPARAMETER|Feil|Et vegobjekt har egengeometri der en eller flere kvalitetsparametere er ugyldige.|
|UGYLDIG_TYPE|Feil|Et vegobjekt har angitt egengeometri med ugyldig geometritype (f.eks. POINT når det forventes LINESTRING), eller stedfestingen er av feil type (f.eks. punktstedfesting når det er et strekningsobjekt).|
