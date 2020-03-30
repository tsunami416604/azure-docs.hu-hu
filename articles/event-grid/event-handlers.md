---
title: Az Azure Event Grid eseménykezelői
description: Az Azure Event Grid támogatott eseménykezelőinek ismertetése. Azure Automation, Functions, Event Hubs, Hibrid kapcsolatok, Logikai alkalmazások, Service Bus, Queue Storage, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 7ea00d663264e902c1818f7a4684e90eccd97b28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265050"
---
# <a name="event-handlers-in-azure-event-grid"></a>Eseménykezelők az Azure Event Gridben

Az eseménykezelő az a hely, ahol az esemény elküldésre kerül. A kezelő további lépéseket tesz az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan konfigurálva van az események kezelésére. Bármely WebHook események kezelésére is használhatja. A WebHook nem kell üzemeltetni az Azure-ban események kezeléséhez. Az Event Grid csak a HTTPS WebHook végpontokat támogatja.

Ez a cikk az egyes eseménykezelők tartalmára mutató hivatkozásokat tartalmaz.

## <a name="azure-automation"></a>Azure Automation

Az Azure Automation használatával automatizált runbookokkal dolgozhatja fel az eseményeket.

|Cím  |Leírás  |
|---------|---------|
|[Oktatóanyag: Azure Automation az Event Griddel és a Microsoft Teamssegítségével](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, amely egy eseményt küld. Az esemény elindítja az Automation runbookot, amely a virtuális gépet címkézi, és egy Microsoft Teams-csatornának küldött üzenetet indít el. |

## <a name="azure-functions"></a>Azure Functions

Az Azure Functions használatával kiszolgáló nélküli választ az eseményekre.

Ha az Azure Functionst használja kezelőként, használja az Event Grid-triggereket az általános HTTP-triggerek helyett. Az Event Grid automatikusan érvényesíti az Event Grid függvény eseményindítóit. Általános HTTP-eseményindítók esetén meg kell valósítani az [érvényesítési választ](security-authentication.md#webhook-event-delivery).

|Cím  |Leírás  |
|---------|---------|
| [Rövid útmutató: Események kezelése funkcióval](custom-event-to-function.md) | Egyéni eseményt küld egy függvénynek feldolgozásra. |
| [Eseményrács eseményindítója az Azure Functionshez](../azure-functions/functions-bindings-event-grid.md) | Az Eseményrács eseményindító használatának áttekintése a Függvényekben. |
| [Oktatóanyag: a feltöltött képek átméretezésének automatizálása az Event Grid segítségével](resize-images-on-storage-blob-upload-event.md) | A felhasználók képeket töltenek fel a webalkalmazáson keresztül a tárfiókba. Tárolási blob létrehozásakor az Event Grid egy eseményt küld a függvényalkalmazásnak, amely átméretezi a feltöltött képet. |
| [Oktatóanyag: big data streamelése adattárházba](event-grid-event-hubs-integration.md) | Amikor az Event Hubs rögzítési fájlt hoz létre, az Event Grid eseményt küld egy függvényalkalmazásnak. Az alkalmazás lekéri a Capture fájlt, és áttelepíti az adatokat egy adattárházba. |
| [Oktatóanyag: Azure Service Bus az Azure Event Grid integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Event Grid üzeneteket küld a Service Bus témakörből a függvényalkalmazásnak és a logikai alkalmazásnak. |

## <a name="event-hubs"></a>Event Hubs

Akkor használja az Event Hubs-ot, ha a megoldás gyorsabban kapja meg az eseményeket, mint ahogy az eseményeket fel tudja dolgozni. Az alkalmazás saját ütemezés szerint dolgozza fel az eseményeket az Event Hubs-ból. Az eseményfeldolgozást a bejövő események kezeléséhez méretezheti.

Az Event Hubs eseményforrásként vagy eseménykezelőként működhet. A következő cikk bemutatja, hogyan használhatja az Event Hubs kezelőként.

|Cím  |Leírás  |
|---------|---------|
| [Rövid útmutató: egyéni események irányítása az Azure Event Hubs-ba az Azure CLI és az Event Grid segítségével](custom-event-to-eventhub.md) | Egyéni eseményt küld egy eseményközpontba egy alkalmazás általi feldolgozásra. |
| [Erőforrás-kezelő sablon: egyéni témakör és Eseményközpontok végpontja](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Erőforrás-kezelő sablon, amely előfizetést hoz létre egy egyéni témakörhöz. Eseményeket küld egy Azure Event Hubs. |

Az Event Hubs adatforrásként való [példázata: Event Hubs source](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

Az Azure Relay hibrid kapcsolatok használatával eseményeket küldhet olyan alkalmazásoknak, amelyek egy vállalati hálózaton belül vannak, és nem rendelkeznek nyilvánosan elérhető végponttal.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: események küldése hibrid kapcsolatra](custom-event-to-hybrid-connection.md) | Egyéni eseményt küld egy meglévő hibrid kapcsolatnak egy figyelőalkalmazás általi feldolgozásra. |

## <a name="logic-apps"></a>Logic Apps

A Logic Apps segítségével automatizálhatja az eseményekre való válaszadás üzleti folyamatait.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: a virtuális gépek változásainak figyelése az Azure Event Grid és a Logic Apps alkalmazásokkal](monitor-virtual-machine-changes-event-grid-logic-app.md) | A logikai alkalmazás figyeli a virtuális gép módosításait, és e-maileket küld ezekről a változásokról. |
| [Oktatóanyag: e-mailes értesítések küldése az Azure IoT Hub-eseményekről a Logic Apps használatával](publish-iot-hub-events-to-logic-apps.md) | A logikai alkalmazás értesítést küld e-mailt minden alkalommal, amikor egy eszköz hozzá kerül az IoT hubhoz. |
| [Oktatóanyag: Azure Service Bus az Azure Event Grid integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Event Grid üzeneteket küld a Service Bus témakörből a függvényalkalmazásnak és a logikai alkalmazásnak. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Service Bus-üzenetsorok

Az Event Gridben lévő eseményeket közvetlenül a Service Bus-várólistákba irányíthatja puffereléshez vagy vezérléshez & vállalati alkalmazások vezérlőforgatókönyveibe.

Az Azure Portalon egy esemény-előfizetés létrehozása közben válassza ki a "Service Bus-várólista" végponttípusként, majd kattintson a "válasszon ki egy végpontot" a Service Bus-várólista kiválasztásához.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>A CLI használata a Service Bus várólista-kezelőjének hozzáadásához

Az Azure CLI esetében a következő példa előfizet, és egy eseményrács-témakört kapcsol össze egy Service Bus-várólistával:

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

### <a name="service-bus-topics"></a>A Service Bus témakörei

Az Event Gridben lévő eseményeket közvetlenül a Service Bus-témakörökbe irányíthatja, hogy az Azure-rendszereseményeket a Service Bus-témakörökkel kezelje, vagy a parancs& vezérlő üzenetkezelési forgatókönyveket.

Az Azure Portalon egy esemény-előfizetés létrehozása közben válassza ki a "Service Bus Topic" végponttípusként, majd kattintson a "select and endpoint" elemre a Service Bus-témakör kiválasztásához.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Service Bus-témakörkezelő hozzáadása a CLI használatával

Az Azure CLI esetében a következő példa előfizet, és egy eseményrács-témakört kapcsol össze egy Service Bus-várólistával:

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

A várólista-tároló segítségével fogadhatja a lehúzandó eseményeket. Előfordulhat, hogy a Várólista-tárolót akkor használja, ha hosszú ideig futó folyamattal rendelkezik, amely túl sokáig tart a válaszadáshoz. Események küldése a várólista-tárolóba, az alkalmazás lehívhatja és feldolgozhatja az eseményeket a saját ütemezése szerint.

|Cím  |Leírás  |
|---------|---------|
| [Rövid útmutató: egyéni események irányítása az Azure Queue storage-ba az Azure CLI és az Event Grid segítségével](custom-event-to-queue-storage.md) | Ez a témakör azt ismerteti, hogy miként küldhet egyéni eseményeket várólista-tárolóba. |

## <a name="webhooks"></a>Webhookok

Webhookok használata az eseményekre válaszoló testreszabható végpontokhoz.

|Cím  |Leírás  |
|---------|---------|
| Rövid útmutató: egyéni események létrehozása és irányítása a – [Azure CLI,](custom-event-quickstart.md) [PowerShell](custom-event-quickstart-powershell.md)és [portál használatával.](custom-event-quickstart-portal.md) | Bemutatja, hogyan küldhet egyéni eseményeket egy WebHook-ra. |
| Rövid útmutató: blob tárolási események útvonala egy egyéni webvégpont - [Azure CLI,](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)és [portál](blob-event-quickstart-portal.md). | Bemutatja, hogyan küldhet blob tárolási eseményeket egy WebHook. |
| [Rövid útmutató: tárolóbeállítási események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan azure CLI container registry események küldéséhez. |
| [Áttekintés: események fogadása HTTP-végpontra](receive-events.md) | Http-végpont érvényesítésének és esemény-előfizetésből való események fogadásának, valamint események fogadásának és deszerializálásának ismertetése. |

## <a name="next-steps"></a>További lépések

* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez olvassa el az [Egyéni események létrehozása és irányítása az Azure Event Griddel című témakört.](custom-event-quickstart.md)
