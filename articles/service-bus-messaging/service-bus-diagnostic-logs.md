---
title: Az Azure Service Bus-diagnosztikai naplók |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a diagnosztikai naplók a Service Bus, az Azure-ban.
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
ms.openlocfilehash: 6ac36fed8f9d3220e6caf6eba94a62d1ec8372c7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846659"
---
# <a name="service-bus-diagnostic-logs"></a>A Service Bus-diagnosztikai naplók

Azure Service Bus két típusú naplók tekintheti meg:
* **[A Tevékenységnaplók](../azure-monitor/platform/activity-logs-overview.md)**. Ezek a naplók egy feladat végrehajtott műveletek vonatkozó adatokat tartalmaznak. A naplók mindig engedélyezve van.
* **[Diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-overview.md)**. Konfigurálhatja a diagnosztikai naplók részletesebb információ minden, a feladatokon belül történik. Diagnosztikai naplók cover tevékenységek a feladat jön létre, amíg a feladat törli, beleértve a frissítéseket és a tevékenységek a feladat futása közben előforduló kezdve.

## <a name="turn-on-diagnostic-logs"></a>Kapcsolja be a diagnosztikai naplók

Alapértelmezés szerint le vannak tiltva a diagnosztikai naplók. Diagnosztikai naplók engedélyezéséhez hajtsa végre az alábbi lépéseket:

1.  Az a [az Azure portal](https://portal.azure.com)alatt **Monitoring + Management**, kattintson a **diagnosztikai naplók**.

    ![panel Navigálás a diagnosztikai naplók](./media/service-bus-diagnostic-logs/image1.png)

2. Kattintson a figyelni kívánt erőforrásra.  

3.  Kattintson a **Diagnosztika bekapcsolása** elemre.

    ![Kapcsolja be a diagnosztikai naplók](./media/service-bus-diagnostic-logs/image2.png)

4.  A **állapot**, kattintson a **a**.

    ![diagnosztikai naplók állapot módosítása](./media/service-bus-diagnostic-logs/image3.png)

5.  Állítsa be a kívánt; archívum cél például egy storage-fiókot, egy eseményközpontba vagy az Azure Log Analytics.

6.  Az új diagnosztikai beállítások mentéséhez.

Új beállítások érvénybe léptetéséhez körülbelül 10 perc múlva. Ezt követően naplók jelenik meg a beállított archív tároló, a **diagnosztikai naplók** panelen.

Konfiguruje se diagnostika kapcsolatos további információkért lásd: a [Azure diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók séma

Az összes napló JavaScript Object Notation (JSON) formátumban vannak tárolva. Mindegyik bejegyzés rendelkezik a karakterlánc típusú, amely a következő szakaszban leírt formátumot használja.

## <a name="operational-logs-schema"></a>Műveleti naplók séma

Naplók a **OperationalLogs** kategória rögzítése, mi történik, a Service Bus-műveletek során. Pontosabban ezek a naplók rögzítése a művelet típusa, beleértve az üzenetsor-létrehozás használt erőforrások és a műveletnek az állapota.

Műveleti napló JSON-sztringek az alábbi táblázatban felsorolt elemeket tartalmazza:

Name (Név) | Leírás
------- | -------
Tevékenységazonosító | Nyomon követésére használt belső azonosító
EventName | Művelet neve           
resourceId | Azure Resource Manager-erőforrás azonosítója
SubscriptionId | Előfizetés azonosítója
EventTimeString | Művelet ideje
EventProperties | Művelet tulajdonságai
status | Művelet állapota
Hívó | Hívó művelet (az Azure Portalon vagy a felügyeleti ügyfél)
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

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi hivatkozások további információt a Service Bus:

* [A Service Bus bemutatása](service-bus-messaging-overview.md)
* [A Service Bus használatának első lépései](service-bus-dotnet-get-started-with-queues.md)
