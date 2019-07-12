---
title: Az Azure Monitor Azure adatkezelő (előzetes verzió) segítségével az adatok lekérdezése
description: Ebben a témakörben adatok lekérdezése az Azure Monitor az Application Insights és a Log Analytics egy Azure Data Explorer-proxy több termék lekérdezések létrehozásával
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811192"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Az Azure Monitor Azure adatkezelő (előzetes verzió) segítségével az adatok lekérdezése

Az Azure Data Explorer proxy fürt (ADX Proxy) olyan entitás, amely lehetővé teszi, hogy több termék lekérdezések között az Azure az adatkezelőt, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview), és [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) a a [Azure Monitor](/azure/azure-monitor/) szolgáltatás. Proxy fürtként leképezheti az Azure Monitor Log Analytics-munkaterületek és Application Insights-alkalmazásokkal. Ezután lekérdezheti a proxy-fürt az adatkezelőt az Azure-eszközökkel és közötti fürt lekérdezés hivatkozik rá. A cikk bemutatja, hogyan egy proxykiszolgáló-fürthöz való kapcsolódáshoz, proxy fürt hozzáadása az Azure Data Explorer webes felhasználói felületén, és lekérdezéseket futtassanak a mesterséges Intelligencia vagy a LA munkaterületek Azure Data Explorer.

Az Azure Data Explorer proxy folyamat: 

![ADX proxy folyamat](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> A ADX Proxy használata előzetes verzióként. Ez a funkció engedélyezéséhez lépjen kapcsolatba a [ADXProxy](mailto:adxproxy@microsoft.com) csapat.

## <a name="connect-to-the-proxy"></a>Csatlakozás a proxykiszolgálóhoz

1. Az Azure Data Explorer natív fürt ellenőrzése (például *súgó* fürt) jelenik meg a bal oldali menüben, a Log Analytics vagy az Application Insights-fürthöz való csatlakozás előtt.

    ![ADX natív fürt](media/adx-proxy/web-ui-help-cluster.png)

1. Az Azure Data Explorer felhasználói felületén (https://dataexplorer.azure.com/clusters) válassza **hozzáadása fürthöz**.

1. Az a **hozzáadása fürthöz** ablakban:

    * Adja hozzá a LA vagy AI-fürt URL-CÍMÉT. Például:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Válassza a **Hozzáadás** lehetőséget.

    ![Fürt hozzáadása](media/adx-proxy/add-cluster.png)

    Ha a kapcsolat hozzá több proxy-fürthöz, minden egyes másik nevet. Ellenkező esetben az összes rendelkeznek ugyanazzal a névvel a bal oldali panelen.

1. A kapcsolat létrejötte után a LA vagy AI-fürt a bal oldali panelen, a natív ADX fürttel fog megjelenni. 

    ![A log Analytics és az Azure Data Explorer fürtök](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Lekérdezések futtatása

Kusto Explorerben ADX webes Explorer, a Jupyter Kqlmagic, vagy a REST API segítségével lekérdezheti a proxy-fürtöket. 

> [!TIP]
> * Adatbázis neve a proxy-fürt a megadott erőforrás névvel kell rendelkeznie. A megkülönböztetik a kis-és nagybetűket.
> * A fürt lekérdezéseket, hogy ellenőrizze, hogy a [az alkalmazások és a munkaterületek elnevezési](#application-insights-app-and-log-analytics-workspace-names) helyes-e.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>A natív Azure adatkezelő fürt lekérdezésének 

Lekérdezések futtatása az Azure Data Explorer fürtön (például *StormEvents* táblára *súgó* fürtben). A lekérdezés futtatásakor győződjön meg arról, hogy a natív Azure adatkezelő fürt van kiválasztva a bal oldali panelen.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![StormEvents tábla lekérdezése](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>A LA vagy AI-fürtön hajtja lekérdezése

A LA vagy AL fürtön futó lekérdezések futtatásakor győződjön meg arról, hogy a LA vagy AI-fürt van kiválasztva a bal oldali panelen. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Lekérdezés LA munkaterület](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>A LA vagy AI-fürt érkezett a ADX lekérdezése  

Lekérdezések a LA vagy AI-fürtön történő futtatásakor a proxy, ellenőrizze a ADX natív fürt van kiválasztva a bal oldali panelen. A következő példa bemutatja egy lekérdezést a LA munkaterület használatával a natív ADX fürt

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Az Azure Data Explorer-proxy lekérdezése](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Adatbázisközi lekérdezés LA vagy AI-fürt és a ADX fürt a ADX érkezett 

A proxy a fürt közötti lekérdezések futtatásakor győződjön meg arról, a ADX natív fürt van kiválasztva a bal oldali panelen. A következő példák azt mutatják be, kombinálása ADX fürt táblák (használatával `union`) LA munkaterülettel.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Adatbázisközi lekérdezés érkezett az Azure Data Explorer](media/adx-proxy/cross-query-adx-proxy.png)

Használatával a [ `join` operátor](/azure/kusto/query/joinoperator), helyett union, megkövetelheti egy futtassák egy Azure Data Explorer natív fürtöt (és nem az a proxy) érhető el. 

## <a name="additional-syntax-examples"></a>További szintaxispéldáival

Az alábbi szintaxissal lehetőségek érhetők el az Application Insights (AI) vagy a Log Analytics (LA) fürtöket hívásakor:

|Szintaxis leírása  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Adatbázis, amely tartalmazza az ebben az előfizetésben csak a megadott erőforrás a fürtön belül (**ajánlott a fürt lekérdezések közötti**) |   fürt (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | fürt (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Fürt, amely tartalmazza az összes apps/munkaterületek ebben az előfizetésben    |     fürt (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    fürt (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Fürt, amely tartalmazza az előfizetés összes alkalmazások/munkaterületet, és ez az erőforráscsoport tagja    |   fürt (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    fürt (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Ebben az előfizetésben csak a meghatározott erőforrást tartalmazó fürt      |    fürt (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  fürt (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Application Insights-alkalmazás és a Log Analytics-munkaterület neve

* Nevek tartalmazhat speciális karaktereket, kódolás a proxy-fürt neve a URL-cím azok van cserélve. 
* Ha a nevek tartalmazzák a karakterek, amelyek nem felelnek meg az [KQL azonosító név szabályok](/azure/kusto/query/schema-entities/entity-names), a dash váltják **-** karakter.

## <a name="next-steps"></a>További lépések

[Lekérdezések írása](write-queries.md)