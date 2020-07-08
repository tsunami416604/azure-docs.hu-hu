---
title: Az Azure IoT Hub-kötések Azure Functions
description: Megtudhatja, hogyan válaszolhat az IoT hub Event streambe Azure Functions küldött eseményekre.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77589726"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT Hub trigger a Azure Functionshoz

Ez a cikk azt ismerteti, hogyan használhatók a IoT Hub Azure Functions kötései. Az IoT Hub támogatás az [Azure Event Hubs-kötésen](functions-bindings-event-hubs.md)alapul.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Míg az alábbi mintakód-minták az Event hub API-t használják, a megadott szintaxis IoT Hub függvények esetében alkalmazható.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>További lépések

- [Események írása egy esemény-adatfolyamba (kimeneti kötés)](./functions-bindings-event-iot-output.md)
