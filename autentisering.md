# Autentisering

## Identifikasjon

Vi ber om at alle klienter identifiserer seg. Fra versjon 3 av APISKRIV er dette påkrevd. Dette gjøres med å sette header:

```
X-Client: NavnPåDinKlient
```

## Autentisering

Autentisering gjøres mot Statens Vegvesen sin OpenAM løsning. En klient må først logge inn med brukernavn og passord for å skaffe seg et token. Tokenet, sammen med navn på tokenet settes så som en cookie som må settes på alle kall til APISKRIV. Token-navn er unikt for hvert miljø.

# Login-request (autentiser):
```
curl -d "{'username': brukernavn, 'password': passord}"
  -H "Content-Type: application/json"
      https://www.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/autentiser
```

# Login-response:
Responsen fra login inneholder tre felt:
 
 1. status: True | False - indikerer om login var vellykket eller ei
 2. token: selve autentiseringstokenet, tom dersom login feiler
 3. tokenname: Navn på token. Må brukes i cookie.
 
```json
{  
  "status": "True", 
  "token": "AQIC5wM2LY4SfcyhHz1Irgsf6pbxoeuY3k9XqvbRtB4-4No.*AAJTSQACMDIAAlNLABMzMDUyMTI1NzE2ODA4ODU0OTczAAJTMQACMDM.*",
  "tokenname": "iPlanetDirectoryProOAM"
}
```

# Valider token

Et token varer i 6 timer (eller til klienten eksplisitt kaller logout). Klienter kan ta vare på tokenet for å slippe å autentisere før hvert kall. Klienter kan sjekke om et token fremdeles er gyldig:

```
curl -d "{'token':stored_token}"
  -H "Content-Type: application/json"
      https://www.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/validate
```
Valider respons:

Responsen fra valider forteller om tokenet fremdeles er gyldig og hvilket brukernavn dette gjelder for:

```json
{  
  "valid": "True", 
  "uid": "USERNAME", 
  "realm": "/external"
}
```


# Bruk av token

Tokennavn og token settes som en cookie på alle kall mot apiskriv. Eksempel-forespørsel, (list endringssett):

```
curl -H "Cookie: iPlanetDirectoryProOAM= AQIC5wM2LY4SfcyhHz1Irgsf6pbxoeuY3k9XqvbRtB4-4No.*AAJTSQACMDIAAlNLABMzMDUyMTI1NzE2ODA4ODU0OTczAAJTMQACMDM.*" https://www.vegvesen.no/nvdb/apiskriv/rest/v2/endringssett
```


# Logout 

Et token kan eksplisitt invalideres ved å kalle logout med tokenet som payload.

```
curl -d "{'token':stored_token}"
  -H "Content-Type: application/json"
      https://www.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/logout
```

Oversikt over påloggingsendepunkter for ulike miljø:

|Miljø|URL|Cookie-name|
|-|-|-|
|UTV/STM|https://www.utv.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/autentiser |iPlanetDirectoryProOAMutv|
|TEST/ATM|https://www.test.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/autentiser |iPlanetDirectoryProOAMTP|
|PROD|https://www.vegvesen.no/ws/no/vegvesen/ikt/sikkerhet/aaa/autentiser |iPlanetDirectoryProOAM|


