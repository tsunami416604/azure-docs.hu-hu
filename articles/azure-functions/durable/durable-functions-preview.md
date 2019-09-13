---
title: Durable Functions előzetes verzió funkciói – Azure Functions
description: További információ a Durable Functions előzetes verziójának szolgáltatásairól.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933241"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 előzetes verzió (Azure Functions)

A *Durable Functions* [Azure functions](../functions-overview.md) és [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) bővítménye, amely lehetővé teszi az állapot-nyilvántartó függvények írását kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat. Ha még nem ismeri a Durable Functionst, tekintse meg az [Áttekintés dokumentációját](durable-functions-overview.md).

Durable Functions 1. x a Azure Functions GA (általánosan elérhető) funkciója, de több olyan alszolgáltatást is tartalmaz, amelyek jelenleg nyilvános előzetes verzióban érhetők el. Ez a cikk az újonnan kiadott előzetes verziójú funkciókat ismerteti, és részletesen bemutatja, hogyan használhatók a működésük, és hogyan kezdhetik meg a használatot.

> [!NOTE]
> Ezek az előzetes verziójú funkciók egy Durable Functions 2,0 kiadás részét képezik, amely jelenleg az **előzetes verzió minőségi kiadása** , amely több feltörési változást tartalmaz. A Azure Functions tartós bővítmények csomagja a nuget.org **2.0.0-betaX**formátumú verziókkal érhető el. Ezek a buildek nem üzemi számítási feladatokhoz készültek, és a későbbi kiadásokban további feltörési változások is előfordulhatnak.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

A Durable Functions 2,0-es verzióban több törési változást is bevezetünk. A meglévő alkalmazások nem lesznek kompatibilisek a Durable Functions 2,0-mel a kód módosítása nélkül. Ez a szakasz néhány változást felsorol:

### <a name="hostjson-schema"></a>Host. JSON séma

A következő kódrészlet a Host. JSON új sémáját mutatja be. Az új alszakaszok a legfontosabb változások, hogy tisztában legyenek a következőkkel:

* `"storageProvider"`(és az `"azureStorage"` alszakasz) a Storage-specifikus konfigurációhoz
* `"tracking"`a nyomon követés és a naplózás konfigurálásához
* `"notifications"`(és az `"eventGrid"` alszakasz) az Event Grid értesítési konfigurációjához

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Mivel a Durable functions 2,0 továbbra is stabilizálódik, a `durableTask` Host. JSON szakasz további módosításait fogja bevezetni. A változásokkal kapcsolatos további információkért tekintse meg [ezt a GitHub-problémát](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Nyilvános interfész módosításai

A Durable Functions által támogatott különböző környezeti objektumok absztrakt alaposztályok voltak, amelyek az egység tesztelése során használhatók. A Durable Functions 2,0 részeként ezek az absztrakt alaposztályok le lettek cserélve a csatolókkal. A konkrét típusokat használó függvény kódját nem érinti a rendszer.

A fő módosításokat a következő táblázat mutatja be:

| Régi típus | Új típus |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

Abban az esetben, ha egy absztrakt alaposztály virtuális metódusokat tartalmaz, ezeket a virtuális metódusokat a ben `DurableContextExtensions`definiált kiterjesztési módszerek váltották fel.

## <a name="entity-functions"></a>Entitás-függvények

A Durable Functions v 2.0.0-Alpha verziótól kezdődően egy új [Entity functions](durable-functions-entities.md) koncepciót vezettünk be.

Az Entity functions olyan műveleteket határoz meg, amelyek olyan kis méretű állapotok olvasására és frissítésére szolgálnak, amelyek *tartós entitások*. A Orchestrator függvényekhez hasonlóan az Entity functions is egy speciális trigger típussal, az *entitások triggerével*működik. A Orchestrator függvényektől eltérően az Entity functions nem rendelkezik konkrét kód megkötésekkel. Az Entity functions emellett explicit módon kezeli az állapotot, nem pedig implicit módon jelképezi az állapotot a vezérlési folyamaton keresztül.

A kezdeti felhasználói visszajelzések alapján később a Durable Functions v 2.0.0 – béta verzióban található entitásokhoz tartozó osztály-alapú programozási modell támogatását is hozzáadjuk.

További információt az [Entity functions](durable-functions-entities.md) című cikkben talál.

## <a name="durable-http"></a>Tartós HTTP

A Durable Functions v 2.0.0-Beta2 kezdődően egy új, [tartós http](durable-functions-http-features.md) -funkciót vezettünk be, amely a következőket teszi lehetővé:

* HTTP API-k hívása közvetlenül a hangkezelő függvényektől (néhány dokumentált korlátozással)
* Automatikus ügyféloldali HTTP 202-állapot lekérdezésének megvalósítása
* Beépített támogatás az Azure által [felügyelt identitásokhoz](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

További információt a [http-szolgáltatások](durable-functions-http-features.md#consuming-http-apis) című cikkben talál.

## <a name="alternate-storage-providers"></a>Alternatív tároló szolgáltatók

A tartós feladathoz tartozó keretrendszer több tárolási szolgáltatót is támogat, [](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)beleértve az [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)-t, a Azure Service Bust, [a memóriában lévő emulátort](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)és egy kísérleti [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) -szolgáltatót. Eddig azonban a Azure Functions tartós feladatának kiterjesztése csak az Azure Storage-szolgáltatót támogatja. A Durable Functions 2,0-től kezdve a helyettesítő tároló-szolgáltatók támogatása a Redis-szolgáltatótól kezdődően történik.

> [!NOTE]
> A Durable Functions 2,0 csak a .NET Standard 2,0-kompatibilis szolgáltatók használatát támogatja.

### <a name="emulator"></a>Emulátor

A [DurableTask. Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) szolgáltató egy helyi memória, nem tartós tárolási szolgáltató, amely helyi tesztelési forgatókönyvekhez alkalmas. A következő minimális **gazdagép. JSON** séma használatával konfigurálható:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (kísérleti)

A [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) szolgáltató az összes előkészítési állapotot egy konfigurált Redis-fürtön tartja fenn.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

Az `connectionStringName` alkalmazásnak egy Alkalmazásbeállítások vagy környezeti változó nevére kell hivatkoznia. Az Alkalmazásbeállítások vagy a környezeti változónak a Redis-karakterlánc értékének kell lennie a *kiszolgáló: Port*formában. Például `localhost:6379` egy helyi Redis-fürthöz való csatlakozáshoz.

> [!NOTE]
> A Redis szolgáltató jelenleg kísérleti jellegű, és csak egyetlen csomóponton futó Function apps-alkalmazásokat támogatja. Nem garantált, hogy a Redis szolgáltató általánosan elérhetővé válik, és előfordulhat, hogy egy későbbi kiadásban is el lesz távolítva.
