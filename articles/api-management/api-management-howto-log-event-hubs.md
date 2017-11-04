---
title: "Hogyan naplózza az eseményeket az Azure Event Hubs az Azure API Management |} Microsoft Docs"
description: "Megtudhatja, hogyan naplózza az eseményeket az Azure Event Hubs az Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 1aba03102dcd96753ef4db57edce889a43e4e3fc
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Hogyan naplózza az eseményeket az Azure Event Hubs az Azure API Management
Az Azure Event Hubs egy kiválóan méretezhető adatbefogadási szolgáltatás, amely másodpercenként több millió esemény fogadására képes, így a csatlakoztatott eszközök és alkalmazások által létrehozott nagy mennyiségű adatot egyszerűen feldolgozhatja és elemezheti. Az Event Hubs úgy működik, mint a "bejárati ajtón" egy eseményfolyamat számára, és amennyiben az eseményközpontnak összegyűjtött adatok átalakíthatók, és bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével tárolják. Az Event Hubs elválasztja az eseménystreamek létrehozását azok felhasználásától, így az események felhasználói a saját ütemezésüknek megfelelően férhetnek hozzá az eseményekhez.

Ez a cikk egy kiegészítő, hogy a [integrálni Azure API Management az Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) videó ismerteti, hogyan lehet az Azure Event Hubs API Management naplózása és.

## <a name="create-an-azure-event-hub"></a>Hozzon létre egy Azure Event Hubs
Egy új Eseményközpont létrehozásához jelentkezzen be a [a klasszikus Azure portálon](https://manage.windowsazure.com) kattintson **új**->**alkalmazásszolgáltatások**->**Service Bus**->**Eseményközpont**->**Gyorslétrehozás**. Adja meg az Eseményközpont nevét régióban, válasszon egy előfizetést, és jelöljön ki egy névteret. Ha korábban még nem létrehozott névtér létrehozhat egyet, írja be a nevet adja meg a **Namespace** szövegmező. Az összes tulajdonság konfigurálása után kattintson **hozzon létre egy új Eseményközpont** az Eseményközpont létrehozásához.

![Eseményközpont létrehozása][create-event-hub]

Ezután keresse meg a **konfigurálása** az új Eseményközpont lapra, és hozzon létre két **megosztott hozzáférési házirendek**. Neve az elsőt **küldő** , és adjon neki **küldése** engedélyek.

![Küldő házirend][sending-policy]

Neve a másikat **fogadása**, adjon neki **figyelésére** engedélyeket, majd kattintson **mentése**.

![A fogadó házirend][receiving-policy]

Minden megosztott elérési házirend lehetővé teszi, hogy az alkalmazások és az Event Hubs az események küldéséhez és fogadásához. Ezek a házirendek a kapcsolati karakterláncok szeretne használni, keresse meg a **irányítópult** a Eseményközpont, majd kattintson a lap **kapcsolatadatok**.

![Kapcsolati karakterlánc][event-hub-dashboard]

A **küldő** kapcsolati karakterlánc használatos naplózási eseményeket, és a **fogadása** az Event Hubs-események letöltésekor használt kapcsolati karakterlánc.

![Kapcsolati karakterlánc][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Az API Management naplózó létrehozása
Most, hogy egy Eseményközpontot,-e a következő lépéssel konfigurálhatja a [naplózó](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) az API-kezelés szolgáltatást, hogy az események bejelentkezhetnek az Eseményközpontba.

Az API Management-figyelő szoftverek használatával vannak konfigurálva a [API Management REST API](http://aka.ms/smapi). Előtt először a REST API használatával, olvassa el a [Előfeltételek](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) , és győződjön meg arról, hogy [engedélyezve van a REST API eléréséhez](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Hozzon létre egy naplózó, hajtsa végre egy HTTP PUT-kérelmet a következő URL-cím sablonja segítségével.

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* Cserélje le `{your service}` az API Management szolgáltatáspéldány nevét.
* Cserélje le `{new logger name}` az új naplózó a kívánt néven. Ez a név fog hivatkozni, amikor konfigurálja a [napló eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) házirend

A következő fejlécek hozzáadása a kéréshez.

* Content-Type: az application/json
* Engedélyezési: SharedAccessSignature 58...
  * Generálása kapcsolatos utasításokat a `SharedAccessSignature` lásd [Azure API Management REST API Authentication](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Adja meg a kérelem törzsében, az alábbi sablon használatával.

```json
{
  "type" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `type`meg kell `AzureEventHub`.
* `description`egy leírást a naplózó biztosít, és szükség esetén egy nulla hosszúságú karakterlánc lehet.
* `credentials`tartalmazza a `name` és `connectionString` az Azure Event hubs.

Ha elvégezte a kérelmet, ha a naplózó létrejön egy állapotkódját `201 Created` adja vissza.

> [!NOTE]
> Más lehetséges visszatérési kódok és azok okok: [hozzon létre egy naplózó](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Hogy hogyan hajtsa végre az egyéb műveletek, például a listában, update és delete, lásd: a [naplózó](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) entitás dokumentációját.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Napló-eventhubs-szabályzatok konfigurálása
Miután beállította a naplózó az API Management, beállíthatja a napló-eventhubs a kívánt események naplózása. A napló-eventhubs házirend a bejövő házirend szakaszban vagy a kimenő házirend szakaszban használható.

Házirendek konfigurálásához, jelentkezzen be a [Azure-portálon](https://portal.azure.com), keresse meg az API Management szolgáltatást, és kattintson **Publisher portal** közzétevő-portál eléréséhez.

![Közzétevő portál][publisher-portal]

Kattintson a **házirendek** a bal oldali API Management menüben válasszon ki a kívánt termék és API-t, és kattintson a **házirend hozzáadása**. Ebben a példában egy házirendet, amellyel visszaigazolása azt a **Echo API** a a **korlátlan** termék.

![Házirend hozzáadása][add-policy]

Vigye a kurzort a a `inbound` házirend szakaszban, és kattintson a **EventHub napló** házirend beszúrására a `log-to-eventhub` Házirendsablon utasítás.

![Házirendszerkesztő][event-hub-policy]

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```

Cserélje le `logger-id` az API Management-naplózó az előző lépésben konfigurált nevével.

Használhat egy kifejezést, amely egy karakterláncot ad vissza, az értéknek a `log-to-eventhub` elemet. Ebben a példában a dátum és idő, szolgáltatásnév, kérelemazonosító, kérelem IP-cím és műveletnév tartalmazó karakterláncot a rendszer naplózza.

Kattintson a **mentése** a frissített házirend konfigurációjának mentéséhez. Amint a rendszer menti a házirend és aktív eseményeket naplózza a kijelölt eseményközpontba.

## <a name="next-steps"></a>Következő lépések
* További tudnivalók az Azure Event Hubs
  * [Ismerkedés az Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Üzenetek fogadása az EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md)
* További tudnivalók az API Management és az Event Hubs-integráció
  * [Naplózó entitáshivatkozás](https://docs.microsoft.com/rest/api/apimanagement/loggers)
  * [napló-eventhub szabályzatainak ismertetése](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Az API-kat az Azure API Management, az Event Hubs és Runscope figyelése](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Bemutató videó megtekintése
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
>
>

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
