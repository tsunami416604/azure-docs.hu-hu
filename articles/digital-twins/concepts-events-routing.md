---
title: Útválasztási események és az Azure digitális Twins üzenetek |} A Microsoft Docs
description: Útválasztási események és üzeneteket az Azure digitális Twins Szolgáltatásvégpontok áttekintése
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: a1a31ec7ee0d1daea9f178e51dc860279d3787ec
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615347"
---
# <a name="routing-events-and-messages"></a>Események és üzenetek útválasztása

IoT-megoldások gyakran több hatékony szolgáltatás, amely tartalmazza a tárolási, elemzési és egyéb egyesítése. Ez a cikk ismerteti az Azure digitális Twins alkalmazások csatlakoztatása az Azure analytics, a mesterséges Intelligencia és a storage-szolgáltatások tegye lehetővé számukra részletesebb elemzéseket és funkciókat.

## <a name="route-types"></a>Útválasztási típus  

Az Azure digitális Twins IoT-események integrálása más Azure-szolgáltatások vagy alkalmazások két lehetőséget kínál:

* **Útválasztási Azure digitális Twins események**: a térbeli objektum graph a módosításokat, a telemetriai adatokat fogadott, vagy a felhasználó által definiált függvény, amely létrehoz egy előre meghatározott feltételek alapján értesítést is aktiválhatja az Azure digitális Twins eseményeket. A felhasználók elküldhetik ezeket az eseményeket az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure Service Bus-üzenettémakörök](https://azure.microsoft.com/services/service-bus/), vagy [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) további feldolgozás céljából.

* **Útválasztási eszköztelemetria**: útválasztási események mellett az Azure digitális Twins is irányíthatja nyers eszköz telemetriai üzeneteket az Event hubs szolgáltatásba a további információkhoz és elemzésekhez juthat. Az ilyen típusú üzenetek nem dolgozza fel az Azure digitális Twins. És csak az event hubs már továbbítva.

A felhasználók megadhatják a kimenő forgalom egy vagy több végpontot küldjön ki eseményt vagy üzenetek továbbítására. Események és üzeneteket küld a végpontokhoz ezek előre meghatározott útválasztási beállítások szerint. Más szóval a felhasználók megadhatnak egy bizonyos végpont graph művelet események fogadásához egy másik eszköz telemetriai események fogadásához, és így tovább.

![Útválasztás az Azure digitális Twins események][1]

A sorrendet, amelyben telemetriai üzeneteket küld az Event Hubs az Útválasztás tart fenn. Így azok megérkeznek a végpont ugyanabban a sorrendben, ahogy eredetileg megkapta. Event Grid és a Service Bus nem garantálják, hogy végpontok kapja, hogy azok történt ugyanabban a sorrendben. Az esemény-séma azonban használhatók a rendelés azonosítása után az esemény érkezik, a végpont a következő időbélyeget tartalmaz.

## <a name="route-implementation"></a>Útvonal-megvalósítás

Az Azure digitális Twins szolgáltatás jelenleg támogatja a következő **EndpointTypes**:

* **Az EventHub** az Event Hubs kapcsolati karakterlánc végpontja.
* **A ServiceBus** van a Service Bus kapcsolati karakterlánc végpont.
* **EventGrid** Event Grid kapcsolati karakterlánc végpontja.

Az Azure digitális Twins jelenleg támogatja a következő **EventTypes** , amely a kiválasztott végpont küld:

* **DeviceMessages** telemetriai üzeneteket küld a felhasználók eszközeiről vagy a rendszer továbbítja.
* **TopologyOperation** , amely módosítja a graph vagy a gráf metaadat-művelet. Példa hozzáadása vagy törlése egy entitás, például egy szóközzel.
* **SpaceChange** kiszámított érték egy helyet, amely egy eszköz telemetriai üzenetet való változás történik.
* **SensorChange** kiszámított érték egy érzékelő által eszköz telemetriai üzenetet változás történik.
* **UdfCustom** egy egyéni értesítési egy felhasználó által definiált függvény.

> [!IMPORTANT]  
> Nem minden **EndpointTypes** támogatja az összes **EventTypes**.
> Tekintse meg a következő táblázat tartalmazza a **EventTypes** , hogy minden engedélyezett **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Végpontok és a példákat események séma létrehozásához további információkért lásd: [kimenő forgalom és a végpontok](how-to-egress-endpoints.md).

## <a name="next-steps"></a>További lépések

- További információ az Azure digitális Twins előzetes verzió korlátai,: [nyilvános előzetes verziójú szolgáltatásokra vonatkozó korlátozások](concepts-service-limits.md).
- Próbálja ki az Azure digitális Twins minta, tekintse meg a [rövid útmutatóban elérhető teremkeresés](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
