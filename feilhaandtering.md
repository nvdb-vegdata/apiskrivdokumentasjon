---
title: Feilhåndtering
order: 4
---

## Feilhåndtering

Alle endepunkter i NVDB API Skriv responderer umiddelbart med en standard [HTTP-statuskode](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
dersom requesten og dens payload ikke er korrekt formatert, anropet er uautentisert eller uautorisert, det er brukt ugyldig HTTP-verb eller
det avdekkes andre problemer.

### HTTP-statuskoder

Følgende HTTP-statuskoder kan inntreffe:

HTTP-status|Typiske årsaker
-|-
302&nbsp;FOUND|Requesten mangler autentiseringstoken, eller autentiseringstokenet er utløpt
400&nbsp;BAD&nbsp;REQUEST|Payloaden er ikke formatert i henhold til skjema
403&nbsp;FORBIDDEN|Anropende bruker er ikke autorisert for endepunktet, eller har ikke tilgang til den ressursen som etterspørres
404&nbsp;NOT&nbsp;FOUND|Ressursen som ble etterspurt i requesten ble ikke funnet
405&nbsp;METHOD&nbsp;NOT&nbsp;ALLOWED|Requesten brukte ugyldig HTTP-verb på ressursen
406&nbsp;NOT&nbsp;ACCEPTABLE|Ressursen kan ikke leveres i den [media-type](http://www.iana.org/assignments/media-types/media-types.xhtml) som requesten ber om i Accept-headeren
415&nbsp;USUPPORTED&nbsp;MEDIA&nbsp;TYPE|Requestens payload har ikke gyldig format
422&nbsp;UNPROCESSABLE&nbsp;ENTITY|Requestens payload er velformet, men kan ikke behandles på grunn av semantiske feil
500&nbsp;INTERNAL&nbsp;SERVER&nbsp;ERROR|Behandling av requesten ble avbrutt av en uventet hendelse, vanligvis en programfeil i NVDB API Skriv

### Feilmeldinger

De fleste responser med HTTP-statuskodene over, ledsages av en payload med en eller flere beskrivende feilmeldinger, f.eks.

```xml
<fault xmlns="http://nvdb.vegvesen.no/apiskriv/fault/v1">
  <message>registrer.vegobjekter[0].stedfesting.stedfestingselementer[0]: Fra må være mindre enn til</message>
</fault>
```

Tekstene i ```<message>``` -elementene kan opptre både på engelsk og norsk, avhengig av om feil detekteres av underliggende rammeverk
eller NVDB API Skriv sin egen forretningslogikk. De fleste feilmeldingene er tekniske av natur og egner seg ikke for presentasjon
til sluttbruker. Det er heller ikke hensikten med slike ```<fault>``` - responser. De fleste av disse responsene indikerer
at klientutvikleren har feilprogrammert eller ikke har tatt hensyn til faktorer som autentisering, autorisasjon o.l. Klienten bør logge slike
responser og betrakte dem som programfeil fra sin side.