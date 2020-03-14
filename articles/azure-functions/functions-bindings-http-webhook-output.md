---
title: HTTP-kimeneti kötések Azure Functions
description: További információ a HTTP-válaszok visszaküldéséről a Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277621"
---
# <a name="azure-functions-http-output-bindings"></a>HTTP-kimeneti kötések Azure Functions

A http-kimeneti kötés használatával válaszolhat a HTTP-kérelem feladójának. Ehhez a kötéshez HTTP-trigger szükséges, és lehetővé teszi az trigger kérelméhez tartozó válasz testreszabását.

A HTTP által aktivált függvények alapértelmezett visszatérési értéke a következő:

- `HTTP 204 No Content` üres törzstel a 2. x és újabb függvényeknél
- `HTTP 200 OK` üres törzstel az 1. x függvényben

## <a name="configuration"></a>Konfiguráció

A következő táblázat a *function. JSON* fájlban beállított kötési konfigurációs tulajdonságokat ismerteti. Az C# osztályok könyvtáraiban nincsenek a *function. JSON* -tulajdonságokkal egyező attribútumok.

|Tulajdonság  |Leírás  |
|---------|---------|
| **type** |`http`értékre kell állítani. |
| **direction** | `out`értékre kell állítani. |
| **név** | A válasz kódjában használt változó neve, vagy `$return` a visszatérési érték használatához. |

## <a name="usage"></a>Használat

HTTP-válasz küldéséhez használja a Language-standard Response mintákat. Vagy C# C# parancsfájl esetén a függvény visszatérési típusának `IActionResult` vagy `Task<IActionResult>`kell lennie. A C#-ben a Return Value attribútum nem kötelező.

A válaszokat például az [trigger példája](./functions-bindings-http-webhook-trigger.md#example)tartalmazza.

## <a name="hostjson-settings"></a>Host.JSON-beállítások

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. A következő példa a Host. JSON fájlt tartalmazza, csak a 2. x + beállításokat a kötéshez. További információ a 2. x verzióban található globális konfigurációs beállításokról: a [Host. JSON dokumentációja Azure functions](functions-host-json.md).

> [!NOTE]
> Az 1. x függvények Host. JSON fájljának hivatkozását lásd: [Host. JSON-dokumentáció Azure functions 1. x-hez](functions-host-json-v1.md#http).

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
| customHeaders|Nincs|Lehetővé teszi egyéni fejlécek beállítását a HTTP-válaszban. Az előző példa hozzáadja a `X-Content-Type-Options` fejlécet a válaszhoz, hogy elkerülje a tartalomtípusok elemzését. |
|dynamicThrottlesEnabled|igaz<sup>\*</sup>|Ha engedélyezve van, ez a beállítás hatására a kérelmek feldolgozási folyamata rendszeres időközönként ellenőrzi a rendszerteljesítmény-számlálókat, például a `connections/threads/processes/memory/cpu/etc`, és ha ezek a számlálók a beépített magas küszöbértéken (80%) vannak, akkor a rendszer a kérelmeket `429 "Too Busy"` válaszként elutasítja, amíg a számláló (k) normál szintre nem tér vissza.<br/><sup>\*</sup> A használati terv alapértelmezett értéke `true`. A dedikált csomag alapértelmezett értéke `false`.|
|hsts|nincs engedélyezve|Ha a `isEnabled` `true`re van állítva, a [.net Core HTTP-alapú szigorú átviteli biztonsági (HSTS) viselkedése](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) kényszerítve van, ahogy az a [`HstsOptions` osztályban](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)van meghatározva. A fenti példában a [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) tulajdonságot is 10 napra állítja be. A `hsts` támogatott tulajdonságai a következők: <table><tr><th>Tulajdonság</th><th>Leírás</th></tr><tr><td>excludedHosts</td><td>Az állomásnevek olyan karakterlánc-tömbje, amelyhez nincs hozzáadva a HSTS fejléce.</td></tr><tr><td>Altartományok belefoglalása</td><td>Logikai érték, amely azt jelzi, hogy engedélyezve van-e a Strict-Transport-Security fejléc includeSubDomain paramétere.</td></tr><tr><td>maxAge</td><td>Karakterlánc, amely a Strict-Transport-Security fejléc Max-Age paraméterét határozza meg.</td></tr><tr><td>Preload</td><td>Logikai érték, amely azt jelzi, hogy engedélyezve van-e a Strict-Transport-Security fejléc Preload paramétere.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|A párhuzamosan végrehajtott HTTP-függvények maximális száma. Ez az érték lehetővé teszi a Egyidejűség szabályozását, ami segíthet az erőforrások kihasználtságának kezelésében. Előfordulhat például, hogy olyan HTTP-függvénnyel rendelkezik, amely nagy mennyiségű rendszererőforrást (memóriát/processzort/szoftvercsatornát) használ, így problémát okoz, ha a párhuzamosság túl magas. Vagy lehet, hogy olyan függvényt használ, amely harmadik féltől származó szolgáltatásnak küldött kimenő kéréseket is tartalmaz, és a hívásokat korlátozni kell. Ezekben az esetekben a szabályozás alkalmazása segíthet. <br/><sup>*</sup> A felhasználási terv alapértelmezett értéke 100. Egy dedikált csomag alapértelmezett értéke nem kötött (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|A függőben lévő kérések maximális száma, amelyek egy adott időpontban vannak tárolva. Ez a korlát olyan kérelmeket tartalmaz, amelyek várólistára kerülnek, de nem indult el, valamint folyamatban van a végrehajtás. Az ezen a korláton túli bejövő kérelmek elutasítása egy 429 "túl elfoglalt" választ tartalmaz. Ez lehetővé teszi, hogy a hívók időalapú újrapróbálkozási stratégiákat alkalmazzanak, és segítséget nyújt a kérelmek maximális késésének szabályozásához is. Ez csak a parancsfájl-gazdagép végrehajtási útvonalán belüli üzenetsor-kezelőt vezérli. Más várólisták, például a ASP.NET kérelmek várólistája továbbra is érvényben marad, és ezt a beállítást nem érinti. <br/><sup>\*</sup> A felhasználási terv alapértelmezett értéke 200. Egy dedikált csomag alapértelmezett értéke nem kötött (`-1`).|
|routePrefix|api|Az útvonal előtagja, amely az összes útvonalra vonatkozik. Az alapértelmezett előtag eltávolításához használjon üres karakterláncot. |

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása HTTP-kérelemből](./functions-bindings-http-webhook-trigger.md)