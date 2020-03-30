---
title: Az Azure IoT Hub kimeneti kötése az Azure Functionshez
description: Ismerje meg, hogyan írhat üzeneteket az Azure IoT Hubs streamelési az Azure Functions használatával.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277426"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Az Azure IoT Hub kimeneti kötése az Azure Functionshez

Ez a cikk bemutatja, hogyan működik az Azure Functions kimeneti kötések az IoT Hub. Az IoT Hub támogatása az [Azure Event Hubs binding-en](functions-bindings-event-hubs.md)alapul.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](functions-bindings-event-iot.md)olvashat.

> [!IMPORTANT]
> Míg a következő kódminták az Event Hub API-t használják, a megadott szintaxis az IoT Hub-függvények esetén alkalmazható.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>További lépések

- [Az eseményközpont eseményfolyamába küldött események reválaszolása (Eseményindító)](./functions-bindings-event-iot-trigger.md)
