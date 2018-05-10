---
title: Az Azure továbbítási API áttekintése |} Microsoft Docs
description: Elérhető Azure továbbítási API-k – áttekintés
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: sethm
ms.openlocfilehash: 00496ca6c0138a840322c053d7d20944df228e9f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="available-relay-apis"></a>Rendelkezésre álló továbbítási API-k

## <a name="runtime-apis"></a>Futásidejű API-k

Az alábbi táblázatban minden jelenleg elérhető továbbítási futásidejű ügyfelek.

A [további információt](#additional-information) szakasz mindegyik futásidejű kódtár állapotával kapcsolatos további információkat tartalmaz.

| Nyelvi/Platform | A szolgáltatás elérhető | Ügyfélcsomag | Tárház |
| --- | --- | --- | --- |
| .NET Standard | Hibrid kapcsolatok | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHubon](https://github.com/azure/azure-relay-dotnet) |
| .NET-keretrendszer | WCF-továbbító | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | – |
| Csomópont | Hibrid kapcsolatok | [A websocket elemeket: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[A websocket elemeket: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-kérelmek: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHubon](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET

A .NET-ökoszisztéma több futtatókörnyezetek rendelkezik, ezért van a továbbítási több .NET-kódtárakra. A .NET-szabvány könyvtár a .NET Core vagy a .NET-keretrendszer használatával, amíg a .NET Framework könyvtár csak a .NET-keretrendszer környezetben futtatható futtatható. További információ a .NET-keretrendszer: [keretrendszer-verziók](/dotnet/articles/standard/frameworks#framework-versions).

A .NET Framework könyvtár csak a WCF programozási modellt támogatja, és a WCF alapján olyan egyéni bináris protokollt támaszkodik `net.tcp` átviteli. Ezzel a protokollal és könyvtár változatlan marad visszamenőleges kompatibilitás a már meglévő alkalmazásokkal.

A .NET-szabvány könyvtárban a hibrid kapcsolatok Relay a HTTP és a websocket elemek épülő protokollhoz definíciója alapul. A könyvtár a HTTP-kéréseket fogadó egy adatfolyam absztrakciós keresztül websocket elemek és egy egyszerű kérés-válasz API kézmozdulat támogatja. A [Web API](https://github.com/Azure/azure-relay-dotnet) minta bemutatja, hogyan hibrid kapcsolatok integrálása az ASP.NET Core webszolgáltatások.

#### <a name="nodejs"></a>Node.js

A hibrid kapcsolatok modulok, a fenti táblázatban cserélje le, vagy az Azure-továbbítási szolgáltatás helyett a helyi hálózati vermet figyelő alternatív megvalósítások rendelkező meglévő Node.js modulok módosítására.

A `hyco-https` modul módosítja, és részben felülbírálja a core Node.js modulok `http` és `https`, amely a HTTPS figyelő megvalósításának számos meglévő Node.js modulok és alkalmazások kompatibilis ezek alapvető modulok.

A `hyco-ws` és `hyco-websocket` modulok módosítani kell a népszerű `ws` és `websocket` modulok a Node.js, a másik figyelő megvalósításokhoz, amelyek lehetővé teszik a modulok és alkalmazások támaszkodva vagy modul működéséhez a hibrid mögött biztosítása Kapcsolatok továbbító.

Ilyen modulokhoz kapcsolatos részletek megtalálhatók a [azure-továbbítási-csomópont](https://github.com/Azure/azure-relay-node) GitHub-tárházban.

## <a name="next-steps"></a>További lépések

Azure-továbbítási kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.md)