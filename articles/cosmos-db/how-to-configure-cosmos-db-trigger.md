---
title: Azure Functions trigger Cosmos DB speciális konfigurációhoz
description: Megtudhatja, hogyan konfigurálhatja a Azure Functions trigger által használt naplózási és kapcsolódási házirendet Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: 8e2b74f95f24e107cb395686fe6dd1c96566bb08
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284174"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>A naplózás és a kapcsolat konfigurálása a Cosmos DB Azure Functions triggerrel

Ez a cikk a Cosmos DB Azure Functions-triggerének használatakor beállítható speciális konfigurációs beállításokat ismerteti.

## <a name="enabling-trigger-specific-logs"></a>Trigger-specifikus naplók engedélyezése

A Cosmos DB Azure Functions-trigger a következőt használja: a [feed Processor Library módosítása](./change-feed-processor.md) . a könyvtár a belső műveletek [hibaelhárítási célból](./troubleshoot-changefeed-functions.md)történő figyelésére szolgáló rendszerállapot-naplókat állít elő.

Az állapotadatok azt írják le, hogy a Cosmos DB Azure Functions triggere hogyan viselkedik a műveletek a terheléselosztási forgatókönyvek vagy az inicializálás során való megkísérlése során.

### <a name="enabling-logging"></a>Naplózás engedélyezése

A naplózás engedélyezéséhez Cosmos DB Azure Functions trigger használata esetén keresse meg a `host.json` fájlt a Azure functions projektben vagy Azure functions alkalmazásban, és [konfigurálja a szükséges naplózás szintjét](../azure-functions/functions-monitoring.md#log-levels-and-categories). Engedélyezze a nyomkövetést a  `Host.Triggers.CosmosDB` következő mintában látható módon:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Miután telepítette az Azure-függvényt a frissített konfigurációval, a Nyomkövetések részeként megjelenik a Cosmos DB naplók Azure Functions triggere. A megadott naplózási szolgáltató naplóit a *Kategória* alatt tekintheti meg `Host.Triggers.CosmosDB` .

### <a name="query-the-logs"></a>Naplók lekérdezése

A következő lekérdezés futtatásával lekérdezheti az [Azure Application Insights Analytics szolgáltatásban](../azure-monitor/app/analytics.md)Cosmos db Azure functions trigger által létrehozott naplókat:

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>A kapcsolatok házirendjének konfigurálása

Két kapcsolattípus van – közvetlen mód és átjáró mód. Ha többet szeretne megtudni ezekről a kapcsolódási módokról, tekintse meg a [kapcsolódási módokat](sql-sdk-connection-modes.md) ismertető cikket. Alapértelmezés szerint az **átjáró** a Cosmos db Azure functions trigger összes kapcsolatának létrehozására szolgál. Előfordulhat azonban, hogy nem a legjobb megoldás a teljesítmény-vezérelt forgatókönyvek esetében.

### <a name="changing-the-connection-mode-and-protocol"></a>A kapcsolati mód és a protokoll módosítása

Az ügyfélkapcsolati házirend konfigurálásához két kulcsfontosságú konfigurációs beállítás érhető el – a **kapcsolati mód** és a **kapcsolati protokoll**. A Azure Functions trigger által használt alapértelmezett kapcsolati mód és protokoll a Cosmos DB és az összes [Azure Cosmos db kötés](../azure-functions/functions-bindings-cosmosdb-v2-output.md)esetében módosítható. Az alapértelmezett beállítások módosításához meg kell keresnie a fájlt a `host.json` Azure functions projektben vagy Azure functions alkalmazásban, és hozzá kell adnia a következő [extra beállítást](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Ahol `connectionMode` a kötelező kapcsolati mód (közvetlen vagy átjáró) és `protocol` a kívánt kapcsolati protokoll (TCP vagy https) szükséges. 

Ha a Azure Functions-projekt a Azure Functions v1 futtatókörnyezettel dolgozik, a konfigurációnak kisebb a neve, akkor a következők `documentDB` helyett érdemes használni `cosmosDB` :

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Azure Functions használati terv üzemeltetési tervének használatakor az egyes példányok esetében a szoftvercsatorna-kapcsolatok mennyisége korlátozva lehet. A közvetlen/TCP mód használata esetén több kapcsolat jön létre, és elérheti a használati [terv korlátját](../azure-functions/manage-connections.md#connection-limit). ebben az esetben használhatja az átjáró üzemmódot, vagy [app Service módban](../azure-functions/functions-scale.md#app-service-plan)futtathatja a Azure functions.

## <a name="next-steps"></a>Következő lépések

* [A Azure Functions a kapcsolatok korlátai](../azure-functions/manage-connections.md#connection-limit)
* A [figyelés engedélyezése](../azure-functions/functions-monitoring.md) a Azure functions alkalmazásokban.
* Megtudhatja, hogyan [diagnosztizálhatja és elháríthatja a gyakori problémákat](./troubleshoot-changefeed-functions.md) a Cosmos db Azure functions triggerének használatakor.