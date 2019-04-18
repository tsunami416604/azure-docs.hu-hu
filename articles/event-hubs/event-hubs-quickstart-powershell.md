---
title: Létrehoz egy eseményközpontot, PowerShell – Azure Event Hubs használatával |} A Microsoft Docs
description: Ez a rövid útmutató azt mutatja be, hogyan hozhat létre egy eseményközpontot az Azure PowerShell-lel, majd hogyan küldhet és fogadhat eseményeket a .NET Standard SDK használatával.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: fba0d3ac5a20bd4f10b815450f6ce08e50f88ea0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681301"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Gyors útmutató: Létrehoz egy eseményközpontot, az Azure PowerShell-lel

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebben a rövid útmutatóban az Azure PowerShell használatával hoz létre eseményközpontot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot][], mielőtt hozzákezd.
- [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb.
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
Az időszak engedélyezett `MessageRetentionInDays` 1 és 7 nap közötti.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Gratulálunk! Az Azure PowerShell segítségével létrehozott egy Event Hubs-névteret és egy eseményközpontot a névtéren belül. 

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Event Hubs-névteret hozott létre, és mintaalkalmazások használatával eseményeket küldött az eseményközpontba, illetve fogadott onnan. Események küldése (vagy) események fogadása az event hubs részletes utasításokért lásd: a **események küldéséhez és fogadásához** oktatóanyagok: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (csak küldése)](event-hubs-c-getstarted-send.md)
- [Az Apache Storm (csak reecive)](event-hubs-storm-getstarted-receive.md)


[hozzon létre egy ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-az-ps
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-Azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
