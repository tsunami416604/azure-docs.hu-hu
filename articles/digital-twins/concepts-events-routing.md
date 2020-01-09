---
title: Útválasztási események és üzenetek – Azure digitális Twins | Microsoft Docs
description: Az útválasztási események és üzenetek szolgáltatásbeli végpontok közötti áttekintése az Azure Digital Twins használatával
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 094a3a838269921339dcd6c2c3b551720f394251
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660324"
---
# <a name="routing-iot-events-and-messages"></a>IoT-események és-üzenetek továbbítása

Eszközök internetes hálózata megoldások gyakran egyesítenek több olyan hatékony szolgáltatást, amely magában foglalja a tárolást, az elemzést és egyebeket. Ez a cikk azt ismerteti, hogyan csatlakoztathatók az Azure Digital Twins-alkalmazások az Azure Analytics, AI és Storage szolgáltatásokhoz, hogy azok mélyebb elemzéseket és funkciókat biztosítsanak.

## <a name="route-types"></a>Útvonalak típusai  

Az Azure Digital Twins két lehetőséget kínál a IoT-események más Azure-szolgáltatásokkal vagy üzleti alkalmazásokkal való összekapcsolására:

* **Azure digitális Twins-események irányítása**: a térbeli gráf egy olyan objektuma, amely megváltoztatja, telemetria a kapott vagy egy olyan felhasználó által definiált függvényt, amely az előre meghatározott feltételek alapján hoz létre értesítéseket az Azure digitális Twins eseményeinek elindításához. A felhasználók ezeket az eseményeket [Azure Event Hubsba](https://azure.microsoft.com/services/event-hubs/), [Azure Service Bus témakörökbe](https://azure.microsoft.com/services/service-bus/)vagy [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) további feldolgozásra is elküldhetik.

* **Útválasztási eszköz telemetria**: az útválasztási események mellett az Azure Digital Twins is átirányíthatja a nyers eszközön telemetria üzeneteket, hogy további elemzések és elemzések céljából Event Hubs. Az Azure digitális Twins nem dolgozza fel az ilyen típusú üzeneteket. Ezeket csak az Event hub továbbítja.

A felhasználók egy vagy több kimenő végpontot is megadhatnak az események küldéséhez vagy az üzenetek továbbításához. Az eseményeket és üzeneteket a rendszer az előre definiált útválasztási beállításoknak megfelelően elküldi a végpontoknak. Más szóval a felhasználók megadhatnak egy bizonyos végpontot a Graph műveleti események fogadására, egy másikat az eszköz telemetria fogadására, és így tovább.

[![Azure digitális ikrek eseményeinek útválasztása](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

A Event Hubs útválasztása megőrzi a telemetria üzenetek küldésének sorrendjét. Így a végpontnak ugyanabban a sorozatban érkeznek, mint az eredetileg érkezett. 

Event Grid és Service Bus nem garantálják, hogy a végpontok ugyanabban a sorrendben kapják meg az eseményeket. Az Event séma azonban tartalmaz egy időbélyeget, amely a sorrend azonosítására szolgál a végponton az események megérkezése után.

## <a name="route-implementation"></a>Útvonal implementációja

Az Azure Digital Twins szolgáltatás jelenleg a következő **EndpointTypes**támogatja:

* A **EventHub** a Event Hubs a kapcsolatok karakterláncának végpontja.
* A **ServiceBus** a Service Bus a kapcsolatok karakterláncának végpontja.
* A **EventGrid** a Event Grid a kapcsolatok karakterláncának végpontja.

Az Azure Digital Twins jelenleg a következő **EventTypes** támogatja, amelyeket a rendszer a kiválasztott végpontnak küld:

* A **DeviceMessages** a felhasználói eszközökről küldött és a rendszer által továbbított telemetria üzenetek.
* A **TopologyOperation** olyan művelet, amely megváltoztatja a gráf gráfját vagy metaadatait. Ilyen például egy entitás hozzáadása vagy törlése, például egy szóköz.
* A **SpaceChange** egy hely számított értékének változása, amely az eszköz telemetria üzenetét eredményezi.
* A **SensorChange** az érzékelő számított értékének egy olyan változása, amely az eszköz telemetria üzenetét eredményezi.
* A **UdfCustom** egy felhasználó által definiált függvény egyéni értesítése.

> [!IMPORTANT]  
> Nem minden **EndpointTypes** támogatja az összes **EventTypes**.
> Az egyes **EndpointType**engedélyezett **EventTypes** a következő táblázat tartalmazza.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>A végpontok és az események sémájának létrehozásával kapcsolatos további információkért lásd: [kimenő és végpontok](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Következő lépések

- Az Azure Digital Twins előzetes verziójának korlátairól további információt a [nyilvános előzetes verzió szolgáltatási korlátai](concepts-service-limits.md)című témakörben talál.

- Egy Azure-beli digitális Twins-minta kipróbálásához tekintse meg a gyors üzembe helyezési lehetőséget a [rendelkezésre álló szobák megtalálásához](quickstart-view-occupancy-dotnet.md).