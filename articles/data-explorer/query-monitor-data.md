---
title: Adatok lekérdezése az Azure Monitorban az Azure Data Explorer rel (előzetes verzió)
description: Ebben a témakörben az Azure Data Explorer-proxy létrehozásával az Azure Data Explorer-proxy t az Application Insights és a Log Analytics szolgáltatással rendelkező termékközi lekérdezésekhez hoz létre.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560422"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Adatok lekérdezése az Azure Monitorban az Azure Data Explorer használatával (előzetes verzió)

Az Azure Data Explorer proxyfürt (ADX-proxy) egy olyan entitás, amely lehetővé teszi, hogy az Azure Data Explorer, [az Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)és a [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) között több terméklekérdezést hajtson végre az [Azure Monitor](/azure/azure-monitor/) szolgáltatásban. Az Azure Monitor Log Analytics-munkaterületek vagy az Application Insights-alkalmazások proxyfürtökként leképezhetők. Ezután lekérdezheti a proxyfürtet az Azure Data Explorer eszközeivel, és hivatkozhat rá egy fürtközi lekérdezésben. A cikk bemutatja, hogyan csatlakozhat egy proxyfürthöz, hogyan adhat hozzá egy proxyfürtöt az Azure Data Explorer webfelhasználói felületéhez, és hogyan futtathat lekérdezéseket az AI-alkalmazásokon vagy la-munkaterületeken az Azure Data Explorerből.

Az Azure Data Explorer proxyfolyamata: 

![ADX proxyfolyamat](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Az ADX-proxy előnézeti módban van. [Csatlakozzon a proxyhoz,](#connect-to-the-proxy) és engedélyezze a fürtök ADX-proxyszolgáltatását. Bármilyen kérdéssel forduljon az [ADXProxy](mailto:adxproxy@microsoft.com) csapatához.

## <a name="connect-to-the-proxy"></a>Csatlakozás a proxyhoz

1. Ellenőrizze, hogy az Azure Data Explorer natív fürtje (például *a súgófürt)* megjelenik-e a bal oldali menüben, mielőtt csatlakozna a Log Analytics vagy az Application Insights-fürthöz.

    ![ADX natív fürt](media/adx-proxy/web-ui-help-cluster.png)

1. Az Azure Data Explorerhttps://dataexplorer.azure.com/clusters)felhasználói felületén ( válassza **a Fürt hozzáadása**lehetőséget .

1. A **Fürt hozzáadása** ablakban adja hozzá az URL-címet a LA vagy a ai-fürthöz. 
    
    * LA esetében:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * A I esetében:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Válassza a **Hozzáadás** lehetőséget.

    ![Fürt hozzáadása](media/adx-proxy/add-cluster.png)

    Ha egynél több proxyfürthöz ad hozzá kapcsolatot, adjon mindegyiknek más nevet. Ellenkező esetben mindegyiknek ugyanaz lesz a neve a bal oldali ablaktáblában.

1. A kapcsolat létrejötte után a LA vagy a AI-fürt a natív ADX-fürtöt tartalmazó bal oldali ablaktáblában jelenik meg. 

    ![Log Analytics és Azure Data Explorer-fürtök](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Lekérdezések futtatása

A lekérdezéseket a Kusto-lekérdezéseket támogató ügyféleszközökkel futtathatja, például: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * Az adatbázis nevének meg kell egyeznie a proxyfürtben megadott erőforrás nevével. A nevek ben a kis- és nagybetűket nem lehet figyelembe.
> * A fürtközi lekérdezések, győződjön meg arról, hogy az Alkalmazás insights-alkalmazások és a Log Analytics-munkaterületek elnevezése helyes.
>     * Ha a nevek speciális karaktereket tartalmaznak, a proxyfürt nevében URL-kódolás lép fel. 
>     * Ha a nevek olyan karaktereket tartalmaznak, amelyek nem felelnek meg **-** a [KQL-azonosító névszabályainak,](/azure/kusto/query/schema-entities/entity-names)a kötőjel karakterrel helyettesíti őket.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Közvetlen lekérdezés az LA vagy AI ADX proxyfürtről

Futtasson lekérdezéseket a LA vagy AI-fürtön. Ellenőrizze, hogy a fürt ki van-e jelölve a bal oldali ablaktáblában. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![LA lekérdezési munkaterülete](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>A LA vagy AI ADX-proxyfürt és az ADX natív fürt keresztlekérdezése 

Amikor a proxyról több fürtlekérdezést futtat, ellenőrizze, hogy az ADX natív fürt je a bal oldali ablaktáblában van-e kiválasztva. Az alábbi példák bemutatják az `union`ADX fürttáblák (használatával) és az LA-munkaterület kombinálását.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Lekérdezés ek közötti kapcsolat az Azure Data Explorer proxyról](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Az [ `join` operátor](/azure/kusto/query/joinoperator)használata , ahelyett, [`hint`](/azure/kusto/query/joinoperator#join-hints) hogy az egyesítés, szükség lehet egy futtatni egy Azure Data Explorer natív fürt (és nem a proxy). 

## <a name="additional-syntax-examples"></a>További szintaktikai példák

Az Application Insights (AI) vagy a Log Analytics (LA) fürtök hívásakor a következő szintaxisi beállítások érhetők el:

|Szintaxis leírása  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Adatbázis olyan fürtön belül, amely csak az előfizetésben megadott erőforrást tartalmazza (**fürtközi lekérdezésekhez ajánlott)** |   fürt(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | fürt(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Az előfizetés összes alkalmazását/munkakörnyezetét tartalmazó fürt    |     fürt(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    fürt(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Az előfizetés összes alkalmazását/munkaterét tartalmazó fürt, amely ennek az erőforráscsoportnak a tagja    |   fürt(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    fürt(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Fürt, amely csak a megadott erőforrást tartalmazza ebben az előfizetésben      |    fürt(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  fürt(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>További lépések

[Lekérdezések írása](write-queries.md)
