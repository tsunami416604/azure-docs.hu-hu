---
title: Azure-továbbító hibrid kapcsolatok – WebSockets a csomópontban
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75352676"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Hibrid kapcsolatok továbbítása WebSockets a Node.js webhelyen

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ebben a rövid útmutatóban node.js küldő és fogadó alkalmazásokat hoz létre, amelyek üzeneteket küldenek és fogadnak az Azure Relay hibrid kapcsolatok websocketjei használatával. Az Azure Relay általános megismeréséről az [Azure Relay](relay-what-is-it.md). 

Ebben a rövid útmutatóban az alábbi lépéseket kell tennie: 

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
Ebben a rövid útmutatóban node.js ügyfél- és kiszolgálóalkalmazásokat hozott létre, amelyek a WebSockets segítségével üzeneteket küldtek és fogadhatnak. Az Azure Relay hibrid kapcsolatok szolgáltatása támogatja a HTTP használatát az üzenetek küldéséhez és fogadásához. A HTTP azure-továbbító hibrid kapcsolatokkal való használatáról a [Node.js HTTP rövid útmutatócímű témakörben olvashat.](relay-hybrid-connections-http-requests-node-get-started.md)

Ebben a rövid útmutatóban a Node.js programot használta ügyfél- és kiszolgálóalkalmazások létrehozásához. Az ügyfél- és kiszolgálóalkalmazások .NET Framework rendszerrel történő írásáról a [.NET WebSockets rövid útmutatóban](relay-hybrid-connections-dotnet-get-started.md) vagy a [.NET HTTP gyorsútmutatóban](relay-hybrid-connections-http-requests-dotnet-get-started.md)olvashat.


