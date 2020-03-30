---
title: AZ API Management szolgáltatás használata HTTP-kérelmek létrehozásához
description: Ismerje meg, hogyan használhatja a kérés- és válaszkezelési szabályzatokat az API Managementben külső szolgáltatások hívására az API-ból
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 1c86570850894a47f57a2d3587811411cc9a76eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190014"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Az Azure API Management szolgáltatás külső szolgáltatásainak használata
Az Azure API Management szolgáltatásban elérhető szabályzatok számos hasznos munkát tehetnek le kizárólag a bejövő kérés, a kimenő válasz és az alapvető konfigurációs információk alapján. Azonban az API Management-házirendek ből származó külső szolgáltatásokkal való interakció sokkal több lehetőséget nyit meg.

Korábban már látta, hogyan kommunikálhat az [Azure Event Hub szolgáltatásnaplózás, figyelés és elemzés.](api-management-log-to-eventhub-sample.md) Ez a cikk bemutatja a szabályzatok, amelyek lehetővé teszik, hogy bármely külső HTTP-alapú szolgáltatás. Ezek a házirendek távoli események indítására vagy az eredeti kérelem és válasz valamilyen módon történő kezelésére használt információk lekérésére használhatók.

## <a name="send-one-way-request"></a>Küldés-egyirányú kérés
Talán a legegyszerűbb külső interakció a tűz-és-felejtsd el a kérés stílusát, amely lehetővé teszi, hogy egy külső szolgáltatást értesítsenek valamilyen fontos eseményről. A vezérlési `choose` folyamat házirend jetheti bármilyen feltétel, amely érdekli.  Ha a feltétel teljesül, külső HTTP-kérelmet is elérhet a [send-one-way-request](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) házirend használatával. Ez lehet egy kérés egy üzenetküldő rendszer, mint a Hipchat vagy a Slack, vagy egy mail API, mint a SendGrid vagy MailChimp, vagy a kritikus támogatási események valami hasonló PagerDuty. Ezek az üzenetküldő rendszerek egyszerű HTTP API-k, amelyek meghívhatók.

### <a name="alerting-with-slack"></a>Figyelmeztetés a Tartalékidővel
A következő példa bemutatja, hogyan küldhet üzenetet egy Slack csevegőszobába, ha a HTTP-válasz állapotkódja 500-nál nagyobb vagy azzal egyenlő. Az 500-as tartományhiba azt jelzi, hogy a háttér-API-val olyan probléma van, amelyet az API ügyfele nem tud megoldani. Általában valamilyen beavatkozást igényel az API Management részében.  

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

A Slack a bejövő webes horgok fogalmát támogatja. Egy bejövő webhook konfigurálásakor a Slack létrehoz egy speciális URL-t, amely lehetővé teszi egy egyszerű POST-kérés tanusítását, és egy üzenetet a Slack csatornára. A létrehozott JSON-törzs a Slack által meghatározott formátumon alapul.

![Tartalék hálóhorog](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>A tűz és a felejtés elég jó?
Vannak bizonyos kompromisszumok használata esetén a tűz-és felejtsd el a stílus kérés. Ha valamilyen okból a kérelem sikertelen, majd a hiba nem jelentik. Ebben a konkrét helyzetben a másodlagos hibajelentési rendszer összetettsége és a válaszra való várakozás többletteljesítményköltsége nem indokolt. Olyan esetekben, ahol elengedhetetlen a válasz ellenőrzése, majd a [send-request](/azure/api-management/api-management-advanced-policies#SendRequest) házirend jobb megoldás.

## <a name="send-request"></a>Küldési kérelem
A `send-request` házirend lehetővé teszi egy külső szolgáltatás használatával összetett feldolgozási funkciók végrehajtásához és az API-felügyeleti szolgáltatásnak, amely további házirend-feldolgozáshoz használható.

### <a name="authorizing-reference-tokens"></a>Hivatkozási jogkivonatok engedélyezése
Az API Management egyik fő funkciója a háttér-erőforrások védelme. Ha az API által használt engedélyezési kiszolgáló [JWT-jogkivonatokat](https://jwt.io/) hoz létre az OAuth2-folyamat `validate-jwt` részeként, ahogy az Azure Active [Directory](../active-directory/hybrid/whatis-hybrid-identity.md) teszi, akkor a szabályzat segítségével ellenőrizheti a jogkivonat érvényességét. Egyes engedélyezési kiszolgálók úgynevezett [hivatkozási jogkivonatokat hoznak](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) létre, amelyek nem ellenőrizhetők az engedélyezési kiszolgálóra való visszahívás nélkül.

### <a name="standardized-introspection"></a>Standardizált önvizsgálat
A múltban nem volt szabványosított módja a referencia-jogkivonat ellenőrző rendszerű engedélyezési kiszolgálóval. Az IETF azonban egy nemrégiben javasolt szabványos [RFC 7662-es](https://tools.ietf.org/html/rfc7662) szabványt tett közzé, amely meghatározza, hogy az erőforrás-kiszolgáló hogyan ellenőrizheti a jogkivonat érvényességét.

### <a name="extracting-the-token"></a>A token kibontása
Az első lépés a jogkivonat kibontása az engedélyezési fejlécből. A fejlécértéket az engedélyezési `Bearer` sémával, egyetlen térközrel, majd az [6750 RFC-kóddal](https://tools.ietf.org/html/rfc6750#section-2.1)kell formázni. Sajnos vannak olyan esetek, amikor az engedélyezési rendszer kimarad. Ennek elemzéskortörténő figyelembe vonásához az API Management felosztja a fejléc értékét egy szóközön, és kiválasztja az utolsó karakterláncot a visszaadott karakterlánctömbből. Ez megoldást kínál a rosszul formázott engedélyezési fejlécek számára.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Az érvényesítési kérelem benyújtása
Miután az API Management rendelkezik az engedélyezési jogkivonat, az API Management kérheti a jogkivonat érvényesítésére. Az RFC 7662 meghívja ezt a `POST` folyamatot önvizsgálatnak, és html-űrlapot kell létrehoznia az önvizsgálat erőforráshoz. A HTML-űrlapnak legalább egy kulcs/érték `token`párt kell tartalmaznia a kulccsal. Ezt a kérelmet az engedélyezési kiszolgálónak is hitelesíteni kell, hogy a rosszindulatú ügyfelek ne tudjanak érvényes jogkivonatokat keresni.

```xml
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
```

### <a name="checking-the-response"></a>A válasz ellenőrzése
Az `response-variable-name` attribútum a visszaadott válasz elérésére szolgál. A tulajdonságban definiált név kulcsként használható `context.Variables` a szótárban `IResponse` az objektum eléréséhez.

A válaszobjektumból lekérheti a törzset, és az RFC 7622 azt mondja az API Managementnek, `active` hogy a válasznak JSON-objektumnak kell lennie, és legalább egy logikai érték nevű tulajdonságot tartalmaznia kell. Ha `active` igaz, akkor a jogkivonat érvényesnek minősül.

Másik lehetőségként, ha az engedélyezési kiszolgáló nem tartalmazza az "aktív" mezőt annak jelzésére, hogy a token érvényes-e, használjon egy eszközt, például postán, hogy meghatározza, milyen tulajdonságok vannak beállítva egy érvényes jogkivonatban. Ha például egy érvényes jogkivonat-válasz "expires_in" nevű tulajdonságot tartalmaz, ellenőrizze, hogy ez a tulajdonságnév létezik-e az engedélyezési kiszolgáló válaszában így:

<ha condition="@((IResponse)context. Változók["tokenstate"]). Body.As<JObject>(). Tulajdonság("expires_in") == null)">

### <a name="reporting-failure"></a>Jelentési hiba
Egy `<choose>` szabályzat segítségével észlelheti, ha a jogkivonat érvénytelen, és ha igen, 401-es választ adhat vissza.

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

Az [RFC 6750,](https://tools.ietf.org/html/rfc6750#section-3) amely `bearer` leírja, hogyan kell használni a `WWW-Authenticate` jogkivonatokat, az API Management egy 401-es választ is visszaad egy fejlécet. A WWW-Authenticate célja, hogy utasítsa az ügyfelet, hogyan kell létrehozni egy megfelelően engedélyezett kérelmet. Az OAuth2 keretrendszerrel lehetséges megközelítések széles skálája miatt nehéz az összes szükséges információt közölni. Szerencsére vannak erőfeszítések folynak, hogy segítsen [az ügyfeleknek felfedezni, hogyan kell megfelelően engedélyezni a kérelmeket egy erőforrás-kiszolgáló](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Végső megoldás
A végén a következő házirendet kapja:

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
              <return-response response-variable-name="existing response variable">
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

Ez csak egy a sok `send-request` példa, hogyan lehet a szabályzat hasznos külső szolgáltatások integrálása a folyamat a kérelmek és válaszok az API Management szolgáltatáson keresztül.

## <a name="response-composition"></a>Válasz összetétele
A `send-request` szabályzat használható egy háttérrendszer elsődleges kérésének javítására, ahogy az előző példában látható, vagy a háttérrendszer-hívás teljes cseréjeként használható. Ezzel a módszerrel egyszerűen létrehozhat összetett erőforrásokat, amelyek több különböző rendszerből összesíthetők.

### <a name="building-a-dashboard"></a>Irányítópult létrehozása
Néha azt szeretné, hogy képes legyen elérhetővé tenni a több háttérrendszerben létező információkat, például egy irányítópult vezetésére. A KPI-k különböző háttérrendszerekből származnak, de nem szeretné, hogy közvetlen hozzáférést biztosítson hozzájuk, és jó lenne, ha az összes információt egyetlen kérésben lelehetne kérni. Talán néhány háttér információ szüksége van néhány szeletelés és kockára vágott, és egy kicsit fertőtlenítő első! Az összetett erőforrás gyorsítótárazása hasznos lehet a háttérrendszer terhelésének csökkentéséhez, mivel tudja, hogy a felhasználók nak szokása az F5 kulcs kalapálása annak érdekében, hogy lássák, változhatnak-e az alulteljesítő metrikák.    

### <a name="faking-the-resource"></a>Az erőforrás megjátszása
Az irányítópult-erőforrás létrehozásának első lépése egy új művelet konfigurálása az Azure Portalon. Ez egy helyőrző művelet, amely a dinamikus erőforrás létrehozásához szükséges kompozíciós házirend konfigurálására szolgál.

![Irányítópult-művelet](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>A kérelmek bekérése
A művelet létrehozása után konfigurálhat egy házirendet kifejezetten az adott művelethez. 

![Irányítópult-művelet](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Az első lépés a lekérdezési paraméterek kinyerése a bejövő kérelemből, így továbbíthatja őket a háttérrendszerbe. Ebben a példában az irányítópult egy adott időszakon alapuló `fromDate` `toDate` információkat jelenít meg, ezért rendelkezik egy és paraméterrel. A `set-variable` házirend segítségével kinyerheti az információkat a kérelem URL-címéről.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Miután rendelkezik ezzel az információval, kéréseket intézhet az összes háttérrendszerhez. Minden kérelem létrehoz egy új URL-címet a paraméter adataival, és meghívja a megfelelő kiszolgálót, és a választ egy környezeti változóban tárolja.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Ezek a kérések sorrendben futnak, ami nem ideális. 

### <a name="responding"></a>Válaszol
Az összetett válasz létrehozásához használhatja a [visszatérés-válasz](/azure/api-management/api-management-advanced-policies#ReturnResponse) házirendet. Az `set-body` elem kifejezéssegítségével létrehozhat egy `JObject` újat, amely az összes tulajdonságként beágyazott összetevő-ábrázolást tartalmaz.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

A teljes irányelv a következőképpen néz ki:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

A helyőrző művelet konfigurációjában beállíthatja, hogy az irányítópult-erőforrás legalább egy órán keresztül gyorsítótárazva legyen. 

## <a name="summary"></a>Összefoglalás
Az Azure API Management szolgáltatás rugalmas szabályzatokat biztosít, amelyek szelektíven alkalmazhatók a HTTP-forgalomra, és lehetővé teszik a háttérszolgáltatások összetételét. Akár riasztási funkciókkal, ellenőrzéssel, érvényesítési képességekkel szeretné továbbítani az API-átjárót, akár több háttérszolgáltatáson alapuló új összetett erőforrásokat szeretne létrehozni, a kapcsolódó szabályzatok a `send-request` lehetőségek tárkait nyitják meg.

