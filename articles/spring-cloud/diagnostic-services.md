---
title: Naplók és metrikák elemzése az Azure Spring Cloud ban | Microsoft dokumentumok
description: Ismerje meg, hogyan elemezheti a diagnosztikai adatokat az Azure Spring Cloudban
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: adbcf28cfbbe2ea3b7cc9c7fd0d1c76246938344
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870411"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Naplók és metrikák elemzése diagnosztikai beállításokkal

Az Azure Spring Cloud diagnosztikai funkcióinak használatával elemezheti a naplókat és a metrikákat az alábbi szolgáltatások bármelyikével:

* Használja az Azure Log Analytics, ahol az adatok írása az Azure Storage.Use Azure Log Analytics, where the data is written to Azure Storage. A naplók Log Analytics-be való exportálása késést okoz.
* Naplók mentése egy tárfiókba naplózásvagy manuális ellenőrzés céljából. Megadhatja a megőrzési időt (napokban).
* Naplót az eseményközpontba egy külső szolgáltatás vagy egyéni elemzési megoldás általi betöltéshez.

Válassza ki a figyelni kívánt naplókategóriát és metrikakategóriát.

> [!TIP]
> Csak szeretné streamelni a naplókat? Nézze meg ezt az [Azure CLI parancsot!](https://docs.microsoft.com/cli/azure/ext/spring-cloud/spring-cloud/app?view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)

## <a name="logs"></a>Naplók

|Napló | Leírás |
|----|----|
| **Alkalmazáskonzol** | Konzolnapló az összes ügyfélalkalmazásról. | 
| **SystemLogs** | Jelenleg csak [a Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) naplózza ezt a kategóriát. |

## <a name="metrics"></a>Mérőszámok

A metrikák teljes listáját a [Spring Cloud Metrics (Tavaszi felhőmutatók) című témakörben található.](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options)

A kezdéshez engedélyezze az adatok fogadását az egyik szolgáltatásnak. A Log Analytics konfigurálásáról az [Azure-figyelő naplószolgáltatásgal kapcsolatos első lépések](../azure-monitor/log-query/get-started-portal.md)című témakörben olvashat. 

## <a name="configure-diagnostics-settings"></a>Diagnosztikai beállítások konfigurálása

1. Az Azure Portalon nyissa meg az Azure Spring Cloud-példányt.
1. Válassza a **Diagnosztikai beállítások lehetőséget,** majd a **Diagnosztika hozzáadása beállítást.**
1. Adja meg a beállítás nevét, majd adja meg, hogy hová szeretné elküldeni a naplókat. Az alábbi három lehetőség közül választhat:
    * **Archiválás tárfiókba**
    * **Streamelés eseményközpontba**
    * **Küldés a Log Analyticsnek**

1. Válassza ki, hogy melyik naplókategóriát és metrikakategóriát szeretné figyelni, majd adja meg a megőrzési időt (napokban). Az adatmegőrzési idő csak a tárfiókra vonatkozik.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> Előfordulhat, hogy a naplók vagy metrikák kibocsátása és a tárfiókban, az eseményközpontban vagy a Log Analytics-ben való megjelenése között akár 15 perc is lehet.

## <a name="view-the-logs-and-metrics"></a>A naplók és a metrikák megtekintése
A naplók és a metrikák megtekintésére különböző módszerek állnak rendelkezésre a következő címsorokban leírtak szerint.

### <a name="use-logs-blade"></a>A Naplók panel használata

1. Az Azure Portalon nyissa meg az Azure Spring Cloud-példányt.
1. A **Naplókeresés** ablaktábla megnyitásához válassza a **Naplók**lehetőséget.
1. A **Napló** keresőmezőben
   * A naplók megtekintéséhez írjon be egy egyszerű lekérdezést, például:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * A mérőszámok megtekintéséhez írjon be egy egyszerű lekérdezést, például:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. A keresési eredmény megtekintéséhez válassza a **Futtatás lehetőséget.**

### <a name="use-log-analytics"></a>A Log Analytics használata

1. Az Azure Portalon a bal oldali ablaktáblában válassza a **Log Analytics**lehetőséget.
1. Válassza ki a Log Analytics munkaterületet, amelyet a diagnosztikai beállítások hozzáadásakor választott.
1. A **Naplókeresés** ablaktábla megnyitásához válassza a **Naplók**lehetőséget.
1. A **Napló** keresőmezőben
   * a naplók megtekintéséhez írjon be egy egyszerű lekérdezést, például:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * a mérőszámok megtekintéséhez írjon be egy egyszerű lekérdezést, például:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. A keresési eredmény megtekintéséhez válassza a **Futtatás lehetőséget.**
1. Az adott alkalmazás vagy példány naplóiban szűrőfeltétel beállításával kereshet:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`a kis- `=~` és nagybetűket, de nem.

Ha többet szeretne megtudni a Log Analytics által használt lekérdezési nyelvről, olvassa el az [Azure Monitor naplólekérdezéseinek című témakört.](../azure-monitor/log-query/query-language.md)

### <a name="use-your-storage-account"></a>A tárfiók használata 

1. Az Azure Portalon a bal oldali ablaktáblában válassza **a Storage-fiókok**lehetőséget.

1. Válassza ki a diagnosztikai beállítások hozzáadásakor kiválasztott tárfiókot.
1. A **Blob Container** ablaktábla megnyitásához válassza **a Blobok**lehetőséget.
1. Az alkalmazásnaplók áttekintéséhez keressen egy **insights-logs-applicationconsole nevű tárolót.**
1. Az alkalmazásmetrikák áttekintéséhez keressen egy **insights-metrics-pt1m nevű tárolót.**

Ha többet szeretne tudni adiagnosztikai adatok tárfiókba küldéséről, olvassa [el az Áruház és a diagnosztikai adatok megtekintése az Azure Storage-ban című témakört.](../storage/common/storage-introduction.md)

### <a name="use-your-event-hub"></a>Az eseményközpont használata

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Eseményközpontok lehetőséget.**

1. Keresse meg és válassza ki a diagnosztikai beállítások hozzáadásakor kiválasztott eseményközpontot.
1. Az **Eseményközpont-lista** ablaktábla megnyitásához válassza az **Eseményközpontok lehetőséget.**
1. Az alkalmazásnaplók áttekintéséhez keressen egy **insights-logs-applicationconsole**nevű eseményközpontot.
1. Az alkalmazásmetrikák áttekintéséhez keressen egy **insights-metrics-pt1m**nevű eseményközpontot.

Ha többet szeretne tudni adiagnosztikai adatok eseményközpontba küldéséről, olvassa el [az Azure-diagnosztika-adatok streamelése a gyors elérési úton az Event Hubs használatával című témakört.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyze-the-logs"></a>A naplók elemzése

Az Azure Log Analytics fut egy Kusto motor, így lekérdezheti a naplókelemzéshez. A naplók Kusto használatával történő lekérdezésének rövid bemutatásához tekintse át a [Log Analytics oktatóanyagát.](../azure-monitor/log-query/get-started-portal.md)

Az alkalmazásnaplók kritikus információkat és részletes naplókat biztosítanak az alkalmazás állapotáról, teljesítményéről és egyebekről. A következő szakaszokban néhány egyszerű lekérdezést, amelyek segítenek megérteni az alkalmazás jelenlegi és korábbi állapotait.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Alkalmazásnaplók megjelenítése az Azure Spring Cloud szolgáltatásból

Az Azure Spring Cloud alkalmazásnaplóinak listájának áttekintéséhez, idő szerint rendezve, először a legutóbbi naplókkal együtt, futtassa a következő lekérdezést:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Hibákat vagy kivételeket tartalmazó naplóbejegyzések megjelenítése

A hibát vagy kivételt megemlítő, rendezetlen naplóbejegyzések áttekintéséhez futtassa a következő lekérdezést:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Ezzel a lekérdezéssel hibákat kereshet, vagy módosíthatja a lekérdezési kifejezéseket, hogy konkrét hibakódokat vagy kivételeket találjon. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Az alkalmazás által az elmúlt órában jelentett hibák és kivételek számának megjelenítése

Ha olyan kördiagramot szeretne létrehozni, amely az alkalmazás által az elmúlt órában naplózott hibák és kivételek számát jeleníti meg, futtassa a következő lekérdezést:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>További információ az alkalmazásnaplók lekérdezéséről

Az Azure Monitor széles körű támogatást nyújt az alkalmazásnaplók lekérdezéséhez a Log Analytics használatával. A szolgáltatásról az [Azure Monitor naplólekérdezéseinek első lépései.](../azure-monitor/log-query/get-started-queries.md) Az alkalmazásnaplók elemzéséhez szükséges lekérdezések létrehozásáról az [Azure Monitor naplólekérdezéseinek áttekintése című témakörben](../azure-monitor/log-query/log-query-overview.md)olvashat bővebben.
