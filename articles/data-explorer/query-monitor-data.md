---
title: Adatlekérdezés Azure Monitor az Azure Adatkezelő (előzetes verzió)
description: Ebben a témakörben egy Azure Adatkezelő proxy létrehozásával Azure Monitor a Application Insights és a Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560422"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Monitor lekérdezése az Azure Adatkezelő használatával (előzetes verzió)

Az Azure Adatkezelő proxy-fürt (ADX proxy) egy olyan entitás, amely lehetővé teszi az Azure Adatkezelő, a [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)és a [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) közötti, [Azure monitor](/azure/azure-monitor/) szolgáltatásban lévő lekérdezések elvégzését. A Azure Monitor Log Analytics-munkaterületeket vagy Application Insights-alkalmazásokat proxy fürtökként képezhető le. Ezután lekérdezheti a proxykiszolgálót az Azure Adatkezelő eszközeivel, és megtekintheti azt egy több fürtből álló lekérdezésben. A cikk bemutatja, hogyan csatlakozhat egy proxy-fürthöz, hogyan adhat hozzá proxykiszolgálót az Azure Adatkezelő webes felhasználói felületéhez, és hogyan futtathat lekérdezéseket az Azure-Adatkezelő AI-alkalmazásaira vagy LA munkaterületeire.

Az Azure Adatkezelő proxy folyamata: 

![ADX-proxy folyamata](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> A ADX proxy előnézet módban van. [Kapcsolódjon a proxyhoz](#connect-to-the-proxy) a fürt ADX-proxy szolgáltatásának engedélyezéséhez. Forduljon a [ADXProxy](mailto:adxproxy@microsoft.com) csapatához bármilyen kérdéssel.

## <a name="connect-to-the-proxy"></a>Csatlakozás a proxyhoz

1. Ellenőrizze, hogy az Azure Adatkezelő natív fürtje (például a *Súgó* fürt) megjelenik-e a bal oldali menüben, mielőtt a Log Analytics vagy Application Insights fürthöz csatlakozna.

    ![ADX natív fürt](media/adx-proxy/web-ui-help-cluster.png)

1. Az Azure Adatkezelő felhasználói felületén (https://dataexplorer.azure.com/clusters)válassza a **fürt hozzáadása**elemet.

1. A **fürt hozzáadása** ablakban adja hozzá a La vagy AI-fürthöz tartozó URL-címet. 
    
    * LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Válassza a **Hozzáadás** lehetőséget.

    ![Fürt hozzáadása](media/adx-proxy/add-cluster.png)

    Ha egynél több proxykiszolgálót ad hozzá, adjon meg minden más nevet. Ellenkező esetben az összes neve megegyezik a bal oldali ablaktáblán.

1. A létrehozást követően a saját LA vagy AI-fürtje megjelenik a bal oldali ablaktáblán a natív ADX-fürttel. 

    ![Log Analytics és Azure Adatkezelő-fürtök](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Lekérdezések futtatása

A lekérdezéseket a Kusto-lekérdezéseket támogató ügyféleszközök használatával futtathatja, például: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, flow, PowerQuery, PowerShell, Lori, Lens, REST API.

> [!TIP]
> * Az adatbázis nevének meg kell egyeznie a fürtben megadott erőforrás nevével. A nevek megkülönböztetik a kis-és nagybetűket.
> * A fürtök közötti lekérdezésekben ellenőrizze, hogy helyesek-e a Application Insights alkalmazások és Log Analytics munkaterületek elnevezése.
>     * Ha a nevek speciális karaktereket tartalmaznak, azokat a rendszer URL-kódolással helyettesíti a proxy fürt nevében. 
>     * Ha a nevek olyan karaktereket tartalmaznak, amelyek nem felelnek meg az [KQL-azonosítók nevének](/azure/kusto/query/schema-entities/entity-names), akkor a kötőjel **-** karakter.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Közvetlen lekérdezés a LA vagy AI ADX-proxy fürtből

Lekérdezéseket futtathat a LA vagy AI-fürtön. Ellenőrizze, hogy a fürt ki van-e választva a bal oldali ablaktáblán. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![LA-munkaterület lekérdezése](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>A LA vagy AI ADX-proxy fürt és a ADX natív fürt lekérdezése 

Ha a proxyról futtat több fürtözött lekérdezést, a bal oldali panelen ellenőrizze, hogy a ADX natív fürt van-e kiválasztva. Az alábbi példák azt mutatják be, hogyan egyesítheti a ADX-fürtöket (`union`) az LA Workspace használatával.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![több lekérdezés az Azure Adatkezelő proxyból](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Ha az [`join` operátort](/azure/kusto/query/joinoperator)használja, az unió helyett [`hint`](/azure/kusto/query/joinoperator#join-hints) kell futtatnia egy Azure adatkezelő natív fürtön (és nem a proxyn). 

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
