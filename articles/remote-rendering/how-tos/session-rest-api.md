---
title: A munkamenet-kezelés REST API-ja
description: A munkamenetek kezelésének ismertetése
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681245"
---
# <a name="use-the-session-management-rest-api"></a>A munkamenet-kezelés REST API-jának használata

Az Azure remote rendering funkció használatához létre kell hoznia egy *munkamenetet.* Minden munkamenet egy virtuális gépnek (VM) felel meg, amelyet az Azure-ban foglal le, és amely egy ügyféleszköz csatlakoztatására vár. Amikor egy eszköz csatlakozik, a virtuális gép megjeleníti a kért adatokat, és az eredményt videoadat-adatként jeleníti meg. A munkamenet létrehozása során kiválasztotta, hogy milyen típusú kiszolgálón kíván futni, amely meghatározza az árképzést. Ha a munkamenet már nincs szükség, meg kell állítani. Ha nem állítja le manuálisan, akkor automatikusan leáll, amikor a munkamenet *bérleti ideje* lejár.

PowerShell-parancsfájlt biztosítunk a *Scripts* mappában található [ARR-minták tárházában,](https://github.com/Azure/azure-remote-rendering) a *RenderingSession.ps1*mappában, amely bemutatja a szolgáltatás használatát. A parancsfájl és a konfiguráció itt ismertetésre: [Példa PowerShell-parancsfájlok](../samples/powershell-example-scripts.md)

> [!TIP]
> Az ezen a lapon felsorolt PowerShell-parancsok egymás kiegészítésére vannak kiírva. Ha az összes parancsfájlt egymás után futtatja ugyanazon a PowerShell-parancssorban, azok egymásra épülnek.

## <a name="regions"></a>Régiók

Tekintse meg az [elérhető régiók listáját](../reference/regions.md) az alap URL-címek a kérelmek et küldeni.

Az alábbi mintaszkriptek esetében a *westus2*régiót választottuk.

### <a name="example-script-choose-an-endpoint"></a>Példa parancsfájl: Válasszon egy végpontot

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Fiókok

Ha nem rendelkezik távoli renderelési fiókkal, [hozzon létre egyet.](create-an-account.md) Minden erőforrást egy *fiókazonosító*azonosít, amely a munkamenet API-k egészében használatos.

### <a name="example-script-set-accountid-and-accountkey"></a>Példa parancsfájl: Fiókazonosító és accountKey beállítása

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Gyakori kérelemfejlécek

* Az *engedélyezési* fejléc nek "`Bearer TOKEN`",`TOKEN`ahol " " értékűnek kell lennie a Secure Token Service által visszaadott hitelesítési jogkivonatnak. [returned by the Secure Token Service](tokens.md)

### <a name="example-script-request-a-token"></a>Példa parancsfájl: Token kérése

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Gyakori válaszfejlécek

* Az *MS-CV* fejlécet a termékcsapat használhatja a szolgáltatáson belüli hívás nyomon követésére.

## <a name="create-a-session"></a>Munkamenet létrehozása

Ez a parancs munkamenetet hoz létre. Az új munkamenet azonosítóját adja vissza. Az összes többi parancshoz szükség van a munkamenet-azonosítóra.

| URI | Módszer |
|-----------|:-----------|
| /v1/fiókok/*accountId*/sessions/create | POST |

**Kérelem törzse:**

* maxLeaseTime (időtartomány): időtúlérték, amikor a virtuális gép automatikusan lelesz szerelve
* modellek (tömb): eszköztároló URL-címei az előterheléshez
* méret (karakterlánc): a virtuális gép mérete (**"standard"** vagy **"prémium").** Lásd a [virtuális gép méretére vonatkozó korlátozásokat.](../reference/limits.md#overall-number-of-polygons)

**Válaszok:**

| Állapotkód | JSON hasznos teher | Megjegyzések |
|-----------|:-----------|:-----------|
| 202 | - sessionId: GUID | Sikeres |

### <a name="example-script-create-a-session"></a>Példa parancsfájl: Munkamenet létrehozása

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

### <a name="example-script-store-sessionid"></a>Példa parancsfájl: Munkamenet-azonosító tárolása

A fenti kérés válasza tartalmaz egy **munkamenet-azonosítót,** amelyre minden követési kérelemhez szüksége van.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Munkamenet frissítése

Ez a parancs frissíti a munkamenet paramétereit. Jelenleg csak a munkamenet bérleti idejét hosszabbíthatja meg.

> [!IMPORTANT]
> A bérleti idő mindig a munkamenet kezdete óta eltelt teljes időként van megadva. Ez azt jelenti, hogy ha egy órás bérleti idővel létrehozott egy munkamenetet, és további egy órával szeretné meghosszabbítani a bérleti időt, frissítenie kell a maxLeaseTime-ot két órára.

| URI | Módszer |
|-----------|:-----------|
| /v1/accounts/*accountID**/session/sessionId* | Javítás |

**Kérelem törzse:**

* maxLeaseTime (időtartomány): időtúlérték, amikor a virtuális gép automatikusan lelesz szerelve

**Válaszok:**

| Állapotkód | JSON hasznos teher | Megjegyzések |
|-----------|:-----------|:-----------|
| 200 | | Sikeres |

### <a name="example-script-update-a-session"></a>Példa parancsfájl: Munkamenet frissítése

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

## <a name="get-active-sessions"></a>Aktív munkamenetek beszereznie

Ez a parancs az aktív munkamenetek listáját adja vissza.

| URI | Módszer |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | GET |

**Válaszok:**

| Állapotkód | JSON hasznos teher | Megjegyzések |
|-----------|:-----------|:-----------|
| 200 | - munkamenetek: munkamenet-tulajdonságok tömbje | A munkamenet-tulajdonságok leírását a "Munkamenet-tulajdonságok beszerezni" című szakaszban |

### <a name="example-script-query-active-sessions"></a>Példa parancsfájl: Aktív munkamenetek lekérdezése

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

## <a name="get-sessions-properties"></a>Munkamenetek tulajdonságainak beszereznie

Ez a parancs egy munkamenetadatait adja vissza, például a virtuális gép állomásnevét.

| URI | Módszer |
|-----------|:-----------|
| /v1/accounts/*accountId*/session/*sessionId*/properties | GET |

**Válaszok:**

| Állapotkód | JSON hasznos teher | Megjegyzések |
|-----------|:-----------|:-----------|
| 200 | - üzenet: karakterlánc<br/>- sessionElapsedTime: timespan<br/>- sessionHostname: karakterlánc<br/>- sessionId: karakterlánc<br/>- sessionMaxLeaseTime: timespan<br/>- sessionSize: enum<br/>- sessionStatus: enum | enum sessionStatus { indítás, kész, leállítás, leállítás, lejárt, hiba}<br/>Ha az állapot "hiba" vagy "lejárt", az üzenet további információkat fog tartalmazni |

### <a name="example-script-get-session-properties"></a>Példa parancsfájl: Munkamenet-tulajdonságok beszerezni

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

Ez a parancs leállít egy munkamenetet. A lefoglalt virtuális gép nem sokkal később visszalesz szerezve.

| URI | Módszer |
|-----------|:-----------|
| /v1/accounts/*accountId**/session/sessionId* | DELETE |

**Válaszok:**

| Állapotkód | JSON hasznos teher | Megjegyzések |
|-----------|:-----------|:-----------|
| 204 | | Sikeres |

### <a name="example-script-stop-a-session"></a>Példa parancsfájl: Munkamenet leállítása

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

* [Példa PowerShell-parancsfájlokra](../samples/powershell-example-scripts.md)
