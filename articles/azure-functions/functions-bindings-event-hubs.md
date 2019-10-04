---
title: Az Azure Event Hubs-kötések Azure Functions
description: Ismerje meg, hogyan használhatók az Azure Event Hubs-kötések a Azure Functionsban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: bc75ad08716a001ae0cfd934dbc8d5da668dc1c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086650"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Az Azure Event Hubs-kötések Azure Functions

Ez a cikk azt ismerteti, hogyan használhatók az [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) -kötések a Azure Functionshoz. Azure Functions támogatja az aktiválási és kimeneti kötéseket a Event Hubshoz.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

Az 1. x Azure Functions-es verzióban a Event Hubs kötések a [Microsoft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet csomagban találhatók, 2. x verzióban.
A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub-adattárban.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A 2. x függvények esetében használja a [Microsoft. Azure. webjobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) csomagot, 3. x verziót.
A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
