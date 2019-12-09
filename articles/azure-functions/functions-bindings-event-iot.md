---
title: Az Azure IoT Hub-kötések Azure Functions
description: Ismerje meg, hogyan használhatók IoT Hub kötések a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 2940f9e2adff82c100ed347431e8c1d27c30202e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924450"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Az Azure IoT Hub-kötések Azure Functions

Ez a cikk azt ismerteti, hogyan használhatók a IoT Hub Azure Functions kötései. Az IoT Hub támogatás az [Azure Event Hubs-kötésen](functions-bindings-event-hubs.md)alapul.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

Az 1. x Azure Functions-es verzióban a IoT Hub kötések a [Microsoft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet csomagban találhatók, 2. x verzióban. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub-tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A 2. x és újabb függvények esetében használja a [Microsoft. Azure. webjobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) csomagot, 3. x verziót. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Míg az alábbi mintakód-minták az Event hub API-t használják, a megadott szintaxis IoT Hub függvények esetében alkalmazható.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
