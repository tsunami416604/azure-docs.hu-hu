---
title: Az Azure-függvények figyelése az Azure Figyelő naplókkal
description: Ismerje meg, hogyan használhatja az Azure Monitor Naplók az Azure Functions funkció-végrehajtások figyelése.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649874"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Az Azure-függvények figyelése az Azure Figyelő naplókkal

Az Azure Functions integrációt kínál az [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md) szolgáltatással a függvények figyeléséhez. Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Functions rendszer által létrehozott és a felhasználó által létrehozott naplók at az Azure Monitor naplók.

Az Azure Monitor Naplók lehetővé teszi, hogy konszolidálja naplók különböző erőforrások ugyanabban a munkaterületen, ahol elemezhető [lekérdezések](../azure-monitor/log-query/log-query-overview.md) gyorsan letölteni, összesíteni és elemezni az összegyűjtött adatokat.  A lekérdezéseket az Azure Portalon a [Log Analytics](../azure-monitor/log-query/portals.md) használatával hozhat létre és tesztelheti, majd ezekkel az eszközökkel közvetlenül elemezheti az adatokat, vagy mentheti a lekérdezéseket [vizualizációkkal](../azure-monitor/visualizations.md) vagy [riasztási szabályokkal](../azure-monitor/platform/alerts-overview.md)való használatra.

Az Azure Monitor az Azure Data Explorer által használt [Kusto lekérdezési nyelv](/azure/kusto/query/) egy olyan verzióját használja, amely egyszerű naplólekérdezésekhez alkalmas, de speciális funkciókat is tartalmaz, például összesítéseket, illesztéseket és intelligens elemzéseket. A lekérdezési nyelvet gyorsan megtanulhatja [több leckével.](../azure-monitor/log-query/get-started-queries.md)

> [!NOTE]
> Az Azure Monitor Naplókkal való integráció jelenleg nyilvános előzetes verzióban érhető el a Windows-használat, a Prémium és a Dedikált üzemeltetési csomagokon futó függvényalkalmazások esetében.

## <a name="setting-up"></a>Beállítása

A **Figyelés csoportban** válassza a **Diagnosztikai beállítások** lehetőséget, majd kattintson **a Diagnosztikai beállítás hozzáadása gombra.**

![Diagnosztikai beállítás hozzáadása](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

A **Diagnosztikai beállítások** lapon válassza a **Küldés a Log Analytics szolgáltatásba**lehetőséget, majd válassza a Log Analytics-munkaterületet. A **log csoportban** válassza **a FunctionAppLogs**lehetőséget, ez a tábla tartalmazza a kívánt naplókat.

![Diagnosztikai beállítás hozzáadása](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Felhasználó által létrehozott naplók

Egyéni naplók létrehozásához a nyelvtől függően használhatja az adott naplózási utasítást, az alábbiakban mintakódrészletek találhatók:


# <a name="c"></a>[C #](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>A naplók lekérdezése

A létrehozott naplók lekérdezéséhez nyissa meg a Log Analytics munkaterületet, amely a függvénynaplók küldésére volt konfigurálva, és kattintson a **Naplók gombra.**

![Lekérdezési ablak la-i munkaterületen](media/functions-monitor-log-analytics/querying.png)

Az Azure Functions az összes naplót írja a **FunctionAppLogs** táblába, íme néhány mintalekérdezés.

### <a name="all-logs"></a>Minden napló

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Adott függvénynaplók

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Kivételek

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>További lépések

- Az [Azure Functions áttekintésének áttekintése](functions-overview.md)
- További információ az [Azure Figyelő naplókról](../azure-monitor/platform/data-platform-logs.md)
- További információ a [lekérdezés nyelvéről](../azure-monitor/log-query/get-started-queries.md).
