# Autentisering

## Identifikasjon av klienter

Alle klienter som anroper NVDB API Skriv må identifisere seg ved å angi X-Client som header i alle requester:

```
X-Client: NavnPåDinKlient
```

I versjon 2 av NVDB API Skriv var dette valgfritt, men fra versjon 3 er headeren obligatorisk.

## Autentisering av brukere

Anrop fra klienter til NVDB API Skriv krever at requesten inneholder et autentiseringstoken, pakket i en cookie. Autentiseringstokenet etableres med et anrop til
Statens vegvesen sin [AAA-tjeneste](https://en.wikipedia.org/wiki/AAA_(computer_security)).
 
### Login

Et autentiseringstoken etableres ved å sende inn brukernavn og passord slik:
```bash
 $ curl -d "{'username': 'olanor', 'password': 'hemmelig'}" -H "Content-Type: application/json"
      https://www.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/autentiser
```
Dersom passordet inneholder æ, ø, eller å må tegnsettet ISO 8859-1.

Responsen fra login inneholder tre felt:
 
 1. status: true|false - indikerer om autentisering var vellykket eller ikke
 2. token: selve autentiseringstokenet, eller tom dersom login feiler
 3. tokenname: Navn på token. Må brukes i cookie.
 
```json
{  
  "status": true, 
  "token": "AQIC5wM2LY4SfcyhHz1Irgsf6pbxoeuY3k9XqvbRtB4-4No.*AAJTSQACMDIAAlNLABMzMDUyMTI1NzE2ODA4ODU0OTczAAJTMQACMDM.*",
  "tokenname": "iPlanetDirectoryProOAM"
}
```

### Valider token

Et token varer i 6 timer, eller til klienten eksplisitt kaller logout. Klienter kan ta vare på tokenet for å slippe å autentisere før hvert kall.
Klienter kan sjekke om et token fremdeles er gyldig slik:

```bash
 $ curl -d "{'token':'mitt_token'}" -H "Content-Type: application/json"
      https://www.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/validate
```

Responsen fra valider forteller om tokenet fremdeles er gyldig og hvilket brukernavn dette gjelder for:

```json
{  
  "valid": true, 
  "uid": "olanor", 
  "realm": "/external"
}
```

### Bruk av token

Tokenets navn og verdi settes som en cookie på alle kall mot NVDB API Skriv. Eksempel-forespørsel (lister ut brukerens endringssett):

```bash
 $ curl -H "Cookie: iPlanetDirectoryProOAM= AQIC5wM2LY4SfcyhHz1Irgsf6pbxoeuY3k9XqvbRtB4-4No.*AAJTSQACMDIAAlNLABMzMDUyMTI1NzE2ODA4ODU0OTczAAJTMQACMDM.*"
      https://www.vegvesen.no/nvdb/apiskriv/rest/v3/endringssett
```

### Logout 

Et token kan eksplisitt invalideres ved å kalle logout med tokenet som payload:

```bash
 $ curl -d "{'token':'mitt_token'}" -H "Content-Type: application/json"
      https://www.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/logout
```

### Påloggingsendepunkter

Oversikt over endepunkter for autentisering i ulike SVV-miljøer:

|Miljø|URL|Cookie-name|
|-|-|-|
|STM|https://www.utv.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/autentiser|iPlanetDirectoryProOAMutv|
|ATM|https://www.test.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/autentiser|iPlanetDirectoryProOAMTP|
|PROD|https://www.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/autentiser|iPlanetDirectoryProOAM|


