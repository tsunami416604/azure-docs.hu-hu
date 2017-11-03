---
title: "Az Azure Service Bus diagnosztikai naplók |} Microsoft Docs"
description: "Ismerje meg, hogyan állíthat be a diagnosztikai naplók az Azure Service Bus."
keywords: 
documentationcenter: .net
services: service-bus-messaging
author: banisadr
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/16/2017
ms.author: sethm
ms.openlocfilehash: 2bf65b7c5b0518da59e767db18fe6f4193e0ab6e
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="service-bus-diagnostic-logs"></a>A Service Bus diagnosztikai naplók

Kétféle típusú naplók az Azure Service Bus tekintheti meg:
* **[Tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Ezek a naplók tartalmaznak egy olyan feladatra végrehajtott műveletek információkat. A naplók mindig engedélyezve vannak.
* **[Diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Konfigurálhatja a diagnosztikai naplók részletesebb információt mindent, ami egy feladat belül történik. Diagnosztika a feladat létrehozásakor, amíg a feladat törli, beleértve a frissítéseket és a feladat futása közben végrehajtott borítóján tevékenységeket naplózza.

## <a name="turn-on-diagnostic-logs"></a>Kapcsolja be a diagnosztikai naplók

Alapértelmezés szerint le vannak tiltva a diagnosztikai naplók. Ahhoz, hogy a diagnosztikai naplók, hajtsa végre a következő lépéseket:

1.  Az a [Azure-portálon](https://portal.azure.com)a **figyelés + felügyeleti**, kattintson a **diagnosztikai naplók**.

    ![A diagnosztikai naplók panel navigációs](./media/service-bus-diagnostic-logs/image1.png)

2. Kattintson a figyelni kívánt erőforrásra.  

3.  Kattintson a **a diagnosztika bekapcsolásához**.

    ![Kapcsolja be a diagnosztikai naplók](./media/service-bus-diagnostic-logs/image2.png)

4.  A **állapot**, kattintson a **a**.

    ![diagnosztikai naplók állapotának módosítása](./media/service-bus-diagnostic-logs/image3.png)

5.  Az archív céljaként használni kívánt; meg például egy tárfiókot, az eseményközpontok vagy Azure Naplóelemzés.

6.  Az új diagnosztikai beállítások mentéséhez.

Új beállítások érvénybe körülbelül 10 perc. Ezt követően naplók jelenik meg a beállított archiválási cél a **diagnosztikai naplók** panelen.

Diagnosztika konfigurálásával kapcsolatos további információkért lásd: a [áttekintés az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók séma

Minden naplók tárolása JavaScript Object Notation (JSON) formátumban történik. Mindegyik bejegyzés rendelkezik, amely a következő szakaszban leírt formátumot használja a karakterlánc típusú.

## <a name="operational-logs-schema"></a>Műveleti naplókat séma

Naplózza a **OperationalLogs** kategória rögzítéséhez, mi történik, a Service Bus-műveletek során. Ezek a naplók pontosabban, a művelet típusa, beleértve a várólista létrehozása, a használt erőforrások és a művelet állapotát rögzíti.

Műveleti napló JSON karakterláncok a következő táblázatban felsorolt elemeket tartalmazza:

Név | Leírás
------- | -------
Tevékenységazonosító | Belső azonosító, nyomon követésére használható
EventName | A művelet neve           
resourceId | Az Azure Resource Manager erőforrás-azonosító
SubscriptionId | Előfizetés azonosítója
EventTimeString | Művelet ideje
EventProperties | A művelet tulajdonságai
status | Műveleti állapota
Hívó | (Az Azure portál vagy a felügyeleti ügyfél) művelettel védőfalkezelőbe
category | OperationalLogs

Íme egy példa egy műveleti napló JSON-karakterlánc:

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

## <a name="next-steps"></a>Következő lépések

Látogasson el az alábbi hivatkozások tudhat meg többet a Service Bus:

* [A Service Bus bemutatása](service-bus-messaging-overview.md)
* [Ismerkedés a Service Bus](service-bus-dotnet-get-started-with-queues.md)
