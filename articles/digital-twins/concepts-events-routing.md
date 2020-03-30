---
title: Útválasztási események és üzenetek - Azure Digital Twins | Microsoft dokumentumok
description: Az útválasztási események és üzenetek áttekintése a szolgáltatásvégpontok számára az Azure Digital Twins segítségével
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 65b760eaf28d907fab3654ed92f960be7556b0d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862356"
---
# <a name="routing-iot-events-and-messages"></a>IoT-események és -üzenetek útválasztása

A dolgok internete megoldások gyakran számos hatékony szolgáltatást egyesítenek, amelyek magukban foglalják a tárolást, az elemzést és még sok mást. Ez a cikk bemutatja, hogyan kapcsolhatja össze az Azure Digital Twins-alkalmazásokat az Azure Analytics, a I és a storage-szolgáltatások között, hogy mélyebb betekintést és funkciókat biztosítson számukra.

## <a name="route-types"></a>Útvonaltípusok  

Az Azure Digital Twins kétféleképpen kapcsolhatja össze az IoT-eseményeket más Azure-szolgáltatásokkal vagy üzleti alkalmazásokkal:

* **Útválasztás Az Azure Digital Twins események:** A térbeli gráfban, amely módosítja, telemetriai adatokat kapott, vagy egy felhasználó által definiált függvény, amely létrehoz egy értesítést előre meghatározott feltételek alapján aktiválhatja az Azure Digital Twins események. A felhasználók ezeket az eseményeket [elküldhetik az Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/)az Azure Service Bus [témakörei](https://azure.microsoft.com/services/service-bus/)vagy [az Azure Event Grid](https://azure.microsoft.com/services/event-grid/) további feldolgozásra.

* **Útválasztási eszköz telemetriai adatok:** Az útválasztási események mellett az Azure Digital Twins is átirányíthatja a nyers eszköz telemetriai üzeneteket az Event Hubs további elemzési és elemzési. Az ilyen típusú üzeneteket az Azure Digital Twins nem dolgozza fel. És csak az eseményközpontba továbbítják őket.

A felhasználók megadhatnak egy vagy több kimenő végpontot események küldéséhez vagy üzenetek továbbításához. Az események és az üzenetek az előre meghatározott útválasztási beállításoknak megfelelően kerülnek elküldésre a végpontok számára. Más szóval a felhasználók megadhatja egy adott végpontot a graph-művelet események fogadásához, egy másikat az eszköz telemetriai eseményeinek fogadásához és így tovább.

[![Azure Digital Twins események útválasztása](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Útválasztás az Event Hubs fenntartja a telemetriai üzenetek küldésének sorrendjét. Így ugyanabban a sorrendben érkeznek a végponthoz, mint amilyeneket eredetileg kaptak. 

Event Grid és a Service Bus nem garantálja, hogy a végpontok fogadása események ugyanabban a sorrendben, hogy előfordultak. Az eseményséma azonban tartalmaz egy időbélyeget, amely a rendelés azonosítására használható, miután az események a végpontra érkeznek.

## <a name="route-implementation"></a>Útvonal-megvalósítás

Az Azure Digital Twins szolgáltatás jelenleg a következő **EndpointTypes típusokat**támogatja:

* **EventHub** az Event Hubs kapcsolati karakterlánc végpont.
* **ServiceBus** a Service Bus kapcsolati karakterlánc végpont.
* **Az EventGrid** az Event Grid kapcsolati karakterláncvégpontja.

Az Azure Digital Twins jelenleg a következő **EventTypes-eket** támogatja, amelyeket a rendszer a kiválasztott végpontra küld:

* **DeviceMessages** telemetriai üzeneteket küldött a felhasználók eszközeiről, és a rendszer által továbbított.
* **TopologyOperation** olyan művelet, amely módosítja a grafikon vagy a metaadatok a diagram. Példa egy entitás, például szóköz hozzáadására vagy törlésére.
* **A SpaceChange** a tér számított értékének változása, amely egy eszköz telemetriai üzenetéből származik.
* **A SensorChange** az érzékelő számított értékének változása, amely egy eszköz telemetriai üzenetéből származik.
* **UdfCustom** egy egyéni értesítés egy felhasználó által definiált függvény.

> [!IMPORTANT]  
> Nem minden **EndpointTypes** támogatja az összes **EventTypes**.
> Tekintse át az alábbi táblázatot az egyes EndpointType típusokhoz engedélyezett **EventTypes** **típusokról.**

|             | DeviceMessages | TopológiaÜzemeltetés | Térváltozás | Érzékelőmódosítása | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>A végpontok és az események sémájára vonatkozó példák létrehozásáról további információt az [Egresés és a végpontok című részében talál.](how-to-egress-endpoints.md)

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az Azure Digital Twins előzetes verziókorlátairól, olvassa el [a Nyilvános előzetes verzió szolgáltatáskorlátait.](concepts-service-limits.md)

- Az Azure Digital Twins-minta kipróbálásához olvassa el a rövid útmutatót a [rendelkezésre álló szobák megkereséséhez.](quickstart-view-occupancy-dotnet.md)