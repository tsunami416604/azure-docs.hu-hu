---
title: Az Azure IoT Hub-kötések az Azure Functionshez
description: Ismerje meg, hogyan reagálhat az IoT hub eseményfolyamába küldött eseményekre az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589726"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Az Azure IoT Hub eseményindítója az Azure Functionshez

Ez a cikk bemutatja, hogyan működik együtt az Azure Functions-kötések az IoT Hub. Az IoT Hub támogatása az [Azure Event Hubs binding-en](functions-bindings-event-hubs.md)alapul.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](functions-bindings-event-iot.md)olvashat.

> [!IMPORTANT]
> Míg a következő kódminták az Event Hub API-t használják, a megadott szintaxis az IoT Hub-függvények esetén alkalmazható.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>További lépések

- [Események írása eseményfolyamba (Kimenetkötés)](./functions-bindings-event-iot-output.md)
