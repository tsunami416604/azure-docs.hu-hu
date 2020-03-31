---
title: Azure Event Grid eseményforrások
description: Azure-előfizetések, Tárolóbeállítás, egyéni témakörök, Event Hubs, IoT Hub, Key Vault, Media Services, erőforráscsoportok, Service Bus, Storage, Térképek, Alkalmazáskonfiguráció, Signal R, Machine Learning.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: d4a426ea1432d0266b7ae9344afefe8ddac1d030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264998"
---
# <a name="event-sources-in-azure-event-grid"></a>Eseményforrások az Azure Event Gridben

Az eseményforrás az, ahol az esemény történik. Számos Azure-szolgáltatás automatikusan konfigurálva van az események küldésére. Eseményeket küldő egyéni alkalmazásokat is létrehozhat. Egyéni alkalmazások nem kell üzemeltetni az Azure-ban, hogy az Event Grid eseményterjesztéshez.

Ez a cikk az egyes eseményforrások tartalmára mutató hivatkozásokat tartalmaz.

## <a name="azure-subscriptions"></a>Azure-előfizetések

Iratkozzon fel az Azure-előfizetések eseményeire, hogy reagáljon az erőforrások változásaira egy Azure-előfizetésben.

|Cím |Leírás  |
|---------|---------|
| [Oktatóanyag: Azure Automation az Event Griddel és a Microsoft Teamssegítségével](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, amely egy eseményt küld. Az esemény elindítja az Automation runbookot, amely a virtuális gépet címkézi, és egy Microsoft Teams-csatornának küldött üzenetet indít el. |
| [Hogyan: feliratkozás eseményekre a portálon keresztül](subscribe-through-portal.md) | A portál használatával előfizethet egy Azure-előfizetés eseményeire. |
| [Azure CLI: Előfizetés eseményekre egy Azure-előfizetéshez](./scripts/event-grid-cli-azure-subscription.md) |Minta parancsfájl, amely létrehoz egy Event Grid-előfizetés egy Azure-előfizetés, és eseményeket küld a WebHook. |
| [PowerShell: előfizetés eseményekre egy Azure-előfizetéshez](./scripts/event-grid-powershell-azure-subscription.md)| Minta parancsfájl, amely létrehoz egy Event Grid-előfizetés egy Azure-előfizetés, és eseményeket küld a WebHook. |
| [Eseményséma](event-schema-subscriptions.md) | Mezők megjelenítése az Azure-előfizetési eseményekben. |

## <a name="container-registry"></a>Container Registry

Iratkozzon fel a Container Registry eseményekre a lemezképek változásainak megválaszolásához.

|Cím |Leírás  |
|---------|---------|
| [Rövid útmutató: tárolóbeállítási események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan azure CLI container registry események küldéséhez. |
| [Eseményséma](event-schema-container-registry.md) | Mezők megjelenítése a Tárolóbeállítás-bejegyzések eseményeiben. |

## <a name="custom-topics"></a>Egyéni témakörök

Iratkozzon fel egyéni témakörökre az alkalmazásesemények megválaszolásához.

|Cím  |Leírás  |
|---------|---------|
| [Rövid útmutató: egyéni események létrehozása és irányítása az Azure CLI-vel](custom-event-quickstart.md) | Bemutatja, hogyan használhatja az Azure CLI-t egyéni események küldésére. |
| [Rövid útmutató: egyéni események létrehozása és irányítása az Azure PowerShell használatával](custom-event-quickstart-powershell.md) | Bemutatja, hogyan használhatja az Azure PowerShellt egyéni események küldésére. |
| [Rövid útmutató: egyéni események létrehozása és irányítása az Azure Portalon](custom-event-quickstart-portal.md) | Bemutatja, hogyan lehet a portál használatával egyéni eseményeket küldeni. |
| [Rövid útmutató: egyéni események irányítása az Azure Queue storage-ba](custom-event-to-queue-storage.md) | Ez a témakör azt ismerteti, hogy miként küldhet egyéni eseményeket várólista-tárolóba. |
| [Hogyan: tegye az egyéni téma](post-to-custom-topic.md) | Bemutatja, hogyan lehet eseményt közzétenni egy egyéni témakörben. |
| [Azure CLI: egyéni témakör létrehozása az Event Grid ben](./scripts/event-grid-cli-create-custom-topic.md)|Mintaparancsfájl, amely egyéni témakört hoz létre. A parancsfájl beolvassa a végpontot és egy kulcsot.|
| [Azure CLI: előfizetni eseményekre egy egyéni témakörhöz](./scripts/event-grid-cli-subscribe-custom-topic.md)|Mintaparancsfájl, amely előfizetést hoz létre egy egyéni témakörhöz. Eseményeket küld egy WebHook.It sends events to a WebHook.|
| [PowerShell: event grid egyéni témakör létrehozása](./scripts/event-grid-powershell-create-custom-topic.md)|Mintaparancsfájl, amely egyéni témakört hoz létre. A parancsfájl beolvassa a végpontot és egy kulcsot.|
| [PowerShell: feliratkozás egyéni témakör eseményeire](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Mintaparancsfájl, amely előfizetést hoz létre egy egyéni témakörhöz. Eseményeket küld egy WebHook.It sends events to a WebHook.|
| [Resource Manager-sablon: egyéni témakör és WebHook-végpont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Erőforrás-kezelő sablon, amely egyéni témakört és előfizetést hoz létre az adott egyéni témakörhöz. Eseményeket küld egy WebHook.It sends events to a WebHook. |
|
| [Erőforrás-kezelő sablon: egyéni témakör és Eseményközpontok végpontja](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Erőforrás-kezelő sablon, amely előfizetést hoz létre egy egyéni témakörhöz. Eseményeket küld egy Azure Event Hubs. |
| [Eseményséma](event-schema.md) | Egyéni események mezőit jeleníti meg. |

## <a name="event-hubs"></a>Event Hubs

Iratkozzon fel az Event Hubs eseményekre a Rögzítés fájleseményeire való válaszadáshoz. Az Event Hubs eseményforrásként vagy eseménykezelőként működhet. Az alábbi cikkek bemutatják, hogyan használhatja az Event Hubs forrásként.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: big data streamelése adattárházba](event-grid-event-hubs-integration.md) | Amikor az Event Hubs rögzítési fájlt hoz létre, az Event Grid eseményt küld egy függvényalkalmazásnak. Az alkalmazás lekéri a Capture fájlt, és áttelepíti az adatokat egy adattárházba. |
| [Eseményséma](event-schema-event-hubs.md) | Mezők megjelenítése az Event Hubs eseményekben. |

Az Eseményközpontok kezelőként való példázata az [Eseményközpontok kezelője](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Iratkozzon fel az IoT Hub-eseményekre a létrehozott, törölt, csatlakoztatott, leválasztott és telemetriai eseményekre való válaszadáshoz.

|Cím  |Leírás  |
|---------|---------|
| [Azure IoT Hub-eseményekkel kapcsolatos e-mail-értesítések küldése a Logic Apps használatával](publish-iot-hub-events-to-logic-apps.md) | A logikai alkalmazás értesítést küld e-mailt minden alkalommal, amikor egy eszköz hozzá adódik az IoT Hubhoz. |
| [Reagálás az IoT Hub-eseményekre az Event Grid használatával műveletek et indíthat el](../iot-hub/iot-hub-event-grid.md) | Az IoT Hub és az Event Grid integrálásának áttekintése. |
| [Eseményséma](event-schema-iot-hub.md) | Mezők megjelenítése az IoT Hub-eseményekben. |
| [Eszköz csatlakoztatva és az eszköz leválasztott eseményeinek megrendelése](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Az eszközkapcsolat állapoteseményeinek rendelése. |

## <a name="key-vault-preview"></a>Key Vault (előzetes verzió)

A Key Vault és az Event Grid integrációja jelenleg előzetes verzióban érhető el. 

Iratkozzon fel a Key Vault-eseményekértesítést, ha egy titkos titok hamarosan lejár, egy titkos titok lejár, vagy egy titkos kulcs egy új verzió érhető el. 

|Cím  |Leírás  |
|---------|---------|
| [Key Vault-események figyelése az Azure Event Griddel](../key-vault/event-grid-overview.md) | A Key Vault és az Event Grid integrálásának áttekintése. |
| [Oktatóanyag: Key Vault-események létrehozása és figyelése az Event Grid segítségével](../key-vault/event-grid-tutorial.md) | További információ az Event Grid-értesítések key vaulthoz való beállításáról. |
| [Eseményséma](event-schema-key-vault.md) | Mezők megjelenítése a Key Vault-eseményekben. |

## <a name="media-services"></a>Media Services

Iratkozzon fel a Media Services-eseményekre, hogy válaszoljon a feladatállapot-eseményekre.

|Cím  |Leírás  |
|---------|---------|
| [Áttekintés: reagálás a Media Services eseményeire](../media-services/latest/reacting-to-media-services-events.md) | A Media Services és az Event Grid integrálásának áttekintése. |
| [Oktatóanyag: az Azure Media Services-események irányítása egy egyéni webes végpontra a CLI használatával](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet eseményeket a Media Services szolgáltatásból. |
| [Eseményséma](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Media Services-események mezőinek megjelenítése. |

## <a name="resource-groups"></a>Erőforráscsoportok

Iratkozzon fel az erőforráscsoport eseményeire, hogy reagáljon az erőforrások ban bekövetkező változásokra egy erőforráscsoportban.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: a virtuális gépek változásainak figyelése az Azure Event Grid és a Logic Apps alkalmazásokkal](monitor-virtual-machine-changes-event-grid-logic-app.md) | A logikai alkalmazás figyeli a virtuális gép módosításait, és e-maileket küld ezekről a változásokról. |
| [Azure CLI: előfizetni eseményekre egy erőforráscsoport](./scripts/event-grid-cli-resource-group.md)| Mintaparancsfájl, amely előfizet egy erőforráscsoport eseményeire. Eseményeket küld egy WebHook.It sends events to a WebHook. |
| [Azure CLI: előfizetni események egy erőforráscsoport és szűrő egy erőforrás](./scripts/event-grid-cli-resource-group-filter.md) | Minta parancsfájl, amely előfizet egy erőforráscsoport eseményeire, és szűri az eseményeket egy erőforráshoz. |
| [PowerShell: előfizetni eseményekegy erőforráscsoport](./scripts/event-grid-powershell-resource-group.md) | Mintaparancsfájl, amely előfizet egy erőforráscsoport eseményeire. Eseményeket küld egy WebHook.It sends events to a WebHook. |
| [PowerShell: előfizetni események egy erőforráscsoport és szűrő egy erőforrás](./scripts/event-grid-powershell-resource-group-filter.md) | Minta parancsfájl, amely előfizet egy erőforráscsoport eseményeire, és szűri az eseményeket egy erőforráshoz. |
| [Erőforrás-kezelő sablon: erőforrás-előfizetés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Előfizet egy Azure-előfizetés vagy erőforráscsoport eseményeire. Eseményeket küld egy WebHook.It sends events to a WebHook. |
| [Eseményséma](event-schema-resource-groups.md) | Az erőforráscsoport-események mezőinek megjelenítése. |

## <a name="service-bus"></a>Service Bus

Iratkozzon fel a Service Bus-eseményekre, hogy aktív figyelő nélkül válaszoljon az üzenetekre.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: Azure Service Bus az Azure Event Grid integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Event Grid üzeneteket küld a Service Bus témakörből a függvényalkalmazásnak és a logikai alkalmazásnak. |
| [Áttekintés: Azure Service Bus az Event Grid-integrációhoz](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | A Service Bus és az Event Grid integrálásának áttekintése. |
| [Eseményséma](event-schema-service-bus.md) | A Service Bus-események mezőinek megjelenítése. |

## <a name="storage"></a>Storage

Iratkozzon fel a Blob Storage-eseményekre a létrehozott és törölt blobesemények megválaszolásához.

>[!NOTE]
> Csak **a storageV2 (általános célú v2)** és **a BlobStorage** támogatási eseményintegrációs tárfiókok. **A storage (általános célú v1)** *nem* támogatja az Event Griddel való integrációt.

|Cím  |Leírás  |
|---------|---------|
| [Rövid útmutató: Blob-tárolási események irányítása egyéni webes végpontra az Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan azure CLI blob tárolási események küldése a WebHook. |
| [Rövid útmutató: Blob-tárolási események irányítása egy egyéni webes végpontra a PowerShell segítségével](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan használhatja az Azure PowerShell tblob-tárolási eseményeket egy WebHook-ra. |
| [Rövid útmutató: Blob-tárolási események létrehozása és irányítása az Azure Portalon](blob-event-quickstart-portal.md) | Bemutatja, hogyan használhatja a portálblob-tárolási események et a WebHook-nak. |
| [Azure CLI: Előfizetni eseményekre egy Blob storage-fiók](./scripts/event-grid-cli-blob.md) | Minta parancsfájl, amely előfizet egy blob tárfiók eseményére. Az eseményt egy WebHook-ra küldi. |
| [PowerShell: előfizetni eseményekre egy Blob storage-fiók](./scripts/event-grid-powershell-blob.md) | Minta parancsfájl, amely előfizet egy blob tárfiók eseményére. Az eseményt egy WebHook-ra küldi. |
| [Erőforrás-kezelő sablon: Blob-tárterület és -előfizetés létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Üzembe helyez egy Azure Blob Storage-fiókot, és feliratkozik a vele kapcsolatos eseményekre. Eseményeket küld egy WebHook.It sends events to a WebHook. |
| [Áttekintés: reagálás a Blob storage eseményeire](../storage/blobs/storage-blob-event-overview.md) | A Blob storage és az Event Grid integrálásának áttekintése. |
| [Eseményséma](event-schema-blob-storage.md) | Mezők megjelenítése a Blob Storage-eseményekben. |

## <a name="maps"></a>Maps
Iratkozzon fel az Azure Maps-eseményekre a geokerítés-eseményekre való reagáláshoz. Például egy alkalmazás e-mail értesítést is kézbesíthet minden alkalommal, amikor egy eszköz belép egy geokerítésbe, vagy kilép.

|Cím  |Leírás  |
|---------|---------|
| [Reagálás az Azure Maps-eseményekre az Event Grid használatával](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Azure Maps és az Event Grid integrálásának áttekintése. |
| [Oktatóanyag: Geokerítés beállítása](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ez az oktatóanyag végigvezeti a geokerítés azure Maps használatával történő beállításának alaplépéseit. Az Azure Event Grid segítségével streamelheti a geokerítés eredményeit, és a geokerítés-eredmények alapján értesítést állíthat be. |
| [Eseményséma](event-schema-azure-maps.md) | Mezőkmegjelenítése az Azure Maps-eseményekben. |

## <a name="app-configuration"></a>Alkalmazás konfigurációja
Iratkozzon fel az Azure App Konfigurációs eseményeire, hogy válaszoljon a kulcsérték-módosítási eseményekre.

|Cím | Leírás |
|---------|---------|
| [Reagálás az Azure App konfigurációs eseményeire az Event Grid használatával](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Azure App Configuration és az Event Grid integrálásának áttekintése. |
| [Rövid útmutató: az Azure App Configuration események irányítása egy egyéni webes végpontra az Azure CLI-vel](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan azure CLI-vel küldhet Azure App Configuration események egy WebHook. |
| [Eseményséma](event-schema-app-configuration.md) | Mezők megjelenítése az Azure App konfigurációs eseményeiben. |

## <a name="azure-signalr"></a>Azure SignalR
Iratkozzon fel az Azure SignalR Service eseményeire az ügyfélkapcsolati események megválaszolásához.

|Cím | Leírás |
|---------|---------|
| [Reagálás az Azure SignalR-szolgáltatás eseményeire az Event Grid használatával](../azure-signalr/signalr-concept-event-grid-integration.md) | Az Azure SignalR-szolgáltatás és az Event Grid integrálásának áttekintése. |
| [Az Azure SignalR-szolgáltatás eseményeinek küldése az Event Gridbe](../azure-signalr/signalr-howto-event-grid-integration.md) | Bemutatja, hogyan küldhet Azure SignalR Service-eseményeket egy alkalmazásnak az Event Griden keresztül. |
| [Eseményséma](event-schema-azure-signalr.md) | Mezők megjelenítése az Azure SignalR Service eseményeiben. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Iratkozzon fel az Azure Machine Learning-munkaterületi eseményekre a modellregisztrációmegválaszolásához.

| Cím | Leírás |
| ----- | ----- |
| [Azure Machine Learning-események felhasználása](../machine-learning/concept-event-grid-integration.md) | Az Azure Machine Learning és az Event Grid integrálásának áttekintése. |
| [Azure Event Grid eseménysémája az Azure Machine Learninghez](event-schema-machine-learning.md) | Mezők megjelenítése az Azure Machine Learning-események. |

## <a name="next-steps"></a>További lépések

* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez olvassa el az [Egyéni események létrehozása és irányítása az Azure Event Griddel című témakört.](custom-event-quickstart.md)
