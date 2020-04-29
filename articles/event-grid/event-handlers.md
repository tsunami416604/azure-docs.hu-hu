---
title: Eseménykezelők Azure Event Grid
description: A Azure Event Grid támogatott eseménykezelőit ismerteti. Azure Automation, functions, Event Hubs, Hibrid kapcsolatok, Logic Apps, Service Bus, Queue Storage, webhookok.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 73118823aec9b8d4bd872986b13f19496240c0b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393442"
---
# <a name="event-handlers-in-azure-event-grid"></a>Eseménykezelők a Azure Event Gridban

Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő további műveletet hajt végre az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan az események kezelésére van konfigurálva. Az események kezelésére bármilyen webhookot is használhat. A webhookot nem kell az Azure-ban üzemeltetni az események kezeléséhez. A Event Grid csak a HTTPS webhook-végpontokat támogatja.

Ez a cikk az egyes eseménykezelők tartalmára mutató hivatkozásokat tartalmaz.

## <a name="azure-automation"></a>Azure Automation

A Azure Automation használatával dolgozhat fel eseményeket automatizált runbookok.

|Cím  |Leírás  |
|---------|---------|
|[Oktatóanyag: Azure Automation a Event Grid és a Microsoft Teams szolgáltatással](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, amely eseményt küld. Az esemény elindítja a virtuális gépet címkéző Automation-runbook, és elindítja a Microsoft Teams Channel szolgáltatásnak küldött üzenetet. |

## <a name="azure-functions"></a>Azure Functions

Az eseményekre való kiszolgáló nélküli válasz Azure Functions használata.

Ha az Azure Functionst használja kezelőként, használja az Event Grid-triggereket az általános HTTP-triggerek helyett. Az Event Grid automatikusan érvényesíti az Event Grid függvény eseményindítóit. Általános HTTP-eseményindítók esetén meg kell valósítani az [érvényesítési választ](security-authentication.md#webhook-event-delivery).

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: események kezelése a függvénnyel](custom-event-to-function.md) | Egyéni eseményt küld egy függvénynek feldolgozásra. |
| [Azure Functions Event Grid trigger](../azure-functions/functions-bindings-event-grid.md) | Az Event Grid trigger használatának áttekintése a functions szolgáltatásban. |
| [Oktatóanyag: feltöltött képek átméretezésének automatizálása Event Grid használatával](resize-images-on-storage-blob-upload-event.md) | A felhasználók képeket töltenek fel a webalkalmazásból a Storage-fiókba. Storage-blob létrehozásakor Event Grid egy eseményt küld a Function alkalmazásnak, amely átméretezi a feltöltött képet. |
| [Oktatóanyag: stream big data adattárházba](event-grid-event-hubs-integration.md) | Amikor Event Hubs létrehoz egy rögzítési fájlt, Event Grid küld egy eseményt egy Function alkalmazásnak. Az alkalmazás lekéri a rögzítési fájlt, és áttelepíti az adattárházba. |
| [Oktatóanyag: Azure Service Bus Azure Event Grid integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld Service Bus témakörből az alkalmazás és a logikai alkalmazás működéséhez. |

## <a name="event-hubs"></a>Event Hubs

Event Hubs akkor használja, ha a megoldás az eseményeket gyorsabban szerzi be, mint amennyit az események feldolgozhatnak. Az alkalmazás az eseményeket a Event Hubs saját ütemterv alapján dolgozza fel. Az események feldolgozását méretezheti úgy, hogy kezelni tudja a bejövő eseményeket.

A Event Hubs az esemény forrásaként vagy eseménykezelőként működhet. A következő cikk bemutatja, hogyan használhatja a Event Hubs kezelőként.

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események irányítása az Azure Event Hubs az Azure CLI-vel és Event Grid](custom-event-to-eventhub.md) | Egyéni eseményt küld egy Event hub-nak egy alkalmazás általi feldolgozáshoz. |
| [Resource Manager-sablon: egyéni témakör és Event Hubs végpont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Resource Manager-sablon, amely létrehoz egy egyéni témakörhöz tartozó előfizetést. Eseményeket küld egy Azure-Event Hubs. |

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

Azure Relay Hibrid kapcsolatok használatával küldhet eseményeket olyan alkalmazásokba, amelyek vállalati hálózaton belül vannak, és nem rendelkeznek nyilvánosan elérhető végponttal.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: események küldése hibrid kapcsolódásra](custom-event-to-hybrid-connection.md) | Egyéni eseményt küld egy meglévő hibrid kapcsolatra egy figyelő alkalmazás általi feldolgozáshoz. |

## <a name="logic-apps"></a>Logic Apps

A Logic Apps segítségével automatizálhatja az üzleti folyamatokat az eseményekre való válaszadáshoz.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: a virtuális gépek változásainak figyelése Azure Event Grid és Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | A logikai alkalmazások figyelik a virtuális gépek változásait, és e-maileket küldenek ezekről a változásokról. |
| [Oktatóanyag: az Azure IoT Hub eseményekre vonatkozó e-mailes értesítések küldése Logic Apps használatával](publish-iot-hub-events-to-logic-apps.md) | A logikai alkalmazások minden alkalommal elküldenek egy értesítő e-mailt, amikor egy eszköz bekerül az IoT hubhoz. |
| [Oktatóanyag: Azure Service Bus Azure Event Grid integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld Service Bus témakörből az alkalmazás és a logikai alkalmazás működéséhez. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Service Bus-üzenetsorok

A Event Gridban lévő eseményeket közvetlenül átirányíthatja Service Bus várólistákba a pufferelés vagy parancs & a vállalati alkalmazásokban való használathoz.

A Azure Portal egy esemény-előfizetés létrehozásakor válassza a "Service Bus üzenetsor" lehetőséget a végpont típusaként, majd kattintson a "végpont kiválasztása" elemre Service Bus üzenetsor kiválasztásához.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Service Bus üzenetsor-kezelő hozzáadása a CLI használatával

Az Azure CLI esetében az alábbi példa egy Event Grid-témakör előfizetését és összekapcsolása Service Bus üzenetsor:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

### <a name="service-bus-topics"></a>Service Bus témakörök

Event Grid közvetlenül Service Bus témakörökre irányíthatja az eseményeket, hogy az Azure rendszereseményeit Service Bus témakörökkel vagy a parancs & vezérlő üzenetkezelési forgatókönyvek kezelésére lehessen használni.

A Azure Portal egy esemény-előfizetés létrehozásakor válassza a "Service Bus témakör" lehetőséget a végpont típusaként, majd kattintson a "kiválasztás és végpont" elemre Service Bus témakör kiválasztásához.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Service Bus témakör-kezelő hozzáadása a CLI használatával

Az Azure CLI esetében az alábbi példa egy Event Grid-témakör előfizetését és összekapcsolása Service Bus üzenetsor:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>Queue Storage

A várólista-tárolóval fogadhat olyan eseményeket, amelyeket le kell húzni. A várólista-tárolót akkor használhatja, ha olyan hosszú ideig futó folyamattal rendelkezik, amely túl sokáig tart a válaszadáshoz. Ha eseményeket küld az üzenetsor-tárolóba, az alkalmazás lekérheti és feldolgozhatja az eseményeket a saját ütemtervén.

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események irányítása az Azure üzenetsor-tárolóba az Azure CLI-vel és a Event Grid](custom-event-to-queue-storage.md) | Útmutató egyéni események üzenetsor-tárolóba való küldéséhez. |

## <a name="webhooks"></a>Webhookok

Webhookok használata az eseményekre reagáló testreszabható végpontokhoz.

|Cím  |Leírás  |
|---------|---------|
| Gyors útmutató: egyéni események létrehozása és irányítása az [Azure CLI](custom-event-quickstart.md), a PowerShell és a [Portal](custom-event-quickstart-portal.md) [használatával](custom-event-quickstart-powershell.md). | Bemutatja, hogyan küldhet egyéni eseményeket egy webhookba. |
| Gyors útmutató: blob Storage-események átirányítása egyéni webes végpontra az- [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), a [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)és a [Portal](blob-event-quickstart-portal.md)használatával. | Bemutatja, hogyan küldhetők blob Storage-események egy webhookba. |
| [Gyors útmutató: tárolói beállításjegyzékbeli események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet Container Registry eseményeket az Azure CLI használatával. |
| [Áttekintés: események fogadása HTTP-végpontra](receive-events.md) | Leírja, hogyan lehet érvényesíteni egy HTTP-végpontot események fogadására, valamint események fogadására és deszerializálására. |

## <a name="next-steps"></a>További lépések

* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
