---
title: Az Azure API Management speciális szabályzatai | Microsoft dokumentumok
description: Ismerje meg az Azure API Managementben használható speciális szabályzatokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: c8ef481fe277d6451923da828f0e7473354c24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266168"
---
# <a name="api-management-advanced-policies"></a>API Management – Speciális szabályzatok

Ez a témakör a következő API Management-házirendek hivatkozási alapként szolgál. A házirendek hozzáadásáról és konfigurálásáról az [API-kezelés házirendjei](https://go.microsoft.com/fwlink/?LinkID=398186)című témakörben talál további információt.

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a>Speciális házirendek

-   [Vezérlőfolyamat](api-management-advanced-policies.md#choose) – Feltételesen alkalmazza a logikai kifejezések kiértékelésének eredményein alapuló [házirend-nyilatkozatokat.](api-management-policy-expressions.md)
-   [Kérés továbbítása](#ForwardRequest) – továbbítja a kérelmet a háttérszolgáltatás.
-   [Egyidejűség korlátozása](#LimitConcurrency) – Megakadályozza, hogy a zárt házirendek egyszerre több kérést hajtsanak végre.
-   [Napló az Event Hub -](#log-to-eventhub) üzenetekküldése a megadott formátumban egy Eseményközpont által meghatározott naplózó entitás.
-   [Mock response](#mock-response) - Megszakítja a folyamat végrehajtását, és egy kigúnyolt választ ad vissza közvetlenül a hívónak.
-   [Újrapróbálkozás](#Retry) – A mellékelt házirend-utasítások végrehajtásának újrapróbálkozása, ha és amíg a feltétel nem teljesül. A végrehajtás a megadott időközönként és a megadott újrapróbálkozások számáig ismétlődik.
-   [Válasz-](#ReturnResponse) Megszakítja a folyamat végrehajtását, és a megadott választ közvetlenül a hívónak adja vissza.
-   [Egyirányú kérelem küldése](#SendOneWayRequest) – Kérés küldése a megadott URL-címre anélkül, hogy választ várna.
-   [Küldési kérelem](#SendRequest) – Kérés küldése a megadott URL-címre.
-   [HTTP-proxy beállítása](#SetHttpProxy) – Lehetővé teszi a továbbított kérelmek http-proxyn keresztültörténő továbbítását.
-   [Kérelem metódusának beállítása](#SetRequestMethod) – Lehetővé teszi a kérelem HTTP-metódusának módosítását.
-   [Állapotkód beállítása](#SetStatus) – A HTTP-állapotkódot a megadott értékre módosítja.
-   [Változó beállítása](api-management-advanced-policies.md#set-variable) – Egy névvel ellátott [környezeti](api-management-policy-expressions.md#ContextVariables) változóban lévő érték megőrzése későbbi hozzáférésesetén.
-   [Trace –](#Trace) egyéni nyomkövetéseket ad hozzá az [API-felügyelő](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimenetéhez, az Application Insights telemetriesés diagnosztikai naplók.
-   [Várakozás](#Wait) – A zárt [küldési kérelemre,](api-management-advanced-policies.md#SendRequest) [az érték gyorsítótárból való beolvasása](api-management-caching-policies.md#GetFromCacheByKey)vagy a folytatás előtt végrehajtandó [vezérlőfolyamat-házirendek](api-management-advanced-policies.md#choose) befejezésére vár.

## <a name="control-flow"></a><a name="choose"></a>Vezérlési folyamat

A `choose` házirend a logikai kifejezések értékelésének eredményén alapuló zárt házirend-kifejezéseket alkalmazza, hasonlóan az if-then-else vagy a programming language kapcsolószerkezetéhez.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a>Politikai nyilatkozat

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

A vezérlési folyamat házirendjének legalább egy `<when/>` elemet tartalmaznia kell. Az `<otherwise/>` elem nem kötelező. Az `<when/>` elemek feltételeit a házirenden belüli megjelenésük sorrendjében értékeli ki a rendszer. A program az első elemben, amelynek feltételattribútuma egyenlő, a program az első `<when/>` elemhez tartozó házirend-utasítás(oka)t `true` alkalmazza. Az `<otherwise/>` elemhez tartozó házirendek, ha vannak, akkor `<when/>` lesznek alkalmazva, ha az elem feltételattribútumai `false`a.

### <a name="examples"></a>Példák

#### <a name="example"></a><a name="ChooseExample"></a>Példa

A következő példa egy [beállított változó házirendet](api-management-advanced-policies.md#set-variable) és két vezérlési folyamatházirendet mutat be.

A beállított változóházirend a bejövő szakaszban `isMobile` található, és létrehoz egy logikai [környezeti](api-management-policy-expressions.md#ContextVariables) változót, amely igaz értékre van állítva, ha a `User-Agent` kérelemfejléc szöveget `iPad` tartalmaz, vagy `iPhone`.

Az első vezérlőfolyamat-házirend is a bejövő szakaszban található, és feltételesen alkalmazza a két `isMobile` [Lekérdezési karakterlánc paraméterének](api-management-transformation-policies.md#SetQueryStringParameter) egyikét a környezeti változó értékétől függően.

A második vezérlőfolyamat-házirend a kimenő szakaszban található, és feltételesen `isMobile` alkalmazza az `true` [XML konvertálása JSON-házirendre,](api-management-transformation-policies.md#ConvertXMLtoJSON) ha a beállítása .

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

### <a name="elements"></a>Elemek

| Elem   | Leírás                                                                                                                                                                                                                                                               | Kötelező |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Válassza ki    | Gyökérelem.                                                                                                                                                                                                                                                             | Igen      |
| mikor      | A `choose` házirend vagy `ifelse` `if` annak részeihez használandó feltétel. Ha `choose` a szabályzat `when` több szakaszt is rendelkezik, azok egymás után kiértékelése történik. Amint `condition` az elem kiértékel, `true`a `when` program nem számít ki további feltételeket. | Igen      |
| Egyébként | A használandó házirendkódrészletet tartalmazza, `when` ha a `true`feltételek egyike sem .                                                                                                                                                                               | Nem       |

### <a name="attributes"></a>Attribútumok

| Attribútum                                              | Leírás                                                                                               | Kötelező |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| condition="Logikai kifejezés &#124; logikai állandó" | A logikai kifejezés vagy állandó kiértékelésére, amikor a tartalmazó `when` házirend utasítás kiértékelése. | Igen      |

### <a name="usage"></a><a name="ChooseUsage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

## <a name="forward-request"></a><a name="ForwardRequest"></a>Kérelem továbbítása

A `forward-request` házirend továbbítja a bejövő kérelmet a [kérelemkörnyezetben](api-management-policy-expressions.md#ContextVariables)megadott háttérszolgáltatásnak. A háttérszolgáltatás URL-címe meg van adva az [API-beállításokban,](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) és a [készlet háttérszolgáltatás-házirend](api-management-transformation-policies.md) használatával módosítható.

> [!NOTE]
> A házirend eltávolítása azt eredményezi, hogy a kérelem nem továbbítva a háttérszolgáltatásba, és a kimenő szakaszban lévő szabályzatok kiértékelése azonnal a bejövő szakaszban lévő házirendek sikeres befejezése után azonnal kiértékelésre kerül.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

A következő API-szintű szabályzat 60 másodperces időkérési időközzel továbbítja az összes API-kérelmet a háttérszolgáltatásnak.

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

Ez a műveletszintű `base` házirend az elemet használja a háttérházirend örökléséhez a szülő API-szintű hatókörből.

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

Ez a műveletszintű szabályzat explicit módon továbbítja az összes kérelmet a háttérszolgáltatásnak 120 időtúllépéssel, és nem örökli a szülő API-szintű háttérházirendet. Ha a háttérszolgáltatás 400 és 599 között egy 400 és 599-es hibaállapot-kóddal válaszol, [a hibaszakasz](api-management-error-handling-policies.md) aktiválódik.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Példa

Ez a műveletszintű házirend nem továbbítja a kérelmeket a háttérszolgáltatásnak.

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

| Elem         | Leírás   | Kötelező |
| --------------- | ------------- | -------- |
| továbbítási kérelem | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum                                     | Leírás                                                                                                                                                                                                                                                                                                    | Kötelező | Alapértelmezett |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout="egész szám"                             | Az időmásodpercben, amíg a RENDSZER a háttérszolgáltatás visszaadja a HTTP-válaszfejléceket, mielőtt időkezelési hiba lépne fel. A minimális érték 0 másodperc. A 240 másodpercnél nagyobb értékek nem biztos, hogy tiszteletben maradnak, mivel az alapul szolgáló hálózati infrastruktúra ezen idő után eldobhatja az alapjárati kapcsolatokat. | Nem       | None    |
| follow-redirects="hamis &#124; igaz"          | Itt adható meg, hogy a háttérszolgáltatás átirányításait az átjáró követi-e, vagy visszaadja-e a hívónak.                                                                                                                                                                                                    | Nem       | hamis   |
| buffer-request-body="hamis &#124; igaz"       | Ha a "true" kérés értéke pufferelt, és [újra](api-management-advanced-policies.md#Retry)fel lesz használva újra .                                                                                                                                                                                               | Nem       | hamis   |
| fail-on-error-status-code="hamis &#124; igaz" | Ha a beállítás igaz eseményindítók [a hiba](api-management-error-handling-policies.md) szakasz válaszkódok a tartományban 400-599, beleértve.                                                                                                                                                                      | Nem       | hamis   |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** háttérszabály
-   **Házirend-hatókörök:** az összes hatókör

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a>Egyidejűség korlátozása

A `limit-concurrency` házirend megakadályozza, hogy a zárt házirendek a megadottnál több kérelemvel hajtsanak végre. A szám túllépése után az új kérelmek azonnal sikertelenek lesznek a 429 Too Many Requests állapotkóddal.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a>Politikai nyilatkozat

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

A következő példa bemutatja, hogyan korlátozhatja a háttérbe küldött kérelmek számát egy háttérváltozó értéke alapján.

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

| Elem           | Leírás   | Kötelező |
| ----------------- | ------------- | -------- |
| korlát-egyidejűség | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                        | Kötelező | Alapértelmezett |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| kulcs       | Egy madzag. Kifejezés engedélyezett. Megadja az egyidejűséghatókört. Több házirend is megosztható. | Igen      | N/A     |
| maximális szám | Egész szám. A házirendbe belépni engedélyezett kérelmek maximális számát adja meg.           | Igen      | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a>Bejelentkezés az Eseményközpontba

A `log-to-eventhub` házirend a megadott formátumban küld üzeneteket egy Logger-entitás által definiált eseményközpontnak. Ahogy a neve is mutatja, a házirend a kiválasztott kérelem- vagy válaszkörnyezet-információk online vagy offline elemzéshez való mentésére szolgál.

> [!NOTE]
> Az eseményközpont konfigurálásának és az események naplózásának részletes útmutatóját az [API-kezelés eseményeinek naplózása az Azure Event Hubs szolgáltatással](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/)című témakörben talál.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Példa

Bármely karakterlánc használható az Event Hubs-ban naplózandó értékként. Ebben a példában a dátum és az idő, a telepítési szolgáltatás neve, a kérelem azonosítója, az IP-cím és a művelet neve az összes bejövő híváshoz az `contoso-logger` azonosítóval regisztrált eseményközpont-naplózóhoz kerül.

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

| Elem         | Leírás                                                                     | Kötelező |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-to-eventhub | Gyökérelem. Ennek az elemnek az értéke az eseményközpontba naplózandó karakterlánc. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum     | Leírás                                                               | Kötelező                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| naplózó-id     | A Naplózó azonosítója regisztrálva van az API Management szolgáltatásban.         | Igen                                                                  |
| partíció-azonosító  | Megadja annak a partíciónak az indexét, amelyben az üzeneteket küldik.             | Választható. Ez az attribútum nem `partition-key` használható, ha használja. |
| partíció-kulcs | Az üzenetek küldésekor a partíció-hozzárendeléshez használt érték. | Választható. Ez az attribútum nem `partition-id` használható, ha használja.  |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

## <a name="mock-response"></a><a name="mock-response"></a>Ál válasz

A `mock-response`, ahogy a neve is mutatja, az API-k és műveletek kigúnyolására szolgál. Megszakítja a normál folyamat végrehajtását, és egy kigúnyolt választ ad vissza a hívónak. A házirend mindig a legmagasabb hűségű válaszokat próbálja visszaadni. Előnyben részesíti a választartalom-példákat, amikor csak azok rendelkezésre állnak. Mintaválaszokat hoz létre a sémákból, ha sémák vannak megadva, és példák nem. Ha nincs enek példák vagy sémák, a rendszer tartalmas válaszokat ad vissza.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

| Elem       | Leírás   | Kötelező |
| ------------- | ------------- | -------- |
| ál-válasz | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum    | Leírás                                                                                           | Kötelező | Alapértelmezett |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| állapotkód  | Megadja a válasz állapotkódját, és a megfelelő példa vagy séma kiválasztására szolgál.                 | Nem       | 200     |
| tartalomtípus | Megadja `Content-Type` a válasz fejlécének értékét, és a megfelelő példa vagy séma kiválasztására szolgál. | Nem       | None    |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, hibás

-   **Házirend-hatókörök:** az összes hatókör

## <a name="retry"></a><a name="Retry"></a>Újra

A `retry` házirend végrehajtja a gyermekházirendek egyszer, majd `condition` újramegpróbálja azok végrehajtását, amíg az újra próbálkozás válik, `false` vagy újra `count` kimerül.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

A következő példában a kérelmek továbbítását a rendszer legfeljebb tízszer próbálja meg egy exponenciális újrapróbálkozási algoritmus használatával. Mivel `first-fast-retry` hamis, minden újrapróbálkozási kísérlet az exponenciális újrapróbálkozási algoritmus hatálya alá tartozik.

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

| Elem | Leírás                                                         | Kötelező |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Gyökérelem. Gyermekelemként bármilyen más házirendet tartalmazhat. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum        | Leírás                                                                                                                                           | Kötelező | Alapértelmezett |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Feltétel        | Logikai konstans vagy [kifejezés,](api-management-policy-expressions.md) amely`false`meghatározza, hogy`true`az újrapróbálkozásokat le kell-e állítani ( ) vagy folytatni kell ( ).      | Igen      | N/A     |
| count            | Pozitív szám, amely megadja a megkísérelt újrapróbálkozások maximális számát.                                                                                | Igen      | N/A     |
| interval         | Pozitív szám másodpercben, amely az újrapróbálkozási kísérletek közötti várakozási intervallumot adja meg.                                                                 | Igen      | N/A     |
| maximális intervallum     | Pozitív szám másodpercben, amely megadja az újrapróbálkozási kísérletek közötti maximális várakozási intervallumot. Exponenciális újrapróbálkozási algoritmus megvalósításához használható. | Nem       | N/A     |
| Delta            | Pozitív szám másodpercben, amely megadja a várakozási időköz növekményét. A lineáris és exponenciális újrapróbálkozási algoritmusok megvalósításához használatos.             | Nem       | N/A     |
| első gyors újrapróbálkozás | Ha `true` a beállítása , az első újrapróbálkozási kísérlet azonnal végrehajtásra kerül.                                                                                  | Nem       | `false` |

> [!NOTE]
> Ha csak `interval` a megadott, **rögzített** időköz újrapróbálkozások történik.
> Ha csak `interval` `delta` a és van megadva, **lineáris** intervallum újrapróbálkozási algoritmust használ a rendszer, ahol az újrapróbálkozások közötti várakozási időt a következő képlet alapján számítja ki a rendszer - `interval + (count - 1)*delta`.
> Ha `interval`a `max-interval` `delta` , és meg vannak adva, **exponenciális** intervallum újrapróbálkozási algoritmust alkalmaz, ahol az újrapróbálkozások közötti `interval` várakozási `max-interval` idő exponenciálisan növekszik az értékről az értékre a következő képlet szerint - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) használható. Vegye figyelembe, hogy a gyermekházirend-használati korlátozásokat ez a házirend örökli.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

## <a name="return-response"></a><a name="ReturnResponse"></a>Visszatérési válasz

A `return-response` házirend megszakítja a folyamat végrehajtását, és alapértelmezett vagy egyéni választ ad vissza a hívónak. Az alapértelmezett `200 OK` válasz törzs nélkül van. Az egyéni válasz megadható egy környezeti változó vagy házirendutasítások segítségével. Ha mindkettő meg van adva, a környezeti változóban található választ a házirend-utasítások módosítják, mielőtt visszaadnák a hívónak.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

| Elem         | Leírás                                                                               | Kötelező |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| visszaadás | Gyökérelem.                                                                             | Igen      |
| set-fejléc      | [Set-header házirend-utasítás.](api-management-transformation-policies.md#SetHTTPheader) | Nem       |
| beállított test        | A [set-body](api-management-transformation-policies.md#SetBody) politikai nyilatkozat.         | Nem       |
| beállított állapot      | [Set-status](api-management-advanced-policies.md#SetStatus) házirend-utasítás.           | Nem       |

### <a name="attributes"></a>Attribútumok

| Attribútum              | Leírás                                                                                                                                                                          | Kötelező  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| válasz-változó-név | A környezeti változó neve, amely például egy felsőszintű [küldési](api-management-advanced-policies.md#SendRequest) kérelem `Response` házirendből származik, és egy objektumot tartalmaz | Választható. |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a>Egyirányú kérelem küldése

A `send-one-way-request` házirend válasz megvárakozása nélkül küldi el a megadott kérelmet a megadott URL-címre.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

Ez a mintaházirend egy `send-one-way-request` példát mutat be, amely a szabályzat használatával üzenetet küld egy Slack csevegőszobába, ha a HTTP-válaszkód nagyobb vagy egyenlő 500-ra. A mintáról további információt az [Azure API Management szolgáltatás külső szolgáltatásainak használata című témakörben talál.](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

| Elem                    | Leírás                                                                                                 | Kötelező                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| küldés-egyirányú kérés       | Gyökérelem.                                                                                               | Igen                             |
| url                        | A kérelem URL-címe.                                                                                     | Nincs ha mode=copy; egyébként igen. |
| method                     | A kérelem HTTP-metódusa.                                                                            | Nincs ha mode=copy; egyébként igen. |
| header                     | Kérelem fejléce. Több fejlécelemet használjon több kérelemfejléchez.                                  | Nem                              |
| body (Törzs)                       | A kérelem törzse.                                                                                           | Nem                              |
| hitelesítési tanúsítvány | [Az ügyfélhitelesítéshez használandó tanúsítvány](api-management-authentication-policies.md#ClientCertificate) | Nem                              |

### <a name="attributes"></a>Attribútumok

| Attribútum     | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="karakterlánc" | Azt határozza meg, hogy ez egy új kérelem vagy az aktuális kérelem másolata. Kimenő módban a mode=copy nem inicializálja a kérelemtörzset.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nem       | Új      |
| név          | Megadja a beállítandó fejléc nevét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Igen      | N/A      |
| létezik-akció | Itt adható meg, hogy milyen műveletet kell végrehajtani, ha a fejléc már meg van adva. Ennek az attribútumnak az alábbi értékek egyikével kell rendelkeznie.<br /><br /> - felülbírálás - felülírja a meglévő fejléc értékét.<br />- skip - nem helyettesíti a meglévő fejlécértéket.<br />- hozzáfűzése - hozzáfűzi az értéket a meglévő fejlécértékhez.<br />- delete - eltávolítja a fejlécet a kérelemből.<br /><br /> Ha úgy `override` van beállítva, hogy több, azonos nevű bejegyzést állítson be, a fejléc az összes bejegyzés nek megfelelően lesz beállítva (amely többször is megjelenik); csak a felsorolt értékek lesznek beállítva az eredményben. | Nem       | Felülbírálja |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

## <a name="send-request"></a><a name="SendRequest"></a>Kérelem küldése

A `send-request` házirend elküldi a megadott kérelmet a megadott URL-címre, és nem vár tovább, mint a beállított időtúlérték.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

Ez a példa egy hivatkozási jogkivonat ellenőrzőmódját mutatja be egy engedélyezési kiszolgálóval. A mintáról további információt az [Azure API Management szolgáltatás külső szolgáltatásainak használata című témakörben talál.](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

| Elem                    | Leírás                                                                                                 | Kötelező                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| küldési kérelem               | Gyökérelem.                                                                                               | Igen                             |
| url                        | A kérelem URL-címe.                                                                                     | Nincs ha mode=copy; egyébként igen. |
| method                     | A kérelem HTTP-metódusa.                                                                            | Nincs ha mode=copy; egyébként igen. |
| header                     | Kérelem fejléce. Több fejlécelemet használjon több kérelemfejléchez.                                  | Nem                              |
| body (Törzs)                       | A kérelem törzse.                                                                                           | Nem                              |
| hitelesítési tanúsítvány | [Az ügyfélhitelesítéshez használandó tanúsítvány](api-management-authentication-policies.md#ClientCertificate) | Nem                              |

### <a name="attributes"></a>Attribútumok

| Attribútum                       | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="karakterlánc"                   | Azt határozza meg, hogy ez egy új kérelem vagy az aktuális kérelem másolata. Kimenő módban a mode=copy nem inicializálja a kérelemtörzset.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nem       | Új      |
| response-variable-name="string" | A válaszobjektumot fogadó környezeti változó neve. Ha a változó nem létezik, akkor a házirend sikeres végrehajtása után [`context.Variable`](api-management-policy-expressions.md#ContextVariables) jön létre, és gyűjteményen keresztül válik elérhetővé.                                                                                                                                                                                                                                                                                                                          | Igen      | N/A      |
| timeout="egész szám"               | Az URL-cím hívásának meghiúsulása előtti időköz másodpercben.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nem       | 60       |
| ignore-hiba                    | Ha igaz, és a kérelem hibát eredményez:<br /><br /> - Ha meg van adva válasz-változó-név, az null értéket fog tartalmazni.<br />- Ha a válasz-változó neve nincs megadva, a környezetben. A kérelem nem lesz frissítve.                                                                                                                                                                                                                                                                                                                                                                                   | Nem       | hamis    |
| név                            | Megadja a beállítandó fejléc nevét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Igen      | N/A      |
| létezik-akció                   | Itt adható meg, hogy milyen műveletet kell végrehajtani, ha a fejléc már meg van adva. Ennek az attribútumnak az alábbi értékek egyikével kell rendelkeznie.<br /><br /> - felülbírálás - felülírja a meglévő fejléc értékét.<br />- skip - nem helyettesíti a meglévő fejlécértéket.<br />- hozzáfűzése - hozzáfűzi az értéket a meglévő fejlécértékhez.<br />- delete - eltávolítja a fejlécet a kérelemből.<br /><br /> Ha úgy `override` van beállítva, hogy több, azonos nevű bejegyzést állítson be, a fejléc az összes bejegyzés nek megfelelően lesz beállítva (amely többször is megjelenik); csak a felsorolt értékek lesznek beállítva az eredményben. | Nem       | Felülbírálja |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a>HTTP-proxy beállítása

A `proxy` házirend lehetővé teszi, hogy a kiszolgálón keresztül továbbított kérelmeket továbbítsa. Csak HTTP (nem HTTPS) támogatott az átjáró és a proxy között. Csak alapszintű és NTLM-hitelesítés.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Példa

Vegye figyelembe, hogy a [tulajdonságokat](api-management-howto-properties.md) a felhasználónév és a jelszó értékeként használja, hogy elkerülje a bizalmas adatok tárolását a házirend-dokumentumban.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elemek

| Elem | Leírás  | Kötelező |
| ------- | ------------ | -------- |
| proxy   | Gyökérelem | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum         | Leírás                                            | Kötelező | Alapértelmezett |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="karakterlánc"      | Proxy URL-címe http://host:portformájában .             | Igen      | N/A     |
| username="karakterlánc" | A proxyval való hitelesítéshez használandó felhasználónév. | Nem       | N/A     |
| password="karakterlánc" | A proxyval való hitelesítéshez használt jelszó. | Nem       | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-request-method"></a><a name="SetRequestMethod"></a>Kérésmetódus beállítása

A `set-method` házirend lehetővé teszi a HTTP-kérelem módszerének módosítását.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Példa

Ez a `set-method` házirendet használó mintaházirend egy példát mutat arra, hogy üzenetet küld egy Slack csevegőszobába, ha a HTTP-válaszkód nagyobb vagy egyenlő 500-ra. A mintáról további információt az [Azure API Management szolgáltatás külső szolgáltatásainak használata című témakörben talál.](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

| Elem    | Leírás                                                       | Kötelező |
| ---------- | ----------------------------------------------------------------- | -------- |
| set-módszer | Gyökérelem. Az elem értéke a HTTP-metódust adja meg. | Igen      |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, hibás

-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-status-code"></a><a name="SetStatus"></a>Állapotkód beállítása

A `set-status` házirend a HTTP-állapotkódot a megadott értékre állítja be.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Példa

Ez a példa bemutatja, hogyan adja vissza a 401-es választ, ha az engedélyezési jogkivonat érvénytelen. További információ: [Külső szolgáltatások használata az Azure API Management szolgáltatásból](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

| Elem    | Leírás   | Kötelező |
| ---------- | ------------- | -------- |
| beállított állapot | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum       | Leírás                                                | Kötelező | Alapértelmezett |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="egész szám"  | A visszaadandó HTTP-állapotkód.                            | Igen      | N/A     |
| reason="karakterlánc" | Az állapotkód visszaküldésének oka. | Igen      | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** kimenő, háttérrendszer, hiba
-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-variable"></a><a name="set-variable"></a>Változó beállítása

A `set-variable` házirend deklarál egy [környezeti](api-management-policy-expressions.md#ContextVariables) változót, és hozzárendeli azt egy [kifejezésen](api-management-policy-expressions.md) vagy karakterlánc-konstanson keresztül megadott értéket. ha a kifejezés literális értéket tartalmaz, akkor a program karakterláncká alakítja, és az érték típusa a lesz. `System.String`

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a>Politikai nyilatkozat

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a>Példa

A következő példa egy meghatározott változóházirendet mutat be a bejövő szakaszban. Ez a `isMobile` halmazváltozó-házirend létrehoz egy logikai [környezeti](api-management-policy-expressions.md#ContextVariables) változót, amely igaz értékre van állítva, ha a `User-Agent` kérelemfejléc szöveget `iPad` tartalmaz, vagy `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elemek

| Elem      | Leírás   | Kötelező |
| ------------ | ------------- | -------- |
| beállított változó | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                              | Kötelező |
| --------- | ------------------------------------------------------------------------ | -------- |
| név      | A változó neve.                                                | Igen      |
| érték     | A változó értéke. Ez lehet kifejezés vagy literális érték. | Igen      |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba
-   **Házirend-hatókörök:** az összes hatókör

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a>Engedélyezett típusok

A házirendben `set-variable` használt kifejezéseknek az alábbi alapvető típusok egyikét kell visszaadniuk.

-   System.Boolean
-   System.SByte fájl
-   System.Byte fájl
-   System.UInt16
-   System.UInt32
-   System.UInt64
-   Rendszer.Int16
-   Rendszer.Int32
-   Rendszer.Int64
-   System.Decimal
-   System.Single
-   System.Double (Kétszeris)
-   System.Guid fájl
-   System.String
-   Rendszer.Karakter
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

## <a name="trace"></a><a name="Trace"></a>Nyomkövetési

A `trace` szabályzat egy egyéni nyomkövetést ad hozzá az API-felügyelő kimenetéhez, az Application Insights telemetries és/vagy diagnosztikai naplók.

-   A házirend hozzáad egy egyéni nyomkövetést az [API-felügyelő](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimenetéhez `Ocp-Apim-Trace` a nyomkövetés aktiválásakor, azaz a kérelemfejléc jelen van, és igaz értékre van állítva, és `Ocp-Apim-Subscription-Key` a kérelemfejléc jelen van, és rendelkezik egy érvényes kulccsal, amely lehetővé teszi a nyomkövetést.
-   A szabályzat létrehoz egy [Trace](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) telemetriát az Application Insightsban, ha az [Application Insights-integráció](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) engedélyezve van, és a `severity` szabályzatban megadott szint a diagnosztikai környezetben megadott `verbosity` szinten vagy annál magasabb.
-   A házirend hozzáad egy tulajdonságot a naplóbejegyzéshez, ha a Diagnosztikai naplók engedélyezve [van,](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) és a házirendben megadott súlyossági szint a diagnosztikai beállításban megadott részletességi szinten vagy annál magasabb.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a>Példa

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elemek

| Elem  | Leírás                                                                                                                                          | Kötelező |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Nyomkövetési    | Gyökérelem.                                                                                                                                        | Igen      |
| message  | A naplózandó karakterlánc vagy kifejezés.                                                                                                                 | Igen      |
| metaadatok | Hozzáad egy egyéni tulajdonságot az Application Insights [Trace](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) telemetriai adatokhoz. | Nem       |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                                               | Kötelező | Alapértelmezett |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | A nyomkövetési megtekintő nek jelentős karakterláncot fűz, és megadja az üzenet forrását.                                   | Igen      | N/A     |
| súlyosság  | Megadja a nyomkövetés súlyossági szintjét. Az engedélyezett `verbose` `information`értékek `error` a , (a legalacsonyabbtól a legmagasabbig). | Nem       | Részletes |
| név      | A tulajdonság neve.                                                                                                     | Igen      | N/A     |
| érték     | Az ingatlan értéke.                                                                                                    | Igen      | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba

-   **Házirend-hatókörök:** az összes hatókör

## <a name="wait"></a><a name="Wait"></a>várj

A `wait` házirend párhuzamosan hajtja végre a közvetlen gyermekházirendeket, és megvárja, amíg az összes vagy annak egyik közvetlen gyermekházirendje befejeződik, mielőtt befejeződne. A várakozási házirend lehet, mint a közvetlen gyermek [házirendek Küldési kérelem](api-management-advanced-policies.md#SendRequest), [Érték beolvasása a gyorsítótárból,](api-management-caching-policies.md#GetFromCacheByKey)és [a szabályozás folyamatházirendek.](api-management-advanced-policies.md#choose)

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Példa

A következő példában `choose` két szabályzat van a `wait` házirend azonnali gyermekházirendjeként. Ezek `choose` a házirendek mindegyike párhuzamosan hajtható végre. Minden `choose` házirend megkísérli a gyorsítótárazott érték beolvasását. Ha van egy gyorsítótár-miss, egy háttérszolgáltatás hívmeg, hogy az érték megadásához. Ebben a `wait` példában a házirend nem fejeződik be, `for` amíg az `all`összes közvetlen gyermekházirend je, mert az attribútum beállítása . Ebben a példában a`execute-branch-one` `value-one`környezeti `execute-branch-two`változók ( , , , és `value-two`) deklarálva vannak a példaházirend hatókörén kívül.

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

| Elem | Leírás                                                                                                   | Kötelező |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| várj    | Gyökérelem. Csak gyermekelemeket `send-request`tartalmazhatnak `cache-lookup-value` `choose` , és házirendeket tartalmazhatnak. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                                                                                                                                                                                                                                                                                                                                            | Kötelező | Alapértelmezett |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| a következőhöz:       | Azt határozza `wait` meg, hogy a házirend megvárja-e az összes azonnali alárendelt házirend teljesítését, vagy csak egy. Az engedélyezett értékek a következők:<br /><br /> - `all`- várja meg, amíg az összes azonnali gyermekpolitika befejeződik<br />- minden - várja meg, amíg az azonnali gyermekpolitika befejeződik. Miután az első azonnali gyermek `wait` szabályzat befejeződött, a szabályzat befejeződik, és bármely más azonnali gyermek szabályzatok végrehajtása megszűnik. | Nem       | összes     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő, háttér-háttér
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések

A házirendekkel kapcsolatos további információkért lásd:

-   [Szabályzatok az API Managementben](api-management-howto-policies.md)
-   [Házirend-kifejezések](api-management-policy-expressions.md)
-   [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
-   [Házirendminták](policy-samples.md)
