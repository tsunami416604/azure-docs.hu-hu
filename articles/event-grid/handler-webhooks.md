---
title: Webhookok Azure Event Grid események eseménykezelői
description: Ismerteti, hogyan használhatók a webhookok Azure Event Grid eseményekhez tartozó eseménykezelőként. Azure Automation a runbookok és a Logic apps az eseménykezelők webhookok használatával történő használatát támogatja.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: ba67b1cd93bc1c713648f799090e0b2cd77cff1b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598618"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhookok, Automation-runbookok, Logic Apps Azure Event Grid események eseménykezelői
Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő további műveletet hajt végre az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan az események kezelésére van konfigurálva. Az események kezelésére bármilyen webhookot is használhat. A webhookot nem kell az Azure-ban üzemeltetni az események kezeléséhez. A Event Grid csak a HTTPS webhook-végpontokat támogatja.

> [!NOTE]
> Azure Automation a runbookok és a Logic apps az eseménykezelők webhookok használatával történő használatát támogatja. 

## <a name="webhooks"></a>Webhookok
Az alábbi cikkekben áttekintheti a webhookok eseménykezelőként való használatát bemutató példákat. 

|Cím  |Leírás  |
|---------|---------|
| Gyors útmutató: egyéni események létrehozása és irányítása az [Azure CLI](custom-event-quickstart.md), a PowerShell és a [Portal](custom-event-quickstart-portal.md) [használatával](custom-event-quickstart-powershell.md). | Bemutatja, hogyan küldhet egyéni eseményeket egy webhookba. |
| Gyors útmutató: blob Storage-események átirányítása egyéni webes végpontra az- [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), a [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)és a [Portal](blob-event-quickstart-portal.md)használatával. | Bemutatja, hogyan küldhetők blob Storage-események egy webhookba. |
| [Gyors útmutató: tárolói beállításjegyzékbeli események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan küldhet Container Registry eseményeket az Azure CLI használatával. |
| [Áttekintés: események fogadása HTTP-végpontra](receive-events.md) | Leírja, hogyan lehet érvényesíteni egy HTTP-végpontot események fogadására, valamint események fogadására és deszerializálására. |


## <a name="azure-automation"></a>Azure Automation
Az eseményeket Azure Automation runbookok használatával is feldolgozhatja. Az események automatikus runbookok való feldolgozása webhookok használatával támogatott. Hozzon létre egy webhookot a runbook, majd használja a webhook kezelőjét. Tekintse meg a következő oktatóanyagot egy példához: 

|Cím  |Leírás  |
|---------|---------|
|[Oktatóanyag: Azure Automation a Event Grid és a Microsoft Teams szolgáltatással](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, amely eseményt küld. Az esemény elindítja a virtuális gépet címkéző Automation-runbook, és elindítja a Microsoft Teams Channel szolgáltatásnak küldött üzenetet. |


## <a name="logic-apps"></a>Logic Apps
A **Logic apps** használatával üzleti folyamatokat valósíthat meg Event Grid események feldolgozásához. Ebben az esetben nem hoz létre webhookot explicit módon. A webhook automatikusan létrejön, ha úgy konfigurálja a logikai alkalmazást, hogy a Event Grid eseményeinek kezelését végzi. Példák a következő oktatóanyagokra: 

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: a virtuális gépek változásainak figyelése Azure Event Grid és Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | A logikai alkalmazások figyelik a virtuális gépek változásait, és e-maileket küldenek ezekről a változásokról. |
| [Oktatóanyag: az Azure IoT Hub eseményekre vonatkozó e-mailes értesítések küldése Logic Apps használatával](publish-iot-hub-events-to-logic-apps.md) | A logikai alkalmazások minden alkalommal elküldenek egy értesítő e-mailt, amikor egy eszköz bekerül az IoT hubhoz. |
| [Oktatóanyag: válaszadás a Azure Event Grid által a Azure Functions és a Azure Logic Apps használatával kapott Azure Service Bus eseményekre](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld Service Bus témakörből az alkalmazás és a logikai alkalmazás működéséhez. |

## <a name="next-steps"></a>További lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 
