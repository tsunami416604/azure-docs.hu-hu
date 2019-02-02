---
title: Az Azure Relay API – áttekintés |} A Microsoft Docs
description: Elérhető az Azure Relay API-k áttekintése
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
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 05d7ac56d6c1c48125eb458d0eee852ba396b300
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663094"
---
# <a name="available-relay-apis"></a>Rendelkezésre álló Relay API-k

## <a name="runtime-apis"></a>Futásidejű API-k

Az alábbi táblázat a jelenleg elérhető Relay futásidejű ügyfelek.

A [további információkat](#additional-information) szakasz egyes futtatókörnyezeti kódtárának állapotával kapcsolatos további információkat tartalmaz.

| Nyelv és Platform | Elérhető a szolgáltatásban | Ügyfélcsomag | Adattár |
| --- | --- | --- | --- |
| .NET Standard | Hibrid kapcsolatok | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHubon](https://github.com/azure/azure-relay-dotnet) |
| .NET-keretrendszer | WCF-továbbító | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | – |
| Csomópont | Hibrid kapcsolatok | [A websockets protokoll: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[A websockets protokoll: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-kéréseket: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHubon](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET

A .NET-ökoszisztéma több modulok rendelkezik, így nincsenek a Relay több .NET-kódtárakra. A .NET Standard kódtár is futtatható a .NET Core vagy a .NET-keretrendszer használatával, míg a .NET-keretrendszer könyvtár csak a .NET-keretrendszer környezetben futtatható. További információ a .NET-keretrendszert: [keretrendszer-verziókat](/dotnet/articles/standard/frameworks).

A .NET-keretrendszer könyvtár csak a WCF programozási modellt támogatja, és a szellemi tulajdont képező bináris protokoll alapján a WCF támaszkodik `net.tcp` átviteli. A protokoll és a könyvtár változatlan marad visszamenőleges kompatibilitási együtt a meglévő alkalmazásokkal.

A .NET Standard kódtár a hibrid kapcsolatok Relay, amely épül, amely a HTTP és a WebSockets protokoll nevű nyílt definíciója alapján történik. A kódtár támogatja egy stream absztrakciós keresztül a websockets protokoll és a egy egyszerű kérés-válasz API kézmozdulat fogadó HTTP-kérelmekre. A [webes API-t](https://github.com/Azure/azure-relay-dotnet) minta bemutatja, hogyan integrálható a hibrid kapcsolatok az ASP.NET Core web services.

#### <a name="nodejs"></a>Node.js

A hibrid kapcsolatok modulok, a fenti táblázatban szereplő cserélje le, vagy módosítani kell a meglévő Node.js modulok az alternatív megvalósításokhoz, amelyen az Azure Relay szolgáltatás helyett a helyi hálózati verem figyelnie.

A `hyco-https` modul módosítja, és részben felülbírálja a core Node.js modulok `http` és `https`, egy HTTPS-figyelő végrehajtása, amely kompatibilis a számos meglévő Node.js modulok és a használó alkalmazások ezek alapvető biztosítása modulok.

A `hyco-ws` és `hyco-websocket` modulok módosítani kell a népszerű `ws` és `websocket` modulok használata a Node.js, alternatív figyelő megvalósításokhoz, amelyek lehetővé teszik a modulok és a hibrid mögött dolgoznak a vagy a modul a függő alkalmazások kezeléséről Továbbítási kapcsolatok.

Ezek a modulok részleteit található a [azure relay-csomópontból álló](https://github.com/Azure/azure-relay-node) GitHub-adattárban.

## <a name="next-steps"></a>További lépések

Az Azure Relay kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.md)
