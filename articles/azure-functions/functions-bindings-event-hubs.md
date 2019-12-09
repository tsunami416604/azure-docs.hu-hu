---
title: Az Azure Event Hubs-kötések Azure Functions
description: Ismerje meg, hogyan használhatók az Azure Event Hubs-kötések a Azure Functionsban.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: 998ba122d6582a86122bbe65ff47bad1ea24a900
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925535"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Az Azure Event Hubs-kötések Azure Functions

Ez a cikk azt ismerteti, hogyan használhatók az [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) -kötések a Azure Functionshoz. Azure Functions támogatja az aktiválási és kimeneti kötéseket a Event Hubshoz.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

Az 1. x Azure Functions-es verzióban a Event Hubs kötések a [Microsoft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet csomagban találhatók, 2. x verzióban.
A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub-tárházban található.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A 2. x és újabb függvények esetében használja a [Microsoft. Azure. webjobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) csomagot, 3. x verziót.
A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
