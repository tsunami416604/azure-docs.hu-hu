---
title: Az Azure Event Hubs API – áttekintés |} A Microsoft Docs
description: Elérhető az Azure Event Hubs API-k áttekintése
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 2523db3d817b4281b6435374ef0cca4e1d2da695
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005857"
---
# <a name="available-event-hubs-apis"></a>Elérhető Event Hubs API-k

Ez a cikk ismerteti az elérhető API-ügyfelek az Event Hubs-erőforrások kezeléséhez használható készletét.

## <a name="runtime-apis"></a>Futásidejű API-k

Az alábbi szakasz az összes jelenleg elérhető az Azure Event Hubs futásidejű ügyfelek. Ezek a kódtárak is néhány korlátozott felügyeleti funkciók, amíg vannak is [adott könyvtárakat](#management-apis) dedikált felügyeleti műveletekhez. A core ezek a kódtárak célja küld és fogad üzeneteket egy eseményközpontba.

Minden egyes futásidejű kódtár az aktuális állapotával kapcsolatos további információkért lásd: [további információkat](#additional-information).

| Nyelv és Platform | Ügyfélcsomag | EventProcessorHost csomag | Tárház |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHubon](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET-keretrendszer | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | – |
| Java | [Maven 3](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven 3](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHubon](https://github.com/Azure/azure-event-hubs-java) |
| Csomópont | [NPM](https://www.npmjs.com/package/azure-event-hubs) | – | [GitHubon](https://github.com/Azure/azure-event-hubs-node) |
| C | – | – | [GitHubon](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET

A .NET-ökoszisztéma több modulok rendelkezik, így több, az Event Hubs .NET-kódtárakra. A .NET Standard kódtár is futtatható a .NET Core vagy a .NET-keretrendszer használatával, míg a .NET-keretrendszer könyvtár csak a .NET-keretrendszer környezetben futtatható. .NET-keretrendszer-verziókkal kapcsolatos további információkért lásd: [keretrendszer-verziókat](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

#### <a name="node"></a>Csomópont

A [Node.js kódtár](https://github.com/Azure/azure-event-hubs-node) jelenleg előzetes verzióban, és a egy ügyféloldali projektként a Microsoft alkalmazottai és külső közreműködők által karbantartott. Az összes közreműködések többek között a forráskód üdvözlő és felül kell vizsgálni.

## <a name="management-apis"></a>Felügyeleti API-k

A következő táblázat felsorolja az összes jelenleg elérhető felügyeleti-specifikus szalagtár. Ezek a kódtárak egyike futásidejű műveletek tartalmaz, és az azzal a céllal, az Event Hubs entitáskezelésről.

| Nyelv és Platform | Felügyeleti csomag | Tárház |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)