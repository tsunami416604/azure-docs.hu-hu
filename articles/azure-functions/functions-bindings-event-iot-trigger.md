---
title: Azure IoT Hub trigger a Azure Functionshoz
description: Megtudhatja, hogyan válaszolhat az IoT hub Event streambe Azure Functions küldött eseményekre.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 25396da3fb9a4293633308bf2e9d3c6b3d07265d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87041637"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT Hub trigger a Azure Functionshoz

Ez a cikk azt ismerteti, hogyan használhatók a IoT Hub Azure Functions kötései. Az IoT Hub támogatás az [Azure Event Hubs-kötésen](functions-bindings-event-hubs.md)alapul.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Míg az alábbi mintakód-minták az Event hub API-t használják, a megadott szintaxis IoT Hub függvények esetében alkalmazható.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>További lépések

- [Események írása egy esemény-adatfolyamba (kimeneti kötés)](./functions-bindings-event-iot-output.md)
