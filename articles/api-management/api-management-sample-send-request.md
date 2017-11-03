---
title: "HTTP-kérelmek létrehozásához az API Management szolgáltatással"
description: "Ismerkedjen meg az API Management kérelem-válasz házirendek segítségével külső szolgáltatások hívja az API-t"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 6b7f1268ea4893307713931e7288f5d38c5ee080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Az Azure API Management szolgáltatás a külső services használatával
Az Azure API Management szolgáltatásban elérhető házirendek alapján csak a bejövő kérelem, a kimenő válasz és alapvető konfigurációs adatai hasznos munkahelyi számos teheti meg. Azonban tudnak kommunikálni az API Management külső szolgáltatások számos további lehetőségek megnyílik házirendek.

Korábban úgy találtuk, hogy azt léphetnek interakcióba az [Azure Event Hubs szolgáltatás naplózási, megfigyelésének és elemzés](api-management-log-to-eventhub-sample.md). Ebben a cikkben a házirendekben, amelyek lehetővé teszik a külső HTTP együttműködhet alapú szolgáltatás bemutatjuk. Ezekkel a szabályzatokkal használható távoli eseményeket kiváltó vagy az eredeti kérelem és válasz valamilyen módon használt adatok beolvasása.

## <a name="send-one-way-request"></a>Küldési-egy-módon-kérelmek
Valószínűleg a legegyszerűbb külső beavatkozásra, amely lehetővé teszi egy külső szolgáltatás értesítést fontos esemény valamilyen kérelem tűz-és-elfelejti stílusát. A vezérlő adatfolyam házirend használhatjuk `choose` feltétel, hogy érdeklődik dolgozunk, és majd, ha a feltétel teljesül, tökéletesíthetjük egy külső HTTP kérelem használatával bármilyen észleléséhez a [küldési-egy-módon-kérelmek](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) házirend. Ennek oka lehet egy kérelem egy üzenetküldési rendszerre, például Hipchat vagy a Slackhez, illetve egy levelezési API SendGrid vagy MailChimp, vagy a kritikus fontosságú támogatási incidensek az alábbihoz hasonló PagerDuty. E üzenetkezelési rendszerek rendelkezik egyszerű HTTP API-k könnyen hívhat meg azt.

### <a name="alerting-with-slack"></a>A Slackhez riasztás
A következő példa bemutatja, hogyan üzenetet küldeni a Slack Csevegés helyiségben, ha a HTTP-válasz állapotkódja nagyobb vagy egyenlő 500. Egy 500 tartomány hiba a háttér-API, amely az ügyfél az API nem maguktól megoldódhatnak problémájára utal. Általában szüksége van valamilyen beavatkozás az eszközeiken.  

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

Slackhez rendelkezik a bejövő webes hurkok fogalmát. Egy bejövő webes hook konfigurálásakor a Slackhez hoz létre egy különleges URL-címet, amely lehetővé teszi egy egyszerű POST kérelem végrehajtásához, és a Slack csatorna üzenetet továbbítani. A JSON-törzsére, amely létrehozhatunk Slackhez által meghatározott formátumban alapul.

![Slack webes Hook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Tűz és elég jól elfelejti?
Nincsenek bizonyos mellékhatásokkal kérelem tűz-és-elfelejti stílus használatával. Ha valamilyen okból, a kérelem sikertelen lesz, akkor a hiba nem fog szerepelni. Adott esetben nem igazolható összetettségét, hogy a másodlagos hibájának jelentéskészítő rendszer és a további teljesítményarányos költsége a válaszra való várakozás közben. A forgatókönyvekben, ahol a válasz ellenőrzése alapvető fontosságú a [küldési-kérelmek](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) házirend jobb megoldás.

## <a name="send-request"></a>Küldési-kérelmek
A `send-request` házirend lehetővé teszi, hogy egy külső szolgáltatás segítségével összetett feldolgozási feladatokat, és térjen vissza az adatokat az API management szolgáltatást, amely nem használható további házirend-feldolgozás.

### <a name="authorizing-reference-tokens"></a>Referencia-tokenek engedélyezése
Az API Management főbb funkcióját háttér erőforrások védi. Ha a hitelesítési kiszolgáló, az API által használt hoz létre [JWT-jogkivonatokat](http://jwt.io/) az OAuth2 folyamat részeként, [Azure Active Directory](../active-directory/active-directory-aadconnect.md) Igen, akkor a `validate-jwt` házirend a token érvényességének ellenőrzése. Azonban néhány engedélyezési kiszolgálók létrehozása, mi nevezzük [jogkivonatok hivatkozhat](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) anélkül, hogy az engedélyezési kiszolgálóra történő hívás, amely nem ellenőrizhető.

### <a name="standardized-introspection"></a>Szabványos introspection
A múltban nincs szabványosított lehetőség van egy engedélyezési kiszolgáló és a hivatkozás-jogkivonat ellenőrzése le lett. Azonban a közelmúltban javasolt szabvány [RFC 7662](https://tools.ietf.org/html/rfc7662) az IETF, amely meghatározza, hogyan erőforrás-kiszolgáló a token érvényességének ellenőrzésére tett közzé.

### <a name="extracting-the-token"></a>A jogkivonat beolvasása
Az első lépés a token kibontani a Authorization fejlécet. A fejléc értékének fájlrendszerrel kell formázni a `Bearer` engedélyezési sémát, egy szóköz, majd az engedélyezési jogkivonatot megfelelően [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Sajnos előfordulhatnak olyan esetek, ahol a hitelesítési séma meg van adva. A fiók elemzésekor, azt ossza fel a fejléc értékének a tárhelyen, és válassza az utolsó karakterlánc karakterláncokat. a visszaadott tömb. Így lehetővé teszi a probléma megoldásához rosszul formázott engedélyezési fejléceket.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Az ellenőrzési kérés
Az engedélyezési jogkivonatot kell, ha a kérelem ellenőrzése a jogkivonat tökéletesíthetjük. RFC 7662 meghívja a folyamat introspection, és megköveteli, hogy Ön `POST` egy HTML-űrlaphoz, introspection-erőforráshoz. A HTML-űrlaphoz, legalább tartalmaznia kell egy kulcs/érték pár kulccsal `token`. A kérést a hitelesítési kiszolgáló is hitelesíteni kell ahhoz, hogy a rosszindulatú ügyfelek nem Ugrás ezeken érvényes jogkivonatokat.

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
A `response-variable-name` attribútum segítségével hozzáférést biztosíthat a visszaadott válaszban. Az ebben a tulajdonságban megadott név lesz a kulcs az használható a `context.Variables` szótár eléréséhez a `IResponse` objektum.

A válasz objektumból beolvasható a törzs és RFC 7622 közli velünk, hogy a válasz egy JSON-objektumnak kell lennie, és tartalmaznia kell legalább egy nevű tulajdonság `active` , amely egy logikai érték. Ha `active` értéke igaz, akkor a jogkivonat érvényes.

### <a name="reporting-failure"></a>Jelentéskészítési hiba
Használjuk a `<choose>` házirend észleli, ha a token érvénytelen, és ha igen, térjen vissza a 401-es válasz.

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

Megfelelően [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) ismerteti, amelyek hogyan `bearer` jogkivonatok kell használni, azt is adja vissza egy `WWW-Authenticate` a 401-es válasz fejléce. A WWW-Authenticate célja, hogy kérje meg egy ügyfél egy megfelelően jogosult kérelem létrehozására. Az OAuth2-keretrendszerben készült lehetséges módszer számos, mert is nehézkes való kommunikációhoz szükséges összes információ. Szerencsére nincsenek azon törekvéseit, hogy folyamatban érdekében [ügyfelek miképpen megfelelően engedélyezik az erőforrás-kiszolgálóhoz intézett kérésekben](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Végső megoldás
Minden a helyére üzembe, azt lekérése a következő házirendet:

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

Ez az csak az egyik számos példát bemutatja a `send-request` házirend használható hasznos külső szolgáltatás integrálása kérelmeit és válaszait az API Management szolgáltatáson keresztül áramló folyamatán.

## <a name="response-composition"></a>Válasz összeállítás
A `send-request` házirend használható a háttérrendszer elsődleges kérelem növelésére, az előző példában szereplő látott, vagy azt a háttér-hívás a teljes csere használható. Ez a módszer használatával könnyen létrehozhatjuk összesítése összetett erőforrásokat több különböző rendszerekből.

### <a name="building-a-dashboard"></a>Irányítópult létrehozása
Néha szükség lehet több háttérrendszerek, például található információt teszi közzé, egy irányítópultot alapjául. A KPI-k minden különböző biztonsági-végpontok, származhat, de nem szeretne közvetlen hozzáférést biztosít, és célszerű, ha minden információi olvashatók az egy kérelemhez. Lehet, hogy a háttér-adatok egy részét kell néhány szeletelhető és feldarabolható, és először egy kis tisztítására! Igényt, hogy összetett erőforrás gyorsítótárazásához egy hasznos lehet a háttér-terhelés csökkentésére, mint tudja a felhasználók rendelkeznek-e egy, az F5 billentyűt beütés láthatók, ha azok underperforming metrikák változhat válhat.    

### <a name="faking-the-resource"></a>Az erőforrás faking
Az irányítópult erőforrás létrehozásának első lépése, hogy egy új művelet konfigurálható az API Management publisher portálon. Ez egy helyőrző művelet segítségével konfigurálhatja a dinamikus erőforrás létrehozásához az összeállítás házirend lesz.

![Irányítópult-művelet](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>A kérést
Egyszer a `dashboard` művelet létrejött konfigurálhatjuk egy kifejezetten az adott műveletre vonatkozó házirendet. 

![Irányítópult-művelet](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Az első lépés a lekérdezési paramétereket kibontani a bejövő kérelem, azt is továbbítják őket a háttéralkalmazás. Ebben a példában az irányítópulton látható információk alapján egy adott időn belül egy ezért egy `fromDate` és `toDate` paraméter. Így a `set-variable` házirend kinyerheti az információt a kérelem URL-címről.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Ha ezt az információt kell tökéletesíthetjük kérések a háttér-rendszereken. Minden kérelmet hoz létre egy új URL-címet a paraméter adatokkal, és meghívja a megfelelő kiszolgálóhoz, és a válasz egy környezeti változó tárolja.

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
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Ezek a kérelmek végrehajtja a sorozatban, ami nem ideális. Egy jövőbeli verzióban azt fogja bevezetése nevű új házirend `wait` , amely engedélyezi az összes ezeket a kéréseket végre párhuzamosan.

### <a name="responding"></a>Válaszol
Használhatunk összetett válasz összeállítani a [visszatérési-válasz](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) házirend. A `set-body` elem kifejezés használatával hozható létre egy új `JObject` a beágyazott tulajdonságként összetevő felelősséget.

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
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
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

A helyőrző konfigurációjában konfigurálhatjuk gyorsítótárazható legalább egy órát, mert az adatok jellege tudjuk az irányítópult erőforrás a művelet azt jelenti, hogy akkor is, ha egy órával elavult, azokat is, hogy elég hatékony, hogy átadja értékes További információ a felhasználók számára.

## <a name="summary"></a>Összefoglalás
Az Azure API Management szolgáltatás biztosítja, hogy a HTTP-forgalom szelektív módon alkalmazható rugalmas házirendek, és lehetővé teszi, hogy a háttér szolgáltatások összeállításban. Hogy meg szeretné javítása érdekében a Funkciók, ellenőrzési, érvényesítési képességek riasztási API átjárót, vagy hozzon létre új összetett erőforrások több háttér-szolgáltatásoknak a `send-request` és a lehetőségek tárházát nyissa meg a kapcsolódó házirendek.

## <a name="watch-a-video-overview-of-these-policies"></a>A házirendek áttekintő videó megtekintése
További információ a [küldési-egy-módon-kérelmek](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [küldési-kérelmek](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest), és [visszatérési-válasz](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) a cikkben szereplő házirendek adja a következő videó megtekintése.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Send-Request-and-Return-Response-Policies/player]
> 
> 

