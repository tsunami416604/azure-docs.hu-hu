---
title: Az Azure IoT Hub-kötések az Azure Functionshez
description: Ismerje meg az IoT Hub-eseményindító és az Azure Functions kötés használatát.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586129"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Az Azure IoT Hub-kötések az Azure Functionshez

Ez a cikkkészlet ismerteti, hogyan működik együtt az Azure Functions-kötések az IoT Hub. Az IoT Hub támogatása az [Azure Event Hubs binding-en](functions-bindings-event-hubs.md)alapul.

> [!IMPORTANT]
> Míg a következő kódminták az Event Hub API-t használják, a megadott szintaxis az IoT Hub-függvények esetén alkalmazható.

| Műveletek | Típus |
|--------|------|
| Válaszoljon az IoT hub eseményfolyamba küldött eseményekre. | [Eseményindító](./functions-bindings-event-iot-trigger.md) |
| Események írása IoT-eseményfolyamba | [Kimenetkötés](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>További lépések

- [Az eseményközpont eseményfolyamába küldött események reválaszolása (Eseményindító)](./functions-bindings-event-iot-trigger.md)
- [Események írása eseményfolyamba (Kimenetkötés)](./functions-bindings-event-iot-output.md)
