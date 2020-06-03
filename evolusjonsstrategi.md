---
title: Evolusjonsstrategi
order: 2
---

## Evolusjonsstrategi

### Versjonering

Versjoneringen i NVDB API Skriv er annerledes enn i NVDB API Les. Siden sistnevnte kun tilbyr GET-requester og inngående payloads
derfor ikke finnes, er regimet redusert til å bruke respons-revisjoner for å angi ønsket format på responsene. URLene har også et versjonsnummer,
med dette indikerer applikasjonsversjonen.

NVDB API Skriv benytter payloads både i requester og responser. Dette krever en mer eksplisitt versjonering. Innholdet i payloads er bygget opp
av entiteter fra et XML-skjema som har et entydig navnerom. Navnerommet inneholder versjonsnummeret til skjemaet. Dette versjonsnummeret
gjenspeiles i URLen til endepunktet som eksponerer entiteten/ressursen.
 
#### Eksempel

Gjeldende versjon for endringssett-entiteten er 3. Registrering av og uthenting av endringssett gjøres derfor via URLen /nvdb/apiskriv/rest/v3/endringssett.
Skjemaet for endringssett-entiteten som støttes av dette endepunktet definerer et navnerom (targetNamespace) som inneholder leddet "v3":

```xml
<xs:schema elementFormDefault="qualified" version="1.0"
  targetNamespace="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3"
  xmlns:tns="http://nvdb.vegvesen.no/apiskriv/domain/changeset/v3"
  xmlns:xs="http://www.w3.org/2001/XMLSchema">
  
  <xs:element name="endringssett" type="tns:Endringssett"/>
  
  <xs:complexType name="Endringssett">
    <xs:all>
      <xs:element name="l&#229;sing" type="tns:L&#229;sing" minOccurs="0"/>
      <xs:element name="eksternRef" type="xs:string" minOccurs="0"/>
      <xs:element name="kontekst" type="xs:string" minOccurs="0"/>
      <xs:element name="validering" type="tns:EndringssettValidering" minOccurs="0"/>
      <xs:element name="registrer" type="tns:Registrering" minOccurs="0"/>
      <xs:element name="oppdater" type="tns:Oppdatering" minOccurs="0"/>
      <xs:element name="korriger" type="tns:Korrigering" minOccurs="0"/>
      <xs:element name="lukk" type="tns:Lukking" minOccurs="0"/>
      <xs:element name="fjern" type="tns:Fjerning" minOccurs="0"/>
      <xs:element name="delvisOppdater" type="tns:DelvisOppdatering" minOccurs="0"/>
      <xs:element name="delvisKorriger" type="tns:DelvisKorrigering" minOccurs="0"/>
      <xs:element ref="tns:status" minOccurs="0"/>
      <xs:element name="ansvarlig" type="xs:string" minOccurs="0"/>
      <xs:element name="datakatalogversjon" type="xs:string"/>
    </xs:all>
    <xs:attribute name="id" type="xs:string"/>
  </xs:complexType>
  ...
</xs:schema>
```

NVDB API Skriv støtter også v2 av endringssett via URLen /nvdb/apiskriv/rest/v2/endringssett. Denne versjonen av entiteten er
vesentlig forskjellig fra og derfor ikke kompatibel med v3:

```xml
<xs:schema elementFormDefault="qualified" version="1.0"
  targetNamespace="http://nvdb.vegvesen.no/apiskriv/domain/v2"
  xmlns:tns="http://nvdb.vegvesen.no/apiskriv/domain/v2"
  xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <xs:element name="endringssett" type="tns:Endringssett"/>

  <xs:complexType name="Endringssett">
    <xs:all>
      <xs:element name="id" type="xs:string" minOccurs="0"/>
      <xs:element name="registrer" type="tns:Registrering" minOccurs="0"/>
      <xs:element name="oppdater" type="tns:Oppdatering" minOccurs="0"/>
      <xs:element name="korriger" type="tns:Korrigering" minOccurs="0"/>
      <xs:element name="slett" type="tns:Sletting" minOccurs="0"/>
      <xs:element name="delvisOppdater" type="tns:DelvisOppdatering" minOccurs="0"/>
      <xs:element name="delvisKorriger" type="tns:DelvisKorrigering" minOccurs="0"/>
      <xs:element ref="tns:status" minOccurs="0"/>
      <xs:element ref="tns:etterbehandling" minOccurs="0"/>
      <xs:element name="ansvarlig" type="xs:string" minOccurs="0"/>
    </xs:all>
    <xs:attribute name="effektDato" type="xs:date" use="required"/>
    <xs:attribute name="datakatalogversjon" type="xs:string" use="required"/>
  </xs:complexType>
  ...
</xs:schema>
```

### Versjoneringsregime

NVDB API Skriv er i stadig utvikling og utvidelser, forbedringer og feilrettinger rulles ut kontinuerlig. For å hindre at eksisterende
klienter får problemer med nye utgaver av APIet, benyttes følgende versjoneringsregime:

* **Brekkende endringer** fører til etablering av nytt endepunkt og entitet-skjema med økt versjonsnummer. Brekkende endringer
kan være:
  * Endret navn eller betydning for et element eller en attributt i skjemaet
  * Endret navn eller betydning for en URL-parameter
  * Fjernet element eller attributt i skjemaet
  * Fjernet URL-parameter
  * Fjernet verdi for en enum-type
  
* **Ikke-brekkende endringer** fører _ikke_ til ny versjon av URL og skjema. Ikke-brekkende endringer kan være:
  * Nytt ikke-obligatorisk element eller attributt i entitet som brukes i request
  * Nytt element eller attributt i entitet som brukes i respons
  * Ny ikke-obligatorisk URL-parameter med standardverdi som gir samme resultat som før endringen
  * Ny verdi for en enum-type
     
### Endringer i datakatalogen

Datakatalogen i NVDB oppdateres jevnlig, typisk én gang i kvartalet. Skjemaene som benyttes for endringssett er strukturelt
frikoblet fra innholdet i datakatalogen. Fjernede vegobjekttyper eller egenskapstyper krever derfor ikke versjonering.

Behandlingsresultatet for et gitt endringssett kan imidlertid bli forskjellig før og etter en datakatalogoppdatering. Dersom
en styringsparameter som påvirker valideringslogikken i NVDB API Skriv endres, vil et endringssett som ble godkjent og utført i går
kunne bli avvist med valideringsfeil i dag.
