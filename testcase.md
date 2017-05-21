#Testcase
Testcase kan hentes programmatisk for å inkluderes i applikasjoner som vil kjøre disse.

Et testcase består av et endringssett og en liste med validatorer. Validatorer beskriver det forventede resultatet fra behandlingen av endringssettet.

Enkelte testcase er designet for å feile. For slike testcase beskriver validatorene den forventede feilmeldingen.

Elementet xmlJobb og jsonJobb brukes i stedet for endringssett for å beskrive requester som ikke er syntaktisk korrekte.

Eksempel
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<testcases xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">
    <testcase id="0" title="Gyldig vegobjekt">
        <endringssett effektDato="2013-10-29" datakatalogversjon="2.01">
            <registrer>
                <vegObjekter>
                    <vegObjekt typeId="581" tempId="-1">
                        <egenskaper>
                            <egenskap typeId="5225">
                                <verdi>Grevlingtunnelen</verdi>
                            </egenskap>
                            <egenskap typeId="9306">
                                <verdi>34343</verdi>
                            </egenskap>
                            <egenskap typeId="9134">
                                <verdi>12176</verdi>
                            </egenskap>
                            <egenskap typeId="8945">
                                <verdi>2000</verdi>
                            </egenskap>
                        </egenskaper>
                        <lokasjon>
                            <punkt lenkeId="345" posisjon="0.3"/>
                        </lokasjon>
                    </vegObjekt>
                </vegObjekter>
            </registrer>
        </endringssett>
        <validation>
            <assert status="200"/>
            <assert contains="UTFØRT"/>
            <assert notcontains="ERROR"/>
        </validation>
    </testcase>
    
    <!-- ... -->

    <testcase id="29" title="Objekt med ugyldig stedfesting">
        <xmlJobb>
            <![CDATA[
                <endringssett effektDato="2013-10-29" datakatalogversjon="2.01" xmlns="http://nvdb.vegvesen.no/apiskriv/domain/v2">
                    <registrer>
                        <vegObjekter>
                            <!-- Fartsgrense -->
                            <vegObjekt typeId="105" tempId="-1">
                                <egenskaper>
                                    <!-- Fartsgrense 60 -->
                                    <egenskap typeId="2021">
                                        <verdi>2732</verdi>
                                    </egenskap>
                                </egenskaper>
                                <lokasjon>
                                    <!-- Ugyldig id-verdi -->
                                    <linje lenkeId="this-should-be-a-number" fra="0.5" til="0.75"/>
                                </lokasjon>
                            </vegObjekt>
                        </vegObjekter>
                    </registrer>
                </endringssett>
            ]]>
        </xmlJobb>
        <jsonJobb>
            <![CDATA[
                {
                    "registrer": {
                        "vegObjekter": [
                            {
                                "lokasjon": {
                                    "linje": {
                                        "lenkeId": "this-should-be-a-number",
                                        "fra": 0.5,
                                        "til": 0.75
                                    }
                                },
                                "typeId": 105,
                                "tempId": "-1",
                                "egenskaper": [
                                    {
                                        "typeId": 2021,
                                        "verdi": ["2732"]
                                    }
                                ]
                            }
                        ]
                    }
                }
            ]]>
        </jsonJobb>
        <validation>
            <assert status="400"/>
            <assert contenttype="application/xml" contains="'this-should-be-a-number' is not a valid value for 'integer'"/>
            <assert contenttype="application/json" contains="Can not construct instance of java.lang.Long from String value 'this-should-be-a-number': not a valid Long value"/>
        </validation>
    </testcase>

    <!-- ... -->
</testcases>
```
