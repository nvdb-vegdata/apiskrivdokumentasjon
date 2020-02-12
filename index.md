---
order: 0
---
# NVDB API Skriv

NVDB API Skriv er et asynkront REST-API for registrering og oppdatering av data i NVDB.

Systemet mottar beskrivelser av vegobjekter (_endringssett_) for _registrering_, _oppdatering_ eller _sletting_ i NVDB. En rekke validerings- og kontrolltrinn gjennomføres før data er godkjent og  sendes til NVDB. Et endringssett under behandling omtales som en jobb. Flere av trinnene i en jobb krever oppslag i NVDB og vil derfor kunne ta en del tid, spesielt for klienter som registrerer mange vegobjekter samtidig. I tillegg vil selve skrivingen til NVDB også ta tid. Videre vil noen oppdateringer ikke kunne skje umiddelbart fordi data er låst av andre klienter. (eller av en annen jobb i dette APIet). En konsekvens av dette er at jobber må eksekveres asynkront. Det vil si at klienten sender forespørsler for å definere og starte jobber, men siden svaret vil ta noe tid å utarbeide kan ikke et endelig svar leveres med en gang. Jobben blir i stedet behandlet i bakgrunnen, i en separat prosess som ikke blokkerer klienten.

## Forutsetninger

For å kunne skrive data til NVDB gjennom API Skriv, trenger du:

* En bruker i Statens Vegvesen med NVDB-roller i LDAP
* Tildelte datarettigheter i API Skriv. Rettigheter tildeles pr kommune, vegkategori og objekttype.

## Prinsipper

NVDB API Skriv setter strenge krav til mottatte data:

* Data valideres strengt ihht siste versjon av datakatalogen [https://datakatalogen.vegdata.no](Datakatalogen)
* Kun data som er komplette og korrekte ihht definisjonene i datakatalogen vil bli lagret i NVDB
* Data valideres også opp mot allerede lagrede data
* Alle data som skrives vha API Skriv må være stedfestet med veglenkeposisjon. Klienter er selv ansvarlig for å sende inn korrekte veglenkeposisjoner for sine objekter.  

## API V3

NVDB API Skriv V3 ble utviklet som en del av regionsreformprosjektet og gikk i produksjon høsten 2019. Se [egen oversikt over endringer i V3](https://apiskriv.vegdata.no/apiskrivv3.html). 

