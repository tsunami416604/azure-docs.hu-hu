---
title: Állítsa be a diagnosztikai naplók – Azure Event Hubs |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a tevékenységnaplókat, és az event hubs az Azure-beli diagnosztikai naplók.
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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c8f2dba8ff30ceae4085d96640623a01b6784b1e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957521"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Állítsa be az Azure event hub diagnosztikai naplók

Az Azure Event hubs két típusú naplók tekintheti meg:

* **[A Tevékenységnaplók](../azure-monitor/platform/activity-logs-overview.md)**: Ezek a naplók rendelkezik egy feladat végrehajtott műveletekkel kapcsolatos információk. A naplók mindig engedélyezve van.
* **[Diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-overview.md)**: A feladat minden, a egy részletesebb nézet, amely akkor fordul elő, a diagnosztikai naplók segítségével konfigurálhat. Diagnosztikai naplók cover tevékenységek a feladat jön létre, amíg a feladat törli, beleértve a frissítéseket és a tevékenységek a feladat futása közben előforduló kezdve.

## <a name="enable-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése

Alapértelmezés szerint le vannak tiltva a diagnosztikai naplók. Diagnosztikai naplók engedélyezéséhez kövesse az alábbi lépéseket:

1.  Az a [az Azure portal](https://portal.azure.com)alatt **Monitoring + Management**, kattintson a **diagnosztikai naplók**.

    ![Panel Navigálás a diagnosztikai naplók](./media/event-hubs-diagnostic-logs/image1.png)

2.  Kattintson a figyelni kívánt erőforrásra.

3.  Kattintson a **Diagnosztika bekapcsolása** elemre.

    ![Kapcsolja be a diagnosztikai naplók](./media/event-hubs-diagnostic-logs/image2.png)

4.  A **állapot**, kattintson a **a**.

    ![Diagnosztikai naplók állapotának módosítása](./media/event-hubs-diagnostic-logs/image3.png)

5.  Állítsa be a kívánt; archívum cél Ha például egy storage-fiókot, egy event hubot vagy az Azure Monitor naplózza.

6.  Az új diagnosztikai beállítások mentéséhez.

Új beállítások érvénybe léptetéséhez körülbelül 10 perc múlva. Ezt követően naplók jelennek meg a beállított archiválási célzott, a **diagnosztikai naplók** ablaktáblán.

Konfiguruje se diagnostika kapcsolatos további információkért lásd: a [Azure diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Diagnosztikai naplók kategóriák

Az Event Hubs két kategóriába diagnosztikai naplóinak rögzíti:

* **Naplói archiválása**: az Event Hubs archívum, kifejezetten kapcsolódó naplók, a naplók archiválása hibák.
* **Műveleti naplók**: információ arról, hogy mi történik, az Event Hubs-műveletek során, a művelet írja be, például eseményközpont létrehozása, a használt erőforrások és a művelet állapotát.

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók séma

Az összes napló JavaScript Object Notation (JSON) formátumban vannak tárolva. Mindegyik bejegyzés rendelkezik a karakterlánc típusú, amely a következő szakaszokban ismertetett formátumot használja.

### <a name="archive-logs-schema"></a>Archív naplók séma

Archív log JSON-sztringek az alábbi táblázatban felsorolt elemeket tartalmazza:

Name (Név) | Leírás
------- | -------
Feladatnév | Leírás a sikertelen feladat.
Tevékenységazonosító | Nyomon követésére használt belső azonosítója.
trackingId | Nyomon követésére használt belső azonosítója.
resourceId | Az Azure Resource Manager-erőforrás azonosítója.
eventHub | Eseményközpont teljes neve (tartalmazza a névtér neve).
partitionId | Event Hub-partícióról ír.
archiveStep | ArchiveFlushWriter
startTime | Hiba a kezdési idő.
hibák | Hiba történt a hányszor.
durationInSeconds | Hiba időtartama.
message | Hibaüzenet jelenik meg.
category | ArchiveLogs

A következő kódot a példából láthatja az archív napló JSON-karakterlánc:

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

### <a name="operational-logs-schema"></a>Műveleti naplók séma

Műveleti napló JSON-sztringek az alábbi táblázatban felsorolt elemeket tartalmazza:

Name (Név) | Leírás
------- | -------
Tevékenységazonosító | Belső azonosítója, célú nyomon követésére szolgál.
EventName | Název operace.  
resourceId | Az Azure Resource Manager-erőforrás azonosítója.
SubscriptionId | Előfizetés-azonosítójára.
EventTimeString | A művelet ideje.
EventProperties | A művelet tulajdonságait.
status | A művelet állapotát.
Hívó | Hívó művelet (az Azure Portalon vagy a felügyeleti ügyfél).
category | OperationalLogs

A következő kódot egy műveleti napló JSON-karakterlánc példája:

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

## <a name="next-steps"></a>További lépések
* [Bevezetés az Event hubs szolgáltatásba](event-hubs-what-is-event-hubs.md)
* [Event Hubs API overview](event-hubs-api-overview.md) (Event Hubs API – áttekintés)
* [Bevezetés az Event Hubs használatába](event-hubs-dotnet-standard-getstarted-send.md)
