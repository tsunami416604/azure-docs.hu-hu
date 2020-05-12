---
title: Azure Relay Hibrid kapcsolatok – WebSockets a .NET-ben
description: Egy C# konzolos alkalmazás írása Azure Relay Hibrid kapcsolatok WebSockets szolgáltatáshoz.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 13612f8ffa343e483165a8dbdd54d1b2b1f5e2cf
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211930"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Ismerkedés a Relay Hibrid kapcsolatok WebSockets szolgáltatással a .NET-ben
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ebben a rövid útmutatóban olyan .NET-küldő és fogadó alkalmazásokat hoz létre, amelyek üzeneteket küldenek és fogadnak a Azure Relay Hibrid kapcsolatok WebSockets használatával. Az általános Azure Relayről a [Azure Relay](relay-what-is-it.md)című témakörben olvashat bővebben. 

Ebben a rövid útmutatóban a következő lépéseket hajtja végre:

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása ezen a névtéren az Azure Portal használatával.
3. Kiszolgálói (figyelő) konzolalkalmazás írása üzenetfogadási céllal.
4. Ügyfél-konzolalkalmazás (küldő) írása üzenetküldési céllal.
5. Alkalmazások futtatása. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* [Visual Studio 2015 vagy újabb](https://www.visualstudio.com). A jelen oktatóanyag példái a Visual Studio 2017-et használják.
* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

## <a name="create-a-namespace"></a>Névtér létrehozása
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Hibrid kapcsolat létrehozása
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Kiszolgálói alkalmazás (figyelő) létrehozása
A Visual Studióban egy C# nyelven íródott konzolalkalmazást hozunk létre az üzenetek figyeléséhez és a Relay-től való fogadásához.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Ügyfélalkalmazás létrehozása (küldő)
A Visual Studióban egy C# nyelven íródott konzolalkalmazást hozunk létre az üzenetek Relay-be való küldéséhez.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Az alkalmazások futtatása
1. Futtassa a kiszolgálóalkalmazást.
2. Futtassa az ügyfélalkalmazást, és adjon meg szöveget.
3. Győződjön meg arról, hogy az alkalmazás konzolja megjeleníti a szöveget, amely az ügyfélalkalmazásban lett megadva.

    ![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Gratulálunk, létrehozott egy teljes Hibrid kapcsolatok alkalmazást!

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban olyan .NET-ügyfél-és kiszolgálói alkalmazásokat hozott létre, amelyek websocketeket használnak az üzenetek küldéséhez és fogadásához. A Azure Relay Hibrid kapcsolatok funkciója a HTTP használatával is támogatja az üzenetek küldését és fogadását. Ha szeretné megtudni, hogyan használhatja a HTTP-t a Azure Relay Hibrid kapcsolatok használatával, tekintse meg a [http](relay-hybrid-connections-http-requests-dotnet-get-started.md)rövid útmutatót.

Ebben a rövid útmutatóban a .NET-keretrendszert használta az ügyfél-és kiszolgálói alkalmazások létrehozásához. Az ügyfél-és kiszolgálói alkalmazások Node. js használatával történő írásához a [Node. js WebSockets](relay-hybrid-connections-node-get-started.md) rövid útmutatójában vagy a [Node. js http](relay-hybrid-connections-http-requests-dotnet-get-started.md)-gyors útmutatóban olvashat.

