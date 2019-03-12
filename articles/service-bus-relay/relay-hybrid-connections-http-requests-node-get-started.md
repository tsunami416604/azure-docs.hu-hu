---
title: Ismerkedés a hibrid Azure Relay-kapcsolatok HTTP-kéréseivel a Node-ban | Microsoft Docs
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
ms.openlocfilehash: e54a096bd27efddaa9eafb8619e787178550a6e0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759255"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Ismerkedés a hibrid Relay-kapcsolatok HTTP-kéréseivel a Node-ban

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ebben a rövid útmutatóban létrehozhat Node.js küldői és fogadói alkalmazásokat, amelyek üzenetek küldése és fogadása a HTTP protokoll használatával. Az alkalmazások az Azure Relay hibrid kapcsolatok szolgáltatását használja. Az általános Azure Relay kapcsolatos további információkért lásd: [Azure Relay](relay-what-is-it.md). 

Ebben a rövid útmutatóban tegye a következőket:

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása ezen a névtéren az Azure Portal használatával.
3. Kiszolgálói (figyelő) konzolalkalmazás írása üzenetfogadási céllal.
4. Ügyfél-konzolalkalmazás (küldő) írása üzenetküldési céllal.
5. Alkalmazások futtatása.

## <a name="prerequisites"></a>Előfeltételek
- [Node.js](https://nodejs.org/en/).
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
Ebben a rövid útmutatóban létrehozott Node.js ügyfél és kiszolgáló-alkalmazások, amelyek HTTP üzenetek küldése és fogadása. Az Azure Relay hibrid kapcsolatok szolgáltatását is támogatja a websockets protokoll használatával üzeneteket küldjön és fogadjon. Ismerje meg, hogyan használja a websockets protokollt az Azure Relay hibrid kapcsolatai, tekintse meg a [WebSockets rövid](relay-hybrid-connections-node-get-started.md).

Ebben a rövid útmutatóban a Node.js ügyféloldali és kiszolgálói alkalmazások létrehozásához használt. Ismerje meg, hogyan írhat .NET-keretrendszer használatával ügyfél- és kiszolgálóalkalmazások, tekintse meg a [.NET Websocketek rövid](relay-hybrid-connections-dotnet-get-started.md) vagy a [.NET HTTP rövid](relay-hybrid-connections-http-requests-dotnet-get-started.md).
