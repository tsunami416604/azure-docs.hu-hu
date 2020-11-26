---
title: Azure Monitor lekérdezése az Azure Adatkezelő használatával (előzetes verzió)
description: Az Azure Adatkezelő az Azure Adatkezelő, Log Analytics munkaterületek és a klasszikus Application Insights alkalmazások közötti, Azure Monitor-alapú lekérdezések elvégzésére használható.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 2a21d7a06e8a92022b620704d1fb51a303da3ae0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185980"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Monitor lekérdezése az Azure Adatkezelő használatával (előzetes verzió)
Az Azure Adatkezelő proxy-fürt lehetővé teszi, hogy az Azure Adatkezelő, a Log Analytics munkaterületek és a klasszikus Application Insights alkalmazások között több lekérdezést végezzen Azure Monitor. Log Analytics-munkaterületeket Azure Monitor vagy klasszikus Application Insights alkalmazásokban proxy fürtökként is leképezheti. Ezután lekérdezheti a proxykiszolgálót az Azure Adatkezelő eszközeivel, és megtekintheti azt egy több fürtből álló lekérdezésben. A cikk bemutatja, hogyan csatlakozhat egy proxy fürthöz, hogyan adhat hozzá proxykiszolgálót az Azure Adatkezelő webes felhasználói felületéhez, és hogyan futtathat lekérdezéseket Log Analytics-munkaterületeken vagy az Azure Adatkezelő klasszikus Application Insights alkalmazásain.

Az alábbi ábra az Azure Adatkezelő proxy folyamatát mutatja be:

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Az Azure adatkezelő proxy folyamata.":::


> [!NOTE]
> Az Azure Adatkezelő proxy nyilvános előzetes verzióban érhető el. [Kapcsolódjon a proxyhoz](#connect-to-the-proxy) , hogy engedélyezze a proxy szolgáltatást a fürtökhöz. 

## <a name="connect-to-the-proxy"></a>Csatlakozás a proxyhoz
A Log Analytics-munkaterület vagy a klasszikus Application Insights alkalmazás összekapcsolásához nyissa meg az[Azure adatkezelő webes felhasználói felületét](https://dataexplorer.azure.com/clusters). Ellenőrizze, hogy az Azure Adatkezelő natív fürtje (például a *Súgó* fürt) megjelenik-e a bal oldali menüben, mielőtt a Log Analytics vagy Application Insights fürthöz csatlakozna.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure Adatkezelő natív fürt.":::

Kattintson a **fürt hozzáadása** lehetőségre, majd adja hozzá a Log Analytics vagy Application Insights-fürt URL-címét az alábbi formátumok egyikében. 
    
* Log Analytics esetén: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Application Insights esetén: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

A kapcsolatok létrehozásához kattintson a **Hozzáadás** gombra.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Fürt hozzáadása.":::
 
> [!NOTE]
> Ha egynél több proxykiszolgálót ad hozzá, adjon meg minden más nevet. Ellenkező esetben az összes neve megegyezik a bal oldali ablaktáblán.

A létrehozást követően a Log Analytics vagy Application Insights fürt megjelenik a natív Azure Adatkezelő-fürt bal oldali ablaktábláján. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics és az Azure Adatkezelő-fürtök.":::
 
> [!NOTE]
> A leképezhető Azure Monitor munkaterületek száma legfeljebb 100 lehet.

## <a name="create-queries-using-azure-monitor-data"></a>Lekérdezések létrehozása a Azure Monitor adataival

A lekérdezéseket a Kusto-lekérdezéseket támogató ügyféleszközök használatával futtathatja, például: Kusto Explorer, Azure Adatkezelő Web UI, Jupyter Kqlmagic, flow, PowerQuery, PowerShell, Lori, Lens és REST API.

> [!NOTE]
> Az Azure Adatkezelő proxy szolgáltatás csak az adatok lekérésére szolgál. További információ: a [függvények támogatása](#function-supportability).

> [!TIP]
> * Az adatbázis nevének meg kell egyeznie a fürtben megadott erőforrás nevével. A rendszer a nevekben megkülönbözteti a kis- és nagybetűket.
> * A fürtök közötti lekérdezésekben ellenőrizze, hogy helyesek-e a Application Insights alkalmazások és Log Analytics munkaterületek elnevezése.
>     * Ha a nevek speciális karaktereket tartalmaznak, azokat a rendszer URL-kódolással helyettesíti a proxy fürt nevében. 
>     * Ha a nevek olyan karaktereket tartalmaznak, amelyek nem felelnek meg a [KQL-azonosítók nevének](/azure/data-explorer/kusto/query/schema-entities/entity-names), a kötőjel karakter váltja fel őket **-** .

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Közvetlen lekérdezés a Log Analytics vagy Application Insights proxy fürtből

Lekérdezéseket futtathat a Log Analytics vagy Application Insights fürtön. Ellenőrizze, hogy a fürt ki van-e választva a bal oldali ablaktáblán. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Lekérdezés Log Analytics munkaterületen.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Log Analytics vagy Application Insights proxy-fürt és az Azure Adatkezelő natív fürt több lekérdezése

Ha a proxyról futtat több fürtözött lekérdezést, ellenőrizze, hogy az Azure Adatkezelő natív fürt ki van-e választva a bal oldali ablaktáblán. Az alábbi példák azt mutatják be, hogyan kombinálható az Azure Adatkezelő-fürt táblái a [Union](/azure/data-explorer/kusto/query/unionoperator) operátorral egy log Analytics munkaterülettel.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
Ha az [ `join` operátort](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor)használja az Union helyett, akkor előfordulhat, hogy egy [tippet](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints) kell futtatnia egy Azure adatkezelő natív fürtön (és nem a proxyn). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Adatok csatlakoztatása Azure Adatkezelő-fürtből egyik bérlőn egy másik Azure Monitor erőforrással

Az Azure Adatkezelő proxy nem támogatja a több-bérlős lekérdezéseket. Egyetlen bérlőbe jelentkezett be a lekérdezés mindkét erőforrásra kiterjedő futtatásához.

Ha az Azure Adatkezelő erőforrás az "A" Bérlőben található, és Log Analytics munkaterület a "B" Bérlőben található, használja a következő két módszer egyikét:

- Az Azure Adatkezelő lehetővé teszi szerepkörök hozzáadását a különböző bérlők résztvevői számára. Adja hozzá felhasználói AZONOSÍTÓját a (z) B Bérlőben az Azure Adatkezelő-fürtön lévő meghatalmazott felhasználóként. Ellenőrizze, hogy az Azure Adatkezelő-fürt *["TrustedExternalTenant"](/powershell/module/az.kusto/update-azkustocluster)* tulajdonsága tartalmazza-e a "B" bérlőt. Futtassa a teljes kereszt-lekérdezést a (z) "B" Bérlőben.

- A [világítótorony](../../lighthouse/index.yml) használatával a Azure monitor-erőforrást az "a" bérlőhöz tervezheti.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Kapcsolódás Azure Adatkezelő-fürtökhöz különböző bérlők között

A Kusto Explorer automatikusan aláírja azt a bérlőt, amelyhez a felhasználói fiók eredetileg tartozik. Ha a többi bérlő erőforrásait ugyanazzal a felhasználói fiókkal szeretné elérni, `tenantId` explicit módon meg kell adni azt a kapcsolati karakterláncban: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Funkció-támogatás

Az Azure Adatkezelő proxy-fürt támogatja a Log Analytics és a Application Insights funkcióit is. Ez a funkció lehetővé teszi, hogy a fürtök közötti lekérdezések közvetlenül egy Azure Monitor táblázatos függvényre hivatkozzon.

A proxy a következő parancsokat támogatja:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Az alábbi ábrán egy példa látható egy táblázatos függvény lekérdezésére az Azure Adatkezelő webes felhasználói felületéről. A függvény használatához futtassa a nevet a lekérdezési ablakban.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Táblázatos függvény lekérdezése az Azure Adatkezelő webes felhasználói felületéről.":::
 
> [!NOTE]
> Azure Monitor csak a táblázatos függvények használatát támogatja, amelyek nem támogatják a paramétereket.

## <a name="additional-syntax-examples"></a>További példák a szintaxisra

A Log Analytics vagy Application Insights fürtök meghívásakor a következő szintaktikai beállítások érhetők el:

|Szintaxis leírása  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Olyan fürtön belüli adatbázis, amely csak az előfizetésben definiált erőforrást tartalmazza (**több fürt lekérdezéséhez ajánlott**) |   fürt ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | fürt ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Az előfizetésben szereplő összes alkalmazást/munkaterületet tartalmazó fürt    |     fürt ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    fürt ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Az előfizetésben szereplő összes alkalmazást/munkaterületet tartalmazó fürt, amely az erőforráscsoport tagjai.    |   fürt ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    fürt ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Ebben az előfizetésben csak a definiált erőforrást tartalmazó fürt      |    fürt ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  fürt ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Következő lépések

- További információ a [log Analytics munkaterületek és Application Insights adatszerkezetéről](data-platform-logs.md).
- Megtudhatja, hogyan [írhat lekérdezéseket az Azure Adatkezelőban](/azure/data-explorer/write-queries).