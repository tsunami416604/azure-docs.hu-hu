---
title: Azure Event Grid eseményforrás
description: Azure-előfizetések, Container Registry, egyéni témakörök, Event Hubs, IoT Hub, Key Vault, Media Services, erőforráscsoportok, Service Bus, tárolás, térképek, alkalmazás konfigurációja, Signal R, Machine Learning.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: d4a426ea1432d0266b7ae9344afefe8ddac1d030
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264998"
---
# <a name="event-sources-in-azure-event-grid"></a>A Azure Event Gridban lévő eseményforrás

Az esemény forrása az esemény. Több Azure-szolgáltatás is automatikusan az események küldésére van konfigurálva. Létrehozhat olyan egyéni alkalmazásokat is, amelyek eseményeket küldenek. Az egyéni alkalmazásokat nem kell az Azure-ban üzemeltetni, hogy Event Grid használjanak az események terjesztéséhez.

Ez a cikk az egyes eseményforrás-forrásokra mutató hivatkozásokat tartalmaz.

## <a name="azure-subscriptions"></a>Azure-előfizetések

Fizessen elő az Azure-előfizetések eseményeire, hogy válaszoljanak az Azure-előfizetések erőforrásainak változásaira.

|Cím |Leírás  |
|---------|---------|
| [Oktatóanyag: Azure Automation a Event Grid és a Microsoft Teams szolgáltatással](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, amely eseményt küld. Az esemény elindítja a virtuális gépet címkéző Automation-runbook, és elindítja a Microsoft Teams Channel szolgáltatásnak küldött üzenetet. |
| [Útmutató: az eseményekre való előfizetés a portálon keresztül](subscribe-through-portal.md) | Az Azure-előfizetések eseményeire való feliratkozáshoz használja a portált. |
| [Azure CLI: Feliratkozás az Azure-előfizetés eseményeire](./scripts/event-grid-cli-azure-subscription.md) |Parancsfájl, amely Event Grid-előfizetést hoz létre egy Azure-előfizetéshez, és eseményeket küld egy webhooknak. |
| [PowerShell: Feliratkozás az Azure-előfizetés eseményeire](./scripts/event-grid-powershell-azure-subscription.md)| Parancsfájl, amely Event Grid-előfizetést hoz létre egy Azure-előfizetéshez, és eseményeket küld egy webhooknak. |
| [Esemény sémája](event-schema-subscriptions.md) | Az Azure-előfizetési események mezőinek megjelenítése. |

## <a name="container-registry"></a>Container Registry

Fizessen elő Container Registry eseményekre, hogy válaszoljanak a képek változásaira.

|Cím |Leírás  |
|---------|---------|
| [Gyors útmutató: tárolói beállításjegyzékbeli események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet Container Registry eseményeket az Azure CLI használatával. |
| [Esemény sémája](event-schema-container-registry.md) | Container Registry események mezőinek megjelenítése. |

## <a name="custom-topics"></a>Egyéni témakörök

Fizessen elő az egyéni témakörökre, hogy válaszoljon az alkalmazás eseményeire.

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események létrehozása és irányítása az Azure CLI-vel](custom-event-quickstart.md) | Bemutatja, hogyan küldhet egyéni eseményeket az Azure CLI használatával. |
| [Gyors útmutató: egyéni események létrehozása és irányítása Azure PowerShell](custom-event-quickstart-powershell.md) | Bemutatja, hogyan lehet a Azure PowerShell használatával egyéni eseményeket küldeni. |
| [Gyors útmutató: egyéni események létrehozása és irányítása a Azure Portal](custom-event-quickstart-portal.md) | Bemutatja, hogyan küldhet egyéni eseményeket a portál használatával. |
| [Gyors útmutató: egyéni események irányítása az Azure üzenetsor-tárolóba](custom-event-to-queue-storage.md) | Útmutató egyéni események üzenetsor-tárolóba való küldéséhez. |
| [Útmutató: közzététel egyéni témakörbe](post-to-custom-topic.md) | Bemutatja, hogyan lehet elküldeni egy eseményt egy egyéni témakörbe. |
| [Azure CLI: Event Grid egyéni témakör létrehozása](./scripts/event-grid-cli-create-custom-topic.md)|Egyéni témakört létrehozó minta parancsfájl. A parancsfájl lekéri a végpontot és a kulcsot.|
| [Azure CLI: előfizetés egyéni témakör eseményeire](./scripts/event-grid-cli-subscribe-custom-topic.md)|Egy egyéni témakör előfizetését létrehozó parancsfájl. Eseményeket küld egy webhooknak.|
| [PowerShell: Event Grid egyéni témakör létrehozása](./scripts/event-grid-powershell-create-custom-topic.md)|Egyéni témakört létrehozó minta parancsfájl. A parancsfájl lekéri a végpontot és a kulcsot.|
| [PowerShell: előfizetés egy egyéni témakör eseményeire](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Egy egyéni témakör előfizetését létrehozó parancsfájl. Eseményeket küld egy webhooknak.|
| [Resource Manager-sablon: egyéni témakör és webhook-végpont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Egy Resource Manager-sablon, amely egyéni témakört és előfizetést hoz létre az adott egyéni témakörhöz. Eseményeket küld egy webhooknak. |
|
| [Resource Manager-sablon: egyéni témakör és Event Hubs végpont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Resource Manager-sablon, amely létrehoz egy egyéni témakörhöz tartozó előfizetést. Eseményeket küld egy Azure-Event Hubs. |
| [Esemény sémája](event-schema.md) | Az egyéni események mezőinek megjelenítése. |

## <a name="event-hubs"></a>Event Hubs

Fizessen elő Event Hubs eseményekre, hogy válaszoljanak a rögzítési fájl eseményeire. A Event Hubs az esemény forrásaként vagy eseménykezelőként működhet. Az alábbi cikkek bemutatják, hogyan használhatja a Event Hubs forrásként.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: stream big data adattárházba](event-grid-event-hubs-integration.md) | Amikor Event Hubs létrehoz egy rögzítési fájlt, Event Grid küld egy eseményt egy Function alkalmazásnak. Az alkalmazás lekéri a rögzítési fájlt, és áttelepíti az adattárházba. |
| [Esemény sémája](event-schema-event-hubs.md) | Event Hubs események mezőinek megjelenítése. |

Event Hubs kezelőként például a [Event Hubs kezelője](event-handlers.md#event-hubs)című témakörben talál példát.

## <a name="iot-hub"></a>IoT Hub

Fizessen elő IoT Hub eseményekre, hogy válaszoljon az eszközre létrehozott, törölt, csatlakoztatott, leválasztott és telemetria eseményekre.

|Cím  |Leírás  |
|---------|---------|
| [Az Azure IoT Hub eseményekre vonatkozó e-mailes értesítések küldése Logic Apps használatával](publish-iot-hub-events-to-logic-apps.md) | A logikai alkalmazások minden alkalommal elküldenek egy értesítő e-mailt, amikor egy eszköz bekerül a IoT Hubba. |
| [IoT Hub eseményekre való reagálás Event Grid használatával a műveletek elindításához](../iot-hub/iot-hub-event-grid.md) | A IoT Hub és a Event Grid integrálásának áttekintése. |
| [Esemény sémája](event-schema-iot-hub.md) | IoT Hub események mezőinek megjelenítése. |
| [Eszköz csatlakoztatott és leválasztott eseményeinek megrendelése](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Az eszköz kapcsolódási állapotával kapcsolatos események megrendelésének módját mutatja be. |

## <a name="key-vault-preview"></a>Key Vault (előzetes verzió)

Key Vault a Event Grid-integráció jelenleg előzetes verzióban érhető el. 

Fizessen elő Key Vault eseményekről, amelyekről értesítést kap, amikor a titkos kulcs lejár, a titkos kód lejár, vagy egy titkos kulcs új verziója érhető el. 

|Cím  |Leírás  |
|---------|---------|
| [Key Vault események figyelése a Azure Event Grid](../key-vault/event-grid-overview.md) | A Key Vault és a Event Grid integrálásának áttekintése. |
| [Oktatóanyag: Key Vault események létrehozása és figyelése a Event Grid](../key-vault/event-grid-tutorial.md) | Megtudhatja, hogyan állíthatja be a Key Vault Event Grid értesítéseit. |
| [Esemény sémája](event-schema-key-vault.md) | Key Vault események mezőinek megjelenítése. |

## <a name="media-services"></a>Media Services

Előfizetés Media Services eseményekre a feladatok állapotára vonatkozó eseményekre való válaszadáshoz.

|Cím  |Leírás  |
|---------|---------|
| [Áttekintés: Media Services eseményekre való reagálás](../media-services/latest/reacting-to-media-services-events.md) | A Media Services és a Event Grid integrálásának áttekintése. |
| [Oktatóanyag: Azure Media Services események átirányítása egyéni webes végpontra a parancssori felület használatával](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet eseményeket Media Servicesból. |
| [Esemény sémája](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services események mezőinek megjelenítése. |

## <a name="resource-groups"></a>Erőforráscsoportok

Fizessen elő az erőforráscsoport eseményeire, hogy válaszoljon az erőforrás-csoport erőforrásainak változásaira.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: a virtuális gépek változásainak figyelése Azure Event Grid és Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | A logikai alkalmazások figyelik a virtuális gépek változásait, és e-maileket küldenek ezekről a változásokról. |
| [Azure CLI: előfizetés egy erőforráscsoport eseményeire](./scripts/event-grid-cli-resource-group.md)| Egy erőforráscsoport eseményeire előfizetett minta parancsfájl. Eseményeket küld egy webhooknak. |
| [Azure CLI: előfizetés egy erőforráscsoport eseményeire és egy erőforrás szűrésére](./scripts/event-grid-cli-resource-group-filter.md) | Egy erőforráscsoport eseményeire előfizetett minta-parancsfájl, amely egy adott erőforráshoz tartozó eseményeket szűri. |
| [PowerShell: előfizetés egy erőforráscsoport eseményeire](./scripts/event-grid-powershell-resource-group.md) | Egy erőforráscsoport eseményeire előfizetett minta parancsfájl. Eseményeket küld egy webhooknak. |
| [PowerShell: előfizetés egy erőforráscsoport eseményeire és egy erőforrás szűrésére](./scripts/event-grid-powershell-resource-group-filter.md) | Egy erőforráscsoport eseményeire előfizetett minta-parancsfájl, amely egy adott erőforráshoz tartozó eseményeket szűri. |
| [Resource Manager-sablon: erőforrás-előfizetés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Feliratkozik az Azure-előfizetésekre vagy-csoportokra vonatkozó eseményekre. Eseményeket küld egy webhooknak. |
| [Eseményséma](event-schema-resource-groups.md) | Mezők megjelenítése az erőforráscsoport eseményeiben. |

## <a name="service-bus"></a>Service Bus

Fizessen elő Service Bus eseményekre, amelyek aktív figyelő nélküli üzenetekre válaszolnak.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: Azure Service Bus Azure Event Grid integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld Service Bus témakörből az alkalmazás és a logikai alkalmazás működéséhez. |
| [Áttekintés: Azure Service Bus Event Grid Integration](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | A Service Bus és a Event Grid integrálásának áttekintése. |
| [Esemény sémája](event-schema-service-bus.md) | Service Bus események mezőinek megjelenítése. |

## <a name="storage"></a>Tárterület

Fizessen elő Blob Storage eseményekre, hogy válaszoljanak a blob által létrehozott és törölt eseményekre.

>[!NOTE]
> Csak a **StorageV2 (általános célú v2)** és a **BlobStorage** típusú tárolási fiókok támogatják az események integrálását. A **Storage (általános célú v1)** *nem* támogatja a Event Grid integrációját.

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: blob Storage-események átirányítása egyéni webes végpontra az Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet blob Storage-eseményeket webhookba az Azure CLI használatával. |
| [Gyors útmutató: blob Storage-események átirányítása egyéni webes végpontra a PowerShell-lel](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azt mutatja be, hogyan használható a Azure PowerShell a blob Storage-események webhookba való küldéséhez. |
| [Gyors útmutató: blob Storage-események létrehozása és átirányítása a Azure Portal](blob-event-quickstart-portal.md) | Bemutatja, hogyan küldhet blob Storage-eseményeket webhookba a portál használatával. |
| [Azure CLI: előfizetés egy blob Storage-fiók eseményeire](./scripts/event-grid-cli-blob.md) | A blob Storage-fiókra vonatkozó eseményre előfizetett minta parancsfájl. Az eseményt egy webhookba küldi. |
| [PowerShell: előfizetés egy blob Storage-fiók eseményeire](./scripts/event-grid-powershell-blob.md) | A blob Storage-fiókra vonatkozó eseményre előfizetett minta parancsfájl. Az eseményt egy webhookba küldi. |
| [Resource Manager-sablon: blob Storage és előfizetés létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Üzembe helyez egy Azure Blob Storage-fiókot, és feliratkozik a vele kapcsolatos eseményekre. Eseményeket küld egy webhooknak. |
| [Áttekintés: a blob Storage eseményeire való reagálás](../storage/blobs/storage-blob-event-overview.md) | A blob Storage Event Grid-val való integrálásának áttekintése. |
| [Esemény sémája](event-schema-blob-storage.md) | Blob Storage események mezőinek megjelenítése. |

## <a name="maps"></a>Maps
Fizessen elő Azure Maps eseményekre, hogy válaszoljon a geokerítésen eseményekre. Egy alkalmazás például minden alkalommal elküldheti az e-mail-értesítést, amikor egy eszköz belép vagy kilép egy geokerítésen.

|Cím  |Leírás  |
|---------|---------|
| [Azure Maps eseményekre való reagálás Event Grid használatával](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Azure Maps és a Event Grid integrálásának áttekintése. |
| [Oktatóanyag: geokerítésen beállítása](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ez az oktatóanyag végigvezeti a geokerítésen a Azure Maps használatával történő beállításához szükséges alapismereteken. Azure Event Grid használatával továbbíthatja a geokerítésen eredményeit, és beállíthat egy értesítést a geokerítésen eredményei alapján. |
| [Esemény sémája](event-schema-azure-maps.md) | Azure Maps események mezőinek megjelenítése. |

## <a name="app-configuration"></a>Alkalmazás konfigurációja
Fizessen elő az Azure-alkalmazás konfigurációs eseményeire, hogy válaszoljon a kulcs-érték módosítási eseményekre.

|Cím | Leírás |
|---------|---------|
| [Az Azure-alkalmazás konfigurációs eseményeire való reagálás Event Grid használatával](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Azure-alkalmazások konfigurációjának Event Gridsal való integrálásának áttekintése. |
| [Gyors útmutató: Azure-alkalmazás konfigurációs eseményeinek átirányítása egyéni webes végpontra az Azure CLI-vel](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet Azure-alkalmazások konfigurációs eseményeit webhookba az Azure CLI használatával. |
| [Esemény sémája](event-schema-app-configuration.md) | Mezők megjelenítése az Azure-alkalmazás konfigurációs eseményeiben. |

## <a name="azure-signalr"></a>Azure SignalR
Fizessen elő az Azure Signaler szolgáltatás eseményeire az ügyfél-kapcsolódási eseményekre való válaszadáshoz.

|Cím | Leírás |
|---------|---------|
| [Az Azure Signaler szolgáltatás eseményeire való reagálás Event Grid használatával](../azure-signalr/signalr-concept-event-grid-integration.md) | Az Azure Signaler szolgáltatás és a Event Grid integrálásának áttekintése. |
| [Az Azure szignáló szolgáltatás eseményeinek küldése Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Bemutatja, hogyan küldhetők az Azure Signaler szolgáltatás eseményei egy alkalmazásnak Event Gridon keresztül. |
| [Esemény sémája](event-schema-azure-signalr.md) | Mezők megjelenítése az Azure Signaler szolgáltatás eseményeiben. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Feliratkozás Azure Machine Learning munkaterület eseményeire a modell regisztrációjának megválaszolásához.

| Cím | Leírás |
| ----- | ----- |
| [Azure Machine Learning események felhasználása](../machine-learning/concept-event-grid-integration.md) | A Azure Machine Learning és a Event Grid integrálásának áttekintése. |
| [Azure Machine Learning Azure Event Gridi esemény sémája](event-schema-machine-learning.md) | A Azure Machine Learning események mezőinek megjelenítése. |

## <a name="next-steps"></a>Következő lépések

* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
