---
title: Autentisering
order: 3
permalink: /autentisering
---

## Autentisering

### Identifikasjon av klienter

Alle klienter som anroper NVDB API Skriv må identifisere seg ved å angi ```X-Client``` som header i alle requester:

```
X-Client: NavnPåDinKlient
```

I en tidligere versjon av NVDB API Skriv var dette valgfritt, men i dagens versjon er headeren obligatorisk.

### Identifikasjon av brukere

Anrop fra klienter til NVDB API Skriv krever at requesten inneholder et gyldig autentiseringstoken, pakket i en cookie.
Autentiseringstokenet etableres med et anrop til Statens vegvesen sin [AAA-tjeneste](https://en.wikipedia.org/wiki/AAA_(computer_security)).
AAA-tjenesten har autogenerert [API-dokumentasjon](https://aaa-api.atlas.vegvesen.no/dokumentasjon/api/index.html#/aaa-API).
 
#### Innlogging

For å logge inn må man ha en Vegvesen-bruker i det aktuelle miljøet (STM, ATM eller PROD). Et autentiseringstoken for PROD etableres ved
å sende inn brukernavn og passord slik:
```bash
$ curl https://aaa-api.atlas.vegvesen.no/autentiser \
  -d "{'username': 'olanor', 'password': 'hemmelig'}" \
  -H "Content-Type: application/json"    
```
Requesten skal bruke tegnsettet UTF-8.

Responsen fra /autentiser inneholder tre felt:
 
```json
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
{  
  "valid": true, 
  "uid": "olanor", 
  "realm": "/external"
}
```

#### Bruk av token

Tokenets navn og verdi settes som en cookie på alle kall mot NVDB API Skriv. En forespørsel for å liste ut brukerens endringssett kan se slik ut:

```bash
$ curl https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett \
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
Docker|http://localhost:8010/ws/no/vegvesen/ikt/sikkerhet/aaa/autentiser
