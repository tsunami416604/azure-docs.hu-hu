---
title: Azure Functions figyelése Azure Monitor naplókkal
description: Megtudhatja, hogyan használhatja a Azure Monitor naplókat Azure Functions a függvények végrehajtásának figyelésére.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: a4fcf6b4dfeae3d8e53e083b2951d9d594c53d73
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966137"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Functions figyelése Azure Monitor naplókkal

A Azure Functions a függvények figyeléséhez [Azure monitor naplókkal](../azure-monitor/platform/data-platform-logs.md) való integrációt biztosít. Ez a cikk bemutatja, hogyan konfigurálhatja a Azure Functionst a rendszer által létrehozott és a felhasználó által létrehozott naplók Azure Monitor naplókba való küldéséhez.

Azure Monitor naplók lehetővé teszik a különböző erőforrásokból származó naplók összevonását ugyanabban a munkaterületen, ahol a [lekérdezésekkel](../azure-monitor/log-query/log-query-overview.md) elemezhető az összegyűjtött adatok gyors lekérése, összevonása és elemzése céljából.  A Azure Portal [log Analytics](../azure-monitor/log-query/portals.md) használatával létrehozhat és tesztelheti a lekérdezéseket, majd közvetlenül elemezheti ezeket az eszközöket, vagy a [vizualizációk](../azure-monitor/visualizations.md) vagy a [riasztási szabályok](../azure-monitor/platform/alerts-overview.md)használatával mentheti a lekérdezéseket.

A Azure Monitor az Azure Adatkezelő által használt [Kusto-lekérdezési nyelv](/azure/kusto/query/) egy olyan verzióját használja, amely az egyszerű naplózási lekérdezésekhez megfelelő, de olyan speciális funkciókat is tartalmaz, mint az összesítések, az illesztések és az intelligens elemzések. A lekérdezés nyelvét [több leckével](../azure-monitor/log-query/get-started-queries.md)is gyorsan megismerheti.

> [!NOTE]
> A Azure Monitor-naplókkal való integráció jelenleg nyilvános előzetes verzióban érhető el a Windows-használat, a prémium szintű és a dedikált üzemeltetési csomagok esetében futó functions-alkalmazásokhoz.

## <a name="setting-up"></a>Beállítás

A figyelés szakaszban válassza a **diagnosztikai beállítások** elemet, majd kattintson a **Hozzáadás**gombra.

![Diagnosztikai beállítás hozzáadása](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

A beállítás lapon válassza a **küldés log Analytics**lehetőséget, majd a **napló** területen válassza a **FunctionAppLogs**lehetőséget, ez a tábla tartalmazza a kívánt naplókat.

![Diagnosztikai beállítás hozzáadása](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Felhasználó által generált naplók

Egyéni naplók létrehozásához az adott nyelvtől függően használhatja az adott naplózási utasítást:

**JavaScript**

```javascript
    context.log('My app logs here.');
```

**Python**

```python
    logging.info('My app logs here.')
```

**.NET**

```csharp
    log.LogInformation("My app logs here.");
```

**Java**

```java
    context.getLogger().info("My app logs here.");
```

**PowerShell**

```powershell
    Write-Host "My app logs here."
```

## <a name="querying-the-logs"></a>Naplók lekérdezése

A generált naplók lekérdezéséhez lépjen a log Analytics-munkaterületre, és kattintson a **naplók**elemre.

![Lekérdezési ablak az LA Workspace-ban](media/functions-monitor-log-analytics/querying.png)

Azure Functions az összes naplót beírja a **FunctionAppLogs** táblába, íme néhány példa a lekérdezésekre.

### <a name="all-logs"></a>Minden napló

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Egy adott függvény naplói

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

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Azure functions áttekintését](functions-overview.md)
- További információ a [Azure monitor naplókról](../azure-monitor/platform/data-platform-logs.md)
- További információ a [lekérdezési nyelvről](../azure-monitor/log-query/get-started-queries.md).
