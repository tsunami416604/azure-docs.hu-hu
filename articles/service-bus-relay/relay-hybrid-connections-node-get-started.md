---
title: "Ismerkedés az Azure Relay Hibrid-kapcsolatokkal a Node-ban | Microsoft Docs"
description: "Node konzolalkalmazás létrehozása hibrid kapcsolatokhoz"
services: service-bus-relay
documentationcenter: node
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 05/22/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: d8f3f6fbe256b34b812369dc1f7492ed4f15d3d3
ms.contentlocale: hu-hu
ms.lasthandoff: 06/05/2017


---
<a id="get-started-with-relay-hybrid-connections" class="xliff"></a>

# Ismerkedés a hibrid Relay-kapcsolatokkal

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

<a id="what-will-be-accomplished" class="xliff"></a>

## Az oktatóanyag célja

A hibrid kapcsolatokhoz egy ügyfélre és egy kiszolgáló-összetevőre is szükség van, így ebben az oktatóanyagban két konzolalkalmazást hozunk létre. A lépések a következők:

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása az Azure Portal használatával.
3. Kiszolgálói konzolalkalmazás írása üzenetfogadási céllal.
4. Ügyfél-konzolalkalmazás írása üzenetküldési céllal.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

1. [NODE.js](https://nodejs.org/en/) (a példa a Node 7.0-t használja).
2. Azure-előfizetés.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

<a id="1-create-a-namespace-using-the-azure-portal" class="xliff"></a>

## 1. Névtér létrehozása az Azure Portal használatával

Ha a Relay-névteret már létrehozta, ugorjon a [Hibrid kapcsolat létrehozása az Azure Portal használatával](#2-create-a-hybrid-connection-using-the-azure-portal) szakaszra.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

<a id="2-create-a-hybrid-connection-using-the-azure-portal" class="xliff"></a>

## 2. Hibrid kapcsolat létrehozása az Azure Portal használatával

Ha már rendelkezik egy létrehozott hibrid kapcsolattal, ugorjon a [Kiszolgálói alkalmazás létrehozása](#3-create-a-server-application-listener) szakaszra.

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

<a id="3-create-a-server-application-listener" class="xliff"></a>

## 3. Kiszolgálói alkalmazás (figyelő) létrehozása

Node.js konzolalkalmazást írunk az üzenetek figyeléséhez és a Relay-től való fogadásához.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

<a id="4-create-a-client-application-sender" class="xliff"></a>

## 4. Ügyfélalkalmazás létrehozása (küldő)

Node.js konzolalkalmazást írunk az üzenetek Relay-be való küldéséhez.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

<a id="5-run-the-applications" class="xliff"></a>

## 5. Az alkalmazások futtatása

1. Futtassa a kiszolgálóalkalmazást.
2. Futtassa az ügyfélalkalmazást, és adjon meg szöveget.
3. Győződjön meg arról, hogy az alkalmazás konzolja kiírja a szöveget, amely az ügyfélalkalmazásban lett megadva.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Gratulálunk, végpontok közötti hibrid kapcsolati alkalmazást hozott létre!

<a id="next-steps" class="xliff"></a>

## Következő lépések:

* [Relay – gyakori kérdések](relay-faq.md)
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)


