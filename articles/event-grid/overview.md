---
title: Közzététel és előfizetés alkalmazás események – Azure Event Griddel
description: Küldjön forrásból származó eseményadatokat az Azure Event Griddel kezelők számára. Esemény-alapú alkalmazásokat fejleszthet, és az Azure-szolgáltatásokkal integrálva.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 12/06/2018
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: 466f7614026866bb038f3c73b23e28e34d9f2e30
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321016"
---
# <a name="what-is-azure-event-grid"></a>Mi az Azure Event Grid?

Az Azure Event Griddel könnyen létrehozhat eseményalapú architektúrával rendelkező alkalmazásokat. Első lépésként válassza ki az Azure-erőforrás szeretne előfizetni, és adjon a az eseménykezelő vagy WebHook-végpontot, az eseményt. Az Event Grid beépített támogatást nyújt az Azure-szolgáltatásokból, például a tárolóblobokból és erőforráscsoportokból érkező eseményekhez. Az Event Grid egyéni témakörök használatával a saját események használatát is támogatja. 

Szűrők segítségével adott eseményeket irányíthat át különböző végpontokra, csoportos küldést végezhet több végpontra, és meggyőződhet róla, hogy az események megbízhatóan célba érnek.

Az Azure Event Grid jelenleg minden nyilvános régióban elérhető. Ez még nem érhető el az Azure Germany, az Azure China vagy az Azure Government-felhőben.

Ez a cikk az Azure Event Gridről nyújt áttekintést. Az Event Grid használatának első lépései: [Egyéni események létrehozása és átirányítása az Azure Event Griddel](custom-event-quickstart.md). 

![Event Grid modell források és a kezelők](./media/overview/functional-model.png)

Ez a kép bemutatja, hogyan Event Grid források és a kezelők csatlakozik, és nem támogatott Integrációk átfogó listáját.

## <a name="event-sources"></a>Eseményforrások

Az egyes források képességeire és a kapcsolódó cikkekre vonatkozó részletekért tekintse meg az [eseményforrásokat](event-sources.md). Jelenleg az alábbi Azure-szolgáltatások támogatják az események Event Gridbe való küldését:

* Azure-előfizetések (kezelési műveletek)
* Container Registry
* Egyéni témakörök
* Event Hubs
* IoT Hub
* Media Services
* Erőforráscsoportok (kezelési műveletek)
* Service Bus
* Storage Blob
* Általános célú v2- (GPv2-) tároló

## <a name="event-handlers"></a>Eseménykezelők

Az egyes kezelők képességeire és a kapcsolódó cikkekre vonatkozó részletekért tekintse meg az [eseménykezelőket](event-handlers.md). Jelenleg az alábbi Azure-szolgáltatások támogatják az Event Gridből származó események kezelését: 

* Azure Automation
* Azure Functions
* Event Hubs
* Hibrid kapcsolatok
* Logic Apps
* Microsoft Flow
* Queue Storage
* Webhookok

## <a name="concepts"></a>Alapelvek

Az Azure Event Grid öt fontos alapfogalma:

* **Események** – Mi történt.
* **Eseményforrások** – Ahol az esemény történt.
* **Témakörök** – A végpont, amelyre a közzétevők az eseményeket küldik.
* **Esemény-előfizetések** -események átirányítása, néha egynél on kezelő a végpontot, illetve a beépített mechanizmus. Az előfizetéseket a kezelők is használják a bejövő események intelligens szűrésére.
* **Eseménykezelők** – Az eseményre reagáló alkalmazás vagy szolgáltatás.

További információ ezekről a fogalmakról: [Az Azure Event Grid alapfogalmai](concepts.md).

## <a name="capabilities"></a>Funkciók

Az Azure Event Grid néhány főbb jellemzője:

* **Egyszerűség** – Egy kattintással irányíthatja az eseményeket az Azure-erőforrásából bármely eseménykezelőre vagy végpontra.
* **Speciális szűrés** -szűrő, az esemény típusa vagy esemény közzététele elérési útját, ellenőrizze, hogy eseménykezelők csak a releváns eseményeket fogadni.
* **Logikájával** – több végpontot is sok helyen szükség szerint példányait az esemény küldendő ugyanahhoz az eseményhez való előfizetés.
* **Megbízhatóság** – 24 órás újra egy exponenciális visszatartással, hogy kézbesíti az eseményeket.
* **Eseményenkénti fizetés** – Csak az Event Grid használatának mennyisége alapján kell fizetnie.
* **Nagy teljesítmény** – Nagy kapacitású számítási feladatokat hozhat létre az Event Griden, amelyek események millióit támogatják másodpercenként.
* **Beépített események** – Az erőforrások által meghatározott, beépített eseményekkel gyorsan munkához láthat.
* **Egyéni események** – Az Event Griddel egyéni eseményeket irányíthat át, szűrhet és kézbesíthet megbízhatóan az alkalmazásában.

Az Event Grid, az Event Hubs és a Service Bus összehasonlítása: [Az üzenetkézbesítő Azure-szolgáltatás kiválasztása](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Mire használhatom az Event Gridet?

Az Azure Event Grid biztosít több technológiáikkal nagymértékben növeljék a kiszolgáló nélküli funkciók, műveletek automatizálása, és [integrációs](http://azure.com/integration) működik: 

### <a name="serverless-application-architectures"></a>Kiszolgáló nélküli alkalmazásarchitektúrák

![Kiszolgáló nélküli architektúra](./media/overview/serverless_web_app.png)

Az Event Grid összeköti az adatforrásokat az eseménykezelőkkel. Ha például Event Grid használatával kiszolgáló nélküli függvény, amely elemzi a blob storage-tárolóba való hozzáadásakor aktiválja. 

### <a name="ops-automation"></a>Műveletek automatizálása

![Műveletek automatizálása](./media/overview/Ops_automation.png)

Az Event Griddel felgyorsíthatja az automatizálást és egyszerűsítheti a szabályzatok kikényszerítését. Ha például Event Grid használatával az Azure Automation értesítése, ha a virtuális gép vagy SQL-adatbázis létrehozása. Használatával az események automatikusan ellenőrizzük, hogy a szolgáltatáskonfiguráció megfelelő, put metaadatok műveleti eszközöknek, címke virtuális gépek vagy munkatétel-fájlok.

### <a name="application-integration"></a>Alkalmazásintegráció

![Alkalmazásintegráció az Azure-ral](./media/overview/app_integration.png)

Az Event Grid más szolgáltatásokkal kapcsolja össze alkalmazását. Létrehozhat például egy egyéni témakört, hogy az alkalmazása eseményadatait az Event Gridbe küldje, és kihasználja az általa kínált megbízható kézbesítést, fejlett átirányítást és a közvetlen Azure-integrációt. Vagy használhatja a Logic Apps Event Grid adatfeldolgozásra tetszőleges helyen, kód írása nélkül. 

## <a name="how-much-does-event-grid-cost"></a>Mennyibe kerül az Event Grid?

Az Azure Event Grid árképzése eseményenkénti fizetésen alapul, tehát a fizetés a használat alapján történik. A havi első 100 000 művelet ingyenes. A műveletek a következőképpen vannak meghatározva: események bejövő forgalma, előfizetési kézbesítési kísérletek, kezelési hívások és tárgyutótag szerinti szűrés. A részleteket lásd az [árképzést ismertető oldalon](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>További lépések

* [Storage Blob-események útválasztása](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Az Event Grid használatával válaszolhat a Storage Blob-eseményekre.
* [Egyéni események: létrehozás és előfizetés](custom-event-quickstart.md)  
  Lásson azonnal munkához, és küldje el saját egyéni eseményeit egy végpontra az Azure Event Grid rövid útmutatója alapján.
* [A Logic Apps használata eseménykezelőként](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Ez az oktatóanyag bemutatja egy olyan alkalmazás létrehozását a Logic Appsszel, amely az Event Grid által leküldött eseményekre reagál.
* [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)  
  Ez az oktatóanyag az Azure Functions használatával streameli az adatokat az Event Hubsból az SQL Data Warehouse-ba.
* [Event Grid REST API-referencia](/rest/api/eventgrid)  
  Segédanyagok biztosít esemény-előfizetések kezeléséhez útválasztást és a szűrést.
