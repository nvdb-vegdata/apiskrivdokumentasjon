---
title: Autentisering
order: 3
permalink: /autentisering
---

## Autentisering

Anrop fra klienter til NVDB API Skriv krever at requesten inneholder et gyldig autentiseringstoken. Dette kan gjøres på to måter:

* **OpenId Connect** - Klienten etablerer et id-token gjennom [OpenId Connect](https://en.wikipedia.org/wiki/OpenID_Connect) -pålogging og leverer dette i requester
til NVDB API Skriv i form av et Bearer-token i en Authorization-header.
* **AAA** - Klienten etablerer et IPlanetDirectoryPro-token gjennom anrop til AAA-tjenesten og leverer dette i requester til NVDB API Skriv i form av en cookie.

Anbefalt autentisering er via id-token fra OpenId Connect. Muligheten til å autentisere via cookie vil opphøre i nær framtid.


### Autentisering via OpenId Connect

Autentisering via OpenId Connect må realiseres på ulike måter avhengig av type klient.

#### Web-baserte klienter

Dersom klienten er en nettleser (SPA-applikasjon) eller en native mobil-app må denne realisere
såkalt [OpenId Connect implicit flow](https://openid.net/specs/openid-connect-core-1_0.html#ImplicitFlowAuth) for å få opprettet et gyldig id-token for sluttbrukeren.
Det finnes en [demo-applikasjon](https://atlas-docs.atlas.vegvesen.no/atlas-dokumentasjon/latest/for_utviklere/demoapplikasjon.html) (kun tilgjengelig i Statens vegvesens lokalnett) som illustrerer hvordan dette kan implementeres.  

Ved realisering av denne typen autentiseringsflyt trenger klienten informasjon om token-utsteder m.m. Dette kan hentes fra endepunktet https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oidc/client-config/{realm},
der {realm} er enten ```employee``` eller ```external```, avhengig av hvilken *identity realm* sluttbrukeren tilhører. Se [seksjon under](#innlogging) for beskrivelse av realms.

#### Ikke web-baserte klienter

Dersom klienten er en skrivebordsapplikasjon (tykk klient) eller et baksystem (tjenestebruker), kan OIDC-autentisering realiseres ved å anrope egne autentiseringsendepunkter i NVDB API Skriv. Seksjonene under beskriver dette.

##### Innlogging

For å logge inn må man ha en Vegvesen-bruker i det aktuelle miljøet (STM, ATM eller PROD). Et id-token for PROD etableres ved
å sende inn brukernavn og passord slik:

```bash
$ curl https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oidc/authenticate \
  -d "{'username': 'olanor', 'password': 'hemmelig', 'realm': 'EMPLOYEE'}" \
  -H "Content-Type: application/json"    
```

Requesten skal bruke tegnsettet UTF-8. Implementasjonen av OIDC i Docker-imaget gir vellykket autentisering når brukernavnet er identisk med passordet.

Feltet ```realm``` er valgfritt og angir brukerens identity realm (brukertype). Tillatte verdier er:

Realm|Beskrivelse
-|-
EMPLOYEE|Personlig bruker ansatt i Statens vegvesen
SERVICE_ACCOUNT|Tjenestebruker
EXTERNAL|Personlig ekstern bruker, ikke ansatt i Statens vegvesen

Dersom feltet utelates benyttes realm ```EMPLOYEE```.

Responsen fra /authenticate inneholder tre felt:
 
```json
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{  
  "idToken": "eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...", 
  "accessToken": "eyJ0eXAiOiJKV1QiLCJ6aXAiOiJOT05FIiwia2lkIjoia1d2OXpQbzVHbFFMamptQk5HR0FtYTJrUTJjPSIsImFsZyI6IlJTMjU2In0...",
  "refreshToken": "eyJ0eXAiOiJKV1QiLCJ6aXAiOiJOT05FIiwia2lkIjoia1d2OXpQbzVHbFFMamptQk5HR0FtYTJrUTJjPSIsImFsZyI6IlJTMjU2In0..."
}
```

Hvert av feltene har et [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT) som verdi.

JWT'en i feltet ```idToken``` skal brukes i Authorization-headere i requester til NVDB API Skriv.

Dersom autentisering mislykkes, f.eks. ved ugyldig brukernavn eller passord, indikeres dette med tomt objekt i responsen:

```json
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{ }
```

##### Fornyelse av id-token

Et id-token varer i svært kort tid, ofte bare 15 minutter. Klienten kan hente utløpstiden til id-tokenet fra JWT-strukturen. For å unngå at sluttbrukeren må logge seg på på nytt hver gang id-tokenet er utløpt, kan et nytt id-token
utstedes ved å bruke refresh-tokenet fra authenticate-responsen:

```bash
$ curl https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oidc/refresh \
  -d "{'refreshToken': 'eyJ0eXAiOiJKV1QiLCJ6aXAiOiJOT05FIiwia2lkIjoia1d2OXpQbzVHbFFMamptQk5HR0FtYTJrUTJjPSIsImFsZyI6IlJTMjU2In0...', 'realm': 'EMPLOYEE'}" \
  -H "Content-Type: application/json"    
```

Feltet ```realm``` er valgfritt og angir brukerens identity realm ved autentisering (se over). Dersom feltet utelates benyttes realm ```EMPLOYEE```.

Responsen fra /refresh inneholder to felt:
 
```json
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{  
  "idToken": "eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ...", 
  "accessToken": "eyJ0eXAiOiJKV1QiLCJ6aXAiOiJOT05FIiwia2lkIjoia1d2OXpQbzVHbFFMamptQk5HR0FtYTJrUTJjPSIsImFsZyI6IlJTMjU2In0..."
}
```

JWT'en i feltet ```idToken``` kan deretter brukes for en ny kort periode i requrester til NVDB API Skriv, inntil det må fornyes igjen.

Et refresh-token har noen timers varighet og fornying vil således før eller siden mislykkes. Responsen fra refresh-endepunktet indikerer dette ved å respondere med tomt objekt:

```json
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{ }
```

#### Bruk av id-token

Etter at vellykket pålogging er ferdig og et OIDC id-token er etablert skal dette angis som verdi for en Authorization-header med prefix "Bearer"
i alle requester til NVDB API Skriv. En forespørsel for å liste ut brukerens endringssett kan se slik ut:

```bash
$ curl https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/endringssett \
  -H "X-Client: curl" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJraWQiOiJrV3Y5elBvNUdsUUxqam1CTkdHQW1hMmtRMmM9IiwiYWxnIjoiUlMyNTYifQ..."
```

Dersom en request mot NVDB API Skriv mangler eller bruker et ugyldig/utløpt id-token vil det responderes med 401 UNAUTHORIZED.

#### Autentiseringsendepunkter

Oversikt over endepunkter for OIDC-autentisering i ulike miljøer:

Miljø|URL
-|-
STM|https://nvdbapiskriv-stm.utv.atlas.vegvesen.no/rest/v1/oidc/{operasjon}
ATM|https://nvdbapiskriv.test.atlas.vegvesen.no/rest/v1/oidc/{operasjon}
PROD|https://nvdbapiskriv.atlas.vegvesen.no/rest/v1/oidc/{operasjon}
Docker|http://localhost:8010/nvdb/apiskriv/rest/v1/oidc/{operasjon}

Operasjon kan være ```authenticate```, ```refresh``` eller ```client-config```.

### Autentisering via AAA-tjenesten

Denne autentiseringmetoden er under avvikling. Alle klienter anmodes om å bruke OpenId Connect. I en overgangsperiode har NVDB API Skriv aktivert
automatisk konvertering av autentiseringstoken fra AAA til motsvarende autentiseringstoken fra OpenId Connect.

Autentiseringstokenet etableres her med et anrop til Statens vegvesen sin [AAA-tjeneste](https://en.wikipedia.org/wiki/AAA_(computer_security)).
AAA-tjenesten har autogenerert [API-dokumentasjon](https://aaa-api.atlas.vegvesen.no/dokumentasjon/api/index.html#/aaa-API).
 
#### Innlogging

For å logge inn må man ha en Vegvesen-bruker i det aktuelle miljøet (STM, ATM eller PROD). Et autentiseringstoken for PROD etableres ved
å sende inn brukernavn og passord slik:
```bash
$ curl https://aaa-api.atlas.vegvesen.no/autentiser \
  -d "{'username': 'olanor', 'password': 'hemmelig'}" \
  -H "Content-Type: application/json"    
```
Requesten skal bruke tegnsettet UTF-8. Implementasjonen av AAA i Docker-imaget gir vellykket autentisering når brukernavnet er identisk med passordet. 

Responsen fra /autentiser inneholder tre felt:
 
```json
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{  
  "status": true, 
  "token": "AQIC5wM2LY4SfcyhHz1Irgsf6pbxoeuY3k9XqvbRtB4-4No.*AAJTSQACMDIAAlNLABMzMDUyMTI1NzE2ODA4ODU0OTczAAJTMQACMDM.*",
  "tokenname": "iPlanetDirectoryProOAM"
}
```

* ```status``` indikerer om autentisering var vellykket eller ikke
* ```token``` inneholder det nye autentiseringstokenet, eller er tom dersom autentiseringen var mislykket
* ```tokenname``` angir navnet på cookien som tokenet skal leveres i

#### Validering av token

Et token varer i 6 timer, eller til klienten eksplisitt kaller /logout. Klienter kan ta vare på tokenet for å slippe å autentisere før hvert kall.
De kan eventuelt kontrollere om tokenet fremdeles er gyldig slik:

```bash
$ curl https://aaa-api.atlas.vegvesen.no/validate \
  -d "{'token':'mitt_token'}" \
  -H "Content-Type: application/json"    
```

Responsen fra /validate forteller om tokenet fremdeles er gyldig og hvilket brukernavn dette gjelder for:

```json
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{  
  "valid": true, 
  "uid": "olanor", 
  "realm": "/external"
}
```

#### Bruk av token

Tokenets navn og verdi settes som en cookie på alle kall mot NVDB API Skriv. En forespørsel for å liste ut brukerens endringssett kan se slik ut:

```bash
$ curl https://nvdbapiskriv.atlas.vegvesen.no/rest/v3/endringssett \
  -H "X-Client: curl" \
  -H "Cookie: iPlanetDirectoryProOAM= AQIC5wM2LY4SfcyhHz1Irgsf6pbxoeuY3k9XqvbRtB4-4No.*AAJTSQACMDIAAlNLABMzMDUyMTI1NzE2ODA4ODU0OTczAAJTMQACMDM.*"
```

Dersom en request mot NVDB API Skriv mangler eller bruker et ugyldig/utløpt token vil skallsikringen internt hos Statens vegvesen
hindre at den når fram til APIet. Klienten vil da motta en 302 FOUND-respons.

#### Utlogging 

Et token kan eksplisitt ugyldiggjøres ved å kalle /logout med tokenet som payload:

```bash
$ curl https://aaa-api.atlas.vegvesen.no/logout \
  -d "{'token':'mitt_token'}" \
  -H "Content-Type: application/json"    
```

#### Påloggingsendepunkter

Oversikt over endepunkter for autentisering i ulike miljøer:

Miljø|URL
-|-
STM|https://aaa-api.utv.atlas.vegvesen.no/autentiser
ATM|https://aaa-api.test.atlas.vegvesen.no/autentiser
PROD|https://aaa-api.atlas.vegvesen.no/autentiser
Docker|http://localhost:8010/aaa-api/autentiser
