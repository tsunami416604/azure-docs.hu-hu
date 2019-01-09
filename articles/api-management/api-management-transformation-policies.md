---
title: Az Azure API Management-átalakítási csoportházirendek |} A Microsoft Docs
description: Ismerje meg az átalakítási szabályzatok az Azure API Management használható.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 3a8b0ecefe9e314e8056c941c9ef4ce32d1cbb75
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119373"
---
# <a name="api-management-transformation-policies"></a>Az API Management átalakítási szabályzatok
Ez a témakör egy hivatkozást kínál a következő az API Management házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [az API Management házirendek](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a> Átalakítási szabályzatok

-   [JSON átalakítása XML](api-management-transformation-policies.md#ConvertJSONtoXML) – alakíthatók át egymásba kérelem vagy válasz törzs JSON-ból XML.

-   [XML átalakítása JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – alakíthatók át egymásba kérelem vagy válasz törzs XML-ről JSON-ná.

-   [A szervezet karakterlánc keresése és cseréje](api-management-transformation-policies.md#Findandreplacestringinbody) - kérelem vagy válasz részkarakterláncot keres, és lecseréli egy másik karakterláncrészletet.

-   [Tartalom URL-címek maszkolja](api-management-transformation-policies.md#MaskURLSContent) -hivatkozások (maszkok) újra ír a válaszban törzs úgy, hogy a megfelelő hivatkozásra az átjárón keresztül mutassanak.

-   [Állítsa be a háttérszolgáltatás](api-management-transformation-policies.md#SetBackendService) – módosítja egy bejövő kérésnek tartozó a háttérszolgáltatáshoz.

-   [Állítsa be a szervezet](api-management-transformation-policies.md#SetBody) – beállítja a bejövő és kimenő kéréseket az üzenet törzse.

-   [HTTP-fejléc beállítása](api-management-transformation-policies.md#SetHTTPheader) – értéket rendel hozzá egy meglévő válasz és/vagy a kérelem fejlécében vagy ad hozzá egy új válasz és/vagy a kérelem fejlécében.

-   [Állítsa be a lekérdezési sztring paramétereként](api-management-transformation-policies.md#SetQueryStringParameter) – ad hozzá, értéke váltja fel, vagy töröl a kérés lekérdezési karakterlánc paramétereként.

-   [URL-cím újraírása](api-management-transformation-policies.md#RewriteURL) -alakítja át a kérelem URL-cím a nyilvános űrlap az űrlap a webszolgáltatás által várt.

-   [Az XSLT-vel XML-átalakítás](api-management-transformation-policies.md#XSLTransform) -XSL átalakító érvényes XML-kérelem vagy válasz törzsében.

##  <a name="ConvertJSONtoXML"></a> JSON átalakítása XML
 A `json-to-xml` házirend egy kérelem vagy válasz törzsében konvertálja JSON XML.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|JSON-xml|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|alkalmaz|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> -mindig - mindig érvényes átalakítás.<br />csak akkor, ha a válasz Content-Type fejléce azt jelzi, hogy jelenléte JSON - tartalom típusa, json - convert.|Igen|–|
|Fontolja meg – fogadja el-fejléc|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> átalakítás – igaz - vonatkoznak, ha JSON Accept fejléc kérelem van szükség.<br />-false - mindig érvényes átalakítás.|Nem|true|
|Parse-dátuma|Ha a beállítása `false` dátumértékeket egyszerűen másolja átalakítás során|Nem|true|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, – hiba

-   **A házirend-hatókörök:** globális, termék, API-művelet

##  <a name="ConvertXMLtoJSON"></a> XML átalakítása JSON
 A `xml-to-json` házirend alakítja át egy kérelem vagy válasz törzsében XML-ről JSON. Ez a házirend segítségével korszerűsítheti az API-k csak XML háttér-webszolgáltatásokat alapján.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|XML-json|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|típusa|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> -javascript-barát – az átalakított JSON a JavaScript-fejlesztőinek rövid űrlapot befogadó.<br />a konvertált JSON - közvetlen – jeleníti meg az eredeti XML-dokumentum szerkezete.|Igen|–|
|alkalmaz|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> -mindig - mindig konvertálni.<br />csak akkor, ha a válasz Content-Type fejléce azt jelzi, hogy jelenléte XML - tartalom típusa, xml - convert.|Igen|–|
|Fontolja meg – fogadja el-fejléc|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> átalakítás – igaz - alkalmazni, ha XML van szükség a kérelem Accept fejlécet.<br />-false - mindig érvényes átalakítás.|Nem|true|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, – hiba

-   **A házirend-hatókörök:** globális, termék, API-művelet

##  <a name="Findandreplacestringinbody"></a> A szervezet karakterlánc keresése és cseréje
 A `find-and-replace` házirend kérelem vagy válasz részkarakterláncot keres, és lecseréli egy másik karakterláncrészletet.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Példa

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|Keresés és csere|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|forrás:|A keresendő karakterlánc.|Igen|–|
|erre:|A behelyettesítendő karakterlánc. Adja meg a nulla hosszúságú helyettesítő karakterláncok eltávolítása a keresési karakterláncot.|Igen|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** globális, termék, API-művelet

##  <a name="MaskURLSContent"></a> A tartalom maszk URL-címek
 A `redirect-content-urls` házirend újra ír a válasz törzsében (maszkok) hivatkozások, úgy, hogy a megfelelő hivatkozásra az átjárón keresztül mutassanak. A kimenő szakaszban használatával írja át a válasz törzse hivatkozásokat, hogy az átjáró mutasson. Használja a bejövő szakaszban az ellenkezője.

> [!NOTE]
>  Ez a házirend nem változtatja meg bármely fejlécértékeket például `Location` fejlécek. Fejléc értékek módosításához használja a [set-fejléc](api-management-transformation-policies.md#SetHTTPheader) házirend.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<redirect-content-urls />
```

### <a name="example"></a>Példa

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|redirect-content-urls|A gyökérelem.|Igen|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő

-   **A házirend-hatókörök:** globális, termék, API-művelet

##  <a name="SetBackendService"></a> A háttérszolgáltatás beállítása
 Használja a `set-backend-service` egy bejövő kérésnek átirányítása egy másik háttérszolgáltatás, mint az API-beállításai a művelethez megadott szabályzat. Ez a szabályzat a házirendben megadott háttérrendszer szolgáltatás kiindulási URL-címét a bejövő kérelem változik.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<set-backend-service base-url="base URL of the backend service" />
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Ebben a példában a service set háttérszabályzat irányítja a kérelmeket a lekérdezési karakterláncban, mint az API-ban megadott egy másik háttérszolgáltatás átadott verzió érték alapján.

Kezdetben a háttérrendszer szolgáltatás kiindulási URL-címe az API-beállítások származik. Ezért a kérelem URL-címe `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` válik `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` ahol `http://contoso.com/api/10.4/` a háttérrendszer szolgáltatás URL-címe az API-beállításaiban.

Ha a [< válasszon\> ](api-management-advanced-policies.md#choose) házirendutasítás alkalmazza a háttérrendszer szolgáltatás kiindulási URL-cím változhat újra, `http://contoso.com/api/8.2` vagy `http://contoso.com/api/9.1`, attól függően, a verzió kérelem lekérdezési paraméter értéke. Például, ha az érték `"2013-15"` a végső kérelem URL-cím lesz `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Ha a kérelem átalakítási-e további kívánt, más [átalakítási szabályzatok](api-management-transformation-policies.md#TransformationPolicies) is használható. Távolítsa el az version lekérdezési paramétert, most, hogy a rendszer ilyenkor irányítja a kérést egy verzió adott háttérrendszer, például a [állítsa be a lekérdezési sztring paramétereként](api-management-transformation-policies.md#SetQueryStringParameter) házirend segítségével távolítsa el az most már redundáns verzió attribútumot.

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Ebben a példában a házirend továbbítja a kérést egy service fabric háttér, a userId lekérdezési karakterlánc értéket partíciókulcsként, valamint az elsődleges replika, a partíció használatához.

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|háttér-szolgáltatás beállítása|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|alap URL-címet|Új háttérrendszer szolgáltatás kiindulási URL-címe.|Nem|–|
|háttér-azonosító|A háttérrendszer irányíthatja a azonosítója.|Nem|–|
|SF partíciókulcs|Csak akkor alkalmazható, ha a háttérrendszer egy Service Fabric-szolgáltatás, és meghatározott backend-id használatával. A névfeloldási szolgáltatás egy adott partícióra feloldásához használt.|Nem|–|
|sf-replica-type|Csak akkor alkalmazható, ha a háttérrendszer egy Service Fabric-szolgáltatás, és meghatározott backend-id használatával. Ha a kérelem el kell küldeni az elsődleges vagy másodlagos replika partíció szabályozza. |Nem|–|
|sf-resolve-condition|Csak akkor alkalmazható, ha a háttérrendszer a Service Fabric-szolgáltatás. A feltétel azonosítása kell-e a hívás a Service Fabric háttérrendszeréhez új megoldás meg kell ismételni.|Nem|–|
|sf-service-instance-name|Csak akkor alkalmazható, ha a háttérrendszer a Service Fabric-szolgáltatás. Lehetővé teszi szolgáltatáspéldányok futásidőben módosításához. |Nem|–|
|sf-listener-name|Csak akkor alkalmazható, ha a háttérrendszer egy Service Fabric-szolgáltatás, és meghatározott backend-id használatával. Service Fabric Reliable Services lehetővé teszi, hogy hozzon létre több kérésfigyelőt egy szolgáltatásban. Ez az attribútum egy adott hallgató válassza, ha egy háttérszolgáltatás Reliable Services rendelkezik több figyelő szolgál. Ha ez az attribútum nincs megadva, az API Management megkísérli használja egy figyelő név nélkül. Egy figyelő név nélkül, amely csak egyetlen figyelője működik, a Reliable Services – jellemző. |Nem|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, háttér

-   **A házirend-hatókörök:** globális, termék, API-művelet

##  <a name="SetBody"></a> Törzs beállítása
 Használja a `set-body` szabályzat beállítása az üzenettörzs a bejövő és kimenő kéréseket. Az üzenettörzs használható eléréséhez a `context.Request.Body` tulajdonság, vagy a `context.Response.Body`, attól függően, hogy a szabályzat a bejövő vagy kimenő szakaszában.

> [!IMPORTANT]
>  Vegye figyelembe, hogy az üzenet használatakor alapértelmezés szerint törzs használatával `context.Request.Body` vagy `context.Response.Body`, az eredeti üzenet törzsének elvész, és állítsa vissza a következő kifejezést a szervezetnek felismerésével. A szervezet tartalom megőrzésére, állítsa be a `preserveContent` paramétert `true` az üzenet elérésekor. Ha `preserveContent` értékre van állítva `true` és a egy másik szervezet által visszaadott a kifejezést, a visszaadott törzse lesz.
>
>  Használata esetén vegye figyelembe az alábbiakat a `set-body` házirend.
>
>  -   Ha használja a `set-body` vissza egy új vagy frissített törzse nem kell beállítani a házirend `preserveContent` való `true` mivel kifejezetten megadja az új törzsének tartalmát.
> -   Megőrzi a bejövő folyamat válasz tartalma nem értelme, mert nem érkezik válasz, még.
> -   Megőrzi a kimenő folyamat kérelem tartalma nem értelme, mert a kérelem már küldte a háttérrendszer ezen a ponton.
> -   Ha ezt a szabályzatot használja, amikor nincs üzenet szövegét, például egy bejövő GET, a függvény kivételt vált ki.

 További információkért lásd: a `context.Request.Body`, `context.Response.Body`, és a `IMessage` szakaszát a [környezeti változó](api-management-policy-expressions.md#ContextVariables) tábla.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Példák

#### <a name="literal-text-example"></a>Szövegkonstans példa

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>A példában eléréséhez a szervezet karakterláncként. Vegye figyelembe, hogy az eredeti kérelem törzse, hogy, hogy hozzá tud férni a folyamat későbbi részében is megőrzi.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Példa a szervezettől a JObject eléréséhez. Vegye figyelembe, hogy azt az eredeti kérelem törzse nem tart fenn, mert fér hozzá a folyamat későbbi részében fogja eredményezni kivételt.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>A termék választ szűrése
 Ez a példa bemutatja, hogyan hajthat végre, tartalomszűrés adatelem távolítsa el a válasz érkezett a háttérszolgáltatás használata esetén a `Starter` termék. Konfigurálása és használata a szabályzat bemutatójáért lásd: [Cloud Cover epizód 177: További API Management funkcióit Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 34:30 előretekerés. Áttekintést a 31:50 kezdőpont [a sötét Sky előrejelzési API](https://developer.forecast.io/) a bemutatóhoz használt.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="using-liquid-templates-with-set-body"></a>A törzs beállítása Liquid-sablonok használatával
A `set-body` házirend használatára konfigurálható a [Liquid](https://shopify.github.io/liquid/basics/introduction/) sablonalapú nyelvet és a egy kérelem vagy válasz törzsében transfom. Ez nagyon hatékony, ha az üzenet formátumát bármilyen módon van szüksége lehet.

> [!IMPORTANT]
> Folyékony megvalósítása a használt a `set-body` szabályzat van konfigurálva, a "C# mód". Ez akkor különösen fontos, ha például a szűrés dologra. Tegyük fel, a dátum szűrő használatával szükséges Pascal kis-és és a C# dátum formázása, például:
>
> {{body.foo.startDateTime| Dátum: "yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Annak érdekében, hogy megfelelően Liquid-sablonnal egy XML-törzs kötést létrehozni, használjon egy `set-header` házirend beállítása a Content-Type vagy application/xml, text és xml (vagy bármely típusú végződő + xml); egy JSON-törzse, kell application/json, text/json (vagy bármilyen végződésű + JSON-).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>JSON átalakítása SOAP Liquid-sablon használatával
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="tranform-json-using-a-liquid-template"></a>Tranform JSON Liquid-sablon használatával
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|törzs beállítása|A gyökérelem. A szöveg vagy egy kifejezés, amely visszaadja a szervezet tartalmazza.|Igen|

### <a name="properties"></a>Tulajdonságok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|sablon|Használja a sablonalapú mód, amely a készlet törzs szabályzat módosítása. Jelenleg az egyetlen támogatott érték van:<br /><br />-liquid - törzs-szabály beállítása fogja használni a liquid sablonalapú motor |Nem|folyékony|

A kérések és válaszok információ eléréséhez, folyékony sablon a context objektumot, a következő tulajdonságokkal kell kötni: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő háttérrendszer

-   **A házirend-hatókörök:** globális, termék, API-művelet

##  <a name="SetHTTPheader"></a> HTTP-fejléc beállítása
 A `set-header` házirend értéket rendel hozzá egy meglévő válasz és/vagy a kérelem fejlécében vagy ad hozzá egy új válasz és/vagy a kérelem fejlécében.

 HTTP-fejlécek listája szúr be a HTTP üzenet. Ha a bejövő folyamatban, ez a szabályzat a HTTP-fejléceket, a kérés átadódik a célszolgáltatás az állítja be. Ha a kimenő folyamatban, ez a szabályzat a HTTP-fejléceket, a válasz az átjáró ügyfél küld állítja be.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```

#### <a name="forward-context-information-to-the-backend-service"></a>Környezeti információk a háttérszolgáltatáshoz továbbítása
 Ez a példa bemutatja, hogyan alkalmazhatja a szabályzatot a háttérszolgáltatáshoz környezeti információkat adhat meg az API szintjén. Konfigurálása és használata a szabályzat bemutatójáért lásd: [Cloud Cover epizód 177: További API Management funkcióit Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és előretekerés 10:30. 12:10: Nincs művelet meghívása a fejlesztői portálon, ahol megtekintheti a szabályzatot a munkahelyi bemutatóját.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 További információkért lásd: [házirend-kifejezések](api-management-policy-expressions.md) és [környezeti változó](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Fejléc több érték is összefűzött a fürt megosztott kötetei szolgáltatás a karakterláncot, például:  
> `headerName: value1,value2,value3`
>
> Kivételek szabványos fejlécek, mely értékeket tartalmazza:
> - vesszőket tartalmazhat (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - Előfordulhat, hogy tartalmazza a dátum (`Cookie`, `Set-Cookie`, `Warning`),
> - tartalmazza a dátum (`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since`, `Last-Modified`, `Retry-After`).
>
> A kivételek esetén fejlécében több érték nem kell fűzni be egy karakterláncot, és lesznek átadva külön fejlécként, például:  
>`User-Agent: value1`  
>`User-Agent: value2`  
>`User-Agent: value3`

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|set-fejléc|A gyökérelem.|Igen|
|érték|Adja meg a fejléc kell beállítani. Az ezzel a névvel több fejlécek hozzá további `value` elemeket.|Igen|

### <a name="properties"></a>Tulajdonságok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|létezik-művelet|Itt adható meg, milyen műveleteket, ha a fejléc már meg van adva. Ez az attribútum a következő értékek egyikét kell rendelkeznie.<br /><br /> -felülbírálás - lecseréli a meglévő fejléc értékét.<br />-skip – nem helyettesíti a meglévő fejléc értéke.<br />-hozzáfűzése - az érték hozzáfűzi a meglévő fejléc értéke.<br />a kérelem - delete - eltávolítja a fejléc.<br /><br /> Ha a beállítása `override` felvétel ugyanazzal a névvel több bejegyzést eredményez az összes bejegyzés (amely lesz látható többször) megfelelően beállítása fejléc; csak a felsorolt értékek jelennek meg az eredményt.|Nem|felülbírálás|
|név|Kell beállítani a fejléc nevét adja meg.|Igen|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** globális, termék, API-művelet

##  <a name="SetQueryStringParameter"></a> Készlet lekérdezési karakterlánc paramétereként
 A `set-query-parameter` házirendnek, cserél értékét, vagy a törlések kérelem lekérdezési karakterlánc paramétereként. Adja át a lekérdezési paraméterek nem kötelező a háttérszolgáltatás által várt segítségével, vagy soha nem szerepelnek a kérelmet.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Környezeti információk a háttérszolgáltatáshoz továbbítása
 Ez a példa bemutatja, hogyan alkalmazhatja a szabályzatot a háttérszolgáltatáshoz környezeti információkat adhat meg az API szintjén. Konfigurálása és használata a szabályzat bemutatójáért lásd: [Cloud Cover epizód 177: További API Management funkcióit Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és előretekerés 10:30. 12:10: Nincs művelet meghívása a fejlesztői portálon, ahol megtekintheti a szabályzatot a munkahelyi bemutatóját.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 További információkért lásd: [házirend-kifejezések](api-management-policy-expressions.md) és [környezeti változó](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|set-query-parameter|A gyökérelem.|Igen|
|érték|Adja meg a lekérdezési paraméter kell beállítani. Az ezzel a névvel több lekérdezési paraméterek hozzá további `value` elemeket.|Igen|

### <a name="properties"></a>Tulajdonságok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|létezik-művelet|Itt adható meg, milyen műveleteket, ha a lekérdezési paraméter már meg van adva. Ez az attribútum a következő értékek egyikét kell rendelkeznie.<br /><br /> -felülbírálás - lecseréli a meglévő paraméter értékét.<br />-skip – nem helyettesíti a meglévő lekérdezési paraméter értéke.<br />-hozzáfűzése - az érték hozzáfűzése a meglévő lekérdezési paraméter értéke.<br />-delete - eltávolítja a lekérdezési paraméter a kérelemből.<br /><br /> Ha a beállítása `override` felvétel ugyanazzal a névvel több bejegyzést eredményez a lekérdezési paramétert (amely lesz látható többször) összes bejegyzés megfelelően beállítása; csak a felsorolt értékek jelennek meg az eredményt.|Nem|felülbírálás|
|név|Itt adható meg kell beállítani a lekérdezési paraméter neve.|Igen|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, háttér

-   **A házirend-hatókörök:** globális, termék, API-művelet

##  <a name="RewriteURL"></a> URL-cím újraírása
 A `rewrite-uri` házirend alakítja át a kérelem URL-CÍMÉT a nyilvános űrlap az űrlap a webszolgáltatás által várt a következő példában látható módon.

-   Nyilvános URL- `http://api.example.com/storenumber/ordernumber`

-   Kérelem URL- `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

 Ez a szabályzat is használható, ha egy emberi és/vagy böngészőt mobilbarát URL-címet kell lesz átalakítva a webszolgáltatás által várt URL-cím formátumra alakítja. Ez a szabályzat csak akkor érvényesíthetők, ha egy másik URL-cím formátumban, például tiszta URL-címek, REST-alapú URL-címek, felhasználóbarát URL-címek vagy tisztán szerkezeti URL-címek, amely nem tartalmazza a lekérdezési karakterlánc, és inkább csak az elérési útját az erőforrás (SEO-barát URL-címeket is közzéteheti a van szüksége Miután a rendszer és a szolgáltató). Ez gyakran történik esztétikai, használhatóságát és keresőmotor-optimalizálás (SEO) céljából.

> [!NOTE]
>  Csak a lekérdezési karakterlánc paraméterei a csoportházirend használatával adhat hozzá. URL-újraírási nem adhat hozzá további sablon elérési út paramétereihez.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|újraírási – uri|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|sablon|A tényleges web service URL-CÍMÉT bármely lekérdezési karakterlánc paraméterei. Kifejezések használata esetén az egész érték kifejezésnek kell lennie.|Igen|–|
|Másolás páratlan paraméterei|Itt adhatja meg, e lekérdezés a bejövő kérelem nem található meg az eredeti URL-sablon a paramétereket az URL-címhez írja át a sablon által definiált|Nem|true|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő

-   **A házirend-hatókörök:** globális, termék, API-művelet

##  <a name="XSLTransform"></a> Az XSLT-vel XML-átalakítás
 A `Transform XML using an XSLT` házirend vonatkozik egy XSL átalakítása XML kérelem vagy válasz törzsében.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Példa

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|xsl-transform|A gyökérelem.|Igen|
|paraméter|Az átalakítás használt változókat határozhat meg segítségével|Nem|
|XSL: stylesheet|Stíluslap gyökérelem. Minden elemek és attribútumok meghatározott hajtsa végre a standard [XSLT-specifikáció](https://www.w3.org/TR/xslt)|Igen|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő

-   **A házirend-hatókörök:** globális, termék, API-művelet

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következők témaköröket:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)
