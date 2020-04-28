---
title: Azure Relay Hibrid kapcsolatok – HTTP-kérelmek a csomópontban
description: Node.js-konzolalkalmazást hozhat létre a hibrid Azure Relay-kapcsolatok HTTP-kéréseihez a Node-ban.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: clemensv
ms.openlocfilehash: d71386b86bf7133bb73ddce2e65c3b88743009ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75462025"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Ismerkedés a hibrid Relay-kapcsolatok HTTP-kéréseivel a Node-ban

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ebben a rövid útmutatóban a Node. js-küldő és a fogadó alkalmazásokat hozza létre, amelyek üzeneteket küldenek és fogadnak a HTTP protokoll használatával. Az alkalmazások a Azure Relay Hibrid kapcsolatok funkcióját használják. Az általános Azure Relayről a [Azure Relay](relay-what-is-it.md)című témakörben olvashat bővebben. 

Ebben a rövid útmutatóban a következő lépéseket hajtja végre:

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása ezen a névtéren az Azure Portal használatával.
3. Kiszolgálói (figyelő) konzolalkalmazás írása üzenetfogadási céllal.
4. Ügyfél-konzolalkalmazás (küldő) írása üzenetküldési céllal.
5. Alkalmazások futtatása.

## <a name="prerequisites"></a>Előfeltételek
- [Node. js](https://nodejs.org/en/)-fájl.
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

## <a name="create-a-namespace-using-the-azure-portal"></a>Névtér létrehozása az Azure Portal használatával
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Hibrid kapcsolat létrehozása az Azure Portal használatával
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Kiszolgálói alkalmazás (figyelő) létrehozása
Írjon egy Node.js-konzolalkalmazást az üzenetek figyeléséhez és a Relaytől való fogadásához.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Ügyfélalkalmazás létrehozása (küldő)

Ha üzenetet kíván küldeni a Relay számára, használhat bármilyen HTTP-klienst, vagy írhat egy Node.js-konzolalkalmazást.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>Az alkalmazások futtatása

1. Futtassa a kiszolgálóalkalmazást: egy Node.js-parancssorba írja be a következőt: `node listener.js`.
2. Futtassa az ügyfélalkalmazást: egy Node.js parancssorba írja be a `node sender.js` parancsot, majd írjon be szöveget.
3. Győződjön meg arról, hogy az alkalmazás konzolja kiírja a szöveget, amely az ügyfélalkalmazásban lett megadva.

Gratulálunk, végpontok közötti hibrid kapcsolatok alkalmazást hozott létre a Node.js használatával.

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban létrehozta a Node. js-ügyfelet és a HTTP protokollt használó kiszolgálói alkalmazásokat az üzenetek küldéséhez és fogadásához. A Azure Relay Hibrid kapcsolatok szolgáltatása szintén támogatja a websocketek használatát az üzenetek küldéséhez és fogadásához. Ha szeretné megismerni, hogyan használhatók a websocketek Azure Relay Hibrid kapcsolatoksal, tekintse meg a [WebSockets](relay-hybrid-connections-node-get-started.md)rövid útmutatót.

Ebben a rövid útmutatóban a Node. js-t használta az ügyfél-és kiszolgálói alkalmazások létrehozásához. Ha szeretné megtudni, hogyan írhat ügyfél-és kiszolgálói alkalmazásokat a .NET-keretrendszerrel, tekintse meg a [.net WebSockets](relay-hybrid-connections-dotnet-get-started.md) rövid útmutatóját vagy a [.net http](relay-hybrid-connections-http-requests-dotnet-get-started.md)-gyors útmutatót.
