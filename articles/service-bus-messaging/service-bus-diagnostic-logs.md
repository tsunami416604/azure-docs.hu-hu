---
title: Az Azure Service Bus diagnosztikai naplói | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure Service Bus hoz elérhető összes működési és diagnosztikai naplóról.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: c8eba538a7015648611e6054ce85b381dcfc9105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760998"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Diagnosztikai naplók engedélyezése a Service Bus számára

Amikor elkezdi használni az Azure Service Bus-névteret, érdemes figyelni, hogyan és mikor a névtér jön létre, törölt, vagy elérhető. Ez a cikk áttekintést nyújt a rendelkezésre álló összes működési és diagnosztikai naplóról.

Az Azure Service Bus jelenleg támogatja a tevékenység et és az üzemeltetési naplókat, amelyek rögzítik az Azure Service Bus névterén végrehajtott *felügyeleti műveleteket.* Ezek a naplók különösen rögzítik a művelet típusát, beleértve a várólista létrehozását, a használt erőforrásokat és a művelet állapotát.

## <a name="operational-logs-schema"></a>Működési naplók sémája

Minden napló JavaScript Object Notation (JSON) formátumban, a következő két helyen tárolódik:

- **AzureActivity:** Az Azure Resource Manager-sablontelepítéseken keresztül a névtérön keresztül végrehajtott műveletekből és műveletekből származó naplókat jeleníti meg.
- **AzureDiagnostics:** naplók at jeleníti meg a műveletek és műveletek, amelyek a névtér az API használatával, vagy az SDK-n alapuló felügyeleti ügyfeleken keresztül végzett műveletek és műveletek.

Az operatív napló JSON-karakterláncai az alábbi táblázatban felsorolt elemeket tartalmazzák:

| Név | Leírás |
| ------- | ------- |
| ActivityId azonosító | Belső azonosító, amely a megadott tevékenység azonosítására szolgál |
| EventName | Művelet neve |
| ResourceId | Az Azure Resource Manager erőforrásazonosítója |
| SubscriptionId | Előfizetés azonosítója |
| EventTimeString | Működési idő |
| Eseménytulajdonságai | A művelet tulajdonságai |
| status | Művelet állapota |
| Hívó | A működés hívója (az Azure Portal vagy a felügyeleti ügyfél) |
| Kategória | Operatív naplók |

Íme egy példa egy operatív napló JSON-karakterláncra:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Operatív naplókban rögzített események és műveletek

Az operatív naplók rögzítik az Azure Service Bus névterén végrehajtott összes felügyeleti műveletet. Az adatműveletek nem kerülnek rögzítésre, mert az Azure Service Bus on végzett nagy mennyiségű adatműveletek.

> [!NOTE]
> Az adatműveletek jobb nyomon követése érdekében javasoljuk, hogy ügyféloldali nyomkövetést használjon.

A következő felügyeleti műveleteket rögzíti kondinaplók: 

| Hatókör | Művelet|
|-------| -------- |
| Névtér | <ul> <li> Névtér létrehozása</li> <li> Névtér frissítése </li> <li> Névtér törlése </li>  </ul> | 
| Várólista | <ul> <li> Várólista létrehozása</li> <li> Várólista frissítése</li> <li> Várólista törlése </li> </ul> | 
| Témakör | <ul> <li> Témakör létrehozása </li> <li> Témakör frissítése </li> <li> Témakör törlése </li> </ul> |
| Előfizetés | <ul> <li> Előfizetés létrehozása </li> <li> Előfizetés frissítése </li> <li> Előfizetés törlése </li> </ul> |

> [!NOTE]
> Jelenleg *olvasási* műveletek nem követi nyomon az operatív naplók.

## <a name="enable-operational-logs"></a>Működési naplók engedélyezése

A működési naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez tegye a következőket:

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az Azure Service Bus-névteret, majd a **Figyelés**csoportban válassza a **Diagnosztikai beállítások lehetőséget.**

   ![A "Diagnosztikai beállítások" hivatkozás](./media/service-bus-diagnostic-logs/image1.png)

1. A **Diagnosztikai beállítások** ablaktáblán válassza a **Diagnosztikai beállítás hozzáadása**lehetőséget.  

   ![A "Diagnosztikai beállítás hozzáadása" hivatkozás](./media/service-bus-diagnostic-logs/image2.png)

1. A diagnosztikai beállításokat az alábbi módon konfigurálhatja:

   a. A **Név** mezőbe írja be a diagnosztikai beállítások nevét.  

   b. Válasszon egyet az alábbi három úti cél közül a diagnosztikai naplókhoz:  
   - Ha az **Archiválás egy tárfiókhoz**lehetőséget választja, konfigurálnia kell azt a tárfiókot, amelyen a diagnosztikai naplók tárolódnak.  
   - Ha a **Stream egy eseményközpontba lehetőséget választja,** konfigurálnia kell azt az eseményközpontot, amelybe a diagnosztikai naplókat streamelni szeretné.
   - Ha a **Küldés a Log Analytics szolgáltatásba lehetőséget választja,** meg kell adnia, hogy a loganalytics melyik példányának küldi el a diagnosztikát.  

   c. Jelölje be az **OperationalLogs jelölőnégyzetet.**

    ![A "Diagnosztikai beállítások" ablaktábla](./media/service-bus-diagnostic-logs/image3.png)

1. Kattintson a **Mentés** gombra.

Az új beállítások körülbelül 10 perc múlva lépnek érvénybe. A naplók a konfigurált archiválási célban, a Diagnosztikai naplók ablaktáblában **jelennek** meg.

A diagnosztikai beállítások konfigurálásáról az [Azure diagnosztikai naplóinak áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md)című témakörben olvashat bővebben.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus szolgáltatásról, olvassa el a következő témakört:

* [Bevezetés a Service Bus szolgáltatásba](service-bus-messaging-overview.md)
* [A Service Bus használatának első lépései](service-bus-dotnet-get-started-with-queues.md)
