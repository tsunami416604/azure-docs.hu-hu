---
title: Eseménynaplózás az Azure Event Hubs az Azure API Management hogyan |} A Microsoft Docs
description: Ismerje meg, hogyan eseménynaplózás az Azure Event Hubs az Azure API Management szolgáltatásban.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 028b36cc442ccef8af4aa401846cbacdaaab35bf
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428482"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Eseménynaplózás az Azure Event Hubs az Azure API Management hogyan
Az Azure Event Hubs egy kiválóan méretezhető adatbefogadási szolgáltatás, amely másodpercenként több millió esemény fogadására képes, így a csatlakoztatott eszközök és alkalmazások által létrehozott nagy mennyiségű adatot egyszerűen feldolgozhatja és elemezheti. Az Event Hubs úgy működik, mint a "bejárati ajtajának" egy eseményfolyamat számára, és az összegyűjtött adatokat egy eseményközpontba, átalakíthatók, és bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével tárolják. Az Event Hubs elválasztja az eseménystreamek létrehozását azok felhasználásától, így az események felhasználói a saját ütemezésüknek megfelelően férhetnek hozzá az eseményekhez.

Ez a cikk remek kiegészítése a [Azure API Management integrálása az Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) videó, valamint bemutatja az Azure Event Hubs használatával az API Management-eseményeket.

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása

Létrehoz egy eseményközpontot, és lekérése, és az Eseményközpontból érkező események küldéséhez és fogadásához szükséges kapcsolati karakterláncokat részletes lépéseiért lásd: [hozzon létre egy Event Hubs-névtér és a egy eseményközpontot, az Azure portal használatával](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Az API Management-naplózó létrehozása
Most, hogy egy Eseményközpontba,-e a következő lépéssel konfigurálhatja a [naplózó](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) az az API Management szolgáltatás úgy, hogy az Event hubs események naplóba.

Az API Management másolása használatával konfigurálhatók a [API Management REST API](https://aka.ms/smapi). Mielőtt először a REST API használatával, tekintse át a [Előfeltételek](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) , és gondoskodjon arról, hogy [engedélyezve van a REST API-hozzáférés](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Hozzon létre egy naplózó, győződjön meg arról, egy HTTP PUT kérelem a következő URL-cím sablon használatával:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Cserélje le `{your service}` az API Management szolgáltatáspéldányhoz nevére.
* Cserélje le `{new logger name}` az új naplózó kívánt nevét. Ez a név hivatkozik, amikor konfigurálja a [log-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) házirend

Adja hozzá a következő fejléceket a kérelmet:

* Content-Type : application/json
* Hitelesítés: SharedAccessSignature 58...
  * Útmutató generálása a `SharedAccessSignature` lásd [Azure API Management REST API-hitelesítés](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Adja meg a kérelem törzsében, az alábbi sablon használatával:

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

* `loggerType` meg kell `AzureEventHub`.
* `description` egy leírást a naplózó biztosít, és a egy nulla hosszúságú karakterlánc is lehet, ha szükséges.
* `credentials` tartalmazza a `name` és `connectionString` az Azure Event hub.

Ha Ön kérést az, ha a naplózó létrehozása állapotkódot `201 Created` adja vissza. Az alábbiakban látható a mintaválasz a fenti példa kérés alapján.

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
> Más lehetséges visszatérési kódok és azok okok: [létrehozni naplózót](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Más műveleteket, mint a listában, frissítés és törlés módjáról, olvassa el a [naplózó](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) entitás dokumentációját.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Napló-eventhubs-szabályzatok konfigurálása

Ha a naplózó már konfigurálva van az API Management szolgáltatásban, a kívánt eseményeket a log-eventhubs házirendeket konfigurálhat. A napló-eventhubs-szabályzat a bejövő ügyfélházirend szekció vagy a szabályzat kimenő szakaszában is használható.

1. Tallózzon az APIM-példányra.
2. Válassza ki az API lapon.
3. Válassza ki az API-t, amelyhez hozzá szeretné adja hozzá a szabályzatot. Ebben a példában egy szabályzatot, amely adunk hozzá a **Echo API** a a **korlátlan** termék.
4. Válassza a **Minden művelet** lehetőséget.
5. A képernyő felső részén válassza ki a tervezés lapon.
6. A bejövő vagy kimenő feldolgozás ablakában kattintson a háromszögre (a Ceruza mellett).
7. Válassza ki a Kódszerkesztő. További információkért lásd: [hogyan állítsa be, vagy szerkesztheti a szabályzatokat](set-edit-policies.md).
8. Vigye a kurzort a a `inbound` vagy `outbound` ügyfélházirend szekció.
9. Válassza a jobb oldali ablak **speciális szabályzatok** > **naplózás az EventHub**. Ennek hatására a `log-to-eventhub` Házirendsablon utasítást.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Cserélje le `logger-id` használt értékkel rendelkező `{new logger name}` létrehozni a naplózót az előző lépésben az URL-címben.

Bármilyen kifejezés, amely egy karakterláncot ad vissza értéket használhatja a `log-to-eventhub` elemet. Ebben a példában a dátum és idő, szolgáltatásnév, kérelem azonosítója, kérelem IP-cím és műveletnév tartalmazó karakterláncot a rendszer naplózza.

Kattintson a **mentése** a frissített szabályzatot konfigurációjának mentéséhez. Amint a rendszer menti a házirendet és aktív eseményt naplózza a kijelölt Eseményközpont.

## <a name="next-steps"></a>További lépések
* További információ az Azure Event Hubs
  * [Az Azure Event Hubs használatának első lépései](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Üzenetek fogadása az EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md)
* További információ az API Management és az Event Hubs-integráció
  * [Naplózó entitások bemutatása](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [napló eventhub házirend-referencia](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Az Azure API Management, az Event Hubs és Moesif API-k monitorozása](api-management-log-to-eventhub-sample.md)  
* Tudjon meg többet [Azure Application Insights-integráció](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
