---
title: "Az Azure továbbítási API áttekintése |} Microsoft Docs"
description: "Elérhető Azure továbbítási API-k – áttekintés"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: fc6db8aba887b186961da9b12e7c5f32afa4355b
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="available-relay-apis"></a>Rendelkezésre álló továbbítási API-k

## <a name="runtime-apis"></a>Futásidejű API-k

Az alábbi táblázatban minden jelenleg elérhető továbbítási futásidejű ügyfelek.

A [további információt](#additional-information) szakasz mindegyik futásidejű kódtár állapotával kapcsolatos további információkat tartalmaz.

| Nyelvi/Platform | A szolgáltatás elérhető | Ügyfélcsomag | Tárház |
| --- | --- | --- | --- |
| .NET Standard | Hibrid kapcsolatok | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHubon](https://github.com/azure/azure-relay-dotnet) |
| .NET-keretrendszer | WCF-továbbító | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | – |
| Csomópont | Hibrid kapcsolatok | [`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket) | [GitHubon](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET

A .NET-ökoszisztéma több futtatókörnyezetek rendelkezik, ezért van több .NET-kódtárakra az Event Hubs számára. A .NET-szabvány könyvtár a .NET Core vagy a .NET-keretrendszer használatával, amíg a .NET Framework könyvtár csak a .NET-keretrendszer környezetben futtatható futtatható. További információ a .NET-keretrendszer: [keretrendszer-verziók](/dotnet/articles/standard/frameworks#framework-versions).

## <a name="next-steps"></a>További lépések

Azure-továbbítási kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.md)