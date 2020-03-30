---
title: A Durable Functions verziói – Azure Functions
description: További információ a Durable Functions verzióiról.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152890"
---
# <a name="durable-functions-versions-overview"></a>A Durable Functions verziók áttekintése

*A Durable Functions* az [Azure Functions](../functions-overview.md) és az [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) bővítménye, amely lehetővé teszi az állapotalapú függvények kiszolgáló nélküli környezetben történő írását. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat. Ha még nem ismeri a Tartós funkciók funkciót, olvassa el az [áttekintő dokumentációt.](durable-functions-overview.md)

## <a name="new-features-in-2x"></a>Új funkciók a 2.x-ben

Ez a szakasz a 2.x verzióban hozzáadott tartós függvények szolgáltatásait ismerteti.

### <a name="durable-entities"></a>Tartós entitások

A Durable Functions 2.x-ben egy új [entitásfunkció-koncepciót](durable-functions-entities.md) vezettünk be.

Az entitásfüggvények a kis állapotdarabok, más néven tartós entitások olvasására és frissítésére szolgáló *műveleteket határozzák meg.* Az orchestrator függvényeihez hasonlóan az entitásfüggvények is speciális eseményindító-típussal rendelkező függvények, *entitáseseményindító*. Az orchestrator függvényeivel ellentétben az entitásfüggvények nem rendelkeznek konkrét kódmegkötések. Az entitásfüggvények explicit módon is kezelik az állapotot, nem pedig implicit módon az állapotot jelölik a vezérlési folyamaton keresztül.

További információ: [a tartós entitások](durable-functions-entities.md) cikk.

### <a name="durable-http"></a>Tartós HTTP

A Durable Functions 2.x-ben bevezettünk egy új [tartós HTTP funkciót,](durable-functions-http-features.md#consuming-http-apis) amely lehetővé teszi:

* Http API-k hívása közvetlenül vezénylési függvények (néhány dokumentált korlátozások).
* Valósítsa meg az automatikus ügyféloldali HTTP 202 állapotlekérdezést.
* Az [Azure felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)beépített támogatása.

További információ: [HTTP-szolgáltatások](durable-functions-http-features.md#consuming-http-apis) ról szóló cikk.

## <a name="migrate-from-1x-to-2x"></a>Áttelepítés 1.x-ről 2.x-re

Ez a szakasz azt ismerteti, hogy miként telepítheti át a meglévő 1.x tartós függvényeket a 2.x verzióra az új szolgáltatások előnyeinek kihasználásához.

### <a name="upgrade-the-extension"></a>A bővítmény frissítése

Telepítse a [Tartós függvények bővítmény](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 2.x-es verzióját a projektben. További információ: [Regisztrálja az Azure Functions kötésbővítményeit.](../functions-bindings-register.md)

### <a name="update-your-code"></a>A kód frissítése

A Durable Functions 2.x számos törési változtatást vezet be. A Durable Functions 1.x alkalmazások kódmódosítások nélkül nem kompatibilisek a Durable Functions 2.x alkalmazásokkal. Ez a szakasz felsorolja azokat a módosításokat, amelyeket az 1.x verziójú függvények 2.x-re való frissítésekor kell végrehajtania.

#### <a name="hostjson-schema"></a>Host.json séma

A Durable Functions 2.x új host.json sémát használ. A fő változások 1.x közé:

* `"storageProvider"`(és `"azureStorage"` az alszakasz) a tárhely-specifikus konfiguráció.
* `"tracing"`a nyomkövetési és naplózási konfigurációhoz.
* `"notifications"`(és `"eventGrid"` az alszakasz) az eseményrács értesítési konfigurációjához.

A [részleteket a Durable Functions host.json referenciadokumentációjában](durable-functions-bindings.md#durable-functions-2-0-host-json) találja.

#### <a name="default-taskhub-name-changes"></a>A taskhub alapértelmezett nevének módosítása

Az 1.x-es verzióban, ha a task hub neve nincs megadva host.json, alapértelmezett "DurableFunctionsHub". A 2.x-es verzióban az alapértelmezett feladatközpont neve most már a függvényalkalmazás nevéből származik. Emiatt, ha nem adott meg feladatközpont nevét a 2.x-re való frissítéskor, a kód új feladatközponttal fog működni, és az összes fedélzeti vezénylési rendszerrel már nem lesz alkalmazás feldolgozása. Ez kerülő megoldásához explicit módon beállíthatja a feladatközpont nevét a "DurableFunctionsHub" v1.x alapértelmezett értékére, vagy követheti a [nulla állásidejű üzembe helyezési útmutatót](durable-functions-zero-downtime-deployment.md) a rendszera repülés közbeni vezénylések törési módosításainak kezelésére vonatkozó részletekért.

#### <a name="public-interface-changes-net-only"></a>Nyilvános felület módosításai (csak.NET)

Az 1.x verzióban a Durable Functions által támogatott különböző _környezetobjektumok_ absztrakt alaposztályokkal rendelkeznek, amelyeket egységtesztelésre szántak. A Durable Functions 2.x részeként ezeket az absztrakt alaposztályokat felületek helyettesítik.

A következő táblázat a főbb változásokat mutatja be:

| 1,x | 2,x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` vagy `IDurableClient` |
| `DurableOrchestrationContext` vagy `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` vagy `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Abban az esetben, ha egy absztrakt alaposztály virtuális metódusokat tartalmazott, `DurableContextExtensions`ezeket a virtuális metódusokat a rendszerben meghatározott kiterjesztett metódusok váltották fel.

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json változások (JavaScript és C# Script)

A Tartós függvények 1.x-ben a `type` `orchestrationClient`vezénylési ügyfélkötés a. A 2.x `durableClient` verzió thasználja helyette.
