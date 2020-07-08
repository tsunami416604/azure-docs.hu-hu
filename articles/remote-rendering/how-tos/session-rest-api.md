---
title: A munkamenet-felügyeleti REST API
description: A munkamenetek felügyeletének ismertetése
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681245"
---
# <a name="use-the-session-management-rest-api"></a>A munkamenet-kezelési REST API használata

Az Azure távoli renderelési funkciójának használatához létre kell hoznia egy *munkamenetet*. Minden egyes munkamenet megfelel egy virtuális géphez (VM), amely az Azure-ban van lefoglalva, és arra vár, hogy csatlakoztasson egy ügyfélszámítógépet. Amikor egy eszköz csatlakozik a szolgáltatáshoz, a virtuális gép megjeleníti a kért adatforrásokat, és a videó streamként szolgáltatja az eredményt. A munkamenet létrehozása során kiválaszthatja, hogy melyik kiszolgálót szeretné futtatni, amely meghatározza a díjszabást. Ha a munkamenetet már nem szükséges, le kell állítani. Ha a munkamenet *címbérleti ideje* lejár, a rendszer automatikusan leállítja, ha nem állítja be manuálisan.

Biztosítunk egy PowerShell-szkriptet az [ARR Samples adattárában](https://github.com/Azure/azure-remote-rendering) a *Scripts* mappában, amelyet a *RenderingSession.ps1*nevezünk, amely a szolgáltatás használatát mutatja be. A szkriptet és annak konfigurációját itt találja: [példa PowerShell-parancsfájlok](../samples/powershell-example-scripts.md)

> [!TIP]
> A lapon felsorolt PowerShell-parancsok célja, hogy kiegészítsék egymást. Ha az összes parancsfájlt ugyanabban a PowerShell-parancssorban futtatja, akkor azok egymásra épülnek.

## <a name="regions"></a>Régiók

Tekintse meg az [elérhető régiók listáját](../reference/regions.md) az alapurl-címek számára, hogy elküldje a kérelmeket.

Az alábbi minta-szkriptek esetében a *westus2*régiót választottuk.

### <a name="example-script-choose-an-endpoint"></a>Példa parancsfájlra: válasszon végpontot

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Fiókok

Ha nem rendelkezik távoli megjelenítési fiókkal, [hozzon létre egyet](create-an-account.md). Minden erőforrást egy *accountId*azonosít, amelyet a rendszer a munkamenet API-k során használ.

### <a name="example-script-set-accountid-and-accountkey"></a>Példa szkriptre: set accountId és accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Gyakori kérelmek fejlécei

* Az *engedélyezési* fejlécnek "" értékűnek kell lennie `Bearer TOKEN` , ahol a " `TOKEN` " a [biztonságos jogkivonat szolgáltatás által visszaadott](tokens.md)hitelesítési jogkivonat.

### <a name="example-script-request-a-token"></a>Példa szkriptre: jogkivonatot kér

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Gyakori válaszok fejlécei

* A termék csapata az *MS-CV* fejléc használatával nyomon követheti a hívást a szolgáltatáson belül.

## <a name="create-a-session"></a>Munkamenet létrehozása

Ez a parancs létrehoz egy munkamenetet. Az új munkamenet AZONOSÍTÓját adja vissza. Minden más parancshoz szüksége lesz a munkamenet-AZONOSÍTÓra.

| URI | Metódus |
|-----------|:-----------|
| /v1/accounts/*accountId*/Sessions/Create | POST |

**Kérelem törzse:**

* maxLeaseTime (TimeSpan): időtúllépési érték, ha a virtuális gép automatikusan leszerelve lesz
* modellek (Array): eszközök tárolójának URL-címei a Preload számára
* size (karakterlánc): a virtuális gép mérete (**"standard"** vagy **"Prémium"**). Lásd a virtuálisgép- [méretek bizonyos korlátozásait](../reference/limits.md#overall-number-of-polygons).

**Válaszok**

| Állapotkód | JSON-adattartalom | Megjegyzések |
|-----------|:-----------|:-----------|
| 202 | -Munkamenet-azonosító: GUID | Sikeres |

### <a name="example-script-create-a-session"></a>Példa szkriptre: munkamenet létrehozása

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Példa a kimenetre:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Példa szkriptre: Store munkamenet-azonosító

A fenti kérelem válasza tartalmaz egy **munkamenet**-azonosítót, amely az összes követési kérelemhez szükséges.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Munkamenet frissítése

Ez a parancs frissíti a munkamenet paramétereit. Jelenleg csak egy munkamenet címbérleti idejét lehet kiterjeszteni.

> [!IMPORTANT]
> A címbérleti időt mindig a munkamenet kezdete óta teljes idő adja meg. Ez azt jelenti, hogy ha egy órás bérlettel rendelkező munkamenetet hozott létre, és egy másik órára szeretné kiterjeszteni a címbérleti időt, a maxLeaseTime két órára kell frissítenie.

| URI | Metódus |
|-----------|:-----------|
| /v1/accounts/*accountID*/Sessions/*munkamenet* -azonosító | JAVÍTÁS |

**Kérelem törzse:**

* maxLeaseTime (TimeSpan): időtúllépési érték, ha a virtuális gép automatikusan leszerelve lesz

**Válaszok**

| Állapotkód | JSON-adattartalom | Megjegyzések |
|-----------|:-----------|:-----------|
| 200 | | Sikeres |

### <a name="example-script-update-a-session"></a>Példa szkriptre: munkamenet frissítése

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Példa a kimenetre:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Aktív munkamenetek beolvasása

Ez a parancs az aktív munkamenetek listáját adja vissza.

| URI | Metódus |
|-----------|:-----------|
| /v1/accounts/*accountId*/Sessions | GET |

**Válaszok**

| Állapotkód | JSON-adattartalom | Megjegyzések |
|-----------|:-----------|:-----------|
| 200 | -Sessions: munkamenet-tulajdonságok tömbje | a munkamenet-tulajdonságok leírását a "munkamenet-tulajdonságok beolvasása" című szakaszban tekintheti meg. |

### <a name="example-script-query-active-sessions"></a>Példa szkriptre: aktív munkamenetek lekérdezése

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Példa a kimenetre:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Munkamenetek tulajdonságainak beolvasása

Ez a parancs egy munkamenetre vonatkozó adatokat ad vissza, például a virtuális gép állomásnevét.

| URI | Metódus |
|-----------|:-----------|
| /v1/accounts/*accountId*/Sessions/*munkamenet*-/Properties | GET |

**Válaszok**

| Állapotkód | JSON-adattartalom | Megjegyzések |
|-----------|:-----------|:-----------|
| 200 | -üzenet: karakterlánc<br/>-sessionElapsedTime: TimeSpan<br/>-sessionHostname: karakterlánc<br/>-Munkamenet-azonosító: karakterlánc<br/>-sessionMaxLeaseTime: TimeSpan<br/>-sessionSize: Enum<br/>-sessionStatus: Enum | Enum sessionStatus {Start, Ready, Leállítás, leállítva, lejárt, hiba}<br/>Ha az állapot "Error" vagy "lejárt", akkor az üzenet további információkat tartalmaz. |

### <a name="example-script-get-session-properties"></a>Példa szkriptre: munkamenet-tulajdonságok beolvasása

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Példa a kimenetre:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Munkamenet leállítása

Ez a parancs leállítja a munkamenetet. A lefoglalt virtuális gép hamarosan visszaigénylésre kerül.

| URI | Metódus |
|-----------|:-----------|
| /v1/accounts/*accountId*/Sessions/*munkamenet* -azonosító | DELETE |

**Válaszok**

| Állapotkód | JSON-adattartalom | Megjegyzések |
|-----------|:-----------|:-----------|
| 204 | | Sikeres |

### <a name="example-script-stop-a-session"></a>Példa szkriptre: munkamenet leállítása

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Példa a kimenetre:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>További lépések

* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)
