---
title: Események naplózása az Azure Event Hubs szolgáltatásba az Azure API Management ben | Microsoft dokumentumok
description: Megtudhatja, hogyan naplózhat eseményeket az Azure Event Hubs szolgáltatásba az Azure API Management szolgáltatásban.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 2f07f6a27e78ee4df8c64a09918758d02c28c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76898794"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Események naplózása az Azure Event Hubs szolgáltatásba az Azure API Management szolgáltatásban
Az Azure Event Hubs egy kiválóan méretezhető adatbefogadási szolgáltatás, amely másodpercenként több millió esemény fogadására képes, így a csatlakoztatott eszközök és alkalmazások által létrehozott nagy mennyiségű adatot egyszerűen feldolgozhatja és elemezheti. Az Event Hubs egy eseményfolyamat "bejárati kapujaként" működik, és ha az adatokat egy eseményközpontba gyűjtik, bármely valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter használatával átalakítható és tárolható. Az Event Hubs elválasztja az eseménystreamek létrehozását azok felhasználásától, így az események felhasználói a saját ütemezésüknek megfelelően férhetnek hozzá az eseményekhez.

Ez a cikk az [Azure API-kezelés integrálása az Event Hubs videóval](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) című videóhoz kapcsolódik, és ismerteti, hogyan naplózhatja az API-kezelés eseményeit az Azure Event Hubs használatával.

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása

Az eseményközpont létrehozásáról és az események nek az Eseményközpontba és az Eseményközpontból történő fogadásához szükséges kapcsolati karakterláncok leválasztásával kapcsolatos részletes tudnivalókért olvassa el [az Event Hubs névtér és az eseményközpont létrehozása az Azure Portalhasználatával című](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)témakört.

## <a name="create-an-api-management-logger"></a>API Management naplózó létrehozása
Most, hogy rendelkezik egy Event Hub, a következő lépés az, hogy konfigurálja a [Logger](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger) az API Management szolgáltatásban, hogy naplózhassa az eseményeket az Event Hub.

Az API Management naplózók az [API Management REST API](https://aka.ms/apimapi)használatával vannak konfigurálva. Részletes kérési példákat [a Naplózók létrehozásáról](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate)című témakörben talál.

## <a name="configure-log-to-eventhubs-policies"></a>Log-to-eventhubs házirendek konfigurálása

Miután a naplózó konfigurálva van az API Management, konfigurálhatja a log-to-eventhubs szabályzatok naplózni a kívánt eseményeket. A log-to-eventhubs házirend a bejövő házirend szakaszban vagy a kimenő házirend szakaszban használható.

1. Tallózzon az APIM-példányra.
2. Válassza az API lapot.
3. Válassza ki azt az API-t, amelyhez hozzá szeretné adni a szabályzatot. Ebben a példában egy szabályzatot adunk hozzá az **Echo API-hoz** a **korlátlan** termékben.
4. Válassza az **Összes művelet lehetőséget.**
5. A képernyő tetején válassza a Tervezés lapot.
6. A Bejövő vagy kimenő feldolgozás ablakban kattintson a háromszögre (a ceruza mellett).
7. Válassza ki a Kódszerkesztőt. További információt a [Házirendek beállítása és szerkesztése](set-edit-policies.md)című témakörben talál.
8. Helyezze a kurzort `outbound` a vagy a `inbound` házirend szakaszba.
9. A jobb oldali ablakban válassza a **Speciális házirendek** > **Naplózás az EventHubra**lehetőséget. Ezzel beszúrja `log-to-eventhub` a házirend-utasítás sablont.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Cserélje `logger-id` le az `{new logger name}` URL-címben használt értéket az előző lépésben a naplózó létrehozásához.

Bármilyen kifejezést használhat, amely karakterláncot ad `log-to-eventhub` vissza az elem értékeként. Ebben a példában egy karakterlánc, amely tartalmazza a dátumot és az időt, a szolgáltatás nevét, a kérelem azonosítóját, a kérelem IP-címét és a művelet nevét.

A frissített házirend-konfiguráció mentéséhez kattintson a **Mentés** gombra. Amint menti a szabályzat aktív, és az események et a kijelölt Event Hub naplózza.

## <a name="next-steps"></a>További lépések
* További információ az Azure Event Hubs-ról
  * [Ismerkedés az Azure Event Hubs-szal](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Üzenetek fogadása az EventProcessorHost szolgáltatással](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md)
* További információ az API-kezelés és az Eseményközpontok integrációjáról
  * [Naplózó entitás hivatkozása](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger)
  * [log-to-eventhub házirend-hivatkozás](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Api-k figyelése az Azure API-kezelés, az Event Hubs és a Moesif segítségével](api-management-log-to-eventhub-sample.md)  
* További információ az [Azure Application Insights-szal való integrációról](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
