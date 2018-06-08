---
title: Az Azure Event rács eseménykezelők
description: Ismerteti a támogatott eseménykezelők Azure esemény rács
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: tomfitz
ms.openlocfilehash: 7c012bdf025a352788aec2d2d70bab33d7914577
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849542"
---
# <a name="event-handlers-in-azure-event-grid"></a>Az eseménykezelők Azure esemény rácsban

Eseménykezelő az a hely, ahol az esemény érkezik. A kezelő bontja feldolgozni az eseményt néhány további művelet. Több Azure-szolgáltatások automatikusan konfigurált események kezelésére. A webhook események kezelésére is használható. A webhook nem kezelhetnek eseményeket az Azure-ban üzemeltetett kell. Esemény rács csak a HTTPS-végpontnak webhook támogatja.

A cikkben minden eseménykezelő hivatkozások olyan témakörökre mutatnak.

## <a name="azure-automation"></a>Azure Automation

Azure Automation segítségével a runbookok automatizált folyamat események.

|Beosztás  |Leírás  |
|---------|---------|
|[Azure Automation szolgáltatásbeli integrálható a esemény rács és a Microsoft csapatai](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, mely küld egy eseményt. Az eseményt akkor váltja ki az Automation-runbook címkéket a virtuális gépet, amely egy Microsoft Teams csatorna küldött üzenetet küld. |

## <a name="azure-functions"></a>Azure Functions

Használja az Azure Functions események kiszolgáló nélküli válasz.

Ha az Azure Functionst használja kezelőként, használja az Event Grid-triggereket az általános HTTP-triggerek helyett. Az Event Grid automatikusan érvényesíti az Event Grid függvény eseményindítóit. Általános HTTP-eseményindítók esetén meg kell valósítani az [érvényesítési választ](security-authentication.md#webhook-event-delivery).

|Beosztás  |Leírás  |
|---------|---------|
| [Az Azure Functions rács eseményindító](../azure-functions/functions-bindings-event-grid.md) | Az esemény rács eseményindító funkciókkal használatának áttekintése. |
| [Feltöltött képek átméretezésének automatizálása az Event Grid használatával](resize-images-on-storage-blob-upload-event.md) | Felhasználók keresztül webalkalmazás képek feltöltése a tárfiókba. A tárolási blob létrehozásakor esemény rács a függvény alkalmazáshoz, amely a feltöltött lemezkép átméretezi küld egy eseményt. |
| [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md) | Az Event Hubs rögzítési fájlt hoz létre, amikor esemény rács függvény alkalmazásokhoz küld egy eseményt. Az alkalmazás kéri le a rögzítést fájlt, és adatokat áttelepíti az adatraktárban. |
| [Az Azure Service Bus számára, Azure esemény rács integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Esemény rács üzeneteket küld a Service Bus-témakörbe alkalmazás és a logikai alkalmazás működik. |

## <a name="event-hubs"></a>Event Hubs

Az Event Hubs akkor használja, ha a megoldás lekérdezi események gyorsabb, mint az eseményeket is feldolgozza. Az alkalmazás saját ütemezést, akkor az Event Hubs eseményeit dolgozza fel. A bejövő események kezelésére a Eseményfeldolgozási méretezheti.

|Beosztás  |Leírás  |
|---------|---------|
| [Egyéni események útvonal az Azure Event hubs az Azure CLI és az esemény rács](custom-event-to-eventhub.md) | Egyéni esemény küld egy eseményközpontot, az alkalmazás általi feldolgozás alatt. |

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

Azure hibrid kapcsolatok segítségével alkalmazásokat, amelyek a vállalati hálózaton belül van, és nem kell nyilvánosan elérhető végponton elküldje az eseményeket.

|Beosztás  |Leírás  |
|---------|---------|
| [Események küldése a hibrid kapcsolat](custom-event-to-hybrid-connection.md) | Egyéni esemény küld a meglévő hibrid kapcsolat feldolgozása a figyelő alkalmazás. |

## <a name="logic-apps"></a>Logic Apps

A Logic Apps segítségével automatizálhatja üzleti folyamatait válaszol az események.

|Beosztás  |Leírás  |
|---------|---------|
| [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md) | Logikai alkalmazás egy virtuális gép módosításai figyeli, és ezeket a módosításokat vonatkozó e-mailek küldése. |
| [E-mail értesítést kapjanak a Logic Apps segítségével Azure IoT Hub-események](publish-iot-hub-events-to-logic-apps.md) | Logikai alkalmazás e-mailben értesítést küld, minden alkalommal, amikor egy eszközt az IoT hub kerül. |
| [Az Azure Service Bus számára, Azure esemény rács integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Esemény rács üzeneteket küld a Service Bus-témakörbe alkalmazás és a logikai alkalmazás működik. |

## <a name="queue-storage"></a>Queue Storage

A Queue storage segítségével lekérése-események fogadásához. A Queue storage lehet használni, ha egy hosszú ideig tartó folyamatot, amely túl lassan válaszol. Az események a Queue storage címen, az alkalmazás is lekéréses és feldolgozni az eseményeket a saját ütemezés szerint.

|Beosztás  |Leírás  |
|---------|---------|
| [Egyéni események útvonal az Azure Queue Storage Azure CLI és az esemény rács](custom-event-to-queue-storage.md) | Egyéni események küldése a várólista tárolási módját ismerteti. |

## <a name="webhooks"></a>Webhookok

Használja a webhookok testre szabható végpontok események válaszolnak.

|Beosztás  |Leírás  |
|---------|---------|
| [HTTP-végponttal események fogadásához](receive-events.md) | Események fogadása egy esemény-előfizetést, és kaphatnak és események deszerializálni HTTP-végponttal ellenőrzését ismerteti. |

## <a name="next-steps"></a>További lépések

* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
