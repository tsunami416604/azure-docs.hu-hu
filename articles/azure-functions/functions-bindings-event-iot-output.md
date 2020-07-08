---
title: Az Azure IoT Hub kimeneti kötése Azure Functions
description: Megtudhatja, hogyan írhat üzeneteket az Azure IoT hubok streamek számára a Azure Functions használatával.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79277426"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Az Azure IoT Hub kimeneti kötése Azure Functions

Ez a cikk azt ismerteti, hogyan használható a IoT Hub Azure Functions kimeneti kötéseinek használata. Az IoT Hub támogatás az [Azure Event Hubs-kötésen](functions-bindings-event-hubs.md)alapul.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Míg az alábbi mintakód-minták az Event hub API-t használják, a megadott szintaxis IoT Hub függvények esetében alkalmazható.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>További lépések

- [Válasz az Event hub esemény-adatfolyamba küldött eseményekre (trigger)](./functions-bindings-event-iot-trigger.md)
