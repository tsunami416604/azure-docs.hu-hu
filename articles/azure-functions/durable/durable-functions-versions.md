---
title: Durable Functions verziók áttekintése – Azure Functions
description: További információ a Durable Functions verziókról.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77152890"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions verziók áttekintése

A *Durable Functions* [Azure functions](../functions-overview.md) és [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) bővítménye, amely lehetővé teszi az állapot-nyilvántartó függvények írását kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat. Ha még nem ismeri a Durable Functionst, tekintse meg az [Áttekintés dokumentációját](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Új funkciók a 2. x-ben

Ez a szakasz a 2. x verzióban hozzáadott Durable Functions funkcióit ismerteti.

### <a name="durable-entities"></a>Tartós entitások

Durable Functions 2. x verzióban egy új [Entity functions](durable-functions-entities.md) koncepciót vezettünk be.

Az Entity functions olyan műveleteket határoz meg, amelyek olyan kis méretű állapotok olvasására és frissítésére szolgálnak, amelyek *tartós entitások*. A Orchestrator függvényekhez hasonlóan az Entity functions is egy speciális trigger típussal, az *entitások triggerével*működik. A Orchestrator függvényektől eltérően az Entity functions nem rendelkezik konkrét kód megkötésekkel. Az Entity functions emellett explicit módon kezeli az állapotot, nem pedig implicit módon jelképezi az állapotot a vezérlési folyamaton keresztül.

További információt a [tartós entitások](durable-functions-entities.md) című cikkben talál.

### <a name="durable-http"></a>Tartós HTTP

Durable Functions 2. x verzióban egy új, [tartós http](durable-functions-http-features.md#consuming-http-apis) -funkciót vezettünk be, amely a következőket teszi lehetővé:

* HTTP API-k hívása közvetlenül a hangkezelő függvényektől (néhány dokumentált korlátozással).
* Automatikus ügyféloldali HTTP 202-állapot lekérdezésének implementálása.
* Beépített támogatás az Azure által [felügyelt identitásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

További információt a [http-szolgáltatások](durable-functions-http-features.md#consuming-http-apis) című cikkben talál.

## <a name="migrate-from-1x-to-2x"></a>Migrálás 1. x és 2. x között

Ez a szakasz azt ismerteti, hogyan telepítheti át a meglévő 1. x verziójú Durable Functions a 2. x verzióra, hogy kihasználhassa az új funkciókat.

### <a name="upgrade-the-extension"></a>A bővítmény frissítése

Telepítse a projektben a [Durable functions kötések bővítmény](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 2. x verzióját. További információért lásd: [Azure functions kötési bővítmények regisztrálása](../functions-bindings-register.md) .

### <a name="update-your-code"></a>A kód frissítése

Durable Functions 2. x több feltörési változást vezet be. Durable Functions 1. x alkalmazások kód módosítása nélkül nem kompatibilisek a 2. x Durable Functions. Ez a szakasz azokat a módosításokat sorolja fel, amelyeket az 1. x függvények 2. x verzióra való frissítésekor kell végeznie.

#### <a name="hostjson-schema"></a>Host. JSON séma

Durable Functions 2. x egy új Host. JSON sémát használ. Az 1. x fő változásai a következők:

* `"storageProvider"`(és az `"azureStorage"` alszakasz) a Storage-specifikus konfigurációhoz.
* `"tracing"`nyomkövetési és naplózási konfigurációhoz.
* `"notifications"`(és az `"eventGrid"` alszakasz) az Event Grid-értesítési konfigurációhoz.

A részletekért tekintse meg a [Durable functions Host. JSON dokumentációját](durable-functions-bindings.md#durable-functions-2-0-host-json) .

#### <a name="default-taskhub-name-changes"></a>A taskhub alapértelmezett nevének módosítása

Ha az 1. x verzióban nincs megadva a Task hub neve a Host. JSON fájlban, az alapértelmezett érték a "DurableFunctionsHub". A 2. x verzióban a feladat-központ alapértelmezett neve mostantól a Function alkalmazás nevéből származik. Emiatt, ha a 2. x verzióra való frissítéskor nem adott meg a Task hub nevét, a kód az új Task hub-vel fog működni, és az összes fedélzeti előkészítési folyamat már nem fog működni az alkalmazásban. Ennek megkerülő megoldásához explicit módon állíthatja be a Task hub nevét az "DurableFunctionsHub" v1. x alapértelmezett értékére, vagy követheti a [nulla állásidővel kapcsolatos üzembe helyezési útmutatót](durable-functions-zero-downtime-deployment.md) , amely részletesen ismerteti, hogyan kezelheti a működés közbeni előkészítési folyamatokat.

#### <a name="public-interface-changes-net-only"></a>Nyilvános interfész módosításai (csak .NET)

Az 1. x verzióban a Durable Functions által támogatott különböző _környezeti_ objektumok absztrakt alaposztályokkal rendelkeznek, amelyek az egység tesztelése során használhatók. Durable Functions 2. x részeként ezeket az absztrakt alaposztályokat a rendszer az illesztőfelületekkel helyettesíti.

A fő módosításokat a következő táblázat mutatja be:

| 1. x | 2. x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` vagy `IDurableClient` |
| `DurableOrchestrationContext` vagy `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` vagy `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Abban az esetben, ha egy absztrakt alaposztály virtuális metódusokat tartalmaz, ezeket a virtuális metódusokat a ben `DurableContextExtensions`definiált kiterjesztési módszerek váltották fel.

#### <a name="functionjson-changes-javascript-and-c-script"></a>function. JSON-változások (JavaScript és C# parancsfájl)

Durable Functions 1. x esetén a hanghangoló ügyfél-kötés a `type` - `orchestrationClient`t használja. Ehelyett a 2. x `durableClient` verziót használja.
