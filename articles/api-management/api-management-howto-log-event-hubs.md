---
title: Események naplózása az Azure Event Hubsba az Azure API Managementban | Microsoft Docs
description: Ismerje meg, hogyan naplózhat eseményeket az Azure Event Hubsba az Azure API Managementban.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898794"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Események naplózása az Azure Event Hubsba az Azure-ban API Management
Az Azure Event Hubs egy kiválóan méretezhető adatbefogadási szolgáltatás, amely másodpercenként több millió esemény fogadására képes, így a csatlakoztatott eszközök és alkalmazások által létrehozott nagy mennyiségű adatot egyszerűen feldolgozhatja és elemezheti. Event Hubs "bejárati ajtóként" viselkedik egy esemény-adatcsatorna esetében, és az adatok egy Event hubhoz való gyűjtése után átalakítható és tárolható bármely valós idejű elemzési szolgáltató vagy kötegelt/Storage-adapter használatával. Az Event Hubs elválasztja az eseménystreamek létrehozását azok felhasználásától, így az események felhasználói a saját ütemezésüknek megfelelően férhetnek hozzá az eseményekhez.

Ez a cikk az [Azure-API Management Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) videóval való integrálását és az Azure Event Hubs használatával történő API Management események naplózását ismerteti.

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása

Az Event hub létrehozásával és a kapcsolati karakterláncok beszerzésével kapcsolatos részletes lépésekért, amelyekkel az Event hub-ba érkező és onnan érkező eseményeket kell elküldeni, tekintse meg [a Event Hubs névtér és az Event hub Azure Portal használatával történő létrehozását](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)ismertető témakört.

## <a name="create-an-api-management-logger"></a>API Management naplózó létrehozása
Most, hogy rendelkezik egy Event hub-val, a következő lépés egy [naplózó](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger) konfigurálása a API Management szolgáltatásban, hogy az eseményeket naplózni tudja az Event hub-ban.

API Management naplózók konfigurálása a [API Management REST API](https://aka.ms/apimapi)használatával történik. A részletes kérelmekre vonatkozó példákat a következő témakörben talál: [naplózók létrehozása](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate).

## <a name="configure-log-to-eventhubs-policies"></a>Eventhubs házirendek konfigurálása

Ha a naplózó konfigurálva van a API Managementban, beállíthatja a eventhubs szabályzatokat a kívánt események naplózásához. A eventhubs házirend a bejövő házirend szakaszban vagy a kimenő házirend szakaszban használható.

1. Tallózzon az APIM-példányra.
2. Válassza az API fület.
3. Válassza ki azt az API-t, amelyhez hozzá szeretné adni a szabályzatot. Ebben a példában egy szabályzatot adunk hozzá az **echo API** -hoz a **korlátlan** termékben.
4. Válassza a **Minden művelet** lehetőséget.
5. A képernyő felső részén válassza a tervezés lapot.
6. A bejövő vagy kimenő feldolgozási ablakban kattintson a háromszögre (a ceruza mellett).
7. Válassza ki a Kódszerkesztőt. További információ: [szabályzatok beállítása vagy szerkesztése](set-edit-policies.md).
8. Vigye a kurzort a `inbound` vagy `outbound` házirend szakaszba.
9. A jobb oldali ablakban válassza a **speciális házirendek** > **napló a EventHub**lehetőséget. Ez beszúrja a `log-to-eventhub` Policy utasítás sablonját.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Cserélje le a `logger-id` értéket az URL-címhez `{new logger name}` használt értékre az előző lépésben a naplózó létrehozásához.

Bármely olyan kifejezés használható, amely egy karakterláncot ad vissza a `log-to-eventhub` elem értékeként. Ebben a példában egy karakterláncot, amely a dátumot és az időt, a szolgáltatás nevét, a kérelem azonosítóját, a kérelem IP-címét és a művelet nevét tartalmazza, a rendszer naplózza.

A frissített házirend-konfiguráció mentéséhez kattintson a **Mentés** gombra. Amint menti a szabályzatot, a rendszer aktív állapotba kerül, és az eseményeket a kijelölt Event hub-ba naplózza.

## <a name="next-steps"></a>Következő lépések
* További információ az Azure Event Hubs
  * [Ismerkedés az Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Üzenetek fogadása a EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md)
* További információ a API Management és a Event Hubs integrációról
  * [Naplózó entitás referenciája](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger)
  * [eventhub házirend-hivatkozás](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Az API-k monitorozása az Azure API Management, a Event Hubs és a Moesif segítségével](api-management-log-to-eventhub-sample.md)  
* További információ az [Azure Application Insights-nal való integrációról](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
