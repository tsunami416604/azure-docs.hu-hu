---
title: Mi az Azure Event Grid?
description: Eseményadatok küldése egy forrásból a kezelők az Azure Event Grid használatával. Eseményalapú alkalmazásokat hozhat létre, és integrálható az Azure-szolgáltatásokkal.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 02/20/2020
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: 65fd9d06c8b25e0aff94429f8eb95bb922da330c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77505909"
---
# <a name="what-is-azure-event-grid"></a>Mi az Azure Event Grid?

Az Azure Event Griddel könnyen létrehozhat eseményalapú architektúrával rendelkező alkalmazásokat. Először válassza ki azt az Azure-erőforrást, amelyre elő szeretne fizetni, majd adja meg az eseménykezelőnek vagy a WebHook-végpontnak az esemény elküldéséhez. Az Event Grid beépített támogatást nyújt az Azure-szolgáltatásokból, például a tárolóblobokból és erőforráscsoportokból érkező eseményekhez. Az Event Grid egyéni témakörök használatával a saját események használatát is támogatja. 

Szűrők segítségével adott eseményeket irányíthat át különböző végpontokra, csoportos küldést végezhet több végpontra, és meggyőződhet róla, hogy az események megbízhatóan célba érnek.

Az Azure Event Grid üzembe helyezése a rendelkezésre állás maximalizálása érdekében érhető el, mivel minden régióban és a rendelkezésre állási zónákközött (az őket támogató régiókban) natív módon több tartalék tartományközött is elérhető. Az Event Grid által támogatott régiók listáját a [Termékek régiónként elérhető című](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)témakörben tájékozott.

Ez a cikk az Azure Event Gridről nyújt áttekintést. Az Event Grid használatának első lépései: [Egyéni események létrehozása és átirányítása az Azure Event Griddel](custom-event-quickstart.md). 

![A források és kezelők Eseményrács modellje](./media/overview/functional-model.png)

Ez a kép azt mutatja be, hogy az Event Grid hogyan kapcsolja össze a forrásokat és a kezelőket, és nem a támogatott integrációk átfogó listáját tartalmazza.

## <a name="event-sources"></a>Eseményforrások

Az egyes források képességeire és a kapcsolódó cikkekre vonatkozó részletekért tekintse meg az [eseményforrásokat](event-sources.md). Jelenleg az alábbi Azure-szolgáltatások támogatják az események Event Gridbe való küldését:

* [Azure-előfizetések (kezelési műveletek)](event-sources.md#azure-subscriptions)
* [Tároló beállításjegyzéke](event-sources.md#container-registry)
* [Egyéni témakörök](event-sources.md#custom-topics)
* [Eseményközpontok](event-sources.md#event-hubs)
* [IoT Hub](event-sources.md#iot-hub)
* [Key Vault (előzetes verzió)](event-sources.md#key-vault-preview)
* [Médiaszolgáltatások](event-sources.md#media-services)
* [Erőforráscsoportok (kezelési műveletek)](event-sources.md#resource-groups)
* [Service Bus](event-sources.md#service-bus)
* [Storage Blob](event-sources.md#storage)
* [Azure Maps](event-sources.md#maps)
* [Alkalmazás konfigurációja](event-sources.md#app-configuration)
* [R jel](event-sources.md#azure-signalr)
* [Machine Learning](event-sources.md#azure-machine-learning)

## <a name="event-handlers"></a>Eseménykezelők

Az egyes kezelők képességeire és a kapcsolódó cikkekre vonatkozó részletekért tekintse meg az [eseménykezelőket](event-handlers.md). Jelenleg az alábbi Azure-szolgáltatások támogatják az Event Gridből származó események kezelését: 

* [Azure-automatizálás](event-handlers.md#azure-automation)
* [Azure Functions](event-handlers.md#azure-functions)
* [Eseményközpontok](event-handlers.md#event-hubs)
* [Hibrid kapcsolatok](event-handlers.md#hybrid-connections)
* [Logic Apps](event-handlers.md#logic-apps)
* [Power Automate (korábbi nevén Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](event-handlers.md#service-bus)
* [Queue Storage](event-handlers.md#queue-storage)
* [Webhookok](event-handlers.md#webhooks)

## <a name="concepts"></a>Alapelvek

Az Azure Event Grid öt fontos alapfogalma:

* **Események** – Mi történt.
* **Eseményforrások** – Ahol az esemény történt.
* **Témakörök** – A végpont, amelyre a közzétevők az eseményeket küldik.
* **Esemény-előfizetések** – A végpont vagy a beépített mechanizmus események, néha több kezelő. Az előfizetéseket a kezelők is használják a bejövő események intelligens szűrésére.
* **Eseménykezelők** – Az eseményre reagáló alkalmazás vagy szolgáltatás.

További információ ezekről a fogalmakról: [Az Azure Event Grid alapfogalmai](concepts.md).

## <a name="capabilities"></a>Funkciók

Az Azure Event Grid néhány főbb jellemzője:

* **Egyszerűség** – Egy kattintással irányíthatja az eseményeket az Azure-erőforrásából bármely eseménykezelőre vagy végpontra.
* **Speciális szűrés** – Az eseménytípus ra vagy az eseményközzétételi útvonalra szűrve győződjön meg arról, hogy az eseménykezelők csak a releváns eseményeket fogadják.
* **Fan-out** - Feliratkozás több végpontok ugyanarra az eseményre, hogy küldje el az esemény másolatát, hogy annyi helyen, mint szükséges.
* **Megbízhatóság** – 24 órás újrapróbálkozás exponenciális visszalépéssel, hogy megbizonyosodjon arról, hogy az események kézbesítésre kerülnek.
* **Eseményenkénti fizetés** – Csak az Event Grid használatának mennyisége alapján kell fizetnie.
* **Nagy teljesítmény** – Nagy kapacitású számítási feladatokat hozhat létre az Event Griden, amelyek események millióit támogatják másodpercenként.
* **Beépített események** – Az erőforrások által meghatározott, beépített eseményekkel gyorsan munkához láthat.
* **Egyéni események** – Az Eseményrács használatával egyéni eseményeket irányíthatja, szűrheti és megbízhatóan kézbesítheti az alkalmazásban.

Az Event Grid, az Event Hubs és a Service Bus összehasonlítása: [Az üzenetkézbesítő Azure-szolgáltatás kiválasztása](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Mire használhatom az Event Gridet?

Az Azure Event Grid számos olyan funkciót kínál, amelyek nagymértékben javítják a kiszolgáló nélküli, a műveleti funkciók automatizálását és [az integrációs](https://azure.com/integration) munkát: 

### <a name="serverless-application-architectures"></a>Kiszolgáló nélküli alkalmazásarchitektúrák

![Kiszolgáló nélküli alkalmazásarchitektúra](./media/overview/serverless_web_app.png)

Az Event Grid összeköti az adatforrásokat az eseménykezelőkkel. Például az Event Grid használatával elindíthategy kiszolgáló nélküli függvényt, amely elemzi a lemezképeket, amikor hozzáadódik egy blob tárolótárolóhoz. 

### <a name="ops-automation"></a>Műveletek automatizálása

![Műveletek automatizálása](./media/overview/Ops_automation.png)

Az Event Griddel felgyorsíthatja az automatizálást és egyszerűsítheti a szabályzatok kikényszerítését. Például az Event Grid használatával értesítheti az Azure Automationt, ha egy virtuális gép vagy SQL-adatbázis jön létre. Az események segítségével automatikusan ellenőrizheti, hogy a szolgáltatáskonfigurációk megfelelőek-e, metaadatokat helyezhet el a műveleti eszközökben, a virtuális gépek címkézésén vagy a fájlmunkaelemekben.

### <a name="application-integration"></a>Alkalmazásintegráció

![Alkalmazásintegráció az Azure-ral](./media/overview/app_integration.png)

Az Event Grid más szolgáltatásokkal kapcsolja össze alkalmazását. Létrehozhat például egy egyéni témakört, hogy az alkalmazása eseményadatait az Event Gridbe küldje, és kihasználja az általa kínált megbízható kézbesítést, fejlett átirányítást és a közvetlen Azure-integrációt. Vagy használhatja event grid logikai alkalmazások feldolgozni az adatokat bárhol, kód írása nélkül. 

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
  Referenciatartalmat tartalmaz az esemény-előfizetések, az útválasztás és a szűrés kezeléséhez.
