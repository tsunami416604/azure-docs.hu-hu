---
title: Speciális házirendek az Azure API Management |} Microsoft Docs
description: További tudnivalók az Azure API Management használható speciális házirendeket.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: dcd4d28341e766baeaf6d581a69312cc33a0282a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30233826"
---
# <a name="api-management-advanced-policies"></a>Házirendek speciális API Management
Ez a témakör egy hivatkozást a következő API-felügyeleti házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [házirendek az API Management](http://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AdvancedPolicies"></a> Speciális házirendek

-   [Folyamat szabályozása](api-management-advanced-policies.md#choose) - feltételesen alkalmazza a házirend-utasításoknál logikai értékelése eredményei alapján [kifejezések](api-management-policy-expressions.md).
-   [Kérés továbbítása a](#ForwardRequest) -továbbítja a kérést a háttérszolgáltatáshoz.
-   [Korlátozza a feldolgozási](#LimitConcurrency) -megakadályozza, hogy a házirendek egyszerre legfeljebb a megadott számú kérelem végrehajtása közé.
-   [Az Event Hubs napló](#log-to-eventhub) -üzeneteket küld egy Eseményközpontot, határozzák meg a tranzakciónaplókat tartalmazó entitás a megadott formátumban.
-   [Válasz mock](#mock-response) -megszakításainak-feldolgozási folyamat végrehajtása és közvetlenül a hívó mocked választ ad vissza.
-   [Próbálja meg újra](#Retry) -újrapróbálkozások zárt házirend utasítás végrehajtása, ha, és amíg a feltétel nem teljesül. Végrehajtási ismételje meg a megadott időközönként, és a legfeljebb a megadott újrapróbálkozások száma.
-   [Térjen vissza a válasz](#ReturnResponse) -megszakításainak-feldolgozási folyamat végrehajtása és a közvetlenül a hívó adott választ.
-   [Egyirányú kérés küldése](#SendOneWayRequest) -kérést küld a megadott URL-cím a válaszra való várakozás nélkül.
-   [Kérés küldése](#SendRequest) -kérést küld a megadott URL-cím.
-   [Állítsa be a HTTP-proxy](#SetHttpProxy) -lehetővé teszi a továbbított kérelmek egy HTTP-proxyn keresztül.
-   [Kérelem módszert állítja be](#SetRequestMethod) -módosíthatja a kérelem HTTP-metódust.
-   [Állítsa be. állapotkód:](#SetStatus) – a HTTP-állapotkód: a megadott értékre változik.
-   [Új érték](api-management-advanced-policies.md#set-variable) -továbbra is fennáll az elnevezett értéket [környezetben](api-management-policy-expressions.md#ContextVariables) változó későbbi eléréshez.
-   [Nyomkövetési](#Trace) -be egy karakterláncot ad a [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimeneti.
-   [Várjon, amíg](#Wait) -megvárja-e a zárójelek között [küldési kérelmek](api-management-advanced-policies.md#SendRequest), [lehet értéket kiolvasni a gyorsítótár](api-management-caching-policies.md#GetFromCacheByKey), vagy [folyamatot szabályozhatja](api-management-advanced-policies.md#choose) házirendeket befejeződjön, mielőtt továbblép.

##  <a name="choose"></a> Folyamata
 A `choose` házirend utasítások logikai kifejezésen, hasonló az if-majd-más vagy kapcsoló értékelése eredménye alapján a programozási nyelven összeállításához zárt házirend vonatkozik.

###  <a name="ChoosePolicyStatement"></a> Házirendutasítás

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

 A vezérlő adatfolyam házirend tartalmaznia kell legalább egy `<when/>` elemet. A `<otherwise/>` elem nem kötelező megadni. A feltételek `<when/>` elemek kiértékelése sorrendben történik, illetve megjelenésük belül a házirend. Az első határolt házirend nyilatkozatuk `<when/>` feltétel attribútum egyenlő elem `true` lépnek érvénybe. Házirendek határolt a `<otherwise/>` elem, ha van ilyen, alkalmazza a rendszer, ha az összes, a `<when/>` elem feltétel attribútumok `false`.

### <a name="examples"></a>Példák

####  <a name="ChooseExample"></a> Példa
 A következő példa bemutatja egy [set-változó](api-management-advanced-policies.md#set-variable) házirend és a két vezérlése áramlási szabályzatokkal.

 A változó szabály beállítása a bejövő szakaszban, és létrehoz egy `isMobile` logikai [környezetben](api-management-policy-expressions.md#ContextVariables) változó értéke igaz, ha a `User-Agent` kérelem fejléc tartalmazza a szöveg `iPad` vagy `iPhone`.

 Az első ellenőrzési folyamata házirend is a bejövő szakaszban, és feltételesen vonatkozik két egyik [állítsa be a lekérdezési karakterlánc paraméter](api-management-transformation-policies.md#SetQueryStringParameter) értékének attól a `isMobile` környezeti változó.

 A második ellenőrzési folyamata házirend kimenő szakaszában és feltételesen alkalmazza a [XML konvertálása JSON formátumúvá](api-management-transformation-policies.md#ConvertXMLtoJSON) házirend amikor `isMobile` értéke `true`.

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>Példa
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

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|Válassza a|A gyökérelem.|Igen|
|mikor|Az állapotot, amelyet használni a `if` vagy `ifelse` részei a `choose` házirend. Ha a `choose` -házirendben engedélyezve van több `when` szakaszok, azok egymás után értékeli ki. Egyszer a `condition` , hogy amikor egy elem értékelődik ki `true`, további `when` feltételek értékelésének.|Igen|
|Ellenkező esetben|A házirend-részlet használható, ha egyike sem tartalmazza a `when` feltételek értékelhetők `true`.|Nem|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|
|---------------|-----------------|--------------|
|feltétel = "logikai kifejezés &#124; logikai állandó"|A logikai kifejezés vagy állandó értékeli ki, ha a tartalmazó `when` házirend-utasítás kiértékelése történik.|Igen|

###  <a name="ChooseUsage"></a> Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="ForwardRequest"></a> Kérés továbbítása
 A `forward-request` házirend továbbítja a bejövő kérelem által a háttérszolgáltatáshoz a kérelemben megadott [környezet](api-management-policy-expressions.md#ContextVariables). A háttérkiszolgáló URL-címe van megadva az API-t [beállítások](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) és módosítható a [be háttérszolgáltatás](api-management-transformation-policies.md) házirend.

> [!NOTE]
>  A csoportházirend-eredményhez eltávolítása a kérelem nem lesznek továbbítva a háttér szolgáltatás és a kimenő szakaszban házirendek kiértékelése azonnal a bejövő szakaszban házirendek sikeres befejezését követően.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false"/>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa
 A következő API-szintű szabályzat továbbítja a háttérszolgáltatáshoz 60 másodperces időtúllépési időköz minden kérelemhez.

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Példa
 Ez a művelet szintű szabályzat használja a `base` elem a háttérrendszer házirend örökli a szülő API-szintű hatókör.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Példa
 Ez a művelet szintű szabályzat explicit módon továbbítja a háttérszolgáltatáshoz 120 időtúllépés az összes kérelmet, és nem örökli a szülő API-szintű háttér házirend.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120"/>
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Példa
 Ez a művelet szintű szabályzat nem továbbítja a kérelem által a háttérszolgáltatáshoz.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|forward-request|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|timeout="integer"|Nem sikerül a időkorlátja, másodpercben. a háttérszolgáltatás hívása előtt.|Nem|300 másodperc|
|follow-redirects="true &#124; false"|Megadja, hogy a háttérszolgáltatáshoz átirányítása követi az átjáró vagy visszaérkezik a hívóhoz.|Nem|false|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** háttér
-   **Házirend hatókörök:** minden hatókör

##  <a name="LimitConcurrency"></a> A feldolgozási korlátot
 A `limit-concurrency` házirend megakadályozza, hogy a zárt házirendek egy adott időpontban legfeljebb a megadott számú kérelem végrehajtása. Alapján meghaladó ezt a számot, új kérelem sikertelen lesz azonnal 429-es jelű túl sok kérelem állapotkóddal.

###  <a name="LimitConcurrencyStatement"></a> Házirendutasítás

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa
 A következő példa bemutatja, hogyan legyen korlátozva egy környezeti változó értéke alapján háttérkiszolgálón továbbított kérelmek számát jelenti.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|limit-concurrency|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|--------------|
|kulcs|Egy karakterláncot. A kifejezés engedélyezett. Meghatározza azt a feldolgozási hatókört. Megoszthatók több házirendet.|Igen|–|
|max-count|Egy egész számot. Megadja, hogy mely adja meg a házirend kérelmek maximális számát.|Igen|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="log-to-eventhub"></a> Az Event Hubs napló
 A `log-to-eventhub` házirend üzeneteket küld a megadott formátumban határozzák meg a tranzakciónaplókat tartalmazó entitás Eseményközpontban. A név azt jelenti, mert a házirend kijelölt kérés vagy válasz környezeti adatok mentése online vagy kapcsolat nélküli elemzéshez használható.

> [!NOTE]
>  Részletes útmutató, az event hubs és a naplózási események konfigurálása, lásd: [útmutató az Azure Event Hubs API Management naplózása](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Házirendutasítás

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Példa
 Bármilyen karakterlánc értékeként használható Event Hubs be kell jelentkeznie. Ebben a példában a dátum és idő, telepítési szolgáltatás neve, kérelemazonosító, IP-cím és az összes bejövő hívás művelet nevét a rendszer naplózza az event hubs naplózó regisztrálva a `contoso-logger` azonosítója.

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|napló-eventhub|A gyökérelem. Ez az elem értéke bejelentkezni az eseményközpont karakterlánc.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|
|---------------|-----------------|--------------|
|logger-id|Az API Management szolgáltatásban regisztrált a naplózó azonosítója.|Igen|
|partition-id|Meghatározza a partíció, ahol az üzenetek küldése történik az index.|Választható. Ez az attribútum nem használható, ha `partition-key` szolgál.|
|a partíciókulcs|Meghatározza azt az értéket a partíció-hozzárendelés üzenettémakörbe küldött üzeneteket.|Választható. Ez az attribútum nem használható, ha `partition-id` szolgál.|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="mock-response"></a> Utánzatait válasz
A `mock-response`, a névként azt jelenti, API-k és műveletek mock szolgál. Normál feldolgozási sor végrehajtása megszakítja, és a hívó mocked választ ad vissza. A házirend mindig megpróbálja térjen vissza a legmagasabb hűség válaszokat. Válasz tartalom példák, amikor a rendelkezésre álló szívesebben. Minta válaszok létrehozott sémák, a sémák találhatók, és többek között nem. Ha a példák és sémák nem találhatók, nem tartalmú válaszok is megjelennek.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>Példák

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|mock-response|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|--------------|
|állapotkód-:|Válasz állapotkódja határozza meg, és válassza ki a megfelelő példa vagy séma használatával.|Nem|200|
|content-type|Itt adhatja meg `Content-Type` válasz állomásfejléc-érték, és válassza ki a megfelelő példa vagy séma.|Nem|None|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő, hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="Retry"></a> Próbálja meg újra
 A `retry` házirend egyszer végrehajtja az alárendelt házirendeket, és majd újrapróbálkozik a végrehajtás, amíg az újra gombra `condition` válik `false` , vagy próbálkozzon újra `count` kimerült.

### <a name="policy-statement"></a>Házirendutasítás

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>Példa
 A következő példában a kérelmet továbbító a rendszer ismét megkísérli legfeljebb tízszeresének exponenciális újrapróbálkozási algoritmust használ. Mivel `first-fast-retry` beállítva hamis értékre, az összes újrapróbálkozások vonatkoznak az exponenciális újrapróbálkozási algoritmust.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request />
</retry>

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|retry|A gyökérelem. Előfordulhat, hogy bármely egyéb házirendek gyermekelemeinek tartalmazni.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|feltétel|Logikai szövegkonstans vagy [kifejezés](api-management-policy-expressions.md) adja meg, ha az újrapróbálkozások le kell állítani (`false`) vagy továbbra is (`true`).|Igen|–|
|darab|Egy pozitív szám, amely megadja az újrapróbálkozások maximális számát kísérlet.|Igen|–|
|interval|Próbálja meg egy pozitív szám, a között az újrapróbálkozási időköz megadása másodpercben.|Igen|–|
|max-interval|Egy pozitív szám, a maximális megadása másodpercben Várjon, amíg az újrapróbálkozási kísérletek közötti időközt. Az exponenciális újrapróbálkozási algoritmust végrehajtásához szolgál.|Nem|–|
|delta|Egy pozitív szám, a várakozási időközt növekmény megadása másodpercben. A lineáris és exponenciális újrapróbálkozási algoritmust végrehajtására szolgál.|Nem|–|
|első fast-újrapróbálkozási|Ha beállítása `true` , az első újrapróbálkozások azonnal megtörténik.|Nem|`false`|

> [!NOTE]
>  Ha csak a `interval` meg van adva, **rögzített** időköz újrapróbálkozások hajtja végre.
> Ha csak a `interval` és `delta` vannak megadva, a **lineáris** időköz újrapróbálkozási algoritmust használnak, ahol az újrapróbálkozások közötti várakozási idő kiszámítása a következő képlet - szerint `interval + (count - 1)*delta`.
> Ha a `interval`, `max-interval` és `delta` vannak megadva, **exponenciális** időköz újrapróbálkozási algoritmust alkalmazza, ha a várakozási idő a próbálkozások közötti inkább exponenciálisan értéke `interval` számára az érték `max-interval` megfelelően a következő forumula - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Vegye figyelembe, hogy ez a házirend öröklik a gyermek házirend használattal kapcsolatos korlátozásokat.

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="ReturnResponse"></a> Válasz visszaadása
 A `return-response` házirend megszakítja a feldolgozási sor végrehajtása, és egy alapértelmezett vagy egyéni reagálva a hívó adja vissza. Alapértelmezett válasz `200 OK` nem szervezethez. Egyéni válasz egy környezeti változó vagy házirend utasítások keresztül adható meg. Mindkét biztosított, ha a válasz a környezeti változó belül található módosul a a házirend-utasításoknál előtt alatt visszaérkezik a hívóhoz.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>Példa

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|visszatérési-válasz|A gyökérelem.|Igen|
|set-fejléc|A [set-fejléc](api-management-transformation-policies.md#SetHTTPheader) házirend-utasítás.|Nem|
|set-szervezet|A [set-törzsének](api-management-transformation-policies.md#SetBody) házirend-utasítás.|Nem|
|set-status|A [állapotának beállítása](api-management-advanced-policies.md#SetStatus) házirend-utasítás.|Nem|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|
|---------------|-----------------|--------------|
|response-variable-name|A környezeti változó neve hivatkozni, például felsőbb rétegbeli [küldési-kérelmek](api-management-advanced-policies.md#SendRequest) házirend, és úgy, hogy egy `Response` objektum|Választható.|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="SendOneWayRequest"></a> Egyirányú kérés küldése
 A `send-one-way-request` házirend a megadott kérést küld a megadott URL-cím a válaszra való várakozás nélkül.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>Példa
 Ez a minta-házirend használatával példáját mutatja be a `send-one-way-request` egy üzenetet küldeni a Slack Csevegés helyiségben, ha a HTTP válaszkódot nagyobb vagy egyenlő 500 házirend. Ez a minta további információkért lásd: [használata az Azure API Management szolgáltatás a külső services](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|send-one-way-request|A gyökérelem.|Igen|
|url|A kérelem URL-CÍMÉT.|Nincs if mód = másolási; Ellenkező esetben igen.|
|metódus|A kérelem HTTP-metódust.|Nincs if mód = másolási; Ellenkező esetben igen.|
|header|Kérelem fejléce. Használjon több több kérelem fejlécek.|Nem|
|törzs|A kérelem törzse.|Nem|
|hitelesítési tanúsítvány|[Az ügyfél-hitelesítéshez használni kívánt tanúsítványt](api-management-authentication-policies.md#ClientCertificate)|Nem|


### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|mode="string"|Meghatározza, hogy ez egy új kérelmet vagy a jelenlegi kérelem egy példányát. Kimenő módban mód = másolása nem sikerült a kérés törzsében.|Nem|Új|
|név|A neve a fejléc kell beállítani.|Igen|–|
|létezik-e művelet|Megadja, milyen műveletet hajtson végre a fejléc már meg van adva. Ez az attribútum a következő értékek egyikének kell lennie.<br /><br /> -felülbírálás - lecseréli a meglévő fejléc értékének.<br />-skip – nem helyettesíti a meglévő-fejléc értékét.<br />-hozzáfűzése - az érték hozzáfűzi a meglévő állomásfejléc-érték.<br />-törlés - eltávolítja a fejlécet a kérelemből.<br /><br /> Ha beállítása `override` történő besorolásakor ugyanazzal a névvel több bejegyzést eredményez az összes bejegyzés (amely jelennek meg több alkalommal) megfelelően történő beállítása fejléc; csak a listában szereplő értékek be lesznek állítva az eredményt.|Nem|override|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="SendRequest"></a> Kérés küldése
 A `send-request` házirend a megadott kérést küld a megadott URL-cím nem tovább, mint a beállított időtúllépési értéket vár.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>Példa
 Ez a példa bemutatja a hivatkozás győződhet token az engedélyezési-kiszolgálóval. Ez a minta további információkért lásd: [használata az Azure API Management szolgáltatás a külső services](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|küldési-kérelmek|A gyökérelem.|Igen|
|url|A kérelem URL-CÍMÉT.|Nincs if mód = másolási; Ellenkező esetben igen.|
|metódus|A kérelem HTTP-metódust.|Nincs if mód = másolási; Ellenkező esetben igen.|
|header|Kérelem fejléce. Használjon több több kérelem fejlécek.|Nem|
|törzs|A kérelem törzse.|Nem|
|hitelesítési tanúsítvány|[Az ügyfél-hitelesítéshez használni kívánt tanúsítványt](api-management-authentication-policies.md#ClientCertificate)|Nem|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|mode="string"|Meghatározza, hogy ez egy új kérelmet vagy a jelenlegi kérelem egy példányát. Kimenő módban mód = másolása nem sikerült a kérés törzsében.|Nem|Új|
|response-variable-name="string"|Ha nincs jelen, `context.Response` szolgál.|Nem|–|
|timeout="integer"|Nem sikerül a időkorlátja, másodpercben. az URL-cím hívása előtt.|Nem|60|
|ignore-error|Ha igaz, és hiba történt a kérelem eredményezi:<br /><br /> -Ha a válasz-változó-név lett megadva, null értéket tartalmaz.<br />-Ha a válasz-változó-neve nincs megadva, a környezetben. Kérés nem fog frissülni.|Nem|false|
|név|A neve a fejléc kell beállítani.|Igen|–|
|létezik-e művelet|Megadja, milyen műveletet hajtson végre a fejléc már meg van adva. Ez az attribútum a következő értékek egyikének kell lennie.<br /><br /> -felülbírálás - lecseréli a meglévő fejléc értékének.<br />-skip – nem helyettesíti a meglévő-fejléc értékét.<br />-hozzáfűzése - az érték hozzáfűzi a meglévő állomásfejléc-érték.<br />-törlés - eltávolítja a fejlécet a kérelemből.<br /><br /> Ha beállítása `override` történő besorolásakor ugyanazzal a névvel több bejegyzést eredményez az összes bejegyzés (amely jelennek meg több alkalommal) megfelelően történő beállítása fejléc; csak a listában szereplő értékek be lesznek állítva az eredményt.|Nem|override|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="SetHttpProxy"></a> HTTP-proxy beállítása
 A `proxy` házirendje lehetővé teszi a kérelmek háttérkiszolgálókon egy HTTP-proxyn keresztül továbbítja. Csak a HTTP (nem HTTPS) az átjáró és a proxy között támogatott. Alapszintű, és csak az NTLM-hitelesítés.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Példa
Vegye figyelembe a használatát [tulajdonságok](api-management-howto-properties.md) tartozó felhasználónevet és jelszót a bizalmas adatok tárolása a házirend-dokumentum elkerülése érdekében.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|Proxy|Legfelső szintű elem|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|url="string"|Proxykiszolgáló URL-cím formájában http://host:port.|Igen|–|
|username="string"|A proxy-nal való hitelesítéshez használt felhasználónevet.|Nem|–|
|password="string"|A proxy-hitelesítéshez használandó jelszó.|Nem|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő

-   **Házirend hatókörök:** minden hatókör

##  <a name="SetRequestMethod"></a> Set kérés metódus
 A `set-method` házirend lehetővé teszi a HTTP-kérési metódust egy kérelem módosítását.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Példa
 Ez a minta a házirend által használt a `set-method` házirend üzenetet küld a Slack Csevegés helyiségben, ha a HTTP válaszkódot nagyobb vagy egyenlő 500 példáját mutatja be. Ez a minta további információkért lásd: [használata az Azure API Management szolgáltatás a külső services](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|set-method|A gyökérelem. Az elem értékét adja meg a HTTP-metódus.|Igen|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="SetStatus"></a> Set-állapotkód:
 A `set-status` házirend értékűre állítja be a HTTP-állapotkód: a megadott érték.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Példa
 Ez a példa bemutatja, hogyan 401-es választ ad vissza, abban az esetben, ha az engedélyezési jogkivonat érvénytelen. További információkért lásd: [külső szolgáltatások az Azure API Management szolgáltatás használata](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|set-status|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|code="integer"|A HTTP-állapotkód való visszatéréshez.|Igen|–|
|reason="string"|Az az oka az állapotkód: visszaadó leírását.|Igen|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** kimenő, háttér,-hiba
-   **Házirend hatókörök:** minden hatókör

##  <a name="set-variable"></a> Új érték
 A `set-variable` házirend deklarál egy [környezetben](api-management-policy-expressions.md#ContextVariables) változó, és egy megadott értéket rendeli az [kifejezés](api-management-policy-expressions.md) vagy egy szöveges karakterlánc. Ha a kifejezés olyan szövegkonstanst tartalmaz karakterláncra lesz konvertálva, és az érték típusa lesz `System.String`.

###  <a name="set-variablePolicyStatement"></a> Házirendutasítás

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

###  <a name="set-variableExample"></a> Példa
 A következő példa bemutatja a változó szabály beállítása a bejövő szakaszban. A set változó házirend hoz létre egy `isMobile` logikai [környezetben](api-management-policy-expressions.md#ContextVariables) változó értéke igaz, ha a `User-Agent` kérelem fejléc tartalmazza a szöveg `iPad` vagy `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|set-variable|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|
|---------------|-----------------|--------------|
|név|Annak a változónak a nevét.|Igen|
|érték|A változó értékét. Ez lehet egy kifejezést, vagy konstansérték.|Igen|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba
-   **Házirend hatókörök:** minden hatókör

###  <a name="set-variableAllowedTypes"></a> Engedélyezett típusokkal
 A használt kifejezések a `set-variable` házirendet a következő alapvető típusok valamelyikét kell visszaadnia.

-   System.Boolean
-   System.SByte
-   System.Byte
-   System.UInt16
-   System.UInt32
-   System.UInt64
-   System.Int16
-   System.Int32
-   System.Int64
-   System.Decimal
-   System.Single
-   System.Double
-   System.Guid
-   System.String
-   System.Char
-   System.DateTime
-   System.TimeSpan
-   System.Byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   System.Decimal?
-   System.Single?
-   System.Double?
-   System.Guid?
-   System.String?
-   System.Char?
-   System.DateTime?

##  <a name="Trace"></a> Nyomkövetési
 A `trace` házirend hozzáadja a karakterlánc a [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimeneti. A házirend hajtja végre, csak ha nyomkövetés akkor váltódik ki, azaz `Ocp-Apim-Trace` fejléc jelen, és állítsa be a `true` és `Ocp-Apim-Subscription-Key` fejléc szerepel, valamint a rendszergazdai fiókhoz társított érvényes kulcs.

### <a name="policy-statement"></a>Házirendutasítás

```xml

<trace source="arbitrary string literal"/>
    <!-- string expression or literal -->
</trace>

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|Nyomkövetési|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|forrás|A literál karakterlánc kifejező a trace viewer, és adja meg az üzenet forrása.|Igen|–|

### <a name="usage"></a>Használat
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba

-   **Házirend hatókörök:** minden hatókör

##  <a name="Wait"></a> várj
 A `wait` házirend hajt végre annak közvetlenül alárendelt házirendjei párhuzamosan, és megvárja-e az összes vagy annak befejezését, mielőtt ő maga befejeződne közvetlenül alárendelt házirendek egyikét. A várakozási házirend lehetnek azonnali gyermek házirendjeit [küldési kérelmek](api-management-advanced-policies.md#SendRequest), [lehet értéket kiolvasni a gyorsítótár](api-management-caching-policies.md#GetFromCacheByKey), és [folyamatot szabályozhatja](api-management-advanced-policies.md#choose) házirendek.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Példa
 A következő példa kétféle `choose` házirendek közvetlenül alárendelt házirendek, mint a `wait` házirend. Ezek mindegyikének `choose` házirendek hajt végre párhuzamosan. Minden egyes `choose` házirend megpróbálja gyorsítótárazott értéke. Ha a gyorsítótár-tévesztései, háttérszolgáltatás nevezik adni az értékét. Ebben a példában a `wait` házirend végrehajtásához összes közvetlenül alárendelt házirendjeit végrehajtani, mert a `for` attribútum van beállítva `all`.   Ebben a példában a környezeti változók (`execute-branch-one`, `value-one`, `execute-branch-two`, és `value-two`) Ez a példa házirend kereteit deklarált.

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>Elemek

|Elem|Leírás|Szükséges|
|-------------|-----------------|--------------|
|várj|A gyökérelem. Tartalmazhat, mint a gyermekelemek csak `send-request`, `cache-lookup-value`, és `choose` házirendek.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Szükséges|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|a következőhöz:|Meghatározza, hogy a `wait` házirend megvárja-e a minden közvetlenül alárendelt házirendek befejezett vagy egyszerűen lesz. Engedélyezett értékek a következők:<br /><br /> -   `all` -Várjon, amíg befejeződik az összes közvetlenül alárendelt házirendek<br />-a - Várjon, amíg egy-egy közvetlen alárendelt házirend befejezéséhez. Az első közvetlenül alárendelt házirend befejezése után a `wait` házirend befejeződött, és bármely más közvetlenül alárendelt házirendek végrehajtása megszakadt.|Nem|összes|

### <a name="usage"></a>Használat

Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Házirend szakaszok:** bejövő, kimenő háttér
-   **Házirend hatókörök:** minden hatókör

## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:
+ [Az API Management házirendek](api-management-howto-policies.md)
+ [Házirend-kifejezések](api-management-policy-expressions.md)
+ [Házirend-hivatkozás](api-management-policy-reference.md) házirend-utasításoknál és a beállítások teljes listáját
+ [Házirend-minták](policy-samples.md)
