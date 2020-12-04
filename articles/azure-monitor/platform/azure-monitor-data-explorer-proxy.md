---
title: Több erőforrás lekérdezése az Azure Adatkezelő használatával Azure Monitor
description: A Azure Monitor használatával több terméket érintő lekérdezéseket hajthat végre az Azure Adatkezelő, Log Analytics munkaterületek és a klasszikus Application Insights alkalmazások között Azure Monitorban.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 5cb2f7b3b07c20e09d61e97412bc35f03b15cb3b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572150"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Több erőforrás lekérdezése az Azure Adatkezelő használatával Azure Monitor
Azure Monitor támogatja az Azure Adatkezelő, a [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)és a [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs)közötti több szolgáltatás lekérdezését. Ezután lekérdezheti az Azure Adatkezelő-fürtöt Log Analytics/Application Insights eszközök használatával, és megtekintheti azt egy több szolgáltatásból álló lekérdezésben. A cikk bemutatja, hogyan végezheti el a több szolgáltatás lekérdezését.

A Azure Monitor Cross Service flow: az :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Azure monitor és az azure adatkezelő a szolgáltatások közötti folyamat.":::

>[!NOTE]
>* Azure Monitor a több szolgáltatás lekérdezése privát előzetes verzióban érhető el – a AllowListing megadása kötelező.
>* Forduljon a [szolgáltatásért felelős csapathoz](mailto:ADXProxy@microsoft.com) bármilyen kérdéssel.
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Több lekérdezés Log Analytics vagy Application Insights erőforrások és az Azure Adatkezelő

A Kusto-lekérdezéseket támogató ügyféleszközök (például: Log Analytics webes felhasználói felület, munkafüzetek, PowerShell, REST API stb.) segítségével futtathat több erőforrás-lekérdezést.

* Adja meg egy Azure Adatkezelő-fürt azonosítóját egy lekérdezésben a "ADX" mintán belül, majd az adatbázis neve és táblázata alapján.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Több szolgáltatás lekérdezése – példa.":::

> [!NOTE]
>* Az adatbázisok nevei megkülönböztetik a kis-és nagybetűket.
>* A több erőforrás lekérdezése riasztásként nem támogatott.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Az Azure Adatkezelő-fürtcsomópontok (Union és JOIN) és az LA Workspace együttes használata.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Több szolgáltatással végzett lekérdezés – példa az Unióval.":::

>[!Tip]
>* A Gyorsírás formátuma megengedett – ClusterName/InitialCatalog. Például a ADX (' Help/Samples ') le van fordítva a ADX (' Help. kusto. Windows. net/Samples ')
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Adatok csatlakoztatása Azure Adatkezelő-fürtből egyik bérlőn egy másik Azure Monitor erőforrással

A szolgáltatások közötti több-bérlős lekérdezések nem támogatottak. Egyetlen bérlőbe jelentkezett be a lekérdezés mindkét erőforrásra kiterjedő futtatásához.

Ha az Azure Adatkezelő erőforrás az "A" Bérlőben található, és Log Analytics munkaterület a "B" Bérlőben található, használja a következő két módszer egyikét:

*  Az Azure Adatkezelő lehetővé teszi szerepkörök hozzáadását a különböző bérlők résztvevői számára. Adja hozzá felhasználói AZONOSÍTÓját a (z) B Bérlőben az Azure Adatkezelő-fürtön lévő meghatalmazott felhasználóként. Ellenőrizze, hogy az Azure Adatkezelő-fürt *["TrustedExternalTenant"](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* tulajdonsága tartalmazza-e a "B" bérlőt. Futtassa a teljes kereszt-lekérdezést a (z) "B" Bérlőben.
*  A [világítótorony](https://docs.microsoft.com/azure/lighthouse/) használatával a Azure monitor-erőforrást az "a" bérlőhöz tervezheti.
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Kapcsolódás Azure Adatkezelő-fürtökhöz különböző bérlők között

A Kusto Explorer automatikusan aláírja azt a bérlőt, amelyhez a felhasználói fiók eredetileg tartozik. Ha a többi bérlő erőforrásait ugyanazzal a felhasználói fiókkal szeretné elérni, `tenantId` explicit módon meg kell adni azt a kapcsolati karakterláncban: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="next-steps"></a>További lépések
* [Írási lekérdezések](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Azure Monitor lekérdezése az Azure-Adatkezelő használatával](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Erőforrás-naplózási lekérdezések végrehajtása Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)
