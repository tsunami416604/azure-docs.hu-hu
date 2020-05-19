---
title: Azure-függvény Azure Event Grid események eseménykezelőként
description: Leírja, hogyan használhatja az Azure functions-t Event Grid eseményekhez tartozó eseménykezelőként.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 3ff3c0013cb7a373461b997b9922612763461b8d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598107"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Azure-függvény Event Grid események eseménykezelőként

Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő végrehajt egy műveletet az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan van konfigurálva az események kezelésére, és **Azure functions** az egyikük. 

A kiszolgáló nélküli architektúrában **Azure functions** használatával válaszolhat a Event Grid eseményeire. Ha egy Azure-függvényt kezelőként használ, használja az általános HTTP-trigger helyett a Event Grid triggert. Event Grid automatikusan érvényesíti Event Grid eseményindítókat. Általános HTTP-eseményindítók esetén saját magának kell végrehajtania az [érvényesítési választ](webhook-event-delivery.md) .

További információ: [Event Grid trigger a Azure functions](../azure-functions/functions-bindings-event-grid.md) számára az Event Grid trigger függvények használatával való használatának áttekintése.

## <a name="tutorials"></a>Oktatóanyagok

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: események kezelése a függvénnyel](custom-event-to-function.md) | Egyéni eseményt küld egy függvénynek feldolgozásra. |
| [Oktatóanyag: feltöltött képek átméretezésének automatizálása Event Grid használatával](resize-images-on-storage-blob-upload-event.md) | A felhasználók képeket töltenek fel a webalkalmazásból a Storage-fiókba. Storage-blob létrehozásakor Event Grid egy eseményt küld a Function alkalmazásnak, amely átméretezi a feltöltött képet. |
| [Oktatóanyag: stream big data adattárházba](event-grid-event-hubs-integration.md) | Amikor Event Hubs létrehoz egy rögzítési fájlt, Event Grid küld egy eseményt egy Function alkalmazásnak. Az alkalmazás lekéri a rögzítési fájlt, és áttelepíti az adattárházba. |
| [Oktatóanyag: Azure Service Bus Azure Event Grid integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld Service Bus témakörből egy Function-alkalmazásba és egy logikai alkalmazásba. |


## <a name="next-steps"></a>További lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 
