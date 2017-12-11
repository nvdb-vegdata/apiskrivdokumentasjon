Denne mappa inneholder en samling eksempler på bruk av Datafangst API'et. Samlingen er en Postman-samling, dvs at du trenger Postman for å hente de opp.

Bruk Datafangst_API_doc_all_[v1 | v2_1].json (v1 og v2.1 formatet til Postman)

Før første kall sendes, så må riktig Authorization og contract-id settes i Pre-Request Script i første kall. Disse brukes for resterende kall. Øvrige parametre som benyttes i eksemplene genereres av å kjøre kallene sekvensielt.

For å sette Authorization, så kan man gå til Authorization-tab på første kall, legge inn brukernavn og passord, legg til i Request. Deretter klipp ut strengen "Basic something" (fra Authorization-header) inn i Pre-request script (som da setter variablen for alle andre kall).
