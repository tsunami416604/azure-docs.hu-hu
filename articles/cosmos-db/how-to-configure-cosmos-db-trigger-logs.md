---
title: Naplók konfigurálása és olvasása Azure Functions triggerrel Cosmos DB
description: Megtudhatja, hogyan teheti elérhetővé a naplókat a Azure Functions naplózási folyamatához Azure Functions trigger használatakor Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441834"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Naplók konfigurálása és olvasása a Azure Functions trigger használatakor Cosmos DB

Ez a cikk azt ismerteti, hogyan konfigurálhatja a Azure Functions környezetét, hogy elküldje a Azure Functions triggert Cosmos DB naplók számára a konfigurált [figyelési megoldásba](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Belefoglalt naplók

A Cosmos DB Azure Functions-trigger a következőt használja: a [feed Processor Library módosítása](./change-feed-processor.md) . a könyvtár a belső műveletek [hibaelhárítási célból](./troubleshoot-changefeed-functions.md)történő figyelésére szolgáló rendszerállapot-naplókat állít elő.

Az állapotadatok azt írják le, hogy a Cosmos DB Azure Functions triggere hogyan viselkedik a műveletek a terheléselosztási forgatókönyvek vagy az inicializálás során való megkísérlése során.

## <a name="enabling-logging"></a>Naplózás engedélyezése

A naplózás engedélyezéséhez Cosmos DB Azure Functions trigger használata esetén keresse meg a `host.json` fájlt a Azure Functions projektben vagy Azure Functions alkalmazásban, és [konfigurálja a szükséges naplózás szintjét](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Az alábbi mintában látható módon engedélyeznie kell a `Host.Triggers.CosmosDB` nyomkövetését:

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

Miután telepítette az Azure-függvényt a frissített konfigurációval, a Nyomkövetések részeként megjelenik a Cosmos DB naplók Azure Functions triggere. Az `Host.Triggers.CosmosDB`*kategóriába* tartozó naplókat a konfigurált naplózási szolgáltatóban tekintheti meg.

## <a name="query-the-logs"></a>Naplók lekérdezése

A következő lekérdezés futtatásával lekérdezheti az [Azure Application Insights Analytics szolgáltatásban](../azure-monitor/app/analytics.md)Cosmos db Azure functions trigger által létrehozott naplókat:

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Következő lépések

* A [figyelés engedélyezése](../azure-functions/functions-monitoring.md) a Azure functions alkalmazásokban.
* Megtudhatja, hogyan [diagnosztizálhatja és elháríthatja a gyakori problémákat](./troubleshoot-changefeed-functions.md) a Cosmos db Azure functions triggerének használatakor.