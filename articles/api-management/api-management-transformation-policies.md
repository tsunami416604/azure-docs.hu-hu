---
title: Azure API-kezelés átalakítási szabályzatai | Microsoft dokumentumok
description: Ismerje meg az Azure API Managementben használható átalakítási szabályzatokat.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 81b7fb687bb6ef88d1ed436923d0e5ff7561c22b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803245"
---
# <a name="api-management-transformation-policies"></a>API Management-átalakítási szabályzatok
Ez a témakör a következő API Management-házirendek hivatkozási alapként szolgál. A házirendek hozzáadásáról és konfigurálásáról az [API-kezelés házirendjei](https://go.microsoft.com/fwlink/?LinkID=398186)című témakörben talál további információt.

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a>Átalakítási házirendek

-   [A JSON konvertálása XML formátumba](api-management-transformation-policies.md#ConvertJSONtoXML) – A kérelem- vagy választörzs et JSON-ból XML formátumba konvertálja.

-   [XML konvertálása JSON-](api-management-transformation-policies.md#ConvertXMLtoJSON) A kérelem- vagy választörzset XML-ről JSON-ra konvertálja.

-   [Karakterlánc keresése és cseréje a törzsben](api-management-transformation-policies.md#Findandreplacestringinbody) – Megkeresi a kérelem- vagy válaszkarakterlánc-részkarakterláncot, és egy másik részkarakterláncra cseréli.

-   [Maszk URL-címek a tartalomban](api-management-transformation-policies.md#MaskURLSContent) - Újraírja (maszkok) linkeket a választörzsben, hogy azok pont az egyenértékű linket az átjárón keresztül.

-   [Háttérszolgáltatás beállítása](api-management-transformation-policies.md#SetBackendService) – módosítja a háttérszolgáltatás egy bejövő kérelemhez.

-   [Készlet beállítása](api-management-transformation-policies.md#SetBody) – Beállítja az üzenet törzsét a bejövő és kimenő kérelmekhez.

-   [HTTP-fejléc beállítása](api-management-transformation-policies.md#SetHTTPheader) – Értéket rendel egy meglévő válasz- és/vagy kérelemfejléchez, vagy új válasz- és/vagy kérelemfejlécet ad hozzá.

-   [Lekérdezési karakterlánc paraméter beállítása](api-management-transformation-policies.md#SetQueryStringParameter) – Hozzáadja, lecseréli vagy törli a kérelem lekérdezési karakterlánc-paraméterét.

-   [URL-cím újraírása](api-management-transformation-policies.md#RewriteURL) – A kérelem URL-címét a nyilvános űrlapról a webszolgáltatás által elvárt űrlapra konvertálja.

-   [XML átalakítása XSLT használatával](api-management-transformation-policies.md#XSLTransform) – XSL-átalakításalkalmazása XML-re a kérelem- vagy választörzsben.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a>JSON konvertálása XML-fájllá
 A `json-to-xml` házirend egy kérés- vagy választörzset JSON-ból XML-vé alakít át.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|json-xml|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|apply|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> - mindig - mindig alkalmazza a konverziót.<br />- content-type-json - konvertálni csak akkor, ha a válasz Content-Type fejléc jelenlétére utal JSON.|Igen|N/A|
|consider-accept-fejléc|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> - true - alkalmazza a konvertálást, ha az XML-t kéri az Elfogadás kérés fejlécében.<br />- hamis -mindig alkalmazza átalakítás.|Nem|igaz|
|elemzés-dátum|Ha `false` a megadott értékek egyszerűen másolva átalakítás során|Nem|igaz|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, hibás

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a>XML konvertálása JSON-fájllá
 A `xml-to-json` házirend egy kérelem- vagy választörzset XML-ről JSON-ra konvertál. Ez a házirend az API-k csak XML-alapú háttér-webszolgáltatások alapján történő modernizálására használható.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|xml-to-json|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|Fajta|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> - javascript-barát - az átalakított JSON egy formája barátságos JavaScript fejlesztők.<br />- közvetlen - az átalakított JSON tükrözi az eredeti XML dokumentum szerkezetét.|Igen|N/A|
|apply|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> - mindig - konvertálni mindig.<br />- content-type-xml - konvertálni csak akkor, ha a válasz Content-Type fejléc e-kód jelenlétét jelzi.|Igen|N/A|
|consider-accept-fejléc|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> - true - alkalmazza a konverziót, ha a JSON kérésre az Elfogadás fejlécben van szükség.<br />- hamis -mindig alkalmazza átalakítás.|Nem|igaz|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, hibás

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a>Karakterlánc keresése és cseréje a testben
 A `find-and-replace` házirend megkeresi a kérelem vagy válasz részkarakterláncát, és lecseréli egy másik részkarakterláncra.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Példa

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|keresés és csere|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|honnan|A keresendő sztring.|Igen|N/A|
|erre:|A behelyettesítendő sztring. A keresési karakterlánc eltávolításához adjon meg egy nulla hosszúságú helyettesítő karakterláncot.|Igen|N/A|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a>URL-címek maszkolása a tartalomban
 A `redirect-content-urls` házirend újraírja (maszkok) hivatkozásokat a választörzsben, hogy azok pont az egyenértékű kapcsolat az átjárón keresztül. Használja a kimenő szakaszban újraírni válasz törzs énekek, hogy azok pont az átjáróra. Ellenkező hatás érdekében használja a bejövő szakaszban.

> [!NOTE]
>  Ez a házirend nem módosítja `Location` a fejlécértékeket, például a fejléceket. A fejlécértékek módosításához használja a [set-header](api-management-transformation-policies.md#SetHTTPheader) házirendet.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<redirect-content-urls />
```

### <a name="example"></a>Példa

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|átirányítás-tartalom-url-ek|Gyökérelem.|Igen|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="set-backend-service"></a><a name="SetBackendService"></a>Háttérszolgáltatás beállítása
 A `set-backend-service` házirend segítségével átirányíthatja a bejövő kérelmet egy másik háttérvégpontra, mint az adott művelet API-beállításaiban megadott. Ez a házirend a bejövő kérelem háttérszolgáltatás alapCÍM-címét a házirendben megadott url-címre módosítja.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<set-backend-service base-url="base URL of the backend service" />
```

vagy

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Háttérentitások felügyeleti [API-n](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) és [PowerShellen](https://www.powershellgallery.com/packages?q=apimanagement)keresztül kezelhetők.

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
Ebben a példában a készlet-háttérszolgáltatás-szabályzat az API-ban megadotttól eltérő háttérszolgáltatásnak átadott verzióérték alapján továbbítja a kérelmeket.

Kezdetben a háttérszolgáltatás alap URL-címe származik az API-beállításokat. Így a `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` kérelem `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/` URL-címe lesz, ahol a háttérszolgáltatás URL-címe az API-beállításokban megadott.

A [<\> a házirend-utasítás](api-management-advanced-policies.md#choose) alkalmazásakor a háttérszolgáltatás alapURL-címe ismét `http://contoso.com/api/8.2` `http://contoso.com/api/9.1`változhat a verziókérés lekérdezési paraméterének értékétől függően. Ha például az `"2013-15"` érték az utolsó `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`kérelem URL-címe lesz.

Ha a kérelem további átalakítása szükséges, más [átalakítási szabályzatok](api-management-transformation-policies.md#TransformationPolicies) is használhatók. Például a verziólekérdezési paraméter eltávolításához most, hogy a kérés egy verzióspecifikus háttérrendszerhez van irányítva, a [Query string paraméterházirend beállítása](api-management-transformation-policies.md#SetQueryStringParameter) a most redundáns verzióattribútum eltávolítására használható.

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
Ebben a példában a házirend a kérelmet egy szolgáltatásháló háttérrendszerbe irányítja, a userId lekérdezési karakterláncot használva partíciókulcsként, és a partíció elsődleges replikáját használva.

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|set-backend-szolgáltatás|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|alap-url|Új háttérszolgáltatás alapURL-címe.|Az `base-url` egyik, vagy `backend-id` jelen kell lennie.|N/A|
|háttér-azonosító|A háttérprogram azonosítója az útvonalhoz. (Háttérentitások [api-n](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) és [PowerShellen](https://www.powershellgallery.com/packages?q=apimanagement)keresztül kezelhetők.)|Az `base-url` egyik, vagy `backend-id` jelen kell lennie.|N/A|
|sf-partíció-kulcs|Csak akkor alkalmazható, ha a háttérszolgáltatás egy Service Fabric-szolgáltatás, és "háttér-id" használatával van megadva. Egy adott partíció feloldására szolgál a névfeloldási szolgáltatásból.|Nem|N/A|
|sf-replika-típus|Csak akkor alkalmazható, ha a háttérszolgáltatás egy Service Fabric-szolgáltatás, és "háttér-id" használatával van megadva. Szabályozza, hogy a kérelem kell-e a partíció elsődleges vagy másodlagos replikáját. |Nem|N/A|
|sf-resolve-állapot|Csak akkor alkalmazható, ha a háttérszolgáltatás egy Service Fabric szolgáltatás. Állapot azonosító ha a hívás a Service Fabric-háttérrendszer új megoldással kell ismételni.|Nem|N/A|
|sf-szolgáltatás-példány-név|Csak akkor alkalmazható, ha a háttérszolgáltatás egy Service Fabric szolgáltatás. Lehetővé teszi a szolgáltatáspéldányok futásidőben való módosítását. |Nem|N/A|
|sf-hallgató-név|Csak akkor alkalmazható, ha a háttérszolgáltatás egy Service Fabric-szolgáltatás, és "háttér-id" használatával van megadva. A Service Fabric megbízható szolgáltatások lehetővé teszi, hogy több figyelők egy szolgáltatásban. Ez az attribútum egy adott figyelő kiválasztásához használható, ha egy háttér-megbízható szolgáltatás egynél több figyelővel rendelkezik. Ha ez az attribútum nincs megadva, az API Management megkísérli a figyelő név nélküli használatát. A név nélküli figyelő jellemző a megbízható szolgáltatások, amelyek csak egy figyelő. |Nem|N/A|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, háttér-

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="set-body"></a><a name="SetBody"></a>Test beállítása
 A `set-body` házirend segítségével beállíthatja a bejövő és kimenő kérelmek üzenettörzsét. Az üzenettörzs eléréséhez használhatja `context.Request.Body` a `context.Response.Body`tulajdonságot vagy a , attól függően, hogy a házirend a bejövő vagy a kimenő szakaszban van-e.

> [!IMPORTANT]
>  Vegye figyelembe, hogy alapértelmezés szerint `context.Request.Body` az `context.Response.Body`üzenet törzsének elérésekor az eredeti üzenettörzs elvész, és a törzs nek vissza kell térnie a kifejezésbe. A törzs tartalmának megőrzéséhez állítsa a `preserveContent` paramétert `true` az üzenet elérésekor. Ha `preserveContent` a `true` kifejezés egy másik törzsre van beállítva, és a kifejezés egy másik törzset ad vissza, a visszaküldött törzset használja a rendszer.
>
>  Kérjük, vegye figyelembe az `set-body` alábbi szempontokat a szabályzat használata során.
>
> - Ha a `set-body` házirendet egy új vagy frissített törzs visszaküldésére `preserveContent` használja, nem kell beállítania, `true` mert kifejezetten adja meg az új törzstartalmat.
>   -   A bejövő folyamat választartalmának megőrzése nincs értelme, mert még nincs válasz.
>   -   A kimenő folyamat ban egy kérelem tartalmának megőrzése nincs értelme, mert a kérelem már elküldve a háttérrendszer ezen a ponton.
>   -   Ha ezt a házirendet akkor használja, ha nincs üzenettörzs, például egy bejövő GET-ben, kivétel jelenik meg.

 További információt `context.Request.Body`a környezet `context.Response.Body` [változó](api-management-policy-expressions.md#ContextVariables) `IMessage` tábla területén található , és a szakaszban talál.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Példák

#### <a name="literal-text-example"></a>Példa konstans szövegre

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Példa a törzs karakterláncként való elérésére. Vegye figyelembe, hogy megőrizzük az eredeti kérelem törzsét, hogy később hozzáférhessünk a folyamatban.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Példa a törzs JObject-ként való elérésére. Vegye figyelembe, hogy mivel nem foglalja le az eredeti kérelem törzse, a folyamat későbbi elérése kivételt eredményez.

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

#### <a name="filter-response-based-on-product"></a>Termék alapján adott válasz szűrése
 Ez a példa bemutatja, hogyan hajthatja végre a tartalomszűrést az adatelemek eltávolításával a háttérszolgáltatástól kapott válaszból a `Starter` termék használatakor. A szabályzat konfigurálásának és használatának bemutatását lásd: [Cloud Cover Episode 177: További API-kezelési funkciók Vlad Vinogradsky-val](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és gyors előretekerés 34:30-ig. Kezdje 31:50-kor a demóhoz használt [The Dark Sky Forecast API](https://developer.forecast.io/) áttekintésének megtekintéséhez.

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

### <a name="using-liquid-templates-with-set-body"></a>Folyékony sablonok használata beállított törzsgel
A `set-body` házirend konfigurálható úgy, hogy a [Liquid](https://shopify.github.io/liquid/basics/introduction/) templating nyelvet használja egy kérelem vagy válasz törzsének átalakításához. Ez nagyon hatékony lehet, ha teljesen át kell alakítania az üzenet formátumát.

> [!IMPORTANT]
> A `set-body` házirendben használt Liquid megvalósítása "C# módban" van konfigurálva. Ez különösen fontos, ha olyan dolgokat csinál, mint a szűrés. Például egy dátumszűrő használatához Pascal burkolat és C# dátumformázás szükséges, pl.:
>
> {{body.foo.startDateTime| Dátum:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Annak érdekében, hogy a Liquid sablon használatával megfelelően kötődhet egy XML-törzshöz, a `set-header` házirend segítségével állítsa be a Content-Type függvényt alkalmazás/xml, szöveg/xml (vagy bármely +xml végződésű szövegre); JSON-törzs esetén alkalmazás/json, szöveg/json (vagy +json végződésű bármely típus) kell, hogy legyen.

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>A JSON átalakítása SOAP-ra folyékony sablon használatával
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

#### <a name="transform-json-using-a-liquid-template"></a>A JSON átalakítása folyékony sablonnal
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|beállított test|Gyökérelem. A szövegtörzset vagy a szövegtörzset visszaadó kifejezéseket tartalmazza.|Igen|

### <a name="properties"></a>Tulajdonságok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|sablon|A beállított törzsházirend által futtatott templating mód módosítására szolgál. Jelenleg az egyetlen támogatott érték:<br /><br />- folyadék - a beállított karosszéria politika fogja használni a folyékony templating motor |Nem||

A kéréssel és a válaszmal kapcsolatos információk eléréséhez a Liquid sablon a következő tulajdonságokkal rendelkező környezeti objektumokhoz köthető: <br />
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
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttér-háttér

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a>HTTP-fejléc beállítása
 A `set-header` házirend értéket rendel egy meglévő válasz- és/vagy kérelemfejléchez, vagy új válasz- és/vagy kérelemfejlécet ad hozzá.

 HTTP-fejlécek listáját szúrja be egy HTTP-üzenetbe. Bejövő folyamatba helyezve ez a házirend beállítja a célszolgáltatásnak átadott kérelem HTTP-fejléceit. Kimenő folyamatba helyezve ez a házirend beállítja az átjáró ügyféljének küldött válasz HTTP-fejléceit.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Példák

#### <a name="example---adding-header-override-existing"></a>Példa - fejléc hozzáadása, meglévők felülírása

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Példa - fejléc eltávolítása

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Környezeti információk továbbítása a háttérszolgáltatásnak
 Ez a példa bemutatja, hogyan alkalmazhat házirendet az API szintjén a háttér-információk szolgáltatása a háttérszolgáltatás. A szabályzat konfigurálásának és használatának bemutatását lásd: [Cloud Cover Episode 177: További API-kezelési funkciók Vlad Vinogradsky-val](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és gyors előretekerés 10:30-ig. 12:10-kor van egy bemutató hívásegy művelet a fejlesztői portálon, ahol láthatja a szabályzat ot a munka.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 További információt a [Házirend-kifejezések](api-management-policy-expressions.md) és [a Környezeti változó című témakörben talál.](api-management-policy-expressions.md#ContextVariables)

> [!NOTE]
> A fejléc több értéke egy CSV-karakterlánchoz van összefűzve, például:`headerName: value1,value2,value3`
>
> A kivételek közé tartoznak a szabványos fejlécek, amelyek értékei:
> - vesszőt (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - tartalmazhat nak`Cookie`dátumot ( , `Set-Cookie`, , `Warning`),
> - tartalmazza a`Date`dátumot `If-Unmodified-Since` `Last-Modified`( `Retry-After`, `Expires` `If-Modified-Since`, , , , .
>
> Ezek a kivételek esetén a több fejlécértékek nem lesznek összefűzve egy karakterláncba, és külön fejlécként lesznek átadhatók, például:`User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|set-fejléc|Gyökérelem.|Igen|
|érték|Megadja a beállítandó fejléc értékét. Több azonos nevű fejléc esetén `value` adjon hozzá további elemeket.|Nem|

### <a name="properties"></a>Tulajdonságok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|létezik-akció|Itt adható meg, hogy milyen műveletet kell végrehajtani, ha a fejléc már meg van adva. Ennek az attribútumnak az alábbi értékek egyikével kell rendelkeznie.<br /><br /> - felülbírálás - felülírja a meglévő fejléc értékét.<br />- skip - nem helyettesíti a meglévő fejlécértéket.<br />- hozzáfűzése - hozzáfűzi az értéket a meglévő fejlécértékhez.<br />- delete - eltávolítja a fejlécet a kérelemből.<br /><br /> Ha úgy `override` van beállítva, hogy több, azonos nevű bejegyzést állítson be, a fejléc az összes bejegyzés nek megfelelően lesz beállítva (amely többször is megjelenik); csak a felsorolt értékek lesznek beállítva az eredményben.|Nem|Felülbírálja|
|név|Megadja a beállítandó fejléc nevét.|Igen|N/A|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a>Lekérdezési karakterlánc paraméter ének beállítása
 A `set-query-parameter` házirend hozzáadja, lecseréli vagy törli a kérelem lekérdezési karakterlánc-paraméterét. A háttérszolgáltatás által elvárt lekérdezési paraméterek átadhatók, amelyek nem kötelezőek, vagy soha nem szerepelnek a kérelemben.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

#### <a name="example"></a>Példa

```xml

<set-query-parameter name="api-key" exists-action="skip">
  <value>12345678901</value>
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Környezeti információk továbbítása a háttérszolgáltatásnak
 Ez a példa bemutatja, hogyan alkalmazhat házirendet az API szintjén a háttér-információk szolgáltatása a háttérszolgáltatás. A szabályzat konfigurálásának és használatának bemutatását lásd: [Cloud Cover Episode 177: További API-kezelési funkciók Vlad Vinogradsky-val](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és gyors előretekerés 10:30-ig. 12:10-kor van egy bemutató hívásegy művelet a fejlesztői portálon, ahol láthatja a szabályzat ot a munka.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 További információt a [Házirend-kifejezések](api-management-policy-expressions.md) és [a Környezeti változó című témakörben talál.](api-management-policy-expressions.md#ContextVariables)

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|set-query-paraméter|Gyökérelem.|Igen|
|érték|Megadja a beállítandó lekérdezési paraméter értékét. Több azonos nevű lekérdezési paraméter `value` esetén adjon hozzá további elemeket.|Igen|

### <a name="properties"></a>Tulajdonságok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|létezik-akció|Megadja az elvégzendő műveletet, ha a lekérdezési paraméter már meg van adva. Ennek az attribútumnak az alábbi értékek egyikével kell rendelkeznie.<br /><br /> - felülbírálás - a meglévő paraméter értékét helyettesíti.<br />- skip - nem helyettesíti a meglévő lekérdezési paraméter értékét.<br />- hozzáfűzése - hozzáfűzi az értéket a meglévő lekérdezési paraméter értékéhez.<br />- delete - eltávolítja a lekérdezési paramétert a kérelemből.<br /><br /> Ha több `override` azonos nevű bejegyzést szeretne besorozni, a lekérdezési paraméter az összes bejegyzés nek megfelelően lesz beállítva (amely többször is megjelenik); csak a felsorolt értékek lesznek beállítva az eredményben.|Nem|Felülbírálja|
|név|Megadja a beállítandó lekérdezési paraméter nevét.|Igen|N/A|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, háttér-

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="rewrite-url"></a><a name="RewriteURL"></a>URL-cím újraírása
 A `rewrite-uri` házirend a kérelem URL-címét a nyilvános űrlapról a webszolgáltatás által várt űrlapra konvertálja, ahogy az a következő példában látható.

- Nyilvános URL -`http://api.example.com/storenumber/ordernumber`

- Kérelem URL-címe -`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Ez a házirend akkor használható, ha egy emberi és/vagy böngészőbarát URL-címet a webszolgáltatás által várt URL-formátumba kell átalakítani. Ezt a házirendet csak akkor kell alkalmazni, ha alternatív URL-formátumot tesz nek ki, például tiszta URL-eket, RESTful URL-eket, felhasználóbarát URL-eket vagy seo-barát URL-eket, amelyek tisztán strukturális URL-ek, amelyek nem tartalmaznak lekérdezési karakterláncot, és ehelyett csak az erőforrás elérési útját tartalmazzák (a séma és a hatóság után). Ez gyakran történik esztétikai, használhatósági, vagy a kereső optimalizálás (SEO) célokra.

> [!NOTE]
>  Lekérdezési karakterlánc-paramétereket csak a házirend használatával adhat hozzá. Az újraíró URL-címhez nem adhat hozzá további sablonelérési paramétereket.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|újraírás-uri|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Kötelező|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|sablon|A tényleges webszolgáltatás URL-címe bármely lekérdezési karakterlánc paraméterével. Kifejezések használataesetén a teljes értéknek kifejezésnek kell lennie.|Igen|N/A|
|copy-nem egyező-params|Itt adható meg, hogy a bejövő kérelemben lévő lekérdezési paraméterek ne legyenek-e hozzáadva az újraírási sablon által meghatározott URL-címhez.|Nem|igaz|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a>XML átalakítása XSLT használatával
 A `Transform XML using an XSLT` házirend egy XSL-átalakítást alkalmaz az XML-re a kérelem- vagy választörzsben.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|xsl-transzformáció|Gyökérelem.|Igen|
|parameter|Az átalakításban használt változók definiálására szolgál|Nem|
|xsl:stíluslap|Gyökérstíluslap elem. Minden elem és attribútum, amelyet a megadott, kövesse a szabványos [XSLT specifikációt](https://www.w3.org/TR/xslt)|Igen|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következők témaköröket:

+ [Szabályzatok az API Managementben](api-management-howto-policies.md)
+ [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
+ [Házirendminták](policy-samples.md)
