---
title: Ismerkedés a hibrid Azure Relay-kapcsolatok websocketjeivel a Node-ban | Microsoft Docs
description: Node.js-konzolalkalmazást hozhat létre a hibrid Azure Relay-kapcsolatok websocketjeihez.
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 9bdf3d319735d8d4ca85235dfb949d440bba9a02
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615076"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Relay hibrid kapcsolatok a websockets protokoll Node.js-ben – első lépések

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ebben a rövid útmutatóban Node.js küldői és fogadói alkalmazásokat, amelyek üzenetek küldése és fogadása az Azure Relay hibrid kapcsolatok Websocketek használatával hoz létre. Az általános Azure Relay kapcsolatos további információkért lásd: [Azure Relay](relay-what-is-it.md). 

Ebben a rövid útmutatóban tegye a következőket: 

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása ezen a névtéren az Azure Portal használatával.
3. Kiszolgálói (figyelő) konzolalkalmazás írása üzenetfogadási céllal.
4. Ügyfél-konzolalkalmazás (küldő) írása üzenetküldési céllal.
5. Alkalmazások futtatása. 

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://nodejs.org/en/).
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

## <a name="create-a-namespace"></a>Névtér létrehozása
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Hibrid kapcsolat létrehozása
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Kiszolgálói alkalmazás (figyelő) létrehozása
Írjon egy Node.js-konzolalkalmazást az üzenetek figyeléséhez és a Relaytől való fogadásához.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Ügyfélalkalmazás létrehozása (küldő)
Írjon egy Node.js konzolalkalmazást az üzenetek a Relay számára való küldéséhez.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>Az alkalmazások futtatása

1. Futtassa a kiszolgálóalkalmazást: egy Node.js-parancssorba írja be a következőt: `node listener.js`.
2. Futtassa az ügyfélalkalmazást: egy Node.js parancssorba írja be a `node sender.js` parancsot, majd írjon be szöveget.
3. Győződjön meg arról, hogy az alkalmazás konzolja kiírja a szöveget, amely az ügyfélalkalmazásban lett megadva.

    ![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Gratulálunk, végpontok közötti hibrid kapcsolatok alkalmazást hozott létre a Node.js használatával.

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban létrehozott Node.js ügyfél és kiszolgáló-alkalmazások, amelyek a websockets protokoll üzenetek küldése és fogadása. Az Azure Relay hibrid kapcsolatok szolgáltatását is támogatja a HTTP protokollon keresztül küldhet és fogadhat üzeneteket. Az Azure Relay hibrid kapcsolatai HTTP használatával kapcsolatban lásd: a [Node.js HTTP rövid](relay-hybrid-connections-http-requests-node-get-started.md).

Ebben a rövid útmutatóban a Node.js ügyféloldali és kiszolgálói alkalmazások létrehozásához használt. Ismerje meg, hogyan írhat .NET-keretrendszer használatával ügyfél- és kiszolgálóalkalmazások, tekintse meg a [.NET Websocketek rövid](relay-hybrid-connections-dotnet-get-started.md) vagy a [.NET HTTP rövid](relay-hybrid-connections-http-requests-dotnet-get-started.md).


