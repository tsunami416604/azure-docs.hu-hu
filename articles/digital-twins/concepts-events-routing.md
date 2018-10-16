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
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324291"
---
# <a name="routing-events-and-messages"></a>Útválasztási események és üzenetek

IoT-megoldások gyakran több nagy teljesítményű szolgáltatásokkal, beleértve a tárolási, elemzési és egyéb egyesítése. Ez a cikk ismerteti, hogyan csatlakozhat az Azure analytics, a mesterséges Intelligencia és a storage-szolgáltatásokhoz az insights és a funkciók mélyebb kapcsolódva Azure digitális Twins alkalmazások.

## <a name="route-types"></a>Útválasztási típus

Digitális Twins IoT-események integrálása más Azure-szolgáltatások vagy alkalmazások két lehetőséget kínál:

* **Útválasztási digitális Twins események**: az Azure digitális Twins események is elindítható, amikor egy objektum a térbeli metaadatgráf-módosítások, amikor telemetriai adatok érkeznek, vagy amikor egy felhasználó által megadott függvény létrehozza az előre meghatározott feltételek alapján értesítést. A felhasználók elküldhetik ezeket az eseményeket az [az Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Service Bus-üzenettémakörök](https://azure.microsoft.com/services/service-bus/), vagy [esemény rácsok](https://azure.microsoft.com/services/event-grid/) további feldolgozás céljából.

* **Útválasztási eszköztelemetria**: útválasztási események mellett az Azure digitális Twins is irányíthatja nyers eszköz telemetriai üzeneteket az Event hubs szolgáltatásba a további információkhoz és elemzésekhez juthat. Az ilyen típusú üzenetek nem dolgozza fel digitális Twins, és csak a rendszer továbbítja a **Eseményközpont**.

A felhasználók megadhatják a kimenő forgalom egy vagy több végpontot küldjön ki eseményt vagy üzenetek továbbítására. Események és üzeneteket küld a végpontokhoz ezek előre meghatározott útválasztási beállítások szerint. Más szóval a felhasználók megadhatják graph művelet események fogadásához bizonyos egy végpontot egy másik eszköz telemetriai események fogadásához, és így tovább.

![Útválasztás digitális Twins események][1]

Az Útválasztás **az Event Hubs** megőrzi a melyik telemetriai üzeneteket küldenek, sorrendben úgy, hogy azok beérkeznek az végponton ugyanabban a sorrendben, ahogy eredetileg megkapta. **Event Grid** és **a Service Bus** nem garantálják, hogy végpontok kapja, hogy azok történt ugyanabban a sorrendben. Azonban az esemény sémája a következő időbélyeget segítségével azonosíthatja a rendelés után az esemény érkezik a végponton tartalmazza.

## <a name="route-implementation"></a>Útvonal-megvalósítás

Az Azure digitális Twins szolgáltatás jelenleg támogatja a következő **EndpointTypes**:

* **Az EventHub**: az Eseményközpont kapcsolati karakterlánc végpontja.
* **A ServiceBus**: Service Bus kapcsolati karakterlánc végpontja.
* **EventGrid**: az Event Grid kapcsolati karakterlánc végpontja.

Az Azure digitális Twins jelenleg támogatja a következő **EventTypes** , amely a kiválasztott végpont küld:

* **DeviceMessages**: telemetriai üzeneteket küld a felhasználók eszközeiről vagy a rendszer továbbítja.
* **TopologyOperation**: műveletek, amelyek a graph és a gráf metaadatok módosítása. Ha például felvételét vagy törlését egy entitás, például egy szóközzel.
* **SpaceChange**: módosításokat egy helyet a kiszámított érték eredményeként egy eszköz telemetriai üzenetet is.
* **SensorChange**: egy érzékelő változásai kiszámított érték eredményeként egy eszköz telemetriai üzenetet is.
* **UdfCustom**: egyéni értesítések egy felhasználó által definiált függvény.

> [!IMPORTANT]
> Nem minden **EndpointTypes** támogatja az összes **EventTypes**.
> Az alábbi táblázat a **EventTypes** , hogy minden engedélyezett **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **EventHub**|     X          |         X         |     X       |      X       |   X       |
| **Szolgáltatásbusz**|              |         X         |     X       |      X       |   X       |
| **EventGrid**|               |         X         |     X       |      X       |   X       |

>[!NOTE]
>Végpontok és a példákat események séma létrehozásához további részletekért tekintse meg [végpontok és a kimenő forgalom](how-to-egress-endpoints.md).

## <a name="next-steps"></a>További lépések

Nyilvános előzetes verzió korlátai kapcsolatos további információkért olvassa el a [Azure digitális Twins előzetes verzió korlátai](concepts-service-limits.md).

Próbálja ki az Azure digitális Twins minta, olvassa el [rövid útmutatóban elérhető teremkeresés](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png