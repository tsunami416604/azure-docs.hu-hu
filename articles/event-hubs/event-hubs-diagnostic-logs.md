---
title: Diagnosztikai naplók beállítása - Azure Event Hub | Microsoft dokumentumok
description: Megtudhatja, hogyan állíthat be tevékenységnaplókat és diagnosztikai naplókat az Azure-beli eseményközpontokhoz.
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
ms.openlocfilehash: 68aa62ad34f8db531d439a581ef024862da0f90c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162310"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnosztikai naplók beállítása az Azure-eseményközpontokhoz

Kétféle naplót tekinthet meg az Azure Event Hubs-hoz:

* **[Tevékenységnaplók:](../azure-monitor/platform/platform-logs-overview.md)** Ezek a naplók a feladaton végrehajtott műveletekkel kapcsolatos információkat tartalmaznak. A naplók mindig engedélyezve vannak.
* **[Diagnosztikai naplók:](../azure-monitor/platform/platform-logs-overview.md)** Diagnosztikai naplók konfigurálása a feladattal kapcsolatban zajló mindenről gazdagabb nézetben. A diagnosztikai naplók a feladat létrehozásától a feladat törléséig végzett tevékenységekre vonatkoznak, beleértve a feladat futása közben előforduló frissítéseket és tevékenységeket is.

## <a name="enable-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése

A diagnosztikai naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez kövesse az alábbi lépéseket:

1.  Az [Azure Portalon](https://portal.azure.com)a **Figyelés + kezelés**csoportban kattintson **a Diagnosztikai naplók**elemre.

    ![Ablaktábla-navigáció a diagnosztikai naplókhoz](./media/event-hubs-diagnostic-logs/image1.png)

2.  Kattintson a figyelni kívánt erőforrásra.

3.  Kattintson a **Diagnosztika bekapcsolása** elemre.

    ![Diagnosztikai naplók bekapcsolása](./media/event-hubs-diagnostic-logs/image2.png)

4.  Az **Állapot**lehetőségért kattintson **a Be gombra.**

    ![A diagnosztikai naplók állapotának módosítása](./media/event-hubs-diagnostic-logs/image3.png)

5.  Állítsa be a kívánt archív célt; például egy tárfiók, egy eseményközpont vagy az Azure Monitor naplók.

6.  Mentse az új diagnosztikai beállításokat.

Az új beállítások körülbelül 10 perc múlva lépnek érvénybe. Ezt követően a naplók megjelennek a konfigurált archiválási célban, a **Diagnosztikai naplók** ablaktáblában.

A diagnosztika konfigurálásáról az [Azure diagnosztikai naplóinak áttekintése](../azure-monitor/platform/platform-logs-overview.md)című témakörben olvashat bővebben.

## <a name="diagnostic-logs-categories"></a>Diagnosztikai naplók kategóriái

Az Event Hubs két kategória diagnosztikai naplóit rögzíti:

* **Archív naplók:** Az Event Hubs archívumokhoz kapcsolódó naplók, különösen az archiválási hibákhoz kapcsolódó naplók.
* **Operatív naplók:** információ arról, hogy mi történik az Event Hubs műveletek során, különösen a művelet típusát, beleértve az eseményközpont létrehozását, a használt erőforrásokat és a művelet állapotát.

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók sémája

Minden napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzéshez olyan karakterláncmezők vannak, amelyek a következő szakaszokban leírt formátumot használják.

### <a name="archive-logs-schema"></a>Archív naplók sémája

Az archív napló JSON-karakterláncai az alábbi táblázatban felsorolt elemeket tartalmaznak:

Név | Leírás
------- | -------
Feladatneve | A sikertelen feladat leírása.
ActivityId azonosító | Belső azonosító, nyomon követéshez használva.
nyomon követésazonosító | Belső azonosító, nyomon követéshez használva.
resourceId | Az Azure Resource Manager erőforrásazonosítója.
eventHub | Az eseményközpont teljes neve (a névtér nevét is tartalmazza).
partitionId | Az Event Hub-partíció írása folyamatban van.
archiveStep | ArchiveFlushWriter
startTime | Hiba kezdési ideje.
Kudarcok | A hiba előfordulásának száma.
durationInSeconds | A kudarc időtartama.
message | Hibaüzenet.
category | Archiválási naplók

A következő kód egy példa egy archív napló JSON-karakterláncra:

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

### <a name="operational-logs-schema"></a>Működési naplók sémája

Az operatív napló JSON-karakterláncai az alábbi táblázatban felsorolt elemeket tartalmazzák:

Név | Leírás
------- | -------
ActivityId azonosító | Belső azonosító, a cél nyomon követésére szolgál.
EventName | A művelet neve.  
resourceId | Az Azure Resource Manager erőforrásazonosítója.
SubscriptionId | Előfizetés-azonosító.
EventTimeString | Itt a műtét ideje.
Eseménytulajdonságai | Műveleti tulajdonságok.
status | Műveleti állapot.
Hívó | A működés hívója (Azure Portal vagy felügyeleti ügyfél).
category | Operatív naplók

A következő kód egy működési napló JSON-karakterláncpéldája:

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
- [Bevezetés az Eseményközpontokba](event-hubs-what-is-event-hubs.md)
- [Event Hubs API overview (Event Hubs API – áttekintés)](event-hubs-api-overview.md)
- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
