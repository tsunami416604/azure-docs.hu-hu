---
title: Az Azure Event Grid Eseményforrások
description: Támogatott eseményforrások ismerteti az Azure Event Gridhez
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: tomfitz
ms.openlocfilehash: 15fe97e39fbc1c669762ddb7523cfd043638d3de
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144229"
---
# <a name="event-sources-in-azure-event-grid"></a>Az Azure Event Grid zdroje událostí

Eseményforrás, ahol az esemény történik. Több Azure-szolgáltatások automatikusan megtörténik az események küldésére. Események küldése egyéni alkalmazásokat is létrehozhat. Az események terjesztését Event Grid használata Azure-ban üzemeltetett egyéni alkalmazásokat nem szükséges.

Ez a cikk tartalmakra mutató hivatkozásokat biztosít minden egyes esemény forrását.

## <a name="azure-subscriptions"></a>Azure-előfizetések

Fizessen elő az Azure-előfizetések események reagálni az igények változásaira erőforrásokat az Azure-előfizetés között.

|Beosztás |Leírás  |
|---------|---------|
| [Oktatóanyag: Az Azure Automation az Event Grid és a Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, amely eseményt küld. Az esemény aktiválása a címkéket a virtuális gép, és a egy Microsoft Teams-csatornához küldött üzenet aktivál egy Automation-runbook. |
| [Útmutató: az eseményekre a portálon keresztül előfizetés](subscribe-through-portal.md) | A portál használatával feliratkozás Azure-előfizetés eseményeire. |
| [Az Azure CLI: feliratkozás Azure-előfizetés eseményeire](./scripts/event-grid-cli-azure-subscription.md) |A példaszkript létrehoz egy Event Grid-előfizetés Azure-előfizetéssel, és elküldi az eseményeket egy Webhookot. |
| [PowerShell: feliratkozás Azure-előfizetés eseményeire](./scripts/event-grid-powershell-azure-subscription.md)| A példaszkript létrehoz egy Event Grid-előfizetés Azure-előfizetéssel, és elküldi az eseményeket egy Webhookot. |
| [Eseményséma](event-schema-subscriptions.md) | Azure-előfizetés események mezőket jeleníti meg. |

## <a name="container-registry"></a>Container Registry

Fizessen elő a Container Registry események reagálni az igények változásaira képeken.

|Beosztás |Leírás  |
|---------|---------|
| [Gyors útmutató: a tároló beállításjegyzék események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja a tároló-beállításjegyzék események küldése az Azure CLI használatával. |
| [Eseményséma](event-schema-container-registry.md) | Container Registry események mezőket jeleníti meg. |

## <a name="custom-topics"></a>Egyéni témakörök

Fizessen elő az alkalmazás válaszadására egyéni témaköröket.

|Beosztás  |Leírás  |
|---------|---------|
| [Rövid útmutató: létrehozása és átirányítása az Azure CLI-vel egyéni események](custom-event-quickstart.md) | Bemutatja, hogyan használható az Azure CLI-vel szeretne egyéni eseményeket küldeni. |
| [Rövid útmutató: létrehozása és átirányítása egyéni események az Azure PowerShell használatával](custom-event-quickstart-powershell.md) | Bemutatja, hogyan használható az Azure PowerShell-lel szeretne egyéni eseményeket küldeni. |
| [Rövid útmutató: létrehozása és átirányítása egyéni események az Azure portal használatával](custom-event-quickstart-portal.md) | Bemutatja, hogyan szeretne egyéni eseményeket küldeni a portállal. |
| [Gyors útmutató: egyéni események átirányítása az Azure Queue storage](custom-event-to-queue-storage.md) | Ismerteti, hogyan lehet egyéni eseményeket küldeni egy Queue Storage. |
| [Útmutató: közzététel egyéni témakörben](post-to-custom-topic.md) | Bemutatja egy egyéni témakört az esemény közzététele. |
| [Azure CLI: egyéni Event Grid-témakör létrehozása](./scripts/event-grid-cli-create-custom-topic.md)|Minta parancsfájl, amely létrehoz egy egyéni témakört. A szkript lekéri a végpont és a egy kulcsot.|
| [Az Azure CLI: feliratkozás egy egyéni témakör eseményeire](./scripts/event-grid-cli-subscribe-custom-topic.md)|Minta parancsfájl, amely egy egyéni témakör-előfizetést hoz létre. Eseményeket küld egy Webhookot.|
| [PowerShell: egyéni Event Grid-témakör létrehozása](./scripts/event-grid-powershell-create-custom-topic.md)|Minta parancsfájl, amely létrehoz egy egyéni témakört. A szkript lekéri a végpont és a egy kulcsot.|
| [PowerShell: feliratkozás egy egyéni témakör eseményeire](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Minta parancsfájl, amely egy egyéni témakör-előfizetést hoz létre. Eseményeket küld egy Webhookot.|
| [Resource Manager-sablon: egyéni témakör és WebHook-végpontot](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Resource Manager-sablonnal, amely létrehoz egy egyéni témakör és egyéni témakör-előfizetés. Eseményeket küld egy Webhookot. |
|
| [Resource Manager-sablon: egyéni témakör és az Event Hubs-végpont](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Resource Manager-sablonnal, amely egy egyéni témakör-előfizetést hoz létre. Az Azure Event Hubs küld eseményeket. |
| [Eseményséma](event-schema.md) | Egyéni események mezőket jeleníti meg. |

## <a name="event-hubs"></a>Event Hubs

Fizessen elő az Event Hubs-események fájl események rögzítése válaszolni. Az Event Hubs egy eseményforrás vagy egy eseménykezelő működhet. A következő cikkek bemutatják, hogyan használható az Event Hubs forrásként.

|Beosztás  |Leírás  |
|---------|---------|
| [Oktatóanyag: big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md) | Az Event Hubs rögzítési fájlt hoz létre, amikor az Event Grid eseményt küld egy függvényalkalmazáshoz. Az alkalmazás lekéri a rögzítési fájlt, és egy data warehouse-bA migrálja az adatokat. |
| [Eseményséma](event-schema-event-hubs.md) | Az Event Hubs-események mezőket jeleníti meg. |

Példák az Event hubs, a kezelő: [az Event Hubs-kezelő](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Feliratkozás az IoT Hub eseményeire eszköz létrehozva, törlése, a csatlakoztatott és a leválasztott eseményeket válaszolni.

|Beosztás  |Leírás  |
|---------|---------|
| [Az Azure IoT Hub-események Logic Apps használatával kapcsolatos e-mail-értesítések küldése](publish-iot-hub-events-to-logic-apps.md) | Egy logikai alkalmazás küld értesítő e-mailt, minden alkalommal, amikor egy eszköz az IoT hubhoz való hozzáadása. |
| [IoT Hub-események reagálnak a műveletek indítása Event Grid használatával](../iot-hub/iot-hub-event-grid.md) | Az IoT Hub integrálása az Event Grid áttekintése. |
| [Eseményséma](event-schema-iot-hub.md) | Megjeleníti a mezők az IoT hubon eseményeket. |
| [Az eszköz csatlakoztatva és eszköz csatlakozik az események sorrendjének](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Bemutatja, hogyan sorrendjének eszközkapcsolat események. |

## <a name="media-services"></a>Media Services

Fizessen elő a feladat állapota válaszadására Media Services-események.

|Beosztás  |Leírás  |
|---------|---------|
| [Áttekintés: reagálás a Media Services-események](../media-services/latest/reacting-to-media-services-events.md) | A Media Services integrálása az Event Grid áttekintése. |
| [Oktatóanyag: Azure Media Services-események átirányítása egyéni webes végpontra parancssori felület használatával](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet eseményeket a Media Services szolgáltatásból. |
| [Eseményséma](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services-események mezőket jeleníti meg. |

## <a name="resource-groups"></a>Erőforráscsoportok

Erőforrás-csoport események reagálni az igények változásaira az erőforrások között egy erőforráscsoportot az előfizetés.

|Beosztás  |Leírás  |
|---------|---------|
| [Oktatóanyag: az Azure Event Grid és a Logic Apps a virtuális gépek módosításainak monitorozása](monitor-virtual-machine-changes-event-grid-logic-app.md) | Egy logikai alkalmazást egy virtuális gép módosításainak figyeli, és ezeket a módosításokat kapcsolatos e-mailt küld. |
| [Az Azure CLI: feliratkozás egy erőforráscsoport eseményeire](./scripts/event-grid-cli-resource-group.md)| Feliratkozik egy erőforráscsoport eseményeire hivatkozó szkriptre példát. Eseményeket küld egy Webhookot. |
| [Az Azure CLI: feliratkozás egy erőforráscsoport és a egy szűrő eseményeire](./scripts/event-grid-cli-resource-group-filter.md) | A példaszkript feliratkozik egy erőforráscsoport eseményeire, és szűri az eseményeket egy erőforráshoz. |
| [PowerShell: feliratkozás egy erőforráscsoport eseményeire](./scripts/event-grid-powershell-resource-group.md) | Feliratkozik egy erőforráscsoport eseményeire hivatkozó szkriptre példát. Eseményeket küld egy Webhookot. |
| [PowerShell: feliratkozás egy erőforráscsoport és a egy szűrő eseményeire](./scripts/event-grid-powershell-resource-group-filter.md) | A példaszkript feliratkozik egy erőforráscsoport eseményeire, és szűri az eseményeket egy erőforráshoz. |
| [Resource Manager-sablon: erőforráscsoport-feliratkozás](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json) | Feliratkozik egy erőforráscsoport eseményeire. Eseményeket küld egy Webhookot. |
| [Eseményséma](event-schema-resource-groups.md) | Megjeleníti a mezők erőforrásban csoport eseményeket. |

## <a name="service-bus"></a>Service Bus

Fizessen elő a Service Bus-eseményekre, anélkül, hogy egy aktív figyelője küldött üzeneteket.

|Beosztás  |Leírás  |
|---------|---------|
| [Oktatóanyag: Azure Service Bus – Azure Event Grid integráció példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld a Service Bus-témakörbe, alkalmazás és a logikai alkalmazás működéséhez. |
| [Áttekintés: Azure Service Bus – Event Grid-integrációt](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | A Service Bus integrálása az Event Grid áttekintése. |
| [Eseményséma](event-schema-service-bus.md) | Service Bus-eseményekre mezőket jeleníti meg. |

## <a name="storage"></a>Storage

Fizessen elő a Blob Storage-események válaszolni a blob-események létrehozása és törlése.

|Beosztás  |Leírás  |
|---------|---------|
| [Gyors útmutató: Blob storage-események átirányítása egyéni webes végpontra az Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan egy Webhookot a blob storage-események küldése az Azure CLI használatával. |
| [Gyors útmutató: Blob storage-események átirányítása egyéni webes végpontra a PowerShell-lel](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan használhatja az Azure Powershellt egy Webhookot a blob storage-események küldéséhez. |
| [Rövid útmutató: hozzon létre, és az Azure Portallal Blob storage-események irányítása](blob-event-quickstart-portal.md) | A portál használata a blob storage-események küldéséhez egy WebHook mutatja. |
| [Az Azure CLI: feliratkozás egy Blob storage-fiók eseményeire](./scripts/event-grid-cli-blob.md) | Feliratkozik egy Blob storage-fiókhoz tartozó esemény hivatkozó szkriptre példát. WebHook elküldi az eseményt. |
| [PowerShell: feliratkozás egy Blob storage-fiók eseményeire](./scripts/event-grid-powershell-blob.md) | Feliratkozik egy Blob storage-fiókhoz tartozó esemény hivatkozó szkriptre példát. WebHook elküldi az eseményt. |
| [Resource Manager-sablon: Blob storage- és előfizetés létrehozása](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json) | Üzembe helyez egy Azure Blob Storage-fiókot, és feliratkozik a vele kapcsolatos eseményekre. Eseményeket küld egy Webhookot. |
| [Áttekintés: reagálás Blob storage-események](../storage/blobs/storage-blob-event-overview.md) | A Blob storage integrálása az Event Grid áttekintése. |
| [Eseményséma](event-schema-blob-storage.md) | A Blob Storage-események mezőket jeleníti meg. |

## <a name="next-steps"></a>További lépések

* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).
