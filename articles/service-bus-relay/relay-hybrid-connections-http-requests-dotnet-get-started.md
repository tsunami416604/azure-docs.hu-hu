---
title: Ismerkedés a hibrid Azure Relay-kapcsolatok HTTP-kéréseivel a .NET-ben | Microsoft Docs
description: C#-konzolalkalmazást hozhat létre a hibrid Azure Relay-kapcsolatok HTTP-kéréseihez a .NET-ben.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: de905466d47774decf864ace5464bb2a68e5e6bc
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51611998"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Ismerkedés a hibrid Relay-kapcsolatok HTTP-kéréseivel a .NET-ben
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ebben a rövid útmutatóban létrehozhat .NET küldői és fogadói alkalmazások, amelyek üzenetek küldése és fogadása a HTTP protokoll használatával. Az alkalmazások az Azure Relay hibrid kapcsolatok szolgáltatását használja. Az általános Azure Relay kapcsolatos további információkért lásd: [Azure Relay](relay-what-is-it.md). 

Ebben a rövid útmutatóban tegye a következőket:

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása ezen a névtéren az Azure Portal használatával.
3. Kiszolgálói (figyelő) konzolalkalmazás írása üzenetfogadási céllal.
4. Ügyfél-konzolalkalmazás (küldő) írása üzenetküldési céllal.
5. Alkalmazások futtatása. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* [Visual Studio 2015 vagy újabb](http://www.visualstudio.com). A jelen oktatóanyag példái a Visual Studio 2017-et használják.
* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

## <a name="create-a-namespace"></a>Névtér létrehozása
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Hibrid kapcsolat létrehozása
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Kiszolgálói alkalmazás (figyelő) létrehozása
A Visual Studióban egy C# nyelven íródott konzolalkalmazást hozunk létre az üzenetek figyeléséhez és a Relay-től való fogadásához.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Ügyfélalkalmazás létrehozása (küldő)
A Visual Studióban egy C# nyelven íródott konzolalkalmazást hozunk létre az üzenetek Relay-be való küldéséhez.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Az alkalmazások futtatása
1. Futtassa a kiszolgálóalkalmazást. A konzolon a következő szöveget fogja látni:

    ```
    Online
    Server listening
    ```
1. Futtassa az ügyfélalkalmazást. Megjelenik a `hello!` üzenet az ügyfélablakban. Az ügyfél elküld egy HTTP-kérelem a kiszolgálóra, és a kiszolgáló válasza egy `hello!`. 
3. A konzolablakok bezárásához nyomja le az **ENTER** billentyűt mindkét konzolablakban. 

Gratulálunk, végpontok közötti hibrid kapcsolati alkalmazást hozott létre!

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott .NET ügyfél és kiszolgáló-alkalmazások, amelyek HTTP üzenetek küldése és fogadása. Az Azure Relay hibrid kapcsolatok szolgáltatását is támogatja a websockets protokoll használatával üzeneteket küldjön és fogadjon. Ismerje meg, hogyan használja a websockets protokollt az Azure Relay hibrid kapcsolatai, tekintse meg a [WebSockets rövid](relay-hybrid-connections-dotnet-get-started.md).

Ebben a rövid útmutatóban a .NET-keretrendszer létrehozásához használt ügyfél és kiszolgáló alkalmazások. Ügyfél- és alkalmazásokat, a Node.js használatával kapcsolatban lásd: a [Node.js Websocketek rövid](relay-hybrid-connections-node-get-started.md) vagy a [Node.js HTTP rövid](relay-hybrid-connections-http-requests-dotnet-get-started.md).