---
title: A Azure Relay API áttekintése | Microsoft Docs
description: Ez a cikk az elérhető Azure Relay API-k (.NET Standard, .NET-keretrendszer, Node. js stb.) áttekintését tartalmazza.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: ad7226b5c5badfddf9f436a1229a48f729485821
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983125"
---
# <a name="available-relay-apis"></a>Elérhető Relay API-k

## <a name="runtime-apis"></a>Futásidejű API-k

A következő táblázat felsorolja az összes jelenleg elérhető Relay Runtime-ügyfelet.

A [További információ](#additional-information) szakasz további információkat tartalmaz az egyes futtatókörnyezeti függvénytárak állapotáról.

| Nyelv/platform | Elérhető funkció | Ügyfélcsomag | Adattár |
| --- | --- | --- | --- |
| .NET Standard | Hibrid kapcsolatok | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET-keretrendszer | WCF-továbbító | [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| Csomópont | Hibrid kapcsolatok | [WebSockets protokoll`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets protokoll`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-kérelmek:`hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET

A .NET-ökoszisztéma több futtatókörnyezettel rendelkezik, ezért a továbbításhoz több .NET-kódtár is tartozik. A .NET Standard könyvtár a .NET Core vagy a .NET-keretrendszer használatával futtatható, míg a .NET-keretrendszer könyvtára csak .NET-keretrendszerbeli környezetben futtatható. További információ a .NET-Keretrendszerekről: [Framework Versions](/dotnet/articles/standard/frameworks).

A .NET-keretrendszer könyvtára csak a WCF programozási modellt támogatja, és a WCF `net.tcp` -átvitelen alapuló, védett bináris protokollra támaszkodik. Ez a protokoll és a könyvtár a meglévő alkalmazásokkal való visszamenőleges kompatibilitás érdekében tart fenn.

A .NET Standard könyvtár a HTTP-és WebSocket-alapú Hibrid kapcsolatok-továbbító nyílt protokoll-definícióján alapul. A függvénytár támogatja a WebSockets szolgáltatással való adatátvitelt, valamint egy egyszerű kérelem-válasz API-kézmozdulatot a HTTP-kérések megválaszolásához. A [webes API](https://github.com/Azure/azure-relay-dotnet) -minta bemutatja, hogyan integrálható a Hibrid kapcsolatok az ASP.net Core webszolgáltatásokhoz.

#### <a name="nodejs"></a>Node.js

A fenti táblázatban felsorolt Hibrid kapcsolatok modulok lecserélik vagy módosítják a meglévő Node. js-modulokat olyan alternatív implementációkkal, amelyek a helyi hálózati verem helyett a Azure Relay szolgáltatást figyelik.

A `hyco-https` modul módosítja és részben felülbírálja a Node. js-modulokat `http` , és `https`egy olyan HTTPS-figyelő megvalósítását biztosítja, amely kompatibilis a számos meglévő Node. js-modullal és-alkalmazással, amelyek ezen alapmodulokra támaszkodnak.

A `hyco-ws` és `hyco-websocket` a modulok a Node `ws` . `websocket` js népszerű és moduljait is módosítják, amelyek olyan alternatív figyelő-implementációkat biztosítanak, amelyek lehetővé teszik a modulok és alkalmazások számára, hogy a hibrid kapcsolatok Relay mögött működjenek.

Ezekről a modulokról az [Azure-Relay-Node](https://github.com/Azure/azure-relay-node) GitHub-tárházban találhat további információt.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Relayről, tekintse meg a következő hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.md)
