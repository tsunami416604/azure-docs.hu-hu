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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 12a20abb4014712f26e5827bcd1d3c822e8b25a1
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750732"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnosztikai naplók beállítása Azure Event hub-hoz

Az Azure Event Hubs-hoz két típusú naplót tekinthet meg:

* **[Tevékenységnaplók](../azure-monitor/platform/platform-logs-overview.md)** : ezek a naplók a feladatokon végrehajtott műveletekkel kapcsolatos információkat tartalmaznak. A naplók mindig engedélyezve vannak.
* **[Diagnosztikai naplók](../azure-monitor/platform/platform-logs-overview.md)** : a diagnosztikai naplókat úgy konfigurálhatja, hogy minden, a feladatokkal megvalósuló sokoldalú képet biztosítson. A diagnosztikai naplók a feladatok a tevékenységből való létrehozásakor felmerülő tevékenységek, beleértve a feladatok futása közben végrehajtott frissítéseket és tevékenységeket is.

## <a name="enable-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése

A diagnosztikai naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez kövesse az alábbi lépéseket:

1.  A [Azure Portal](https://portal.azure.com) **monitoring és felügyelet**alatt kattintson a **diagnosztikai naplók**elemre.

    ![A panel navigációja a diagnosztikai naplókhoz](./media/event-hubs-diagnostic-logs/image1.png)

2.  Kattintson a figyelni kívánt erőforrásra.

3.  Kattintson a **Diagnosztika bekapcsolása** elemre.

    ![Diagnosztikai naplók bekapcsolása](./media/event-hubs-diagnostic-logs/image2.png)

4.  A **status (állapot**) elemnél kattintson **a be**gombra.

    ![Diagnosztikai naplók állapotának módosítása](./media/event-hubs-diagnostic-logs/image3.png)

5.  Állítsa be a kívánt archiválási célt; például egy Storage-fiók, egy Event hub vagy egy Azure Monitor napló.

6.  Mentse az új diagnosztikai beállításokat.

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezt követően a naplók megjelennek a konfigurált archiválási célhelyen a **diagnosztikai naplók** panelen.

A diagnosztika konfigurálásával kapcsolatos további információkért tekintse meg az [Azure diagnosztikai naplók áttekintése](../azure-monitor/platform/platform-logs-overview.md)című témakört.

## <a name="diagnostic-logs-categories"></a>Diagnosztikai naplók kategóriái

Event Hubs két kategóriához rögzíti a diagnosztikai naplókat:

* **Archiválási naplók**: Event Hubs archívumokhoz kapcsolódó naplók, pontosabban az archiválási hibákkal kapcsolatos naplók.
* **Operatív naplók**: a Event Hubs műveletek során felmerülő információk, pontosabban a művelet típusa, beleértve az Event hub létrehozását, a felhasznált erőforrásokat és a művelet állapotát.

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók sémája

Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzés tartalmaz egy karakterlánc-mezőt, amely a következő szakaszokban ismertetett formátumot használja.

### <a name="archive-logs-schema"></a>Archiválási naplók sémája

Az Archive log JSON-karakterláncok az alábbi táblázatban felsorolt elemeket tartalmazzák:

Név | Leírás
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

### <a name="operational-logs-schema"></a>Operatív naplók sémája

Az operatív napló JSON-karakterláncai az alábbi táblázatban felsorolt elemeket tartalmazzák:

Név | Leírás
------- | -------
Tevékenységazonosító | A cél nyomon követéséhez használt belső azonosító.
EventName | A művelet neve.  
resourceId | Azure Resource Manager erőforrás-azonosító.
SubscriptionId | Előfizetés azonosítója.
EventTimeString | Működési idő.
EventProperties | Művelet tulajdonságai
Állapot | Művelet állapota.
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

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Event Hubsba](event-hubs-what-is-event-hubs.md)
* [Event Hubs API – áttekintés](event-hubs-api-overview.md)
* [Bevezetés az Event Hubs használatába](event-hubs-dotnet-standard-getstarted-send.md)
