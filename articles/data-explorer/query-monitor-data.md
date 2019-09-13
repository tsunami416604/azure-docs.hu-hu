---
title: Azure Monitor lekérdezése az Azure Adatkezelő használatával (előzetes verzió)
description: Ebben a témakörben egy Azure Adatkezelő proxy létrehozásával Azure Monitor a Application Insights és a Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 43d91bff6b8b67e79a9549c1524f918166c9adc4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933997"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Monitor lekérdezése az Azure Adatkezelő használatával (előzetes verzió)

Az Azure Adatkezelő proxy-fürt (ADX proxy) egy olyan entitás, amely lehetővé teszi az Azure Adatkezelő, a [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)és a [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) közötti, [Azure monitor](/azure/azure-monitor/) szolgáltatásban lévő lekérdezések elvégzését. Azure Monitor Log Analytics-munkaterületeket és Application Insights-alkalmazásokat is leképezheti proxy fürtként. Ezután lekérdezheti a proxykiszolgálót az Azure Adatkezelő eszközeivel, és megtekintheti azt egy több fürtből álló lekérdezésben. A cikk bemutatja, hogyan csatlakozhat egy proxy-fürthöz, hogyan adhat hozzá proxykiszolgálót az Azure Adatkezelő webes felhasználói felületéhez, és hogyan futtathat lekérdezéseket az Azure-Adatkezelő AI-alkalmazásaira vagy LA munkaterületeire.

Az Azure Adatkezelő proxy folyamata: 

![ADX-proxy folyamata](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> A ADX proxy előnézet módban van. A szolgáltatás engedélyezéséhez forduljon a [ADXProxy](mailto:adxproxy@microsoft.com) csapatához.

## <a name="connect-to-the-proxy"></a>Csatlakozás a proxyhoz

1. Ellenőrizze, hogy az Azure Adatkezelő natív fürtje (például a *Súgó* fürt) megjelenik-e a bal oldali menüben, mielőtt a Log Analytics vagy Application Insights fürthöz csatlakozna.

    ![ADX natív fürt](media/adx-proxy/web-ui-help-cluster.png)

1. Az Azure adatkezelő felhasználói felületén https://dataexplorer.azure.com/clusters) (válassza a **fürt hozzáadása**elemet.

1. A **fürt hozzáadása** ablakban:

    * Adja hozzá az URL-címet a LA vagy AI-fürthöz. Például:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Válassza a **Hozzáadás** lehetőséget.

    ![Fürt hozzáadása](media/adx-proxy/add-cluster.png)

    Ha egynél több proxykiszolgálót ad hozzá, adjon meg minden más nevet. Ellenkező esetben az összes neve megegyezik a bal oldali ablaktáblán.

1. A létrehozást követően a saját LA vagy AI-fürtje megjelenik a bal oldali ablaktáblán a natív ADX-fürttel. 

    ![Log Analytics és Azure Adatkezelő-fürtök](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Lekérdezések futtatása

A Kusto Explorer, a ADX web Explorer, a Jupyter Kqlmagic vagy a REST API használatával kérdezheti le a proxykiszolgálót. 

> [!TIP]
> * Az adatbázis nevének meg kell egyeznie a fürtben megadott erőforrás nevével. A nevek megkülönböztetik a kis-és nagybetűket.
> * A fürtök közötti lekérdezésekben ellenőrizze, hogy helyesek-e a Application Insights alkalmazások és Log Analytics munkaterületek elnevezése.
>     * Ha a nevek speciális karaktereket tartalmaznak, azokat a rendszer URL-kódolással helyettesíti a proxy fürt nevében. 
>     * Ha a nevek olyan karaktereket tartalmaznak, amelyek nem felelnek meg a [KQL-azonosítók nevének](/azure/kusto/query/schema-entities/entity-names), a kötőjel **-** karakter váltja fel őket.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Lekérdezés a natív Azure Adatkezelő-fürtön 

Lekérdezéseket futtathat az Azure Adatkezelő-fürtön (például *StormEvents* -tábla a *Súgó* fürtben). A lekérdezés futtatásakor ellenőrizze, hogy a natív Azure Adatkezelő-fürt ki van-e választva a bal oldali ablaktáblán.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![StormEvents-tábla lekérdezése](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Lekérdezés a LA vagy AI-fürtön

Amikor lekérdezést futtat az LA vagy az AL-fürtön, ellenőrizze, hogy a bal oldali panelen van-e kiválasztva a LA vagy AI-fürt. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![LA-munkaterület lekérdezése](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Az ADX proxy lekérdezése a LA vagy AI-fürtből  

Ha a proxyból futtatja a lekérdezéseket az LA vagy AI-fürtön, a bal oldali panelen ellenőrizze, hogy a ADX natív fürt van-e kiválasztva. Az alábbi példa az LA munkaterület lekérdezését mutatja be a natív ADX-fürt használatával.

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Lekérdezés az Azure Adatkezelő proxyról](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>LA vagy AI-fürt és a ADX-fürt több lekérdezése a ADX-proxyból 

Ha a proxyról futtat több fürtözött lekérdezést, a bal oldali panelen ellenőrizze, hogy a ADX natív fürt van-e kiválasztva. Az alábbi példák azt mutatják be, hogyan kombinálható `union`a ADX-fürt táblái (a) és az La Workspace együttes használatával.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Több lekérdezés az Azure Adatkezelő proxyról](media/adx-proxy/cross-query-adx-proxy.png)

[`hint`](/azure/kusto/query/joinoperator#join-hints) Ha az [ `join` operátort](/azure/kusto/query/joinoperator)használja, az Unió helyett a parancsot kell futtatnia egy Azure adatkezelő natív fürtön (és nem a proxyn). 

## <a name="additional-syntax-examples"></a>További példák a szintaxisra

A Application Insights (AI) vagy Log Analytics (LA) fürtök meghívásakor a következő szintaxisú beállítások érhetők el:

|Szintaxis leírása  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Olyan fürtön belüli adatbázis, amely csak az előfizetésben definiált erőforrást tartalmazza (**több fürt lekérdezéséhez ajánlott**) |   fürt (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | fürt (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Az előfizetésben szereplő összes alkalmazást/munkaterületet tartalmazó fürt    |     fürt (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    fürt (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Az előfizetésben szereplő összes alkalmazást/munkaterületet tartalmazó fürt, amely az erőforráscsoport tagjai.    |   fürt (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    fürt (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Ebben az előfizetésben csak a definiált erőforrást tartalmazó fürt      |    fürt (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  fürt (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>További lépések

[Lekérdezések írása](write-queries.md)
