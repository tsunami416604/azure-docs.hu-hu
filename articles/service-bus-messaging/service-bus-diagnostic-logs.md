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
ms.openlocfilehash: 6443cb727573645792a4e6c929b80c3406d72025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261804"
---
# <a name="service-bus-diagnostic-logs"></a>Diagnosztikai naplók Service Bus

A Azure Service Bus kétféle naplót tekinthet meg:
* **[Tevékenységek naplói](../azure-monitor/platform/activity-logs-overview.md)** . Ezek a naplók a feladatokon végrehajtott műveletekkel kapcsolatos információkat tartalmazzák. A naplók mindig engedélyezve van.
* **[Diagnosztikai naplók](../azure-monitor/platform/resource-logs-overview.md)** . A diagnosztikai naplókat megadhatja a feladatokon belül megjelenő összes adatról. Diagnosztikai naplók cover tevékenységek a feladat jön létre, amíg a feladat törli, beleértve a frissítéseket és a tevékenységek a feladat futása közben előforduló kezdve.

## <a name="turn-on-diagnostic-logs"></a>Kapcsolja be a diagnosztikai naplók

A diagnosztikai naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez hajtsa végre a következő lépéseket:

1.  Az a [az Azure portal](https://portal.azure.com)alatt **Monitoring + Management**, kattintson a **diagnosztikai naplók**.

    ![paneles navigáció a diagnosztikai naplókhoz](./media/service-bus-diagnostic-logs/image1.png)

2. Kattintson a figyelni kívánt erőforrásra.  

3.  Kattintson a **Diagnosztika bekapcsolása** elemre.

    ![Diagnosztikai naplók bekapcsolása](./media/service-bus-diagnostic-logs/image2.png)

4.  A **állapot**, kattintson a **a**.

    ![állapot-diagnosztikai naplók módosítása](./media/service-bus-diagnostic-logs/image3.png)

5.  Állítsa be a kívánt archiválási célt; például egy Storage-fiók, egy Event hub vagy egy Azure Monitor napló.

6.  Az új diagnosztikai beállítások mentéséhez.

Új beállítások érvénybe léptetéséhez körülbelül 10 perc múlva. Ezután a naplók megjelennek a konfigurált archiválási célhelyen a **diagnosztikai naplók** panelen.

Konfiguruje se diagnostika kapcsolatos további információkért lásd: a [Azure diagnosztikai naplók áttekintése](../azure-monitor/platform/resource-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók séma

Az összes napló JavaScript Object Notation (JSON) formátumban vannak tárolva. Minden bejegyzés tartalmaz egy karakterlánc-mezőt, amely a következő szakaszban ismertetett formátumot használja.

## <a name="operational-logs-schema"></a>Műveleti naplók séma

A **OperationalLogs** kategóriába tartozó naplók rögzítik, hogy mi történik Service Bus műveletek során. Ezek a naplók pontosan rögzítik a művelet típusát, beleértve a várólista létrehozását, a felhasznált erőforrásokat és a művelet állapotát.

Műveleti napló JSON-sztringek az alábbi táblázatban felsorolt elemeket tartalmazza:

Name (Név) | Leírás
------- | -------
Tevékenységazonosító | A nyomon követéshez használt belső azonosító
EventName | Művelet neve           
resourceId | Erőforrás-azonosító Azure Resource Manager
SubscriptionId | Előfizetés azonosítója
EventTimeString | Működési idő
EventProperties | Művelet tulajdonságai
State | Művelet állapota
Caller | A művelet hívója (Azure Portal vagy felügyeleti ügyfél)
category | OperationalLogs

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

## <a name="next-steps"></a>További lépések

Tekintse meg a következő hivatkozásokat, amelyekkel többet is megtudhat a Service Bus:

* [Bevezetés a Service Busba](service-bus-messaging-overview.md)
* [Ismerkedés a Service Bus](service-bus-dotnet-get-started-with-queues.md)
