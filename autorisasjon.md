# Autorisasjon

## Identifikasjon

```
Content-Type: application/xml; charset=UTF-8
Accept: application/xml
X-Client: DinKlient
```


## Autorisasjon
Login-request:
```
curl -d "{}"
  -H "X-OpenAM-Username: <username>"
  -H "X-OpenAM-Password: <passord>"
  -H "Content-Type: application/json"
      https://www.vegvesen.no/openam/json/authenticate?realm=External&authIndexType=module&authIndexValue=LDAP
```

Login-response:
```json
{  
    "tokenId": "AQIC5wM2LY4SfcyhHz1Irgsf6pbxoeuY3k9XqvbRtB4-4No.*AAJTSQACMDIAAlNLABMzMDUyMTI1NzE2ODA4ODU0OTczAAJTMQACMDM.*",
    "successUrl": "/openam/console"
}
```
Eksempel-forespørsel:

```
curl -H "Cookie: iPlanetDirectoryProOAM= AQIC5wM2LY4SfcyhHz1Irgsf6pbxoeuY3k9XqvbRtB4-4No.*AAJTSQACMDIAAlNLABMzMDUyMTI1NzE2ODA4ODU0OTczAAJTMQACMDM.*" https://www.vegvesen.no/nvdb/apiskriv/rest/v2/endringssett
```
Autentiseringstokenet varer i 6 timer er inntil https://www.vegvesen.no/openam/UI/Logout anropes eksplisit (med token i header).

Logout:
```
curl -H "Cookie: iPlanetDirectoryProOAM= AQIC5wM2LY4SfcyhHz1Irgsf6pbxoeuY3k9XqvbRtB4-4No.*AAJTSQACMDIAAlNLABMzMDUyMTI1NzE2ODA4ODU0OTczAAJTMQACMDM.*" https://www.vegvesen.no/openam/UI/Logout
```

Oversikt over påloggingsendepunkter for ulike miljø:

|Miljø|URL|Cookie-name|
|-|-|-|
|UTV/STM|https://www.utv.vegvesen.no/openam/json/authenticate?... |iPlanetDirectoryProOAMutv|
|TEST/ATM|https://www.test.vegvesen.no/openam/json/authenticate?... |iPlanetDirectoryProOAMTP|
|PROD|https://www.vegvesen.no/openam/json/authenticate?... |iPlanetDirectoryProOAM|

Query-parametre: `realm=External&authIndexType=module&authIndexValue=LDAP`
