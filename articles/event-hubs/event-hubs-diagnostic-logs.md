---
title: "Az Azure Event Hubs diagnosztikai naplók |} Microsoft Docs"
description: "Ismerje meg, hogyan állíthat be a diagnosztikai naplók az Azure event hubs számára."
keywords: 
documentationcenter: 
services: event-hubs
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: bcc8427d57a001f73d321fbf35c5226a047b68d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-diagnostic-logs"></a>Event Hubs diagnosztikai naplók

Kétféle típusú naplók az Azure Event Hubs tekintheti meg:
* **[Tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Ezek a naplók rendelkezik egy olyan feladatra végrehajtott műveletek kapcsolatos információkat. A naplók mindig engedélyezve vannak.
* **[Diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Egy feladat gazdagabb nézetét mindent, ami történik a diagnosztikai naplók konfigurálható. Diagnosztika a feladat létrehozásakor, amíg a feladat törli, beleértve a frissítéseket és a feladat futása közben végrehajtott borítóján tevékenységeket naplózza.

## <a name="turn-on-diagnostic-logs"></a>Kapcsolja be a diagnosztikai naplók

Alapértelmezés szerint le vannak tiltva a diagnosztikai naplók. Diagnosztikai naplók engedélyezése:

1.  Az a [Azure-portálon](https://portal.azure.com)a **figyelés + felügyeleti**, kattintson a **diagnosztikai naplók**.

    ![A diagnosztikai naplók panel navigációs](./media/event-hubs-diagnostic-logs/image1.png)

2.  Kattintson a figyelni kívánt erőforrásra.

3.  Kattintson a **a diagnosztika bekapcsolásához**.

    ![Kapcsolja be a diagnosztikai naplók](./media/event-hubs-diagnostic-logs/image2.png)

4.  A **állapot**, kattintson a **a**.

    ![Diagnosztikai naplók állapotának módosítása](./media/event-hubs-diagnostic-logs/image3.png)

5.  Az archív céljaként használni kívánt; meg például egy tárfiókot, az eseményközpontok vagy Azure Naplóelemzés.

6.  Az új diagnosztikai beállítások mentéséhez.

Új beállítások érvénybe körülbelül 10 perc. Ezt követően naplók jelenik meg a beállított archiválási cél a **diagnosztikai naplók** panelen.

Diagnosztika konfigurálásával kapcsolatos további információkért lásd: a [áttekintés az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-categories"></a>Diagnosztikai naplók kategóriák
Az Event Hubs két kategóriába tartozó diagnosztikai naplókhoz rögzíti:

* **ArchiveLogs**: naplók az Event Hubs archívumot, pontosabban, a naplók archiválása hibák.
* **OperationalLogs**: kapcsolatos tudnivalókért történik az Event Hubs műveletei során, a művelet írja be, beleértve event hub létrehozása, a használt erőforrások és a művelet állapotát.

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók séma
Minden naplók tárolása JavaScript Object Notation (JSON) formátumban történik. Mindegyik bejegyzés rendelkezik, amely a következő szakaszokban ismertetett formátumot használja a karakterlánc típusú.

### <a name="archive-logs-schema"></a>Archív naplók séma

Archív napló JSON karakterláncok a következő táblázatban felsorolt elemeket tartalmazza:

Név | Leírás
------- | -------
Feladatnév | Nem sikerült a feladat leírása.
Tevékenységazonosító | Belső azonosító, nyomon követésére használható.
trackingId | Belső azonosító, nyomon követésére használható.
resourceId | Az Azure Resource Manager erőforrás-azonosító.
Az EventHub | Az Event hubs teljes név (tartalmazza a névtér neve).
partitionId | Event Hub partíció ír.
archiveStep | ArchiveFlushWriter
startTime | Hiba kezdési időpontja.
hibák | Száma hiba történt.
durationInSeconds | Hiba időtartama.
Üzenet | Hibaüzenet jelenik meg.
category | ArchiveLogs

A következő kódot az archív napló JSON-karakterláncban példája:

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

### <a name="operational-logs-schema"></a>Műveleti naplókat séma

Műveleti napló JSON karakterláncok a következő táblázatban felsorolt elemeket tartalmazza:

Név | Leírás
------- | -------
Tevékenységazonosító | Belső azonosító segítségével nyomon követhető a célra.
EventName | Művelet neve.  
resourceId | Az Azure Resource Manager erőforrás-azonosító.
SubscriptionId | Előfizetés-azonosító.
EventTimeString | Művelet ideje.
EventProperties | A művelet tulajdonságait.
status | A művelet állapotát.
Hívó | (Az Azure portál vagy a felügyeleti ügyfél) művelettel védőfalkezelőbe.
category | OperationalLogs

A következő kód egy műveleti napló JSON-karakterláncban példája:

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

## <a name="next-steps"></a>Következő lépések
* [Az Event Hubs bemutatása](event-hubs-what-is-event-hubs.md)
* [Event Hubs API overview](event-hubs-api-overview.md) (Event Hubs API – áttekintés)
* [Bevezetés az Event Hubs használatába](event-hubs-dotnet-standard-getstarted-send.md)
