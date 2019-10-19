---
title: Naplók és mérőszámok elemzése az Azure Spring Cloud-ban | Microsoft Docs
description: Ismerje meg, hogyan elemezheti a diagnosztikai adatait az Azure Spring Cloud-ban
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 955641f3511989baa5bfc3c0fa4d7df7ccbf9bfa
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554573"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Naplók és mérőszámok elemzése diagnosztikai beállításokkal

Az Azure Spring Cloud diagnosztikai funkciójának használatával a naplók és a metrikák a következő szolgáltatások bármelyikével elemezhetők:

* Használja az Azure Log Analyticst, ahol az adatírás azonnal megtörténik anélkül, hogy először a Storage-ba kellene írnia.
* A naplózáshoz vagy a manuális ellenőrzéshez mentse őket egy Storage-fiókba. Megadhatja a megőrzési időt (napokban).
* Továbbíthatja őket az Event hub-nak egy harmadik féltől származó szolgáltatás vagy egyéni elemzési megoldás betöltéséhez.

A kezdéshez engedélyezze, hogy az egyik szolgáltatás megkapja az adatfogadást. A Log Analytics konfigurálásával kapcsolatos további információkért tekintse meg a [log Analytics beszerzése a Azure monitorban](../azure-monitor/log-query/get-started-portal.md)című témakört. 

## <a name="configure-diagnostics-settings"></a>Diagnosztikai beállítások konfigurálása

1. A Azure Portal nyissa meg az Azure Spring Cloud-példányát.
1. Válassza a **diagnosztikai beállítások** lehetőséget, majd válassza a **diagnosztika hozzáadása beállítást**.
1. Adja meg a beállítás nevét, majd válassza ki, hová szeretné elküldeni a naplókat. A következő három lehetőség bármelyik kombinációját kiválaszthatja:
    * **Archiválás egy Storage-fiókba**
    * **Stream az Event hub-ba**
    * **Küldés Log Analytics**

1. Válassza ki a figyelni kívánt naplózási kategóriát és metrikai kategóriát, majd adja meg a megőrzési időt (napokban). A megőrzési idő csak a Storage-fiókra vonatkozik.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> A naplók és a metrikák kibocsátása között akár 15 percet is igénybe vehet, és amikor megjelennek a Storage-fiókban, az Event hub vagy a Log Analytics.

## <a name="view-the-logs"></a>A naplók megtekintése

### <a name="use-log-analytics"></a>A Log Analytics használata

1. A Azure Portal a bal oldali ablaktáblán válassza a **log Analytics**lehetőséget.
1. Válassza ki a diagnosztikai beállítások hozzáadásakor kiválasztott Log Analytics munkaterületet.
1. A **naplóbeli keresés** ablaktábla megnyitásához válassza a **naplók**lehetőséget.
1. A **napló** keresési mezőjében adjon meg egy egyszerű lekérdezést, például:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. A keresés eredményének megtekintéséhez válassza a **Futtatás**lehetőséget.
1. A szűrési feltétel beállításával az adott alkalmazás vagy példány naplóit is megkeresheti:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Ha többet szeretne megtudni a Log Analytics használt lekérdezési nyelvről, tekintse meg a [Azure monitor a naplók lekérdezését](../azure-monitor/log-query/query-language.md)ismertető témakört.

### <a name="use-your-storage-account"></a>A Storage-fiók használata 

1. A Azure Portal a bal oldali ablaktáblán válassza a **Storage-fiókok**lehetőséget.

1. Válassza ki azt a Storage-fiókot, amelyet a diagnosztikai beállítások hozzáadásakor választott ki.
1. A blob- **tároló** panel megnyitásához válassza a **Blobok**lehetőséget.
1. Az alkalmazás naplófájljainak áttekintéséhez keressen rá az elemzések **-naplók-applicationconsole**nevű tárolóra.
1. Az alkalmazás metrikáinak áttekintéséhez keressen rá a következő nevű tárolóra: **pt1m**.

A diagnosztikai adatok Storage-fiókba való küldésével kapcsolatos további tudnivalókért lásd: [diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Az Event hub használata

1. A Azure Portal a bal oldali ablaktáblán válassza a **Event Hubs**lehetőséget.

1. Keresse meg és válassza ki azt az Event hub-t, amelyet a diagnosztikai beállítások hozzáadásakor választott ki.
1. Az **Event hub-lista** panel megnyitásához válassza a **Event Hubs**lehetőséget.
1. Az alkalmazás naplófájljainak áttekintéséhez keressen rá az "elemzések – **naplók – applicationconsole**" nevű Event hub kifejezésre.
1. Az alkalmazás-metrikák áttekintéséhez keressen rá az **pt1m**nevű Event hub kifejezésre.

Ha többet szeretne megtudni a diagnosztikai információk esemény-központba küldéséről, olvassa el a következő témakört: [Streaming Azure Diagnostics adatok a gyors elérési úton Event Hubs használatával](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>A naplók elemzése

Az Azure Log Analytics biztosítja a Kusto, hogy lekérdezze a naplókat az elemzéshez. A naplók Kusto használatával történő lekérdezésének gyors bevezetéséhez tekintse át a [log Analytics oktatóanyagot](../azure-monitor/log-query/get-started-portal.md).

Az alkalmazás naplói kritikus információkat biztosítanak az alkalmazás állapotáról, teljesítményéről és egyéb adatairól. A következő részekben néhány egyszerű lekérdezés segíti az alkalmazás jelenlegi és múltbeli állapotának megértését.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Alkalmazás-naplók megjelenítése az Azure Spring Cloud-ból

Ha szeretné áttekinteni az Azure Spring Cloud-ból származó alkalmazás-naplók listáját, akkor az idő szerint rendezve jelenítse meg a legutóbbi naplókat, és futtassa az alábbi lekérdezést:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Hibákat vagy kivételeket tartalmazó naplók bejegyzéseinek megjelenítése

A hibát vagy kivételt megemlítő rendezetlen naplóbejegyzések áttekintéséhez futtassa a következő lekérdezést:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Ezzel a lekérdezéssel hibákat kereshet, vagy módosíthatja a lekérdezési feltételeket adott hibakódok vagy kivételek kereséséhez. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Az alkalmazás által az elmúlt órában jelentett hibák és kivételek számának megjelenítése

A következő lekérdezés futtatásával létrehozhat egy tortadiagramot, amely megjeleníti az alkalmazás által az elmúlt órában naplózott hibák és kivételek számát:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>További információ az alkalmazási naplók lekérdezéséről

Azure Monitor széles körű támogatást nyújt az alkalmazások naplófájljainak lekérdezéséhez Log Analytics használatával. A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő témakört: az [első lépések a naplózási lekérdezésekkel kapcsolatban Azure monitor](../azure-monitor/log-query/get-started-queries.md). Az alkalmazás naplófájljainak elemzésére szolgáló lekérdezések létrehozásával kapcsolatos további információkért lásd: [a Azure monitorban található lekérdezések áttekintése](../azure-monitor/log-query/log-query-overview.md).
