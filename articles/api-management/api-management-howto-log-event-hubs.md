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
ms.openlocfilehash: 646d9206ec82d5f35ccab9365e76276ff779d225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073489"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Események naplózása az Azure Event Hubsba az Azure-ban API Management
Az Azure Event Hubs egy kiválóan méretezhető adatbefogadási szolgáltatás, amely másodpercenként több millió esemény fogadására képes, így a csatlakoztatott eszközök és alkalmazások által létrehozott nagy mennyiségű adatot egyszerűen feldolgozhatja és elemezheti. Event Hubs "bejárati ajtóként" viselkedik egy esemény-adatcsatorna esetében, és az adatok egy Event hubhoz való gyűjtése után átalakítható és tárolható bármely valós idejű elemzési szolgáltató vagy kötegelt/Storage-adapter használatával. Az Event Hubs elválasztja az eseménystreamek létrehozását azok felhasználásától, így az események felhasználói a saját ütemezésüknek megfelelően férhetnek hozzá az eseményekhez.

Ez a cikk az [Azure-API Management Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) videóval való integrálását és az Azure Event Hubs használatával történő API Management események naplózását ismerteti.

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása

Az Event hub létrehozásával és a kapcsolati karakterláncok beszerzésével kapcsolatos részletes lépésekért, amelyekkel az Event hub-ba érkező és onnan érkező eseményeket kell elküldeni, tekintse meg [a Event Hubs névtér és az Event hub Azure Portal használatával történő létrehozását](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)ismertető témakört.

## <a name="create-an-api-management-logger"></a>API Management naplózó létrehozása
Most, hogy rendelkezik egy Event hub-val, a következő lépés egy [naplózó](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) konfigurálása a API Management szolgáltatásban, hogy az eseményeket naplózni tudja az Event hub-ban.

API Management naplózók konfigurálása a [API Management REST API](https://aka.ms/smapi)használatával történik. A REST API első használata előtt tekintse át az előfeltételeket, és győződjön meg arról, hogy [engedélyezte a hozzáférést a REST APIhoz](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI). [](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest)

A naplózó létrehozásához tegye a következő URL-sablon használatával a HTTP PUT-kérelmet:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Cserélje `{your service}` le a nevet a API Management szolgáltatás példányának nevére.
* Cserélje `{new logger name}` le az nevet az új naplózni kívánt névre. Erre a névre hivatkozik a [eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub) házirend konfigurálásakor

Adja hozzá a következő fejléceket a kérelemhez:

* Content-Type: Application/JSON
* Engedély SharedAccessSignature 58...
  * A `SharedAccessSignature` lásd: [Azure API Management REST API-hitelesítés](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication)létrehozásával kapcsolatos utasítások.

A kérelem törzsének megadásához használja a következő sablont:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType`értékre kell állítani `AzureEventHub`.
* `description`a naplózó opcionális leírását tartalmazza, és szükség esetén nulla hosszúságú karakterláncot is használhat.
* `credentials`az `name` Azure Event `connectionString` hub-t és az-t tartalmazza.

Ha a kérést a naplózó létrehozásakor végzi, a `201 Created` rendszer a visszaadott állapotkódot adja vissza. Alább látható egy minta válasz a fenti minta-kérelem alapján.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> További lehetséges visszatérési kódok és azok okai a következő témakörben találhatók: [adatgyűjtő létrehozása](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Ha szeretné megtudni, hogyan hajthat végre más műveleteket, például listát, frissítést és törlést [](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) , tekintse meg a naplózó entitás dokumentációját.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Eventhubs házirendek konfigurálása

Ha a naplózó konfigurálva van a API Managementban, beállíthatja a eventhubs szabályzatokat a kívánt események naplózásához. A eventhubs házirend a bejövő házirend szakaszban vagy a kimenő házirend szakaszban használható.

1. Tallózzon az APIM-példányra.
2. Válassza az API fület.
3. Válassza ki azt az API-t, amelyhez hozzá szeretné adni a szabályzatot. Ebben a példában egy szabályzatot adunk hozzá az **echo API** -hoz a **korlátlan** termékben.
4. Válassza a **Minden művelet** lehetőséget.
5. A képernyő felső részén válassza a tervezés lapot.
6. A bejövő vagy kimenő feldolgozási ablakban kattintson a háromszögre (a ceruza mellett).
7. Válassza ki a Kódszerkesztőt. További információ: szabályzatok [beállítása vagy szerkesztése](set-edit-policies.md).
8. Vigye a kurzort a `inbound` vagy `outbound` a házirend szakaszba.
9. A jobb oldali ablakban válassza a **speciális szabályzatok** > **napló EventHub**lehetőséget. Ez beszúrja `log-to-eventhub` a házirend-utasítás sablonját.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Cserélje `logger-id` le az értéket az URL- `{new logger name}` ben használt értékre az előző lépésben létrehozott naplózó létrehozásához.

Bármely olyan kifejezést használhat, amely egy karakterláncot ad vissza a `log-to-eventhub` elem értékeként. Ebben a példában egy karakterláncot, amely a dátumot és az időt, a szolgáltatás nevét, a kérelem azonosítóját, a kérelem IP-címét és a művelet nevét tartalmazza, a rendszer naplózza.

A frissített házirend-konfiguráció mentéséhez kattintson a **Mentés** gombra. Amint menti a szabályzatot, a rendszer aktív állapotba kerül, és az eseményeket a kijelölt Event hub-ba naplózza.

## <a name="next-steps"></a>További lépések
* További információ az Azure Event Hubs
  * [Ismerkedés az Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Üzenetek fogadása a EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md)
* További információ a API Management és a Event Hubs integrációról
  * [Naplózó entitás referenciája](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [eventhub házirend-hivatkozás](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Az API-k monitorozása az Azure API Management, a Event Hubs és a Moesif segítségével](api-management-log-to-eventhub-sample.md)  
* További információ az [Azure Application Insights](api-management-howto-app-insights.md) -nal való integrációról

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
