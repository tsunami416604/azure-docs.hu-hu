---
title: A Azure Relay API áttekintése | Microsoft Docs
description: Ez a cikk az elérhető Azure Relay API-k (.NET Standard, .NET-keretrendszer, Node. js stb.) áttekintését tartalmazza.
services: event-hubs
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
ms.openlocfilehash: 41d9e2026c19c959dc6fe2546b0ef699571ec7cd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513966"
---
# <a name="available-relay-apis"></a>Elérhető Relay API-k

## <a name="runtime-apis"></a>Futásidejű API-k

A következő táblázat felsorolja az összes jelenleg elérhető Relay Runtime-ügyfelet.

A [További információ](#additional-information) szakasz további információkat tartalmaz az egyes futtatókörnyezeti függvénytárak állapotáról.

| Nyelv/platform | Elérhető funkció | Ügyfélcsomag | Adattár |
| --- | --- | --- | --- |
| .NET Standard | Hibrid kapcsolatok | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET-keretrendszer | WCF Relay | [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | – |
| Csomópont | Hibrid kapcsolatok | [WebSockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-kérelmek: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>További információk

#### <a name="net"></a>.NET

A .NET-ökoszisztéma több futtatókörnyezettel rendelkezik, ezért a továbbításhoz több .NET-kódtár is tartozik. A .NET Standard könyvtár a .NET Core vagy a .NET-keretrendszer használatával futtatható, míg a .NET-keretrendszer könyvtára csak .NET-keretrendszerbeli környezetben futtatható. További információ a .NET-Keretrendszerekről: [Framework Versions](/dotnet/articles/standard/frameworks).

A .NET-keretrendszer könyvtára csak a WCF programozási modellt támogatja, és a WCF `net.tcp`-átvitelen alapuló, védett bináris protokollra támaszkodik. Ez a protokoll és a könyvtár a meglévő alkalmazásokkal való visszamenőleges kompatibilitás érdekében tart fenn.

A .NET Standard könyvtár a HTTP-és WebSocket-alapú Hibrid kapcsolatok-továbbító nyílt protokoll-definícióján alapul. A függvénytár támogatja a WebSockets szolgáltatással való adatátvitelt, valamint egy egyszerű kérelem-válasz API-kézmozdulatot a HTTP-kérések megválaszolásához. A [webes API](https://github.com/Azure/azure-relay-dotnet) -minta bemutatja, hogyan integrálható a Hibrid kapcsolatok az ASP.net Core webszolgáltatásokhoz.

#### <a name="nodejs"></a>Node.js

A fenti táblázatban felsorolt Hibrid kapcsolatok modulok lecserélik vagy módosítják a meglévő Node. js-modulokat olyan alternatív implementációkkal, amelyek a helyi hálózati verem helyett a Azure Relay szolgáltatást figyelik.

A `hyco-https` modul módosítja és részben felülbírálja az alapszintű Node. js-modulokat `http` és `https`, így biztosítva egy olyan HTTPS-figyelő megvalósítását, amely kompatibilis a számos meglévő Node. js modullal és alkalmazással, amelyek ezen alapmodulokra támaszkodnak.

A `hyco-ws` és `hyco-websocket` modulok a Node. js-hez készült népszerű `ws` és `websocket` modulokat módosítják, amelyek olyan alternatív figyelő-implementációkat biztosítanak, amelyek lehetővé teszik a modulok és alkalmazások számára, hogy az Hibrid kapcsolatok továbbító mögött működjenek.

Ezekről a modulokról az [Azure-Relay-Node](https://github.com/Azure/azure-relay-node) GitHub-tárházban találhat további információt.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Relayről, tekintse meg a következő hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Továbbító – gyakori kérdések](relay-faq.md)
