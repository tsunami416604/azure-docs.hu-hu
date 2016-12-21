---
title: "Ismerkedés a hibrid Relay-kapcsolatokkal | Microsoft Docs"
description: "Node konzolalkalmazás létrehozása hibrid kapcsolatokhoz"
services: service-bus-relay
documentationcenter: node
author: jtaubensee
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub,sethm
translationtype: Human Translation
ms.sourcegitcommit: e26b6005116c7aacdf42afea6ea39e100a9d8080
ms.openlocfilehash: 218082e4cedba2c11871d47bcfb7b92688d97f04


---
# <a name="get-started-with-relay-hybrid-connections"></a>Ismerkedés a hibrid Relay-kapcsolatokkal
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Az oktatóanyag célja
A hibrid kapcsolatokhoz egy ügyfélre és egy kiszolgáló-összetevőre is szükség van, így ebben az oktatóanyagban két konzolalkalmazást hozunk létre. A lépések a következők:

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása az Azure Portal használatával.
3. Kiszolgálói konzolalkalmazás írása üzenetfogadási céllal.
4. Ügyfél-konzolalkalmazás írása üzenetküldési céllal.

## <a name="prerequisites"></a>Előfeltételek
1. [NODE.js](https://nodejs.org/en/) (a példa a Node 7.0-t használja).
2. Azure-előfizetés.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Névtér létrehozása az Azure Portal használatával
Ha a Relay-névteret már létrehozta, ugorjon a [Hibrid kapcsolat létrehozása az Azure Portal használatával](#2-create-a-hybrid-connection-using-the-azure-portal) szakaszra.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Hibrid kapcsolat létrehozása az Azure Portal használatával
Ha már rendelkezik egy létrehozott hibrid kapcsolattal, ugorjon a [Kiszolgálói alkalmazás létrehozása](#3-create-a-server-application-listener) szakaszra.

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Kiszolgálói alkalmazás (figyelő) létrehozása
Node.js konzolalkalmazást írunk az üzenetek figyeléséhez és a Relay-től való fogadásához.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Ügyfélalkalmazás létrehozása (küldő)
Node.js konzolalkalmazást írunk az üzenetek Relay-be való küldéséhez.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Az alkalmazások futtatása
1. Futtassa a kiszolgálóalkalmazást.
2. Futtassa az ügyfélalkalmazást, és adjon meg szöveget.
3. Győződjön meg arról, hogy az alkalmazás konzolja kiírja a szöveget, amely az ügyfélalkalmazásban lett megadva.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Gratulálunk, végpontok közötti hibrid kapcsolatok alkalmazást hozott létre.

## <a name="next-steps"></a>Következő lépések:
* [Relay – gyakori kérdések](relay-faq.md)
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO3-->


