---
title: Eseménykezelők Azure Event Grid
description: A Azure Event Grid támogatott eseménykezelőit ismerteti. Azure Automation, functions, Event Hubs, Hibrid kapcsolatok, Logic Apps, Service Bus, Queue Storage, webhookok.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: c29526bdee5ad70b3acb23117ee003877f52a55e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592457"
---
# <a name="event-handlers-in-azure-event-grid"></a>Eseménykezelők a Azure Event Gridban
Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő további műveletet hajt végre az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan az események kezelésére van konfigurálva. Az események kezelésére bármilyen webhookot is használhat. A webhookot nem kell az Azure-ban üzemeltetni az események kezeléséhez. A Event Grid csak a HTTPS webhook-végpontokat támogatja.

## <a name="supported-event-handlers"></a>Támogatott eseménykezelők
A támogatott eseménykezelők a következők: 

- [Webhookok](handler-webhooks.md). Azure Automation runbookok és Logic Apps webhookok használatával támogatottak. 
- [Azure functions](handler-functions.md)
- [Event hubok](handler-event-hubs.md)
- [Hibrid kapcsolatok továbbítása](handler-relay-hybrid-connections.md)
- [Várólisták és témakörök Service Bus](handler-service-bus.md)
- [Tárolási várólisták](handler-storage-queues.md)

## <a name="next-steps"></a>További lépések
- Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
