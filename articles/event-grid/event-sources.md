---
title: Az Azure Event rács Eseményforrások
description: Ismerteti a támogatott eseményforrások Azure esemény rács
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: ce5c6ccd4c2e51375bbbb4df677ee96c839b30f6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="event-sources-in-azure-event-grid"></a>Azure Event rácsban Eseményforrások

Egy eseményforrás, ahol az esemény akkor fordul elő. Több Azure-szolgáltatások automatikusan konfigurált küldi az eseményeket. Létrehozhat egyéni alkalmazásokat, amelyek küldi az eseményeket is. Egyéni alkalmazásokat nem kell az események terjesztését esemény rács használandó Azure-ban üzemeltetett.

Ez a cikk nyújt minden eseményforrás hivatkozások olyan témakörökre mutatnak.

## <a name="azure-subscriptions"></a>Azure-előfizetések

Fizessen elő a Azure-előfizetések események válaszolni erőforrások változásainak Azure-előfizetés között.

|Beosztás |Leírás  |
|---------|---------|
| [Azure Automation szolgáltatásbeli integrálható a esemény rács és a Microsoft csapatai](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, mely küld egy eseményt. Az eseményt akkor váltja ki az Automation-runbook címkéket a virtuális gépet, amely egy Microsoft Teams csatorna küldött üzenetet küld. |
| [Esemény séma](event-schema-subscriptions.md) | Azure-előfizetés események mezők jeleníti meg. |

## <a name="custom-topics"></a>Egyéni kapcsolatos témakörök

Egyéni témakörök alkalmazásesemények válaszolni előfizetni.

|Beosztás  |Leírás  |
|---------|---------|
| [Hozzon létre és az Azure parancssori felület segítségével egyéni események útvonal](custom-event-quickstart.md) | Egyéni események küldése az Azure parancssori felület használatával jeleníti meg. |
| [Hozzon létre és irányításához az Azure PowerShell egyéni események](custom-event-quickstart-powershell.md) | Bemutatja, hogyan egyéni események küldése az Azure PowerShell használatával. |
| [Hozzon létre és útvonal-egyéni események és az Azure portál](custom-event-quickstart-portal.md) | Bemutatja, hogyan használhatja a portált egyéni események küldésére. |
| [Küldje el a témakör egyéni](post-to-custom-topic.md) | Bemutatja, hogyan elküldeni egy eseményt, egy egyéni témakörbe. |
| [Egyéni események útvonal az Azure Queue Storage](custom-event-to-queue-storage.md) | Egyéni események küldése a várólista tárolási módját ismerteti. |
| [Esemény séma](event-schema.md) | Egyéni események mezők jeleníti meg. |

## <a name="event-hubs"></a>Event Hubs

Az Event Hubs események fájl események rögzítése válaszolni előfizetni.

|Beosztás  |Leírás  |
|---------|---------|
| [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md) | Az Event Hubs rögzítési fájlt hoz létre, amikor esemény rács függvény alkalmazásokhoz küld egy eseményt. Az alkalmazás kéri le a rögzítést fájlt, és adatokat áttelepíti az adatraktárban. |
| [Esemény séma](event-schema-event-hubs.md) | Az Event Hubs események mezők jeleníti meg. |

## <a name="iot-hub"></a>IoT Hub

Előfizetés IoT Hub eszköz válaszolni események létrehozása és események törlése.

|Beosztás  |Leírás  |
|---------|---------|
| [E-mail értesítést kapjanak a Logic Apps segítségével Azure IoT Hub-események](publish-iot-hub-events-to-logic-apps.md) | Logikai alkalmazás e-mailben értesítést küld, minden alkalommal, amikor egy eszközt az IoT hub kerül. |
| [Az IoT-központ események reagálnak műveleteket esemény rács használatával](../iot-hub/iot-hub-event-grid.md) | Iot-központok integrálása esemény rács áttekintése. |
| [Esemény séma](event-schema-iot-hub.md) | Mezők az IoT hubon eseményt tartalmazza. |

## <a name="media-services"></a>Media Services

Fizessen elő a Media Services események feladat állapotesemények válaszolni.

|Beosztás  |Leírás  |
|---------|---------|
| [Reagál a Media Services-események](../media-services/latest/reacting-to-media-services-events.md) | A Media Services integrálása esemény rács áttekintése. |
| [Azure Media Services eseményeket továbbítani egy egyéni webkiszolgáló-végpont parancssori felület használatával](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan is küldi az eseményeket a Media Services. |
| [Esemény séma](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Media Services események mezők jeleníti meg. |

## <a name="resource-groups"></a>Erőforráscsoportok

Fizessen elő a erőforrás események csoportosítása válaszolni erőforrások változásainak között egy erőforráscsoportot.

|Beosztás  |Leírás  |
|---------|---------|
| [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md) | Logikai alkalmazás egy virtuális gép módosításai figyeli, és ezeket a módosításokat vonatkozó e-mailek küldése. |
| [Eseményséma](event-schema-resource-groups.md) | Látható mezők erőforrás események csoportosítása. |

## <a name="service-bus"></a>Service Bus

Fizessen elő a Service Bus események üzenetek nélkül egy aktív figyelő válaszolni.

|Beosztás  |Leírás  |
|---------|---------|
| [Az Azure Service Bus számára, Azure esemény rács integrációs példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Esemény rács üzeneteket küld a Service Bus-témakörbe alkalmazás és a logikai alkalmazás működik. |
| [Az Azure Service Bus számára, esemény rács integrációjának áttekintése](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | A Service Bus integrálása esemény rács áttekintése. |
| [Esemény séma](event-schema-service-bus.md) | A Service Bus események mezők jeleníti meg. |

## <a name="storage"></a>Storage

A Blob Storage-események válaszolni a blob-létrehozott és a törölt események előfizetni.

|Beosztás  |Leírás  |
|---------|---------|
| [A Blob storage-események átirányítása egy egyéni webkiszolgáló-végpont Azure parancssori felülettel](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan blob storage-események küldése az Azure parancssori felület használatával. |
| [Útvonal Blob storage-események egy egyéni webkiszolgáló-végponthoz, a PowerShell használatával](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan blob storage-események küldése az Azure PowerShell használatával. |
| [Reagálás Blob Storage-eseményekre](../storage/blobs/storage-blob-event-overview.md) | A Blob storage integrálása esemény rács áttekintése. |
| [Esemény séma](event-schema-blob-storage.md) | A Blob Storage-események mezők jeleníti meg. |

## <a name="next-steps"></a>További lépések

* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
