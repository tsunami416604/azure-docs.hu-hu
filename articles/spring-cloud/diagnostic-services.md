---
title: Naplók és mérőszámok elemzése az Azure Spring Cloud-ban | Microsoft Docs
description: Ismerje meg, hogyan elemezheti a diagnosztikai információkat az Azure Spring Cloud-ban
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038885"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>Naplók és mérőszámok elemzése diagnosztikai beállításokkal

Az Azure Spring Cloud diagnosztikai funkciója lehetővé teszi a naplók és metrikák elemzését a következő szolgáltatások egyikével:

* Elemezze azokat az Azure Log Analyticsban, ahol az adatírások azonnal az Azure Log Analyticsba kerülnek, és nem kell először a tárolóba írnia az adatbevitelt.
* A naplózáshoz vagy a manuális ellenőrzéshez mentse őket egy Storage-fiókba. Megadhatja a megőrzési időt (napokban).
* A harmadik féltől származó szolgáltatás vagy egyéni elemzési megoldás általi betöltéshez továbbíthatja azokat Event Hubs.

Első lépésként engedélyeznie kell ezeknek a szolgáltatásoknak az elérését az adatfogadáshoz.  A Log Analytics konfigurálásával kapcsolatos további tudnivalókért tekintse át [ezt az oktatóanyagot](../azure-monitor/log-query/get-started-portal.md).  

## <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

1. Nyissa meg az Azure Spring Cloud-példányát a Azure Portal.
1. Válassza a **diagnosztikai beállítások** menüpontot.
1. Kattintson a **diagnosztikai beállítások hozzáadása** gombra.
1. Adja meg a beállítás nevét, és válassza ki, hová szeretné elküldeni a naplókat. A következő három lehetőség bármelyik kombinációját kiválaszthatja:
    * Archiválás tárfiókba
    * Streamelés eseményközpontba
    * Küldés a Log Analyticsnek

1. Válassza ki a figyelni kívánt naplózási kategóriát és metrikai kategóriát, és adja meg a megőrzési időt (napokban). A megőrzési idő csak a Storage-fiókra vonatkozik.
1. A beállítás alkalmazásához válassza a **Mentés** lehetőséget.

> [!NOTE]
> A naplók és a metrikák kibocsátása között akár 15 percet is igénybe vehet, és amikor megjelennek a Storage-fiók/Event hub/Log Analyticsban.

## <a name="viewing-logs"></a>Naplók megjelenítése

### <a name="using-log-analytics"></a>A Log Analytics használata

1. A Azure Portal válassza a bal oldali navigációs menü Log Analytics elemét.
1. Válassza ki a diagnosztikai beállítások hozzáadásakor kiválasztott Log Analytics munkaterületet.
1. Válassza a `Logs` lehetőséget a naplóbeli Keresés panel megnyitásához.
1. Adjon meg egy egyszerű lekérdezést a napló keresési mezőjében.  Példa:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Válassza a `Run` lehetőséget a keresési eredmény megtekintéséhez.
1. A szűrési feltétel beállításával az adott alkalmazás vagy példány naplóit is megkeresheti:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

További információ a [cikkben](../azure-monitor/log-query/query-language.md) log Analytics használt lekérdezési nyelvről

### <a name="using-logs-and-metrics-in-storage-account"></a>Naplók és metrikák használata a Storage-fiókban

1. A Azure Portal válassza a bal oldali navigációs menü Storage-fiókok elemét.
1. Válassza ki azt a Storage-fiókot, amelyet a diagnosztikai beállítások megadásakor választott.
1. Válassza a `Blobs` bejegyzést a blob-tároló panel megnyitásához.
1. Keresse meg `insights-logs-applicationconsole` nevű tárolót az alkalmazás naplófájljainak áttekintéséhez.
1. Keresse meg `insights-metrics-pt1m` nevű tárolót az alkalmazás metrikáinak áttekintéséhez.

[További információ a diagnosztikai adatok Storage-fiókba küldéséről.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Event Hubs használata

1. A Azure Portal válassza a bal oldali navigációs menü Event Hubs elemét.
1. Keresse meg és válassza ki a diagnosztikai beállítások hozzáadásakor kiválasztott Event Hubs.
1. Válassza a `Event Hubs` lehetőséget az Event hub-lista panel megnyitásához.
1. Keressen egy `insights-logs-applicationconsole` nevű Event hub-t az alkalmazás naplófájljainak áttekintéséhez.
1. Keressen egy `insights-metrics-pt1m` nevű Event hub-t az alkalmazás metrikáinak áttekintéséhez.

[További információ a diagnosztikai adatok esemény-központba való küldéséről](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyzing-logs"></a>Naplók elemzése

Az Azure Log Analytics biztosítja a Kusto, így az elemzéshez lekérdezheti a naplókat.  Tekintse át a [log Analytics oktatóanyagot](../azure-monitor/log-query/get-started-portal.md) , amely gyors bevezetést nyújt a naplók Kusto használatával történő lekérdezéséhez.

Az alkalmazás naplói kritikus információkat biztosítanak az alkalmazás állapotáról, teljesítményéről és egyéb adatairól.  Az alábbiakban néhány egyszerű lekérdezés segíti az alkalmazás jelenlegi és múltbeli állapotának megismerését.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Alkalmazás-naplók megjelenítése az Azure Spring Cloud-ból

Az Azure Spring Cloud-ból származó alkalmazás-naplók listájának áttekintéséhez a legújabb naplók szerint rendezve, az első:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Hibákat vagy kivételeket tartalmazó naplók bejegyzéseinek megjelenítése

Ez a lekérdezés lehetővé teszi, hogy áttekintse a hibát vagy kivételt megemlítő naplóbejegyzéseket.  A rendszer nem rendezi az eredményeket.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Ezzel a lekérdezéssel hibákat kereshet, vagy módosíthatja a lekérdezési feltételeket adott hibakódok vagy kivételek kereséséhez.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Az alkalmazás által az elmúlt órában jelentett hibák és kivételek számának megjelenítése

Ez a lekérdezés egy tortadiagramot hoz létre, amely az alkalmazás által az elmúlt órában naplózott hibák és kivételek számát jeleníti meg:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>További információ az alkalmazási naplók lekérdezéséről

Azure Monitor széles körű támogatást nyújt az alkalmazások naplófájljainak Log Analytics használatával történő lekérdezéséhez.  Ha többet szeretne megtudni erről a szolgáltatásról, tekintse át a Azure Monitor használatával történő [naplózási lekérdezéseket](../azure-monitor/log-query/get-started-queries.md) ismertető oktatóanyagot. A [Azure monitor lévő naplók áttekintése](../azure-monitor/log-query/log-query-overview.md) további információkat nyújt az alkalmazás naplófájljainak elemzéséhez szükséges lekérdezések létrehozásáról.
