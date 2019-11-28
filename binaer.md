# Binær

Endringssett kan ikke ta i mot binære objekter (BLOB) som egenskapsverdier direkte. Binærobjektene må først lastes opp
via et eget endepunkt. Opplastingen responderer med en ressursid som benyttes som egenskapsverdi i endringssettet.

## Last opp binærdata
Laster opp binærdata og responderer med en id som deretter kan brukes som referanse i et endringssett.

Mønster
```
POST /nvdb/apiskriv/rest/v2/binaer
```
Forespørsel
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Content-Type|MediaType|Hvilken som helst MediaType, f.eks. image/png|
|Content-Disposition|Tekst|Hvis en ønsker å angi et filnavn for egen referanse, f.eks. filename="N808080-1.png"|

Kropp
Binærdata

Eksempel
```
POST /nvdb/apiskriv/rest/v2/binaer HTTP/1.1
Content-Type: image/png
Content-Disposition: filename="N808080-1.png"

.PNG........IHDR.............:~.U....IDATx.cj.......U.o....IEND.B`.
```
Svar
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Location|URI|Angir hvor en kan lese ressursen.|

Kropp
Eksempel
```
HTTP/1.1 201 Created
Location: http://localhost:8080/nvdb/apiskriv/rest/v2/binaer/e62a6896-ff48-474c-a5f3-bb5ccc45c9c9
Last opp multipart/form-data
```
Samme formål som over

Mønster
```html
<form method="post" action="/nvdb/apiskriv/rest/v2/binaer" enctype="multipart/form-data">
  <!-- Viktig: name="content" -->
  <input type="file" name="content"/>
  <input type="submit"/>
</form>
```

## Last ned binærdata
Tillater nedlasting av tidligere opplastede binærdata.
Mønster
```
GET /nvdb/apiskriv/rest/v2/binaer/{ressursId}
```
Forespørsel
Eksempel
```
GET /nvdb/apiskriv/rest/v2/binaer/e62a6896-ff48-474c-a5f3-bb5ccc45c9c9
```
Svar
Hode

|Navn|Type|Beskrivelse|
|-|-|-|
|Content-Type|MediaType|Angitt hvis den ble angitt under opplasting|
|Content-Disposition|Tekst|Angitt hvis den ble angitt under opplasting|

Kropp
Binærdata

Eksempel
```
HTTP/1.1 200 OK
Content-Type: image/png
Content-Disposition: filename="N808080-1.png"


.PNG........IHDR.............:~.U....IDATx.cj.......U.o....IEND.B`.
```

## Binærobjekt som egenskapsverdi

For å angi binærobjekt som egenskapsverdi i et vegobjekt benyttes elementet `<binaer>`, f.eks.:
```xml
...
  <egenskap typeId="7900">
    <binaer ressursId="e62a6896-ff48-474c-a5f3-bb5ccc45c9c9" format="image/png"/>
  </egenskap>
...
```

|Attributt|Beskrivelse|
|-|-|
|ressursId|Id mottatt fra opplastingsendepunktet|
|format|MediaType i henhold til [http://www.iana.org/assignments/media-types/media-types.xhtml]|
