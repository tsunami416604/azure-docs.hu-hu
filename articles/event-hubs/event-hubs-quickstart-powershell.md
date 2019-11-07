---
title: 'Rövid útmutató: Event hub létrehozása a PowerShell használatával – Azure Event Hubs'
description: Ez a rövid útmutató azt mutatja be, hogyan hozhat létre egy eseményközpontot az Azure PowerShell-lel, majd hogyan küldhet és fogadhat eseményeket a .NET Standard SDK használatával.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 2a88fe340037c31a71cb34181682095f31400c36
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720609"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Rövid útmutató: Eseményközpont létrehozása a PowerShell-lel

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebben a rövid útmutatóban az Azure PowerShell használatával hoz létre eseményközpontot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot][], mielőtt hozzákezd.
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- A [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 2.0-s vagy újabb verziója.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyileg használja a PowerShellt, a rövid útmutató elvégzéséhez a PowerShell legújabb verzióját kell futtatnia. Ha telepíteni vagy frissíteni szeretne: [Az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport Azure-erőforrások logikai gyűjteménye. Az eseményközpont létrehozásához szüksége lesz egy erőforráscsoportra. 

A következő példa létrehoz egy erőforráscsoportot az USA keleti régiójában. Cserélje le a `myResourceGroup` elemet a használni kívánt erőforráscsoport nevére:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az erőforráscsoport létrehozása után hozzon létre egy Event Hubs-névteret az erőforráscsoportban. Az Event Hubs-névtér egyedi, teljes tartománynevet biztosít, amelyben létrehozhatja az eseményközpontot. Cserélje le a `namespace_name` elemet a névtér egyedi nevére:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Most, hogy rendelkezik Event Hubs-névtérrel, hozzon létre egy eseményközpontot abban a névtérben:  
A `MessageRetentionInDays` engedélyezett időtartama 1 és 7 nap között van.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Gratulálunk! Az Azure PowerShell segítségével létrehozott egy Event Hubs-névteret és egy eseményközpontot a névtéren belül. 

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Event Hubs-névteret hozott létre, és mintaalkalmazások használatával eseményeket küldött az eseményközpontba, illetve fogadott onnan. Az események küldése az Event hub-tól (vagy) események fogadására vonatkozó részletes utasításokért lásd a **küldési és fogadási események** oktatóanyagokat: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak reecive)](event-hubs-storm-getstarted-receive.md)


[hozzon létre egy ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-az-ps
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-Azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
