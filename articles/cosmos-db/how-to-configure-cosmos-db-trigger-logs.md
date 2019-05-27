---
title: Az Azure Cosmos DB eseményindító-naplói
description: Ismerje meg, hogyan teszi közzé az Azure Cosmos DB-eseményindító bejegyzéseit, amelyek az Azure Functions-naplózás folyamat
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: maquaran
ms.openlocfilehash: bf5216dc3b296c98176387c6e2cfff7c31daedab
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241021"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Eseménynaplók konfigurálása, és olvassa el az Azure Cosmos DB-eseményindító

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Functions-környezet az Azure Cosmos DB-eseményindító naplókat küld a konfigurált [figyelési megoldás](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Belefoglalt naplók

Az Azure Cosmos DB-eseményindító használja a [módosítási hírcsatorna feldolgozói kódtára](./change-feed-processor.md) belső használatra, és a tár létrehoz egy belső műveleteinek figyeléséhez használható egészségügyi naplók [hibaelhárítási célból](./troubleshoot-changefeed-functions.md).

Az egészségügyi naplók ismertetik, hogyan viselkedik az Azure Cosmos DB-eseményindító, terheléselosztási forgatókönyveket vagy az inicializálás során a műveletek megkísérlésekor.

## <a name="enabling-logging"></a>Naplózás engedélyezése

Keresse meg az Azure Cosmos DB-eseményindító naplózás engedélyezéséhez a `host.json` fájlt az Azure Functions-projektet vagy az Azure Functions és [szükséges naplózási szint konfigurálása](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). A nyomkövetések számára engedélyeznie kell a `Host.Triggers.CosmosDB` az alábbi mintában látható módon:

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

Az Azure-függvény üzembe helyezése a frissített konfigurációval, után látni fogja az Azure Cosmos DB-eseményindító naplók trasování részeként. A konfigurált naplózás szolgáltató alatt tekintheti meg a naplókat a *kategória* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Naplók lekérdezése

Futtassa a következő lekérdezést a lekérdezés a naplók által létrehozott az Azure Cosmos DB-eseményindító a [Azure Application Insights Analytics](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>További lépések

* [Engedélyezze a monitorozást](../azure-functions/functions-monitoring.md) az Azure Functions alkalmazásokban.
* Ismerje meg, hogyan [diagnosztizálása és a gyakori problémák megoldása](./troubleshoot-changefeed-functions.md) Azure Functions az Azure Cosmos DB-eseményindító használata során.