---
title: Az Azure Relay API áttekintése | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az elérhető Azure-továbbító API-król (.NET Standard, .NET Framework, Node.js stb.)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513966"
---
# <a name="available-relay-apis"></a>Elérhető relé API-k

## <a name="runtime-apis"></a>Futásidejű API-k

Az alábbi táblázat az összes jelenleg elérhető továbbító futásidejű ügyfelet sorolja fel.

A [további információk](#additional-information) szakasz további információkat tartalmaz az egyes futásidejű tárak állapotáról.

| Nyelv/platform | Elérhető funkció | Ügyfélcsomag | Adattár |
| --- | --- | --- | --- |
| .NET Standard | Hibrid kapcsolatok | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [Github](https://github.com/azure/azure-relay-dotnet) |
| .NET-keretrendszer | WCF-továbbító | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| Csomópont | Hibrid kapcsolatok | [Websockets:`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets:`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-kérések:`hyco-https`](https://www.npmjs.com/package/hyco-https) | [Github](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET

A .NET ökoszisztémának több futásideje van, ezért a továbbítóhoz több .NET kódtár is található. A .NET Standard könyvtár a .NET Core vagy a .NET Framework használatával futtatható, míg a . A . [framework versions](/dotnet/articles/standard/frameworks)

A . `net.tcp` Ez a protokoll és tárak megmaradnak a meglévő alkalmazásokkal való visszamenőleges kompatibilitás érdekében.

A .NET Standard függvénytár a HTTP- és WebSockets-alapú hibrid kapcsolattovábbító nyílt protokolldefinícióján alapul. A könyvtár támogatja a websocketeken keresztüli adatfolyam-absztrakciót és egy egyszerű kérés-válasz API-kézmozdulatot a HTTP-kérelmek megválaszolására. A [webes API-minta](https://github.com/Azure/azure-relay-dotnet) bemutatja, hogyan integrálható a hibrid kapcsolatok ASP.NET Core webszolgáltatások.

#### <a name="nodejs"></a>Node.js

A fenti táblázatban felsorolt hibrid kapcsolatok modulok lecserélik vagy módosítják a meglévő Node.js modulokat olyan alternatív implementációkkal, amelyek a helyi hálózati verem helyett az Azure Relay szolgáltatást figyelik.

A `hyco-https` modul módosítja és részben felülbírálja az alapvető `http` `https`Node.js modulokat, és https-figyelő implementációt biztosít, amely kompatibilis számos meglévő Node.js modullal és alkalmazással, amelyek ezekre az alapvető modulokra támaszkodnak.

A `hyco-ws` `hyco-websocket` és a modulok `ws` `websocket` módosítják a népszerű és a modulok Node.js, alternatív figyelő implementációk, amelyek lehetővé teszik a modulok és alkalmazások támaszkodva mindkét modul a hibrid kapcsolatok továbbítása mögött.

Ezek a modulok az [azure-relay-node](https://github.com/Azure/azure-relay-node) GitHub-tárházban találhatók.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Relayről, látogasson el az alábbi hivatkozásokra:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.md)
