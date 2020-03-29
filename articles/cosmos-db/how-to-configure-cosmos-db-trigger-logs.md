---
title: Naplók konfigurálása és olvasása az Azure Functions eseményindítóval a Cosmos DB-hez
description: Ismerje meg, hogyan teheti elérhetővé a naplókat az Azure Functions naplózási folyamatának az Azure Functions trigger cosmos DB használatával
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441834"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>A naplók konfigurálása és olvasása az Azure Functions trigger használatakor a Cosmos DB-hez

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Functions környezetben, hogy küldje el az Azure Functions eseményindító cosmos DB naplók a konfigurált [figyelési megoldás.](../azure-functions/functions-monitoring.md)

## <a name="included-logs"></a>Mellékelt naplók

A Cosmos DB Azure Functions eseményindítója belsőleg használja a [Figyelőprocesszor-könyvtár módosítása](./change-feed-processor.md) funkciót, és a könyvtár állapotnaplókat hoz létre, amelyek [hibaelhárítási célból](./troubleshoot-changefeed-functions.md)a belső műveletek figyelésére használhatók.

Az állapotnaplók ismertetik, hogyan viselkedik az Azure Functions a Cosmos DB eseményindítója, amikor a terheléselosztási forgatókönyvek vagy inicializálás során műveleteket kísérel meg.

## <a name="enabling-logging"></a>Naplózás engedélyezése

Ha engedélyezni szeretné a naplózást az Azure `host.json` Functions eseményindító használatakor a Cosmos DB számára, keresse meg a fájlt az Azure Functions projektben vagy az Azure Functions alkalmazásban, és [konfigurálja a szükséges naplózás szintjét.](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson) Engedélyeznie kell a nyomkövetéseket `Host.Triggers.CosmosDB` a következő minta szerint:

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

Miután az Azure-függvény telepítve van a frissített konfigurációval, látni fogja az Azure Functions eseményindító a Cosmos DB naplók a nyomkövetések részeként. A naplókat a beállított naplózási szolgáltatóban a Kategória területen *tekintheti* `Host.Triggers.CosmosDB`meg.

## <a name="query-the-logs"></a>A naplók lekérdezése

Futtassa a következő lekérdezést az Azure Functions eseményindító jaa a Cosmos DB által az Azure Application Insights' Analytics szolgáltatásban létrehozott naplók [lekérdezéséhez:](../azure-monitor/app/analytics.md)

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>További lépések

* [Engedélyezze a figyelést](../azure-functions/functions-monitoring.md) az Azure Functions-alkalmazásokban.
* Ismerje meg, hogyan [diagnosztizálhatja és háríthatja el](./troubleshoot-changefeed-functions.md) a gyakori problémákat az Azure Functions eseményindító használatakor a Cosmos DB használatával.