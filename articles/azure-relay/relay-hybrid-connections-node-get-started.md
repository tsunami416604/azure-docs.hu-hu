---
title: Azure Relay Hibrid kapcsolatok – WebSockets a csomópontban
description: Node.js-konzolalkalmazást hozhat létre a hibrid Azure Relay-kapcsolatok websocketjeihez.
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 818db4db082a441877b573fd52361e63becce374
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211891"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Ismerkedés a Relay Hibrid kapcsolatok WebSockets szolgáltatással a Node. js-ben

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ebben a rövid útmutatóban olyan Node. js-küldő és fogadó alkalmazásokat hoz létre, amelyek üzeneteket küldenek és fogadnak a Azure Relay Hibrid kapcsolatok WebSockets használatával. Az általános Azure Relayről a [Azure Relay](relay-what-is-it.md)című témakörben olvashat bővebben. 

Ebben a rövid útmutatóban a következő lépéseket hajtja végre: 

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása ezen a névtéren az Azure Portal használatával.
3. Kiszolgálói (figyelő) konzolalkalmazás írása üzenetfogadási céllal.
4. Ügyfél-konzolalkalmazás (küldő) írása üzenetküldési céllal.
5. Alkalmazások futtatása. 

## <a name="prerequisites"></a>Előfeltételek

- [Node. js](https://nodejs.org/en/)-fájl.
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
Ebben a rövid útmutatóban létrehozott egy Node. js-ügyfelet és kiszolgálói alkalmazást, amely websocketeket használt az üzenetek küldéséhez és fogadásához. A Azure Relay Hibrid kapcsolatok funkciója a HTTP használatával is támogatja az üzenetek küldését és fogadását. Ha szeretné megtudni, hogyan használhatja a HTTP-t a Azure Relay Hibrid kapcsolatok használatával, tekintse meg a [Node. js http](relay-hybrid-connections-http-requests-node-get-started.md)-gyors útmutatóját.

Ebben a rövid útmutatóban a Node. js-t használta az ügyfél-és kiszolgálói alkalmazások létrehozásához. Ha szeretné megtudni, hogyan írhat ügyfél-és kiszolgálói alkalmazásokat a .NET-keretrendszerrel, tekintse meg a [.net WebSockets](relay-hybrid-connections-dotnet-get-started.md) rövid útmutatóját vagy a [.net http](relay-hybrid-connections-http-requests-dotnet-get-started.md)-gyors útmutatót.


