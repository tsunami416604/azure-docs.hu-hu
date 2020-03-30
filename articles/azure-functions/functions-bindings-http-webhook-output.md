---
title: Az Azure Functions HTTP kimeneti kötései
description: Ismerje meg, hogyan küldhet vissza HTTP-válaszokat az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277621"
---
# <a name="azure-functions-http-output-bindings"></a>Az Azure Functions HTTP kimeneti kötései

A HTTP-kimeneti kötés segítségével válaszoljon a HTTP-kérelem küldőjének. A kötéshez HTTP-triggerre van szükség. Lehetővé teszi a trigger kéréséhez társított válasz testreszabását.

A HTTP által aktivált függvényalapértelmezett visszatérési értéke a következő:

- `HTTP 204 No Content`üres testtel a 2.x vagy magasabb függvényekben
- `HTTP 200 OK`üres testtel az 1.x függvényekben

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban beállított kötési konfigurációs tulajdonságokat ismerteti. C# osztálykönyvtárak esetén nincsenek ezeknek a *function.json* tulajdonságoknak megfelelő attribútumtulajdonságok.

|Tulajdonság  |Leírás  |
|---------|---------|
| **Típus** |A beállításnak `http`a beállítására kell beállítható. |
| **direction** | A beállításnak `out`a beállítására kell beállítható. |
| **név** | A válasz függvénykódjában vagy `$return` a visszatérési érték használatához használt változónév. |

## <a name="usage"></a>Használat

HTTP-válasz küldéséhez használja a szabványos nyelvi válaszmintákat. A C# vagy C# parancsfájlban `IActionResult` adja `Task<IActionResult>`meg a függvény visszatérési típusát vagy a . A C#-ban nem szükséges visszatérési érték attribútum.

Például a válaszok, lásd az [eseményindító példa](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>host.json beállítások

Ez a szakasz a kötéshez a 2.x-es vagy újabb verziókban elérhető globális konfigurációs beállításokat ismerteti. Az alábbi host.json példafájl csak a kötés 2.x+ verzióját tartalmazza. A 2.x-es és az azt meghaladó verziók globális konfigurációs beállításairól az [Azure Functions host.json referencia című témakörében](functions-host-json.md)olvashat bővebben.

> [!NOTE]
> A host.json 1.x függvényében a [host.json témakörben az Azure Functions 1.x host.json hivatkozása](functions-host-json-v1.md#http)látható.

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
| egyénifejlécek|Nincs|Lehetővé teszi egyéni fejlécek beállítását a HTTP-válaszban. Az előző példa `X-Content-Type-Options` hozzáadja a fejlécet a válaszhoz, hogy elkerülje a tartalomtípus-szippantást. |
|dynamicThrottlesEnabled|Igaz<sup>\*</sup>|Ha engedélyezve van, ez a beállítás hatására a `connections/threads/processes/memory/cpu/etc` kérelemfeldolgozási folyamat rendszeresen ellenőrzi a rendszer teljesítményszámlálóit, például, és ha `429 "Too Busy"` ezek a számlálók bármelyike túllépi a beépített magas küszöbértéket (80%), a rendszer válaszokkal utasítja el a kérelmeket, amíg a számláló(k) vissza nem tér a normál szintre.<br/><sup>\*</sup>A felhasználási terv `true`alapértelmezett beállítása a . A Dedikált csomag alapértelmezett `false`beállítása .|
|hsts|nincs engedélyezve|Ha `isEnabled` a `true`beállítás értéke, a [.NET Core HTTP szigorú átviteli biztonság (HSTS) viselkedése](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) az [ `HstsOptions` osztályban](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)meghatározott módon érvényesül. A fenti példa [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) is beállítja a tulajdonság 10 nap. A támogatott `hsts` tulajdonságok a következők: <table><tr><th>Tulajdonság</th><th>Leírás</th></tr><tr><td>kizártHázigazdák</td><td>Olyan állomásnevek karakterlánctömbje, amelyhez a HSTS-fejléc nincs hozzáadva.</td></tr><tr><td>includeSubDomains</td><td>Logikai érték, amely azt jelzi, hogy a Szigorú átvitel-biztonság fejléc includeSubDomain paramétere engedélyezve van-e.</td></tr><tr><td>maxAge (maxAge)</td><td>A Szigorú átvitel-biztonság fejléc maximális életkorparaméterét meghatározó karakterlánc.</td></tr><tr><td>preload</td><td>Logikai érték, amely azt jelzi, hogy a Szigorú átvitel-biztonság fejléc előterhelési paramétere engedélyezve van-e.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|A párhuzamosan végrehajtott HTTP-függvények maximális száma. Ez az érték lehetővé teszi az egyidejűség szabályozását, amely segíthet az erőforrás-kihasználtság kezelésében. Előfordulhat például, hogy van egy HTTP-függvény, amely nagyszámú rendszererőforrást (memória/cpu/socket) használ, így problémákat okoz, ha az egyidejűség túl magas. Vagy előfordulhat, hogy egy funkció, amely kimenő kérelmeket egy harmadik fél szolgáltatás, és ezeket a hívásokat kell korlátozni. Ezekben az esetekben a fojtószelep alkalmazása itt segíthet. <br/><sup>*</sup>A felhasználási csomag alapértelmezett beállítása 100. A Dedikált csomag alapértelmezett beállítása nincs`-1`korlátozás nélkül ( ).|
|maxOutstandingRequests|200<sup>\*</sup>|Az adott időpontban lefolytatott kérelmek maximális száma. Ez a korlát tartalmazza a várólistára helyezett, de még nem elkezdett végrehajtást, valamint a folyamatban lévő végrehajtások. Az ezen a korláton felülérkező bejövő kérelmeket a rendszer 429 "Túl elfoglalt" választ küld. Ez lehetővé teszi a hívók számára, hogy időalapú újrapróbálkozási stratégiákat alkalmazzanak, és segít a maximális kérelem-késések szabályozásához. Ez csak a parancsfájlgazda végrehajtási útvonalán belül előforduló sorban állásokat szabályozza. Más várólisták, például a ASP.NET kérési várólista továbbra is érvényben maradnak, és ez a beállítás nem érinti őket. <br/><sup>\*</sup>A felhasználási csomag alapértelmezett beállítása 200. A Dedikált csomag alapértelmezett beállítása nincs`-1`korlátozás nélkül ( ).|
|routePrefix|api-t|Az összes útvonalra vonatkozó útvonalelőtag. Az alapértelmezett előtag eltávolításához használjon üres karakterláncot. |

## <a name="next-steps"></a>További lépések

- [Függvény futtatása HTTP-kérelemből](./functions-bindings-http-webhook-trigger.md)