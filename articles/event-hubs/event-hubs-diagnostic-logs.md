---
title: Diagnosztikai naplók beállítása – Azure Event hub | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a tevékenységek naplóit és a diagnosztikai naplókat az Azure-beli Event hubokhoz.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 0fb5da965a9b13667b8a128e83a5a4cd2c2b28d7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691840"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnosztikai naplók beállítása az Azure-eseményközpontokhoz

Az Azure Event Hubs-hoz két típusú naplót tekinthet meg:

* **[Tevékenységnaplók](../azure-monitor/platform/platform-logs-overview.md)**: ezek a naplók egy adott feladaton végzett műveletekkel kapcsolatos információkat tartalmaznak. A naplók mindig engedélyezve vannak. A tevékenység naplójának bejegyzései a Azure Portalban az Event hub-névtér bal oldali ablaktábláján a **műveletnapló** lehetőség kiválasztásával láthatók. Például: "névtér létrehozása vagy frissítése", "az Event hub létrehozása vagy frissítése".

    ![Event Hubs névtérhez tartozó műveletnapló](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnosztikai naplók](../azure-monitor/platform/platform-logs-overview.md)**: a diagnosztikai naplók részletes információkat biztosítanak a névtérhez az API használatával vagy a Language SDK felügyeleti ügyfelein keresztül végrehajtott műveletekről és műveletekről. 
    
    A következő szakasz bemutatja, hogyan engedélyezheti a diagnosztikai naplókat egy Event Hubs névtérhez.

## <a name="enable-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése
A diagnosztikai naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez kövesse az alábbi lépéseket:

1.  A [Azure Portal](https://portal.azure.com)navigáljon a Event Hubs-névtérhez. 
2. A bal oldali ablaktábla **figyelés** területén válassza a **diagnosztikai beállítások** lehetőséget, majd válassza a **+ diagnosztikai beállítás hozzáadása**elemet. 

    ![Diagnosztikai beállítások lap – diagnosztikai beállítás hozzáadása](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. A **Kategória részletei** szakaszban válassza ki az engedélyezni kívánt **diagnosztikai naplók típusait** . Ezekről a kategóriákról a jelen cikk későbbi részében talál részleteket. 
5. A **cél részletei** szakaszban állítsa be a kívánt archiválási célt (célhelyet); például egy Storage-fiók, egy Event hub vagy egy Log Analytics munkaterület.

    ![Diagnosztikai beállítások hozzáadása lap](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  A diagnosztikai beállítások mentéséhez válassza az eszköztár **Mentés** elemét.

    Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezt követően a naplók megjelennek a konfigurált archiválási célhelyen a **diagnosztikai naplók** panelen.

    A diagnosztika konfigurálásával kapcsolatos további információkért tekintse meg az [Azure diagnosztikai naplók áttekintése](../azure-monitor/platform/platform-logs-overview.md)című témakört.

## <a name="diagnostic-logs-categories"></a>Diagnosztikai naplók kategóriái

Event Hubs a következő kategóriákhoz tartozó diagnosztikai naplókat rögzíti:

| Kategória | Leírás | 
| -------- | ----------- | 
| Archiválási naplók | Adatokat rögzít [Event Hubs rögzítési](event-hubs-capture-overview.md) műveletekről, pontosabban a rögzítési hibákkal kapcsolatos naplókat. |
| Operatív naplók | Rögzítse az Azure Event Hubs-névtéren végrehajtott összes felügyeleti műveletet. Az adatműveletek nem kerülnek rögzítésre, mert az Azure Event Hubson végrehajtott nagy mennyiségű adatművelet miatt. |
| Naplók automatikus méretezése | A Event Hubs névtérben végzett automatikus kikapcsolású műveletek rögzítése. |
| Kafka-koordinátor naplói | A Event Hubs kapcsolódó Kafka-koordinátori műveleteket rögzíti. |
| Kafka felhasználói hibák naplói | Az Event Hubs-on meghívott Kafka API-k adatait rögzíti. |
| Event Hubs Virtual Network (VNet) kapcsolati esemény | Az IP-címekre és a virtuális hálózatokra vonatkozó adatokat rögzíti, amelyek a Event Hubs felé irányuló forgalmat küldenek. |
| Ügyfél által felügyelt kulcsfontosságú felhasználói naplók | Az ügyfél által felügyelt kulcshoz kapcsolódó műveletek rögzítése. |


Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzés tartalmaz egy karakterlánc-mezőt, amely a következő szakaszokban ismertetett formátumot használja.

## <a name="archive-logs-schema"></a>Archiválási naplók sémája

Az Archive log JSON-karakterláncok az alábbi táblázatban felsorolt elemeket tartalmazzák:

Name | Leírás
------- | -------
Feladatnév | A sikertelen feladat leírása
Tevékenységazonosító | A nyomon követéshez használt belső azonosító
trackingId | A nyomon követéshez használt belső azonosító
resourceId | Erőforrás-azonosító Azure Resource Manager
eventHub | Event hub teljes neve (tartalmazza a névtér nevét)
partitionId | Az Event hub-partíció írása folyamatban
archiveStep | lehetséges értékek: ArchiveFlushWriter, DestinationInit
startTime | Sikertelen kezdési idő
hibák | A hiba előfordulási idejének száma
durationInSeconds | Hiba időtartama
message | Hibaüzenet
category | ArchiveLogs

A következő kód egy példa egy archivált log JSON-karakterláncra:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Operatív naplók sémája

Az operatív napló JSON-karakterláncai az alábbi táblázatban felsorolt elemeket tartalmazzák:

Name | Leírás
------- | -------
Tevékenységazonosító | Belső azonosító, követési célokra használatos |
EventName | Művelet neve |
resourceId | Erőforrás-azonosító Azure Resource Manager |
SubscriptionId | Előfizetés azonosítója |
EventTimeString | Működési idő |
EventProperties | Művelet tulajdonságai |
status | Művelet állapota |
Hívó | A művelet hívója (Azure Portal vagy felügyeleti ügyfél) |
Kategória | OperationalLogs |

A következő kód példa egy operatív napló JSON-karakterláncára:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Naplók autoskálázása séma
Az autoscale log JSON az alábbi táblázatban felsorolt elemeket tartalmazza:

| Name | Leírás |
| ---- | ----------- | 
| TrackingId | Belső azonosító, amely nyomkövetési célokra szolgál |
| ResourceId | Azure Resource Manager erőforrás-azonosító. |
| Üzenet | Tájékoztató üzenet, amely részletesen ismerteti az automatikus feltöltés műveleteit. Az üzenet tartalmazza az adott névtér átviteli egységének előző és aktuális értékét, valamint azt, hogy mi indította el a TU-t. |

## <a name="kafka-coordinator-logs-schema"></a>A Kafka-koordinátor naplói sémája
A Kafka-koordinátor log JSON a következő táblázatban felsorolt elemeket tartalmazza:

| Name | Leírás |
| ---- | ----------- | 
| Kérelemazonosító | A kérelem azonosítója, amely nyomkövetési célokra szolgál |
| ResourceId | Erőforrás-azonosító Azure Resource Manager |
| Művelet | A csoport koordinálásakor végzett művelet neve |
| ClientID | Ügyfél-azonosító |
| Namespacename tulajdonság | Névtér neve | 
| SubscriptionId | Azure-előfizetés azonosítója |
| Üzenet | Tájékoztató vagy figyelmeztető üzenet, amely részletesen ismerteti a csoportos koordináció során végrehajtott műveleteket. |

### <a name="example"></a>Példa

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Kafka felhasználói hiba naplóinak sémája
A Kafka felhasználói hibanapló JSON a következő táblázatban felsorolt elemeket tartalmazza:

| Name | Leírás |
| ---- | ----------- |
| TrackingId | Nyomkövetési azonosító, amely nyomkövetési célokra szolgál. |
| Namespacename tulajdonság | Névtér neve |
| Eventhub | Event Hubs neve |
| PartitionId | Partícióazonosító |
| Csoportazonosító | Csoportazonosító |
| ClientID | Ügyfél-azonosító |
| ResourceId | Azure Resource Manager erőforrás-azonosító. |
| Üzenet | Tájékoztató üzenet, amely a hibával kapcsolatos részleteket tartalmaz |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs virtuális hálózati kapcsolati esemény sémája

Event Hubs Virtual Network (VNet) kapcsolati esemény JSON az alábbi táblázatban felsorolt elemeket tartalmazza:

| Name | Leírás |
| ---  | ----------- | 
| SubscriptionId | Azure-előfizetés azonosítója |
| Namespacename tulajdonság | Névtér neve |
| IPAddress | Az Event Hubs szolgáltatáshoz csatlakozó ügyfél IP-címe |
| Műveletek | A Event Hubs szolgáltatás által a kapcsolódási kérelmek kiértékelése során végzett művelet. A támogatott műveletek **elfogadják a kapcsolatokat** , és **megtagadják a kapcsolatokat**. |
| Ok | A művelet elvárt okát adja meg |
| Darabszám | Az adott művelet előfordulásainak száma |
| ResourceId | Azure Resource Manager erőforrás-azonosító. |

### <a name="example"></a>Példa

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Ügyfél által felügyelt kulcsfontosságú felhasználói naplók
Az ügyfél által felügyelt kulcs felhasználói napló JSON a következő táblázatban felsorolt elemeket tartalmazza:

| Name | Leírás |
| ---- | ----------- | 
| Kategória | Az üzenet kategóriájának típusa A következő értékek egyike: **hiba** és **információ** |
| ResourceId | Belső erőforrás-azonosító, amely tartalmazza az Azure-előfizetés AZONOSÍTÓját és a névtér nevét |
| KeyVault | A Key Vault erőforrás neve |
| Kulcs | A Key Vault kulcs neve. |
| Verzió | A Key Vault kulcs verziója |
| Művelet | A kérelmek kiszolgálására tett művelet neve |
| Kód | Állapotkód |
| Üzenet | Üzenet, amely egy hiba vagy tájékoztató üzenet részleteit tartalmazza |



## <a name="next-steps"></a>További lépések
- [Bevezetés a Event Hubsba](event-hubs-what-is-event-hubs.md)
- [Event Hubs API overview (Event Hubs API – áttekintés)](event-hubs-api-overview.md)
- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
