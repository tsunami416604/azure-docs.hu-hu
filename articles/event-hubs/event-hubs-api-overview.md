---
title: "Azure Event Hubs API – áttekintés |} Microsoft Docs"
description: "Elérhető Azure Event Hubs API-kat áttekintése"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: abd44fd0c9cbfab2365b1552e3cd90e84a5348d7
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="available-event-hubs-apis"></a>Rendelkezésre álló Event Hubs API-k

Ez a cikk ismerteti az elérhető API-ügyfelek használható Event Hubs-erőforrások kezelése.

## <a name="runtime-apis"></a>Futásidejű API-k

Minden jelenleg elérhető Azure Event Hubs futásidejű ügyfelek leírása a következő: Ezek a könyvtárak is közé korlátozott felügyeleti funkciók, amíg vannak is [adott könyvtárakat](#management-apis) dedikált felügyeleti műveletekhez. A core ezeket a könyvtárakat célja üzeneteket küldjön és fogadjon az eseményközpontban lévő.

Lásd: [további információt](#additional-information) kapcsolatos további információkért az egyes futásidejű kódtár aktuális állapotát.

| Nyelvi/Platform | Ügyfélcsomag | EventProcessorHost csomag | Tárház |
| --- | --- | --- | --- |
| .NET-szabvány | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET-keretrendszer | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/A |
| Java | [Maven 3](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven 3](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Csomópont | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C# | N/A | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET
A .NET-ökoszisztéma több futtatókörnyezetek rendelkezik, ezért van több .NET-kódtárakra az Event Hubs számára. A .NET-szabvány könyvtár a .NET Core vagy a .NET-keretrendszer használatával, amíg a .NET Framework könyvtár csak a .NET-keretrendszer környezetben futtatható futtatható. További információ a .NET-keretrendszer: [keretrendszer-verziók](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

#### <a name="node"></a>Csomópont

A Node.js kódtár jelenleg előzetes verzióban érhetők, és a Microsoft alkalmazottai és külső közreműködő szerepkörrel rendelkező személyek által kezelt oldal projektként. Beleértve az adatforrás összes hozzájárulást üdvözlő és felül kell vizsgálni.

## <a name="management-apis"></a>API-val

Minden jelenleg elérhető felügyeleti adott könyvtárakat listáját a következő: Nincs ezeket a könyvtárakat futásidejű műveletek tartalmazhat, és az azzal a céllal, az Event Hubs entitások kezelése.

| Nyelvi/Platform | Felügyeleti csomag | Tárház |
| --- | --- | --- | --- |
| .NET-szabvány | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)