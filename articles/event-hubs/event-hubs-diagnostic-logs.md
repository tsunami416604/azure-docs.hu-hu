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
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509775"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnosztikai naplók beállítása az Azure-eseményközpontokhoz

Az Azure Event Hubs-hoz két típusú naplót tekinthet meg:

* **[Tevékenységnaplók](../azure-monitor/platform/platform-logs-overview.md)**: ezek a naplók egy adott feladaton végzett műveletekkel kapcsolatos információkat tartalmaznak. A naplók mindig engedélyezve vannak. A tevékenység naplójának bejegyzései a Azure Portalban az Event hub-névtér bal oldali ablaktábláján a **műveletnapló** lehetőség kiválasztásával láthatók. Például: "névtér létrehozása vagy frissítése", "az Event hub létrehozása vagy frissítése".

    ![Event Hubs névtérhez tartozó műveletnapló](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnosztikai naplók](../azure-monitor/platform/platform-logs-overview.md)**: a diagnosztikai naplókat úgy konfigurálhatja, hogy minden, a feladatokkal megvalósuló sokoldalú képet biztosítson. A diagnosztikai naplók a feladatok a tevékenységből való létrehozásakor felmerülő tevékenységek, beleértve a feladatok futása közben végrehajtott frissítéseket és tevékenységeket is.

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

- **Archiválási naplók**: Event Hubs archívumokhoz kapcsolódó naplók, pontosabban az archiválási hibákkal kapcsolatos naplók.
- **Operatív naplók**: a Event Hubs műveletek során felmerülő információk, pontosabban a művelet típusa, beleértve az Event hub létrehozását, a felhasznált erőforrásokat és a művelet állapotát.
- **Naplók automatikus méretezése**: a Event Hubs névterekben végzett automatikus skálázási műveletekkel kapcsolatos információk. 
- **Kafka-koordinátor naplói** – a Event Hubs kapcsolódó Kafka-koordinátori műveletekkel kapcsolatos információk. 
- **Kafka felhasználói naplók**: a Event Hubs kapcsolódó Kafka-felhasználói műveletekkel kapcsolatos információk. 
- **Event Hubs Virtual Network (VNet) kapcsolati esemény**: Event Hubs virtuális hálózati kapcsolati eseményekkel kapcsolatos információk. 
- **Ügyfél által felügyelt kulcs felhasználói naplói**: információ az ügyfél által felügyelt kulcssal kapcsolatos műveletekről. 


    Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzés tartalmaz egy karakterlánc-mezőt, amely a következő szakaszokban ismertetett formátumot használja.

## <a name="archive-logs-schema"></a>Archiválási naplók sémája

Az Archive log JSON-karakterláncok az alábbi táblázatban felsorolt elemeket tartalmazzák:

Name (Név) | Leírás
------- | -------
Feladatnév | A sikertelen feladat leírása.
Tevékenységazonosító | A nyomon követéshez használt belső azonosító.
trackingId | A nyomon követéshez használt belső azonosító.
resourceId | Azure Resource Manager erőforrás-azonosító.
eventHub | Az Event hub teljes neve (tartalmazza a névtér nevét).
partitionId | Az Event hub-partíció írása folyamatban van.
archiveStep | ArchiveFlushWriter
startTime | Sikertelen kezdési idő.
hibák | Hibák száma.
durationInSeconds | A hiba időtartama.
message | Hibaüzenet.
category | ArchiveLogs

A következő kód egy példa egy archivált log JSON-karakterláncra:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
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

Name (Név) | Leírás
------- | -------
Tevékenységazonosító | A cél nyomon követéséhez használt belső azonosító.
EventName | A művelet neve.  
resourceId | Azure Resource Manager erőforrás-azonosító.
SubscriptionId | Előfizetés azonosítója.
EventTimeString | Működési idő.
EventProperties | Művelet tulajdonságai
status | Művelet állapota.
Hívó | A művelet hívója (Azure Portal vagy felügyeleti ügyfél).
category | OperationalLogs

A következő kód példa egy operatív napló JSON-karakterláncára:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Naplók autoskálázása séma
Az autoscale log JSON az alábbi táblázatban felsorolt elemeket tartalmazza:

| Name (Név) | Leírás |
| ---- | ----------- | 
| trackingId | Belső azonosító, amely nyomkövetési célokra szolgál |
| resourceId | Belső azonosító, amely tartalmazza az Azure-előfizetés AZONOSÍTÓját és a névtér nevét |
| message | Tájékoztató üzenet, amely részletesen ismerteti az automatikus feltöltés műveleteit. Az üzenet tartalmazza az adott névtér átviteli egységének előző és aktuális értékét, valamint azt, hogy mi indította el a TU-t. |

## <a name="kafka-coordinator-logs-schema"></a>A Kafka-koordinátor naplói sémája
A Kafka-koordinátor log JSON a következő táblázatban felsorolt elemeket tartalmazza:

| Name (Név) | Leírás |
| ---- | ----------- | 
| Kérelemazonosító | a kérelem azonosítója, amely nyomkövetési célokra szolgál |
| resourceId | Belső azonosító, amely tartalmazza az Azure-előfizetés AZONOSÍTÓját és a névtér nevét |
| operationName | A csoport koordinálásakor végzett művelet neve |
| ügyfél-azonosító | Ügyfél-azonosító |
| Namespacename tulajdonság | Névtér neve | 
| subscriptionId | Azure-előfizetés azonosítója |
| message | Tájékoztató üzenet, amely részletesen ismerteti a felhasználói csoport koordinálásakor végrehajtott műveleteket. |

## <a name="kafka-user-error-logs-schema"></a>Kafka felhasználói hiba naplóinak sémája
A Kafka felhasználói hibanapló JSON a következő táblázatban felsorolt elemeket tartalmazza:

| Name (Név) | Leírás |
| ---- | ----------- |
| trackingId | nyomkövetési azonosító, amely nyomkövetési célokra szolgál. |
| Namespacename tulajdonság | Névtér neve |
| eventhub | Event Hubs neve |
| partitionId | Partícióazonosító |
| csoportazonosító | Csoportazonosító |
| ClientID | Ügyfél-azonosító |
| resourceId | Belső azonosító, amely tartalmazza az Azure-előfizetés AZONOSÍTÓját és a névtér nevét |
| message | Tájékoztató üzenet, amely a hibával kapcsolatos részleteket tartalmaz |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs virtuális hálózati kapcsolati esemény sémája

Event Hubs Virtual Network (VNet) kapcsolati esemény JSON az alábbi táblázatban felsorolt elemeket tartalmazza:

| Name (Név) | Leírás |
| ---  | ----------- | 
| subscriptionId | Azure-előfizetés azonosítója |
| Namespacename tulajdonság | Névtér neve |
| IP-cím | Az Event Hubs szolgáltatáshoz csatlakozó ügyfél IP-címe |
| action | A Event Hubs szolgáltatás által a kapcsolódási kérelmek kiértékelése során végzett művelet. A támogatott műveletek a következők: **AcceptConnection** és **RejectConnection**. |
| reason | A művelet elvárt okát adja meg |
| count | Az adott művelet előfordulásainak száma |
| resourceId | A belső erőforrás azonosítója, amely tartalmazza az előfizetés-azonosítót és a névtér nevét. |

## <a name="customer-managed-key-user-logs"></a>Ügyfél által felügyelt kulcsfontosságú felhasználói naplók
Az ügyfél által felügyelt kulcs felhasználói napló JSON a következő táblázatban felsorolt elemeket tartalmazza:

| Name (Név) | Leírás |
| ---- | ----------- | 
| category | Az üzenet kategóriájának típusa A következő értékek egyike: **hiba** és **információ** |
| resourceId | Belső erőforrás-azonosító, amely tartalmazza az Azure-előfizetés AZONOSÍTÓját és a névtér nevét |
| keyVault | A Key Vault erőforrás neve |
| kulcs | A Key Vault kulcs neve. |
| version | A Key Vault kulcs verziója |
| művelet | A kérelmek kiszolgálására tett művelet neve |
| code | Állapotkód |
| message | Üzenet, amely egy hiba vagy tájékoztató üzenet részleteit tartalmazza |



## <a name="next-steps"></a>További lépések
- [Bevezetés a Event Hubsba](event-hubs-what-is-event-hubs.md)
- [Event Hubs API overview (Event Hubs API – áttekintés)](event-hubs-api-overview.md)
- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
