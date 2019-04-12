---
title: API Management szolgáltatással létrehozni a HTTP-kérelmekre
description: Kérés- és szabályzatok használata az API Management a külső szolgáltatások hívni az API-ból
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
ms.openlocfilehash: 2c4e5d0117f046343b140ef2b2c46c074c835075
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59505651"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Az Azure API Management szolgáltatás a külső szolgáltatások használata
Az Azure API Management szolgáltatásban elérhető házirendek számos hasznos munkát alapján csak a bejövő kérelem, a kimenő válaszok és alapvető konfigurációs adatai hajthatja végre. Azonban képes arra, hogy az API Management külső szolgáltatásokkal interakcióba sok további lehetőség nyílik házirendeket.

Hogyan kezelheti az korábban már látott a [Azure Event Hubs szolgáltatás a naplózás, figyelés és elemzés](api-management-log-to-eventhub-sample.md). Ez a cikk bemutatja a házirendekben, amelyek lehetővé teszik bármely külső HTTP-alapú szolgáltatással. Ezekkel a szabályzatokkal használható indítására, távoli események vagy az eredeti kérés-válasz valamilyen módon kezeléséhez használt információk beolvasásakor.

## <a name="send-one-way-request"></a>Send-One-Way-Request
Valószínűleg a legegyszerűbb külső interakciónak számít, amely lehetővé teszi, hogy értesítést kapjon, néhány fontos esemény típusú külső szolgáltatásból kérés aktiválásának és elfelejt stílusát. A control flow házirend `choose` feltételt, amely az Önt érdeklő bármilyen típusú észleléséhez használható.  Ha a feltétel teljesül, akkor is használhatja egy külső HTTP kérés használatával az [küldési – egy-módon-kérelmek](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) házirend. Ez lehet egy üzenetkezelési rendszer például Hipchat vagy a Slack, illetve mint a SendGrid vagy a MailChimp API levél kérést, vagy a kritikus támogatási incidensek a PagerDuty alábbihoz hasonló. E üzenetkezelési rendszerek rendelkezik meghívható műveletkészletet jelölnek, egyszerű HTTP API-k.

### <a name="alerting-with-slack"></a>Slack-riasztások
Az alábbi példa bemutatja, hogyan üzenet küldése egy Slack csevegőszoba, ha a HTTP-válaszként kapott állapotkód nem nagyobb, mint 500. Egy 500 tartomány hiba történt a háttérrendszeri API az API-ügyfél nem oldható fel magukat problémájára utal. Általában valamilyen az API Management részéről beavatkozást igényel.  

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

Slack bejövő webhook fogalma rendelkezik. Egy bejövő webhook konfigurálásakor a Slack egy speciális URL-címet, amely lehetővé teszi, hogy hajtsa végre egy egyszerű POST-kérelmet, és adja át az üzenetet a Slack-csatornának állít elő. Az Ön által létrehozott JSON-törzse Slack által definiált formátumban alapul.

![Slack-Webhook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Fire, és elég jól elfelejtette?
Bizonyos hátrányai is vannak, a kérés aktiválásának és elfelejt stílusát használatakor. Ha valamilyen okból, a kérelem meghiúsul, akkor nem kell jelenteni a hibát. Ez az adott esetben az összetettséget, hogy a jelentéskészítő rendszer és a további teljesítmény költsége a választ vár a többi másodlagos hiba nem igazolható. Forgatókönyvek, ahol ez elengedhetetlen, ellenőrizze a választ akkor a [küldési-kérelmek](/azure/api-management/api-management-advanced-policies#SendRequest) házirend nem jobb megoldás.

## <a name="send-request"></a>Send-Request
A `send-request` házirend lehetővé teszi, hogy egy külső szolgáltatás használatával összetett feldolgozás feladatokat és adatokat az API management szolgáltatás, amely további házirend feldolgozásának beállítása is használható.

### <a name="authorizing-reference-tokens"></a>Referencia-tokenek engedélyezése
Az API Management a fő függvény háttérerőforrásokhoz védelmére használja. Az engedélyezési kiszolgáló, az API által használt hozott létre [JWT-jogkivonatokkal](https://jwt.io/) az OAuth2 folyamat részeként, [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) használható, akkor a `validate-jwt` házirend a token érvényességének ellenőrzése. Egyes engedélyezési kiszolgálók létrehozása ún [jogkivonatok hivatkozhat](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) anélkül, hogy egy visszahívást, az engedélyezési kiszolgálón, amely nem ellenőrizhető.

### <a name="standardized-introspection"></a>Szabványos önelemzési
A múltban történt nem szabványos lehet az engedélyezési kiszolgálón egy referencia-token ellenőrzése. Azonban nemrégiben javasolt szabványos [RFC 7662](https://tools.ietf.org/html/rfc7662) , amely meghatározza, hogyan erőforrás-kiszolgáló egy token érvényességének ellenőrzéséhez az IETF által lett közzétéve.

### <a name="extracting-the-token"></a>A jogkivonat beolvasása
Az első lépés, hogy a jogkivonat kinyerni az engedélyeztetési fejléc. A fejléc értéke legyen formázva a `Bearer` engedélyezési séma, egy szóközt, majd az engedélyezési jogkivonatot megfelelően [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Sajnos előfordulhatnak olyan esetek, ahol az engedélyezési séma van hagyva. Ez a fiók elemzésekor, az API Management a fejléc értéke adhatja meg bontja, és az utolsó karakterlánc kiválasztja a visszaadott karakterláncok tömbje. A program rosszul formázott engedélyezési fejléceket biztosít egy megkerülő megoldás.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Az ellenőrzési kérés
Miután az API Management rendelkezik az engedélyezési jogkivonatra, az API Management teheti a jogkivonat érvényesítésére vonatkozó kérelem. RFC 7662 meghívja a folyamatot az önelemzési, és ehhez meg kell adni, `POST` HTML-űrlapból az önelemzési erőforráshoz. A HTML-űrlaphoz, legalább tartalmaznia kell egy kulcs/érték pár kulccsal `token`. A kérést, az engedélyezési kiszolgáló is hitelesíteni kell, annak érdekében, hogy a rosszindulatú ügyfelek nem go ezeken érvényes jogkivonatokat.

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
A `response-variable-name` attribútum segítségével hozzáférést biztosíthat a visszaadott válasz. Ez a tulajdonság megadott neve használható egy kulcsot a `context.Variables` szótár eléréséhez a `IResponse` objektum.

A válasz objektumból kérheti le a szervezet és RFC 7622, hogy a válasz egy JSON-objektumot kell lennie, és tartalmaznia kell legalább egy nevű tulajdonság arra utasítja az API Management `active` , amely egy logikai érték. Amikor `active` értéke igaz, akkor számít, hogy a jogkivonat érvényes.

### <a name="reporting-failure"></a>Jelentési hiba
Használhat egy `<choose>` észleli, ha a jogkivonat érvénytelen, és ha igen, a házirend 401-es választ ad vissza.

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

Megfelelően [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) melyik leírás fedi le hogyan `bearer` jogkivonatok kell használni, az API Management is adja vissza egy `WWW-Authenticate` a 401-es válasz fejléce. A WWW-Authenticate célja, hogy egy ügyfél a megfelelő engedéllyel rendelkező kérelem létrehozására utasítja. Miatt számos megközelítés lehetséges az OAuth2-keretrendszerrel meglehetősen nehéz kommunikációhoz szükséges összes információ. Szerencsére nincsenek folyamatban segítségével erőfeszítések [ügyfelek felderítése hogyan kell megfelelően egy erőforrás-kiszolgálóhoz intézett kérésekben](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Végső megoldás
A végén kérje le a következő szabályzatot:

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

Ez az, hogy csak az egyik bemutatja a példákat `send-request` házirend segítségével hasznos külső szolgáltatások integrálása a kérelmek és válaszok az API Management szolgáltatáson keresztül áramló folyamatán.

## <a name="response-composition"></a>Válasz-összeállítás
A `send-request` egy háttérrendszer elsődleges kérést növelésére, az előző példában látott, vagy azt a háttér-hívás egy teljes csere használható házirend használható. Az ezzel a technikával könnyedén létrehozhat összetett erőforrások, amelyek összesítve vannak a több különböző rendszerek.

### <a name="building-a-dashboard"></a>Irányítópult létrehozása
Néha érdemes lehet elérhetővé tenni az adatokat, hogy több háttérrendszerekhez, például megtalálható, egy irányítópult alapjául. KPI-k összes különböző háttérrendszereket, származnak, de nem szeretne hozzájuk a közvetlen hozzáférést biztosít a, és lenne nem lenne nagyszerű, ha az összes információt sikerült beolvasni az egyetlen kérés. Például a háttérbeli adatok egy részét kell néhány tovább szeletelve és darabolva és egy kis megtisztítása során először! Képes arra, hogy a gyorsítótár adott összetett erőforrást akkor lehet hasznos a háttér-terhelés csökkentésére, mint már tudja, a felhasználók rendelkeznek egy habitus az F5 billentyűt újrapróbálkozni láthatók, ha azok az alulteljesítő metrikák változhatnak.    

### <a name="faking-the-resource"></a>Az erőforrás faking
Az irányítópult-erőforrás létrehozásához az első lépés, hogy egy új művelet konfigurálása az Azure Portalon. Ez az egy helyőrző művelet segítségével hozhat létre a dinamikus erőforrás összeállítás szabályzat konfigurálása.

![Irányítópult-művelet](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>A kérelem indítására
A művelet létrehozása után konfigurálhat egy szabályzatot, kifejezetten az adott műveletnél. 

![Irányítópult-művelet](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Az első lépés, hogy a lekérdezési paramétereket kinyerése a bejövő kérelem, így továbbíthatja őket a háttérrendszerhez. Ebben a példában az irányítópulton látható időn alapulnak, és így egy `fromDate` és `toDate` paraméter. Használhatja a `set-variable` házirend az információk nyerhetők a kérelem URL-CÍMÉT.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Miután ezt az információt, hogy kéréseket a háttér-rendszerek. Minden kérelmet hoz létre egy új URL-címet a paraméter információkkal, és meghívja a megfelelő kiszolgálóhoz, és tárolja a válasz egy környezeti változó.

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

Ezek a kérelmek hajtsa végre a feladatütemezés, ami nem ideális. 

### <a name="responding"></a>Válaszol
Hozza létre a összetett választ, használhatja a [visszatérési-válasz](/azure/api-management/api-management-advanced-policies#ReturnResponse) házirend. A `set-body` elem kifejezés használatával hozhat létre egy új `JObject` a beágyazott tulajdonságokként összetevő reprezentációinak.

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

A teljes házirend a következőképpen néz ki:

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

A konfigurációban a helyőrző művelet konfigurálhatja úgy az irányítópult erőforrás legalább egy órát a gyorsítótárba. 

## <a name="summary"></a>Összegzés
Az Azure API Management-szolgáltatás, amely a HTTP-forgalom szelektív módon alkalmazható rugalmas házirendek biztosít, és lehetővé teszi a háttérszolgáltatások összetételét. Hogy növelje a riasztási funkciók, ellenőrzés, ellenőrzési funkciókat az API-átjáró, vagy hozzon létre új összetett erőforrások több háttérszolgáltatással alapján szeretné a `send-request` és a kapcsolódó házirendek megnyissa a lehetőségek.

