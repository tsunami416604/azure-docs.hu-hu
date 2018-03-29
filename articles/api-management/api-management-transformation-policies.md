---
title: Az Azure API Management-átalakítási csoportházirendek |} Microsoft Docs
description: További tudnivalók az Azure API Management használható átalakítási csoportházirendek.
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
ms.openlocfilehash: 3eb9d6851c30f11980d47d4e48b158217e41995d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="api-management-transformation-policies"></a>Az API Management-átalakítási csoportházirendek
Ez a témakör egy hivatkozást a következő API-felügyeleti házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [házirendek az API Management](http://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a> Átalakítási csoportházirendek

-   [JSON átalakítása XML](api-management-transformation-policies.md#ConvertJSONtoXML) - konvertálja kérés vagy válasz body JSON formátumból az XML.

-   [XML konvertálása JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - konvertálja kérés vagy válasz body az XML-JSON.

-   [Keresés és csere törzsében karakterlánc](api-management-transformation-policies.md#Findandreplacestringinbody) - kérés vagy válasz karakterláncrész keresése, és lecseréli egy másik karakterláncrészletet.

-   [URL-címek maszkolja a tartalom](api-management-transformation-policies.md#MaskURLSContent) -hivatkozások átírja (maszkok) a válaszban szereplő body, hogy azok az átjárón keresztül a megfelelő hivatkozásra mutat.

-   [Állítsa be háttérszolgáltatás](api-management-transformation-policies.md#SetBackendService) -módosítja a háttérszolgáltatáshoz egy bejövő kérelemhez.

-   [Állítsa be a szervezet](api-management-transformation-policies.md#SetBody) -beállítja az üzenettörzs, a bejövő és kimenő kérelmek.

-   [Set HTTP-fejléc](api-management-transformation-policies.md#SetHTTPheader) - hozzárendel egy értéket egy meglévő válasz és/vagy a kérelem fejlécében vagy ad hozzá egy új válasz és/vagy a kérelem fejlécében.

-   [Állítsa be a lekérdezési karakterlánc paraméter](api-management-transformation-policies.md#SetQueryStringParameter) - ad hozzá, értéke váltja fel, vagy törli a kérelem lekérdezési karakterláncot.

-   [URL-cím újraírása](api-management-transformation-policies.md#RewriteURL) – a kérelem URL-címe nyilvános formájukban a várt érték a webszolgáltatás által az űrlap alakítja.

-   [Átalakítás XSLT használatával XML](api-management-transformation-policies.md#XSLTransform) -XSL-átalakítás alkalmazása XML-kérés vagy válasz törzsében.

##  <a name="ConvertJSONtoXML"></a> JSON átalakítása XML
 A `json-to-xml` házirend alakít egy kérés vagy válasz törzsében JSON formátumból XML.

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
|alkalmaz|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> átalakítás - mindig - mindig érvényesek.<br />-tartalom típus-json - konvertálás csak akkor, ha a response Content-Type fejléc JSON jelenlétét jelzi.|Igen|–|
|Vegye figyelembe-elfogadja-fejléc|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> átalakítás - igaz - alkalmazni, amennyiben JSON kérelem Accept fejlécet.<br />-hamis - átalakítás mindig érvényesek.|Nem|true|
|elemzési-dátuma|Ha beállítása `false` dátumértékek egyszerűen kerülnek átalakítás során|Nem|true|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő, hiba

-   **Házirend hatókörök:** globális, termék, API-művelet

##  <a name="ConvertXMLtoJSON"></a> XML konvertálása JSON
 A `xml-to-json` házirend alakít egy kérés vagy válasz törzsének XML-fájljából JSON. Ez a házirend segítségével korszerűsítésére API-kat csak XML-háttérrendszer webszolgáltatások alapján.

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
|xml-to-json|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|típusa|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> -javascript-barát – az átalakított JSON a JavaScript-fejlesztők számára egyszerű űrlap rendelkezik.<br />a konvertált JSON - közvetlen – az eredeti XML-dokumentum struktúra tükrözi.|Igen|–|
|alkalmaz|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> -mindig - átalakítás mindig.<br />-tartalom típus-xml - konvertálás csak akkor, ha a response Content-Type fejléc XML jelenlétét jelzi.|Igen|–|
|Vegye figyelembe-elfogadja-fejléc|Az attribútum a következő értékek egyikére kell beállítani.<br /><br /> átalakítás - igaz - alkalmazni, amennyiben XML Accept fejlécet kérés van szükség.<br />-hamis - átalakítás mindig érvényesek.|Nem|true|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő, hiba

-   **Házirend hatókörök:** globális, termék, API-művelet

##  <a name="Findandreplacestringinbody"></a> Keresés és csere törzsében karakterlánc
 A `find-and-replace` házirend kérés vagy válasz karakterláncrész keresése, és lecseréli egy másik karakterláncrészletet.

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
|keresése és cseréje|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|forrás:|A keresendő karakterláncot.|Igen|–|
|erre:|A behelyettesítendő karakterláncot. Adja meg a nulla hosszúságú helyettesítő karakterláncok eltávolítása a keresési karakterláncot.|Igen|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** globális, termék, API-művelet

##  <a name="MaskURLSContent"></a> A tartalom maszk URL-címek
 A `redirect-content-urls` házirend átírja a válasz törzsében (maszkok) hivatkozásokat úgy, hogy azok az átjárón keresztül a megfelelő hivatkozásra mutat. A kimenő szakaszban használatával újra írási válasz törzsében hivatkozások be az átjáró mutasson. A bejövő szakaszban lévő használhatók az ellenkezője.

> [!NOTE]
>  Ez a házirend nem változik, mint bármely térközkaraktert `Location` fejléceket. A fejléc értékei módosításához használja a [set-fejléc](api-management-transformation-policies.md#SetHTTPheader) házirend.

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
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő

-   **Házirend hatókörök:** globális, termék, API-művelet

##  <a name="SetBackendService"></a> Háttér-szolgáltatás beállítása
 Használja a `set-backend-service` olyan bejövő kérelemre átirányítása egy másik háttér, mint a megadott API-beállítások az adott műveletre vonatkozó házirendet. Ez a házirend a háttérrendszer alap URL-címe a bejövő kérelem a házirendben megadott módosításait.

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
Ebben a példában a készlet háttér szolgáltatás házirend továbbítja a kérelmet a verzióértéket, mint a megadott API-ban egy másik háttérszolgáltatás a lekérdezési karakterláncban átadott alapján.

Kezdetben a háttérrendszer szolgáltatás alap URL-címet a API-beállítások származik. Ezért a kérelem URL-címe `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` válik `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` ahol `http://contoso.com/api/10.4/` a háttér szolgáltatás URL-cím megadott API-beállítások.

Ha a [< válasszon\> ](api-management-advanced-policies.md#choose) alkalmazott házirend-utasítás a háttérrendszer szolgáltatás alap URL-címet módosíthatja újra közül csak az egyiket `http://contoso.com/api/8.2` vagy `http://contoso.com/api/9.1`, attól függően, a verzió kérelem lekérdezési paraméter értéke. Ha az érték például `"2013-15"` a végső kérelem URL-cím lesz `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Ha a kérelem átalakítása-e további kívánt, más [átalakítási csoportházirendek](api-management-transformation-policies.md#TransformationPolicies) is használható. Ahhoz például, hogy távolítsa el a version lekérdezési paramétert, most, hogy a kérelem verziót adott backend routedevent irányítása a [állítsa be a lekérdezési karakterlánc paraméter](api-management-transformation-policies.md#SetQueryStringParameter) házirend segítségével távolítsa el a most redundáns version attribútum.

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
Ebben a példában a házirend továbbítja a kérelmet a service fabric háttér, használja a userId lekérdezési karakterláncot, mint a partíciós kulcs, és a partíció az elsődleges replika használatával.

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|set-backend-service|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|base-url|Új háttér alap URL-címe.|Nem|–|
|háttér-azonosító|A háttér-hez irányítandó azonosítója.|Nem|–|
|sf-partition-key|Csak érvényes a háttérkiszolgálón a Service Fabric-szolgáltatás, és a "háttér-id" van megadva. Egy adott partícióra a névfeloldási szolgáltatást használ.|Nem|–|
|sf-replica-type|Csak érvényes a háttérkiszolgálón a Service Fabric-szolgáltatás, és a "háttér-id" van megadva. Szabályozza, hogy a kérelem el kell küldeni egy partíció elsődleges vagy másodlagos replikája. |Nem|–|
|sf-resolve-condition|Csak érvényes a Service Fabric-szolgáltatás esetén a háttér. A feltétel azonosítja a Service Fabric háttér hívása új megoldás meg kell ismételni van-e.|Nem|–|
|sf-service-instance-name|Csak érvényes a Service Fabric-szolgáltatás esetén a háttér. Lehetővé teszi, hogy futásidőben szolgáltatáspéldány módosítása. |Nem|–|
|sf-listener-name|Csak érvényes a háttérkiszolgálón a Service Fabric-szolgáltatás, és a "háttér-id" van megadva. Service Fabric Reliable Services lehetővé teszi egy szolgáltatás több figyelők létrehozására. Ez az attribútum egy adott hallgató válassza ki, ha a háttérkiszolgáló megbízható szolgáltatás több figyelő szolgál. Ha ez az attribútum nincs megadva, az API Management név nélküli figyelő használatát kísérli meg. Figyelő név nélküli része jellemzően Reliable Services, amelyek csak egy figyelőt. |Nem|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, háttér

-   **Házirend hatókörök:** globális, termék, API-művelet

##  <a name="SetBody"></a> Set törzse
 Használja a `set-body` házirend beállítása az üzenettörzs, a bejövő és kimenő kérelmek. Az üzenettörzs használható eléréséhez a `context.Request.Body` tulajdonság vagy a `context.Response.Body`, attól függően, hogy a házirend van-e a bejövő vagy kimenő szakaszban.

> [!IMPORTANT]
>  Vegye figyelembe, hogy az üzenet elérésekor alapértelmezés szerint body használatával `context.Request.Body` vagy `context.Response.Body`, az eredeti üzenettörzs elvész, és a szervezet vissza a kifejezésben vissza kell állítani. A szervezet tartalom megőrzése érdekében állítsa a `preserveContent` paramétert `true` az üzenet való hozzáféréskor. Ha `preserveContent` értéke `true` és egy másik szervezet által visszaadott a kifejezést, a visszaadott törzs szolgál.
>
>  Használata esetén vegye figyelembe a következőket kell figyelembe venni a `set-body` házirend.
>
>  -   Használatakor a `set-body` házirend számára, nem kell telepítenie egy új vagy frissített törzs visszatérési `preserveContent` való `true` mert kifejezetten megadja az új üzenettörzs tartalmát.
> -   A bejövő feldolgozási válasz tartalmának megőrzi értelmetlen, mert még nincs válasz.
> -   Megőrzi a kérést a kimenő folyamat tartalmának értelmetlen mert elküldte a kérelmet már van a háttérrendszerhez ezen a ponton.
> -   Ha ezt a házirendet használja, amikor nincs az üzenet törzse, például egy bejövő GET, a rendszer kivételt hoz létre.

 További információkért lásd: a `context.Request.Body`, `context.Response.Body`, és a `IMessage` szakaszában a [környezeti változó](api-management-policy-expressions.md#ContextVariables) tábla.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Példák

#### <a name="literal-text-example"></a>Szöveg – példa

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Példa a szervezet karakterláncként elérése. Vegye figyelembe, hogy az eredeti kérés törzsében, hogy a Microsoft-e hozzáférési engedélye a folyamat későbbi szakaszában vannak megőrzi.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accesing-it-later-in-the-pipeline-will-result-in-an-exception"></a>A példa egy JObject a szervezettől elérése. Vegye figyelembe, hogy azt nem az eredeti kérés törzsében, a folyamat későbbi szakaszában okoz kivétel egypéldányú lefoglalja mivel.

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

#### <a name="filter-response-based-on-product"></a>A termék szűrő választ
 A példa bemutatja, hogyan hajthat végre a tartalom alapján történő szűrés adatelemek eltávolítása a válasz érkezett a háttérszolgáltatás használata esetén a `Starter` termék. A házirenddel és konfigurálása a bemutatója, lásd: [felhő fedik le a epizód 177: több API a felügyeleti funkcióinak Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 34:30 előretekerés. Megtekinthet egy áttekintést 31:50 kezdjék [a sötét égbolt előrejelzési API](https://developer.forecast.io/) ebben a bemutatóban használt.

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

### <a name="using-liquid-templates-with-set-body"></a>A készlet szövegtörzzsel folyékony sablonokkal
A `set-body` házirend beállítható úgy, hogy használja a [folyékony](https://shopify.github.io/liquid/basics/introduction/) templating nyelv transfom olyan kérésre vagy válaszra törzsét. Ez nagyon hatékony, ha az üzenet formátuma teljesen átalakításához szüksége lehet.

> [!IMPORTANT]
> Folyadék megvalósítása szerepel a `set-body` házirend úgy van konfigurálva, a "C# módban". Ez különösen fontos műveleteket, mint a szűrés során. Tegyük fel, dátum szűrő használata szükséges Pascal és nagybetűhasználatnak, valamint C# dátum formázás, például:
>
> {{body.foo.startDateTime| Dátum: "yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Ahhoz, hogy megfelelően egy XML-törzsére folyékony sablonnal kötést létrehozni, használja a `set-header` házirend beállítása a Content-Type vagy application/xml, text/xml (vagy bármely típusú végződő + xml), egy JSON-törzsére, kell lennie az application/json, text/json (vagy bármilyen végződő + JSON-ban).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>JSON átalakítása SOAP folyékony sablon használatával
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

#### <a name="tranform-json-using-a-liquid-template"></a>Tranform JSON folyékony sablon használatával
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
|set-szervezet|A gyökérelem. A szöveg vagy egy törzs visszaadó kifejezések tartalmaz.|Igen|

### <a name="properties"></a>Tulajdonságok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|sablon|Segítségével módosíthatja, amely a szervezet szabály beállítása templating módját. Jelenleg az egyetlen támogatott érték:<br /><br />-folyékony - törzs-szabály beállítása fogja használni a folyékony templating motor |Nem|folyadék|

A kérelem és válasz adataihoz való hozzáférést, a folyékony sablon köthető egy környezeti objektum a következő tulajdonságokkal: <br />
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
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér

-   **Házirend hatókörök:** globális, termék, API-művelet

##  <a name="SetHTTPheader"></a> HTTP-fejléc beállítása
 A `set-header` házirend hozzárendel egy értéket egy meglévő válasz és/vagy a kérelem fejlécében vagy ad hozzá egy új válasz és/vagy a kérelem fejlécében.

 A HTTP-fejlécek listáját szúr be a HTTP üzenet. Ha egy bejövő folyamat, ez a házirend a HTTP-fejléceket, a kérés átadódik a célként megadott szolgáltatás az állítja be. Ha egy kimenő folyamat, ez a házirend a HTTP-fejléceket, a válasz küldi el az átjáró ügyfél állítja be.

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

#### <a name="forward-context-information-to-the-backend-service"></a>Környezet adatainak által a háttérszolgáltatáshoz továbbítsa
 Ez a példa bemutatja, hogyan alkalmazni a házirendet, adja meg a környezet adatainak által a háttérszolgáltatáshoz API szinten. A házirenddel és konfigurálása a bemutatója, lásd: [felhő fedik le a epizód 177: több API a felügyeleti funkcióinak Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és előretekerés 10:30. 12:10 nincs egy bemutatója művelet hívása a fejlesztői portálra, ahol láthatja a házirendet, a munkahelyi hálózatban.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 További információkért lásd: [házirend-kifejezések](api-management-policy-expressions.md) és [környezeti változó](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|set-fejléc|A gyökérelem.|Igen|
|érték|Meghatározza azt az értéket, a fejléc kell beállítani. Ezzel a névvel több fejléc fel további `value` elemek.|Igen|

### <a name="properties"></a>Tulajdonságok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|létezik-e művelet|Megadja, milyen műveletet hajtson végre a fejléc már meg van adva. Ez az attribútum a következő értékek egyikének kell lennie.<br /><br /> -felülbírálás - lecseréli a meglévő fejléc értékének.<br />-skip – nem helyettesíti a meglévő-fejléc értékét.<br />-hozzáfűzése - az érték hozzáfűzi a meglévő állomásfejléc-érték.<br />-törlés - eltávolítja a fejlécet a kérelemből.<br /><br /> Ha beállítása `override` történő besorolásakor ugyanazzal a névvel több bejegyzést eredményez az összes bejegyzés (amely jelennek meg több alkalommal) megfelelően történő beállítása fejléc; csak a listában szereplő értékek be lesznek állítva az eredményt.|Nem|override|
|név|A fejlécben állítható nevét adja meg.|Igen|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** globális, termék, API-művelet

##  <a name="SetQueryStringParameter"></a> Lekérdezési karakterlánc-paraméter beállítása
 A `set-query-parameter` házirendnek, cserél értékét, vagy a törlések kérelem lekérdezési karakterláncot. Felelt meg a lekérdezés által a háttérszolgáltatáshoz nem kötelező várt paraméter használható, vagy soha nem szerepelnek a kérelmet.

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

#### <a name="forward-context-information-to-the-backend-service"></a>Környezet adatainak által a háttérszolgáltatáshoz továbbítsa
 Ez a példa bemutatja, hogyan alkalmazni a házirendet, adja meg a környezet adatainak által a háttérszolgáltatáshoz API szinten. A házirenddel és konfigurálása a bemutatója, lásd: [felhő fedik le a epizód 177: több API a felügyeleti funkcióinak Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és előretekerés 10:30. 12:10 nincs egy bemutatója művelet hívása a fejlesztői portálra, ahol láthatja a házirendet, a munkahelyi hálózatban.

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
|érték|Meghatározza azt az értéket, a lekérdezési paraméter kell beállítani. Ezzel a névvel több lekérdezésparamétereket fel további `value` elemek.|Igen|

### <a name="properties"></a>Tulajdonságok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|létezik-e művelet|Megadja, milyen műveletet hajtson végre a következő lekérdezésparaméter már meg van adva. Ez az attribútum a következő értékek egyikének kell lennie.<br /><br /> -felülbírálás - lecseréli a meglévő paraméter értékét.<br />-skip - függvény nem cseréli le a meglévő lekérdezési paraméter értéke.<br />-hozzáfűzése - az érték hozzáfűzi a meglévő lekérdezési paraméter értéke.<br />-törlés - eltávolítja a következő lekérdezésparaméter a kérelmet.<br /><br /> Ha beállítása `override` történő besorolásakor ugyanazzal a névvel több bejegyzés a következő lekérdezésparaméter összes bejegyzés (amely jelennek meg több alkalommal) megfelelően történő beállítása eredményez; csak a listában szereplő értékek be lesznek állítva az eredményt.|Nem|override|
|név|Megadja annak a nevét, a lekérdezési paramétert kell beállítani.|Igen|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, háttér

-   **Házirend hatókörök:** globális, termék, API-művelet

##  <a name="RewriteURL"></a> URL-cím újraírása
 A `rewrite-uri` házirend konvertálja a kérelem URL-CÍMÉT a nyilvános űrlapból az űrlapot, a webszolgáltatás által várt a következő példában látható módon.

-   Nyilvános URL- `http://api.example.com/storenumber/ordernumber`

-   Kérelem URL- `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

 Ez a házirend használhatja emberi és/vagy a böngésző-barát URL-címet kell lennie alakítja át a webszolgáltatás által várt URL-cím formátumú. Ez a házirend csak kell alkalmazni, amikor az ilyen tiszta URL-címeket, a RESTful URL-címek, a felhasználóbarát URL-címek vagy a Keresőmotor-barát URL-címeket tisztán strukturális URL-címeket, nem tartalmazhat lekérdezési karakterláncot, és ehelyett tartalmaznak az csak az elérési útját az erőforrás (például egy másik URL-formátum Miután a rendszer és a szolgáltató). Ez gyakran történik esztétikai, a használhatóság és a keresőmotor (keresőmotor-Optimalizáláshoz) optimalizálási céllal.

> [!NOTE]
>  Csak a házirenddel lekérdezési karakterlánc paramétereket adhat hozzá. Extra Sablonparaméterek elérési útja nem adható hozzá az átírást URL-címben.

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
|Módosítsa úgy-uri|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|sablon|A tényleges webes szolgáltatás URL-cím elé a lekérdezési karakterlánc paramétereket. Kifejezések használata esetén a teljes érték kifejezésnek kell lennie.|Igen|–|
|Másolás páratlan számú paraméterei|Megadja, hogy nincs jelen az eredeti URL-sablonban a bejövő kérelemben szereplő lekérdezési paraméterek kerülnek újbóli írása sablonban megadott URL-|Nem|true|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő

-   **Házirend hatókörök:** termék, API-művelet

##  <a name="XSLTransform"></a> Az XSLT-vel XML átalakítása
 A `Transform XML using an XSLT` házirend XSL-átalakítás alkalmazása XML-kérés vagy válasz törzsében.

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
|paraméter|Adja meg a transzformáció bemeneti használt változókat használatával|Nem|
|XSL: stylesheet|Stíluslap gyökérelem. Minden elemek és attribútumok vannak meghatározva hajtsa végre a szabványos [XSLT-specifikációja](http://www.w3.org/TR/xslt)|Igen|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő

-   **Házirend hatókörök:** globális, termék, API-művelet

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következők témaköröket:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [Házirend-hivatkozás](api-management-policy-reference.md) házirend-utasításoknál és a beállítások teljes listáját
+ [Házirend-minták](policy-samples.md)
