---
title: Diagnosztikai naplók Azure Service Bus | Microsoft Docs
description: Ismerje meg, hogyan állíthat be diagnosztikai naplókat a Service Bushoz az Azure-ban.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592409"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Diagnosztikai naplók engedélyezése Service Bushoz

Ha megkezdi a Azure Service Bus névteret, érdemes figyelnie, hogyan és mikor hozza létre, törli vagy eléri a névteret. Ez a cikk az összes elérhető operatív/diagnosztikai napló áttekintését tartalmazza.

A Azure Service Bus jelenleg olyan tevékenység-/működési naplókat támogat, amelyek rögzítik a Azure Service Bus névtéren végrehajtott **felügyeleti műveleteket** . Ezek a naplók pontosan rögzítik a művelet típusát, beleértve a várólista létrehozását, a felhasznált erőforrásokat és a művelet állapotát.

## <a name="operational-logs-schema"></a>Operatív naplók sémája

Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva az alábbi 2 helyen.

- **AzureActivity** – naplókat jelenít meg a portálon vagy Azure Resource Manager sablonon keresztül végrehajtott műveletekről/műveletekről.
- **AzureDiagnostics** – megjeleníti a névterekben az API használatával vagy a Language SDK felügyeleti ügyfelein keresztül végrehajtott műveletek/műveletek naplóit.

Az operatív napló JSON-karakterláncai az alábbi táblázatban felsorolt elemeket tartalmazzák:

| Név | Leírás |
| ------- | ------- |
| Tevékenységazonosító | A megadott tevékenység azonosítására szolgáló belső azonosító |
| eventName | Művelet neve |
| ResourceId | Erőforrás-azonosító Azure Resource Manager |
| SubscriptionId | Subscription ID (Előfizetés azonosítója) |
| EventTimeString | Működési idő |
| EventProperties | Művelet tulajdonságai |
| Állapot | Művelet állapota |
| Hívó | A művelet hívója (Azure Portal vagy felügyeleti ügyfél) |
| Kategória | OperationalLogs |

Példa egy operatív napló JSON-karakterláncára:

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

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>Milyen eseményeket/műveleteket rögzít a rendszer a műveleti naplókban?

A műveleti naplók rögzítik a Azure Service Bus névtéren végrehajtott összes felügyeleti műveletet. Az adatműveletek nem lesznek rögzítve, mert a Azure Service Buson végrehajtott nagy mennyiségű adatművelet miatt.

> [!NOTE]
> Az adatműveletek jobb nyomon követéséhez ajánlott az ügyféloldali nyomkövetés használata.

Az üzemeltetési naplók az alábbi felügyeleti műveleteket rögzítik – 

| Hatókör | Művelet|
|-------| -------- |
| Névtér | <ul> <li> Névtér létrehozása</li> <li> Névtér frissítése </li> <li> Névtér törlése </li>  </ul> | 
| Queue | <ul> <li> Várólista létrehozása</li> <li> Üzenetsor frissítése</li> <li> Üzenetsor törlése </li> </ul> | 
| Témakör | <ul> <li> Témakör létrehozása </li> <li> Témakör frissítése </li> <li> Témakör törlése </li> </ul> |
| Előfizetés | <ul> <li> Előfizetés létrehozása </li> <li> Előfizetés frissítése </li> <li> Előfizetés törlése </li> </ul> |

> [!NOTE]
> Az **olvasási** műveletek jelenleg nincsenek nyomon követve az operatív naplókban.

## <a name="how-to-enable-operational-logs"></a>Hogyan lehet engedélyezni a műveleti naplókat?

Az operatív naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com)navigáljon a Azure Service Bus névtérhez, és a **figyelés**területen kattintson a **diagnosztikai beállítások**elemre.

   ![paneles navigáció a diagnosztikai naplókhoz](./media/service-bus-diagnostic-logs/image1.png)

2. A diagnosztikai beállítások konfigurálásához kattintson a **diagnosztikai beállítás hozzáadása** elemre.  

   ![diagnosztikai naplók bekapcsolása](./media/service-bus-diagnostic-logs/image2.png)

3. A diagnosztikai beállítások konfigurálása
   1. Adjon meg egy **nevet** a diagnosztikai beállítások azonosításához.
   2. Válasszon célhelyet a diagnosztika számára.
      - Ha kiválasztja a **Storage-fiókot**, konfigurálnia kell azt a Storage-fiókot, ahol a diagnosztika el lesz tárolva.
      - Ha az **Event hubokat**választja, konfigurálnia kell a megfelelő Event hub-t, ahol a diagnosztikai beállítások továbbítva lesznek.
      - Ha **log Analytics**választ, meg kell adnia, hogy a diagnosztika melyik példánya lesz elküldve a log Analytics.
    3. **OperationalLogs**-keresés.

       ![állapot-diagnosztikai naplók módosítása](./media/service-bus-diagnostic-logs/image3.png)

4. Kattintson a **Save** (Mentés) gombra.


Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezután a naplók megjelennek a konfigurált archiválási célhelyen a **diagnosztikai naplók** panelen.

A diagnosztika konfigurálásával kapcsolatos további információkért tekintse meg az [Azure diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő hivatkozásokat, amelyekkel többet is megtudhat a Service Bus:

* [Bevezetés a Service Busba](service-bus-messaging-overview.md)
* [Ismerkedés a Service Bus](service-bus-dotnet-get-started-with-queues.md)
