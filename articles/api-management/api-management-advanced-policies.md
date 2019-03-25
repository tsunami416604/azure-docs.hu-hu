---
title: Az Azure API Management speciális szabályzatok |} A Microsoft Docs
description: További információ a használható az Azure API Management speciális házirendeket.
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
ms.openlocfilehash: 9d3bc50e1578704de029d53c0b1eaa21e74182cf
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401919"
---
# <a name="api-management-advanced-policies"></a>Az API Management speciális szabályzatok

Ez a témakör egy hivatkozást kínál a következő az API Management házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [az API Management házirendek](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AdvancedPolicies"></a> Speciális szabályzatok

-   [Átvitelvezérlés](api-management-advanced-policies.md#choose) – feltételesen érvényes logikai érték kiértékelésének eredménye alapján a házirend-utasítások [kifejezések](api-management-policy-expressions.md).
-   [Kérés továbbítása](#ForwardRequest) -továbbítja a kérést a háttérszolgáltatáshoz.
-   [Korlátozza az egyidejűség](#LimitConcurrency) -megakadályozza, hogy egyszerre több, mint a megadott számú kérelem által végrehajtása az szabályzatok idézőjelek között.
-   [Naplózás az Eseményközpontba](#log-to-eventhub) -üzeneteket küld egy Eseményközpontba egy naplózó entitás által meghatározott a megadott formátumban.
-   [Válaszok utánzása](#mock-response) -megszakításainak folyamat-végrehajtás és közvetlenül a hívó számára utánzott válaszokat adjanak vissza.
-   [Ismételje meg](#Retry) -újrapróbálkozik a mellékelt házirend-utasítások végrehajtása, ha, és amíg a feltétel nem teljesül. Végrehajtási fog a megadott időközönként ismételje meg, és legfeljebb a megadott újrapróbálkozások száma.
-   [Választ adja vissza](#ReturnResponse) -megszakításainak folyamat-végrehajtás és a közvetlenül a hívó adott választ adja vissza.
-   [Az egyirányú kérést küldhet](#SendOneWayRequest) -kérést küld a megadott URL-cím válaszra való várakozás nélkül.
-   [Kérés küldése a](#SendRequest) -kérést küld a megadott URL-cím.
-   [Állítsa be a HTTP-proxy](#SetHttpProxy) – lehetővé teszi, hogy a HTTP-proxyn keresztül továbbított átirányíthatja a kéréseket.
-   [Állítsa be a kérelmi metódust](#SetRequestMethod) -kérelem HTTP-metódus módosíthatja.
-   [Állítsa be az állapotkód](#SetStatus) – HTTP-állapotkód módosítja a megadott érték.
-   [Változó beállítása](api-management-advanced-policies.md#set-variable) -továbbra is fennáll egy értéket egy elnevezett [környezet](api-management-policy-expressions.md#ContextVariables) változó későbbi eléréshez.
-   [Nyomkövetési](#Trace) -hozzáad egy karakterláncot, a [API Inspectorral](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimeneti.
-   [Várjon](#Wait) -vár az idézőjelek között [küldési kérelmek](api-management-advanced-policies.md#SendRequest), [érték lekérése a gyorsítótárból](api-management-caching-policies.md#GetFromCacheByKey), vagy [átvitelvezérlés](api-management-advanced-policies.md#choose) házirendek befejezését, mielőtt továbblépne.

## <a name="choose"></a> Átvitelvezérlés

A `choose` házirend érvényes zárt házirend utasítások alapján logikai kifejezésen, egy kapcsoló vagy egy if-majd-más hasonló értékelése eredményét programozási nyelven hozhat létre.

### <a name="ChoosePolicyStatement"></a> Házirendutasítás

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

A control flow szabályzatnak tartalmaznia kell legalább egy `<when/>` elemet. A `<otherwise/>` elem nem kötelező. A feltételek `<when/>` elemek belül a házirend a megjelenésük sorrendjében értékeli ki. A házirend-utasítások belül, az első `<when/>` elem az attribútum egyenlő feltétel `true` lépnek érvénybe. Házirendek DataTable függvényből a `<otherwise/>` elem, ha van ilyen, lépnek érvénybe, ha az összes, a `<when/>` elem feltétel attribútumok `false`.

### <a name="examples"></a>Példák

#### <a name="ChooseExample"></a> Példa

A következő példa bemutatja egy [változó beállítása](api-management-advanced-policies.md#set-variable) házirend és a két ellenőrzési folyamat házirendek.

A változó beállított szabályzat, a bejövő szakaszban, és létrehoz egy `isMobile` logikai [környezet](api-management-policy-expressions.md#ContextVariables) változó értéke igaz, ha a `User-Agent` kérés fejlécében a szöveg tartalmazza `iPad` vagy `iPhone`.

Az első ellenőrzési folyamat házirend is a bejövő szakaszban, és feltételesen vonatkozik egy két [állítsa be a lekérdezési sztring paramétereként](api-management-transformation-policies.md#SetQueryStringParameter) függően értékét a `isMobile` környezeti változót.

A második ellenőrzési folyamat szabályzat kimenő szakaszában, és feltételesen alkalmazza a [XML átalakítása JSON-ná](api-management-transformation-policies.md#ConvertXMLtoJSON) házirend amikor `isMobile` értékre van állítva `true`.

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

### <a name="elements"></a>Elemek

| Elem   | Leírás                                                                                                                                                                                                                                                               | Szükséges |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Válassza a    | A gyökérelem.                                                                                                                                                                                                                                                             | Igen      |
| mikor      | Az állapotot, használja a `if` vagy `ifelse` részei a `choose` házirend. Ha a `choose` házirend rendelkezik több `when` szakaszok, azok egymás után értékeli. Egyszer a `condition` egy akkor, ha az elem kiértékelése `true`, nincs további `when` értékeli ki a feltételeket. | Igen      |
| Ellenkező esetben | A szabályzat kódrészletet használja, ha egyike sem tartalmazza a `when` feltétel `true`.                                                                                                                                                                               | Nem       |

### <a name="attributes"></a>Attribútumok

| Attribútum                                              | Leírás                                                                                               | Szükséges |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| feltétel = "logikai kifejezés &#124; logikai állandó" | A logikai kifejezés vagy állandó értékeli ki, mikor a tartalmazó `when` házirendutasítás értékeli ki. | Igen      |

### <a name="ChooseUsage"></a> Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="ForwardRequest"></a> Kérés továbbítása

A `forward-request` a házirend továbbítja a bejövő kérelem a háttérszolgáltatáshoz, a kérelemben megadott [környezet](api-management-policy-expressions.md#ContextVariables). A háttérkiszolgáló URL-címe van megadva az API-ban [beállítások](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) és módosítható a [háttérszolgáltatás beállítása](api-management-transformation-policies.md) házirend.

> [!NOTE]
> A csoportházirend-eredményhez eltávolításával, a háttérkiszolgáló nem továbbítja a kérést a szolgáltatás és a szabályzatok a kimenő szakaszban értékeli ki a azonnal a sikeres telepítést a szabályzatok a bejövő szakaszban.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false" buffer-request-body="true | false" />
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

A következő API-szintű szabályzat továbbítja a háttérszolgáltatáshoz 60 másodperces időtúllépési időköz minden API-kérések.

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

Ez a művelet-szintű szabályzat használja a `base` elem a háttérrendszer szabályzatot örökli a felsőbb szintű API-hatókör.

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

Ez a művelet-szintű szabályzat explicit módon továbbítja a háttérszolgáltatáshoz 120 időtúllépés az összes kérelem, és nem örököl a felsőbb szintű háttérszabályzat API-t.

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

Ez a művelet-szintű szabályzat nem továbbítja a kérelmeket a háttérszolgáltatáshoz.

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

| Elem         | Leírás   | Szükséges |
| --------------- | ------------- | -------- |
| forward-request | A gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum                               | Leírás                                                                                                      | Szükséges | Alapértelmezett     |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------- | ----------- |
| timeout="integer"                       | Az időkorlát másodpercben a háttérszolgáltatás hívása előtt sikertelen lesz. Minimális értéke 0 másodpercet. Maximális érték: 240 másodperc.| Nem       | 240 másodperc |
| follow-redirects="true &#124; false"    | Itt adhatja meg, hogy a háttérszolgáltatásból átirányítja az átjáró követ vagy vissza a hívónak.      | Nem       | false       |
| kéréstörzs puffer = "true &#124; false" | Ha értéke "true" kérelem pufferelve van-e, és fogja használni a [újra](api-management-advanced-policies.md#Retry). | Nem       | false       |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** háttérrendszer
-   **A házirend-hatókörök:** minden hatókör

## <a name="LimitConcurrency"></a> Egyidejűségi korlát

A `limit-concurrency` szabályzat megakadályozza, hogy a karakterláncon belül lévő házirendek bármikor nagyobb, mint a megadott számú kérelem végrehajtása. Követően meghaladja ezt a számot, új kérelem sikertelen lesz azonnal 429 Too Many Requests állapotkód.

### <a name="LimitConcurrencyStatement"></a> Házirendutasítás

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan korlátozhatja a környezeti változók értéke alapján a háttérrendszernek továbbított kérések száma.

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

| Elem           | Leírás   | Szükséges |
| ----------------- | ------------- | -------- |
| határérték-egyidejűség | A gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                        | Szükséges | Alapértelmezett |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| kulcs       | Egy karakterlánc. A kifejezés engedélyezett. Meghatározza azt a párhuzamosság hatókört. Több szabályzat is megoszthatók. | Igen      | –     |
| maximális darabszám | Egész szám. Adja meg a szabályzat engedélyezett kérelmek maximális száma határozza meg.           | Igen      | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="log-to-eventhub"></a> Naplózás az Eseményközpontba

A `log-to-eventhub` házirend a megadott formátumban üzeneteket küld egy eseményközpontba egy naplózó entitás által meghatározott. Ahogy a neve is mutatja, a szabályzat mentése folyamatban van a kijelölt kérések és válaszok környezeti információkat online vagy kapcsolat nélküli elemzéshez szolgál.

> [!NOTE]
> Lépésenkénti útmutató az eseményközpont és a naplózási események konfigurálása, lásd: [az API Management-események naplózása az Azure Event Hubs hogyan](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Házirendutasítás

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Példa

Event hubs szolgáltatás bekerülhet bármilyen karakterlánc használható értéket. Ebben a példában a dátum és idő, üzembe helyezési szolgáltatás neve, kérelem azonosítója, ip-cím és az összes bejövő hívások műveletnév naplózza az event hubs naplózó regisztrálva a `contoso-logger` azonosítója.

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

| Elem         | Leírás                                                                     | Szükséges |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-to-eventhub | A gyökérelem. Ez az elem értéke a karakterláncot az eseményközpont bejelentkezni. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum     | Leírás                                                               | Szükséges                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | A naplózó azonosítóját az API Management szolgáltatás regisztrálva.         | Igen                                                                  |
| partition-id  | Itt adhatja meg, ahol az üzenetek küldése történik a partíció indexét.             | Választható. Ez az attribútum nem használható, ha `partition-key` szolgál. |
| partition-key | Meghatározza azt az értéket partíció-hozzárendelést a küldött üzeneteket. | Választható. Ez az attribútum nem használható, ha `partition-id` szolgál.  |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="mock-response"></a> Válaszok utánzása

A `mock-response`, mint a név azt jelenti, utánzása az API-k és műveletek segítségével. Ez megszakítja a normál folyamat-végrehajtás, és a hívónak utánzott válaszokat adjanak vissza. A szabályzat mindig megpróbálja a legnagyobb pontosságú válaszok visszaadása. Azt a válasz content példákat, ha elérhető legszívesebben. Állít elő a minta válaszokat a sémák, amikor sémák, és példák nem. Példák és sémák nem észlelhető, ha a rendszer válaszoknál, amelyeknél a nem tartalmat adja vissza.

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

| Elem       | Leírás   | Szükséges |
| ------------- | ------------- | -------- |
| helyettem – válasz | A gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum    | Leírás                                                                                           | Szükséges | Alapértelmezett |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| status-code  | Adja meg a válasz állapotkódja, és válassza ki a megfelelő példa vagy séma szolgál.                 | Nem       | 200     |
| content-type | Itt adható meg `Content-Type` válasz fejléc értéke, és válassza ki a megfelelő példa vagy séma szolgál. | Nem       | None    |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="Retry"></a> Próbálkozzon újra

A `retry` házirend annak gyermek házirendek egyszer fut le, és majd újrapróbálkozik a végrehajtása az újrapróbálkozás `condition` válik `false` , vagy próbálkozzon újra `count` , a rendszer.

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

A következő példában tízszer exponenciális újrapróbálkozás algoritmus segítségével akár kérelem továbbítását rendszer. Mivel `first-fast-retry` értéke hamis értékre, minden újrapróbálkozási kísérlet vonatkoznak rá az exponenciális újrapróbálkozás algoritmus.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>Elemek

| Elem | Leírás                                                         | Szükséges |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | A gyökérelem. Bármely egyéb házirendek is tartalmazhat, az alárendelt elemei. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum        | Leírás                                                                                                                                           | Szükséges | Alapértelmezett |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| feltétel        | (Boolean) literál vagy [kifejezés](api-management-policy-expressions.md) megadása, ha az újrapróbálkozás le kell állítani (`false`) vagy a folyamatos (`true`).      | Igen      | –     |
| count            | Egy pozitív szám, az újrapróbálkozások maximális számát megadó sikertelen bejelentkezési kísérletet.                                                                                | Igen      | –     |
| interval         | Próbálja meg egy pozitív szám a várakozási időköz az újrapróbálkozás között megadása másodpercben.                                                                 | Igen      | –     |
| max-interval     | Egy pozitív szám, a maximális megadása másodpercben Várjon, amíg az újrapróbálkozási kísérletek közötti időköz. Exponenciális újrapróbálkozási algoritmusok megvalósításának szolgál. | Nem       | –     |
| delta            | Egy pozitív szám, a várakozási időköz növekmény megadása másodpercben. Az exponenciális, és lineáris újrapróbálkozási algoritmusok megvalósításának szolgál.             | Nem       | –     |
| első gyors – újrapróbálkozás | Ha beállítása `true` , az első újrapróbálkozási kísérlet azonnal megtörténik.                                                                                  | Nem       | `false` |

> [!NOTE]
> Ha csak a `interval` meg van adva, **rögzített** újrapróbálkozások időköze el kell végezni.
> Ha csak a `interval` és `delta` meg van adva, egy **lineáris** időköz újrapróbálkozási algoritmust használja, amennyiben az újrapróbálkozások közötti várakozási idő számítják ki a következő képlet - megfelelően `interval + (count - 1)*delta`.
> Ha a `interval`, `max-interval` és `delta` meg van adva, **exponenciális** időköz újrapróbálkozási algoritmust alkalmazza, ahol a várakozási idő a próbálkozások közötti exponenciálisan értékét a `interval` , az érték `max-interval` megfelelően a következő képlet - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Vegye figyelembe, hogy gyermek házirend használattal kapcsolatos korlátozások örökli ezt a házirendet.

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="ReturnResponse"></a> Visszaadott válasz

A `return-response` házirend megszakítása folyamat-végrehajtás és egy alapértelmezett vagy egyéni válasz a hívó adja vissza. Alapértelmezett válasz `200 OK` nincs a szervezethez. Egyéni válasz egy környezeti változót vagy a szabályzat utasítások segítségével adható meg. Mindkét által biztosított, a válasz tartalmazza a környezeti változó által módosított a házirend-utasítások előtt a hívónak visszaadott.

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

| Elem         | Leírás                                                                               | Szükséges |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| a visszaadandó-válasz | A gyökérelem.                                                                             | Igen      |
| set-fejléc      | A [set-fejléc](api-management-transformation-policies.md#SetHTTPheader) házirendutasítás. | Nem       |
| törzs beállítása        | A [törzs beállítása](api-management-transformation-policies.md#SetBody) házirendutasítás.         | Nem       |
| állapot beállítása      | A [állapotának beállítása](api-management-advanced-policies.md#SetStatus) házirendutasítás.           | Nem       |

### <a name="attributes"></a>Attribútumok

| Attribútum              | Leírás                                                                                                                                                                          | Szükséges  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| válasz-változóhoz-name | A környezeti változó nevére hivatkozik, például egy felsőbb szintű [küldési-kérelmek](api-management-advanced-policies.md#SendRequest) házirend- és tartalmazó egy `Response` objektum | Választható. |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="SendOneWayRequest"></a> Egyirányú kérelem küldésével

A `send-one-way-request` házirend a megadott kérést küld a megadott URL-cím válaszra való várakozás nélkül.

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

Ez a minta-házirend használatával példán látható a `send-one-way-request` házirend üzenet küldése egy Slack csevegőszoba, ha nagyobb vagy egyenlő 500-as HTTP-válaszkódot. Ez a minta további információkért lásd: [az Azure API Management szolgáltatás a külső szolgáltatások használata](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Elem                    | Leírás                                                                                                 | Szükséges                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| send-one-way-request       | A gyökérelem.                                                                                               | Igen                             |
| url                        | A kérelem URL-címe                                                                                     | Ha nincs mód = copy; Ellenkező esetben igen. |
| method                     | A kérelem HTTP-metódust.                                                                            | Ha nincs mód = copy; Ellenkező esetben igen. |
| header                     | Kérelem fejléce. Több elemet is fejléc használata több kérelemfejlécek.                                  | Nem                              |
| törzs                       | A kérelem törzse.                                                                                           | Nem                              |
| hitelesítés – tanúsítvány | [Az ügyfél-hitelesítéshez használandó tanúsítványt](api-management-authentication-policies.md#ClientCertificate) | Nem                              |

### <a name="attributes"></a>Attribútumok

| Attribútum     | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Szükséges | Alapértelmezett  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | Meghatározza, hogy ez egy új kérelmet, vagy egy másolatot a jelenlegi kérelem. A kimenő mód mód = másolása nem sikerült inicializálni a kérelem törzsében.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nem       | Új      |
| név          | A fejléc kell beállítani a nevét adja meg.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Igen      | –      |
| létezik-művelet | Itt adható meg, milyen műveleteket, ha a fejléc már meg van adva. Ez az attribútum a következő értékek egyikét kell rendelkeznie.<br /><br /> -felülbírálás - lecseréli a meglévő fejléc értékét.<br />-skip – nem helyettesíti a meglévő fejléc értéke.<br />-hozzáfűzése - az érték hozzáfűzi a meglévő fejléc értéke.<br />a kérelem - delete - eltávolítja a fejléc.<br /><br /> Ha a beállítása `override` felvétel ugyanazzal a névvel több bejegyzést eredményez az összes bejegyzés (amely lesz látható többször) megfelelően beállítása fejléc; csak a felsorolt értékek jelennek meg az eredményt. | Nem       | felülbírálás |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="SendRequest"></a> Kérelem küldése

A `send-request` házirend a megadott kérést küld a megadott URL-cím nem haladja meg a beállított időtúllépési értéket vár.

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

Ez a példa bemutatja egy hivatkozás ellenőrzése egyik módja jogkivonat egy engedélyezési kiszolgálón. Ez a minta további információkért lásd: [az Azure API Management szolgáltatás a külső szolgáltatások használata](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Elem                    | Leírás                                                                                                 | Szükséges                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| send-request               | A gyökérelem.                                                                                               | Igen                             |
| url                        | A kérelem URL-címe                                                                                     | Ha nincs mód = copy; Ellenkező esetben igen. |
| method                     | A kérelem HTTP-metódust.                                                                            | Ha nincs mód = copy; Ellenkező esetben igen. |
| header                     | Kérelem fejléce. Több elemet is fejléc használata több kérelemfejlécek.                                  | Nem                              |
| törzs                       | A kérelem törzse.                                                                                           | Nem                              |
| hitelesítés – tanúsítvány | [Az ügyfél-hitelesítéshez használandó tanúsítványt](api-management-authentication-policies.md#ClientCertificate) | Nem                              |

### <a name="attributes"></a>Attribútumok

| Attribútum                       | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Szükséges | Alapértelmezett  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | Meghatározza, hogy ez egy új kérelmet, vagy egy másolatot a jelenlegi kérelem. A kimenő mód mód = másolása nem sikerült inicializálni a kérelem törzsében.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nem       | Új      |
| response-variable-name="string" | Környezeti változó, amelyek megkapják a válasz objektum neve. Ha a változó nem létezik, akkor a házirend sikeres végrehajtása után jön létre, és -en keresztül elérhető lesz [ `context.Variable` ](api-management-policy-expressions.md#ContextVariables) gyűjtemény.                                                                                                                                                                                                                                                                                                                          | Igen      | –      |
| timeout="integer"               | Az időkorlát másodpercben az URL-cím hívása előtt sikertelen lesz.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nem       | 60       |
| ignore-error                    | Ha igaz, és hiba történt a kérés eredményeket:<br /><br /> – Ha a válasz-változóhoz-name tartalmazni fog a null érték lett megadva.<br />– Ha a válasz-változó-neve nincs megadva, az összefüggésben. Kérelem nem fog frissülni.                                                                                                                                                                                                                                                                                                                                                                                   | Nem       | false    |
| név                            | A fejléc kell beállítani a nevét adja meg.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Igen      | –      |
| létezik-művelet                   | Itt adható meg, milyen műveleteket, ha a fejléc már meg van adva. Ez az attribútum a következő értékek egyikét kell rendelkeznie.<br /><br /> -felülbírálás - lecseréli a meglévő fejléc értékét.<br />-skip – nem helyettesíti a meglévő fejléc értéke.<br />-hozzáfűzése - az érték hozzáfűzi a meglévő fejléc értéke.<br />a kérelem - delete - eltávolítja a fejléc.<br /><br /> Ha a beállítása `override` felvétel ugyanazzal a névvel több bejegyzést eredményez az összes bejegyzés (amely lesz látható többször) megfelelően beállítása fejléc; csak a felsorolt értékek jelennek meg az eredményt. | Nem       | felülbírálás |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="SetHttpProxy"></a> A HTTP-proxy beállítása

A `proxy` házirend lehetővé teszi a háttérrendszereket HTTP-proxyn keresztül továbbított átirányíthatja a kéréseket. Csak a HTTP (nem HTTPS) az átjáró és a proxy között támogatott. Alapszintű, és csak NTLM-hitelesítés.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Példa

Figyeljük meg [tulajdonságok](api-management-howto-properties.md) értékekként, a felhasználónevet és jelszót, hogy lehetőleg ne tároljon bizalmas adatokat a szabályzat-dokumentumban.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elemek

| Elem | Leírás  | Szükséges |
| ------- | ------------ | -------- |
| Proxy   | Legfelső szintű elem | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum         | Leírás                                            | Szükséges | Alapértelmezett |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | Proxykiszolgáló URL-cím formájában http://host:port.             | Igen      | –     |
| username="string" | A proxy-hitelesítéshez használandó felhasználónév. | Nem       | –     |
| password="string" | A proxy-hitelesítéshez használandó jelszó. | Nem       | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő

-   **A házirend-hatókörök:** minden hatókör

## <a name="SetRequestMethod"></a> Set-kérelmi metódus

A `set-method` házirend lehetővé teszi, hogy egy kérelem HTTP-kérési metódust módosíthatja.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Példa

Ez a minta házirend által használt a `set-method` házirend üzenetet küld egy Slack csevegőszoba, ha a HTTP-válaszkód nem nagyobb, mint 500 példán látható. Ez a minta további információkért lásd: [az Azure API Management szolgáltatás a külső szolgáltatások használata](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Elem    | Leírás                                                       | Szükséges |
| ---------- | ----------------------------------------------------------------- | -------- |
| Set-metódus | A gyökérelem. Az elem értékét adja meg a HTTP-metódus. | Igen      |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="SetStatus"></a> Set-állapotkód:

A `set-status` házirend beállítja a HTTP-állapotkód: a megadott érték.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Példa

Ez a példa bemutatja, hogyan 401-es választ ad vissza, ha a hitelesítési jogkivonat érvénytelen. További információkért lásd: [az Azure API Management szolgáltatás a külső szolgáltatások használata](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

| Elem    | Leírás   | Szükséges |
| ---------- | ------------- | -------- |
| állapot beállítása | A gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum       | Leírás                                                | Szükséges | Alapértelmezett |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="integer"  | A HTTP-állapotkód: való visszatéréshez.                            | Igen      | –     |
| reason="string" | Az az oka az állapotkódot visszaadó leírása. | Igen      | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** kimenő, háttér, – hiba
-   **A házirend-hatókörök:** minden hatókör

## <a name="set-variable"></a> Változó beállítása

A `set-variable` házirend deklarálja a [környezet](api-management-policy-expressions.md#ContextVariables) változó és keresztül megadott értéket rendel egy [kifejezés](api-management-policy-expressions.md) vagy egy szöveges karakterlánc. Ha a kifejezés tartalmaz egy konstans át lesz alakítva egy karakterláncot, és az érték típusa `System.String`.

### <a name="set-variablePolicyStatement"></a> Házirendutasítás

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a> Példa

A következő példa bemutatja a változó szabály beállítása a bejövő szakaszban. A változó beállított szabályzat létrehoz egy `isMobile` logikai [környezet](api-management-policy-expressions.md#ContextVariables) változó értéke igaz, ha a `User-Agent` kérés fejlécében a szöveg tartalmazza `iPad` vagy `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elemek

| Elem      | Leírás   | Szükséges |
| ------------ | ------------- | -------- |
| set-variable | A gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                              | Szükséges |
| --------- | ------------------------------------------------------------------------ | -------- |
| név      | A változó neve.                                                | Igen      |
| érték     | A változó értékét. Ez lehet konstans érték vagy egy kifejezés. | Igen      |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba
-   **A házirend-hatókörök:** minden hatókör

### <a name="set-variableAllowedTypes"></a> Engedélyezett típusokkal

A használt kifejezések a `set-variable` házirendet kell eredményként adnia az alábbi alapvető típusok egyikét.

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

## <a name="Trace"></a> Nyomkövetési

A `trace` házirendet be egy karakterláncot ad hozzá a [API Inspectorral](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimeneti. A szabályzat fogja végrehajtani, csak ha nyomkövetés akkor aktiválódik, azaz `Ocp-Apim-Trace` kérelem fejléce jelen, és állítsa be a `true` és `Ocp-Apim-Subscription-Key` kérelem fejléce jelen, és tárolja a rendszergazdai fiókkal társított érvényes kulcs.

### <a name="policy-statement"></a>Házirendutasítás

```xml

<trace source="arbitrary string literal">
    <!-- string expression or literal -->
</trace>

```

### <a name="elements"></a>Elemek

| Elem | Leírás   | Szükséges |
| ------- | ------------- | -------- |
| Nyomkövetési   | A gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                             | Szükséges | Alapértelmezett |
| --------- | --------------------------------------------------------------------------------------- | -------- | ------- |
| source    | A karakterlánc-literál jelentéssel bíró a trace viewer, és adja meg az üzenet forrása. | Igen      | –     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba

-   **A házirend-hatókörök:** minden hatókör

## <a name="Wait"></a> várj

A `wait` házirend azonnali gyermek házirendjeit végrehajtása párhuzamosan történik, és megvárja, amíg az összes vagy annak befejezését, mielőtt az befejezné közvetlenül alárendelt szabályzatokra. A várakozási házirend lehetnek azonnali gyermek házirendjeit [küldési kérelmek](api-management-advanced-policies.md#SendRequest), [érték lekérése a gyorsítótárból](api-management-caching-policies.md#GetFromCacheByKey), és [átvitelvezérlés](api-management-advanced-policies.md#choose) házirendeket.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Példa

Az alábbi példában kettő `choose` közvetlenül alárendelt szabályzat, szabályzatok a `wait` házirend. Minden egyes `choose` házirendek végrehajtása párhuzamosan történik. Minden egyes `choose` házirend használatával megkísérli lekérni egy gyorsítótárazott értéket. Gyorsítótár-tévesztés esetén háttérszolgáltatás nevezzük, adja meg az értéket. Ebben a példában a `wait` házirend nem fejeződik be, amíg minden hozzá tartozó közvetlenül alárendelt szabályzat végrehajtani, mert a `for` attribútum `all`. Ebben a példában a környezeti változók (`execute-branch-one`, `value-one`, `execute-branch-two`, és `value-two`) deklarált ebben a példában házirend hatókörén kívül esik.

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

| Elem | Leírás                                                                                                   | Szükséges |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| várj    | A gyökérelem. Csak a gyermek elemként tartalmazhat `send-request`, `cache-lookup-value`, és `choose` házirendeket. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                                                                                                                                                                                                                                                                                                                                            | Szükséges | Alapértelmezett |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| időtartam:       | Meghatározza, hogy a `wait` házirend megvárja, amíg az összes közvetlenül alárendelt szabályzatok befejezett vagy egyszerűen az egy. Engedélyezett értékek a következők:<br /><br /> - `all` -minden közvetlenül alárendelt szabályzat befejezéséhez várjon<br />-bármely – amíg bármilyen közvetlenül alárendelt házirend végrehajtásához. Az első közvetlenül alárendelt házirend befejezését követően a `wait` házirend befejeződött, és bármely más közvetlenül alárendelt házirendek végrehajtása megszakadt. | Nem       | összes     |

### <a name="usage"></a>Használat

Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **A házirend-szakaszok:** bejövő, kimenő háttérrendszer
-   **A házirend-hatókörök:** minden hatókör

## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

-   [Az API Management házirendek](api-management-howto-policies.md)
-   [Házirend-kifejezések](api-management-policy-expressions.md)
-   [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
-   [A házirend-minták](policy-samples.md)
