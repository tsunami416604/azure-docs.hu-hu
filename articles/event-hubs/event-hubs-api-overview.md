---
title: Az Azure Event Hubs API áttekintése | Microsoft Docs
description: Ez a cikk áttekintést nyújt a rendelkezésre álló API-król (futtatókörnyezet és felügyelet) az Azure Event Hubs szolgáltatás használatához.
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
ms.openlocfilehash: 08e10996f633d35ffbf946b61937a0e9a76f7227
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162225"
---
# <a name="available-event-hubs-apis"></a>Elérhető Event Hubs API-k

Ez a cikk az Event Hubs-erőforrások kezeléséhez használható elérhető API-ügyfelek készletét ismerteti.

## <a name="runtime-apis"></a>Futásidejű API-k

A következő szakasz az összes jelenleg elérhető Azure Event Hubs Runtime-ügyfelet ismerteti. Habár a kódtárak némelyike korlátozott felügyeleti funkciókat is tartalmaz, a felügyeleti műveletekhez külön [könyvtárak](#management-apis) is tartoznak. Ezen könyvtárak fő témája az, hogy üzeneteket küldjön és fogadjon az Event hub-ból.

További információ az egyes futásidejű könyvtárak aktuális állapotáról: [További információk](#additional-information).

| Nyelv/platform | Ügyfél-csomag | EventProcessorHost-csomag | Tárház |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET-keretrendszer | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/A |
| Java | [Maven 3](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven 3](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Csomópont | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/A | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | N/A | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET

A .NET-ökoszisztéma több futtatókörnyezettel rendelkezik, így több .NET-kódtár is Event Hubs. A .NET Standard könyvtár a .NET Core vagy a .NET-keretrendszer használatával futtatható, míg a .NET-keretrendszer könyvtára csak .NET-keretrendszerbeli környezetben futtatható. A .NET-keretrendszer verzióival kapcsolatos további információkért lásd: [keretrendszer verziói](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Csomópont

A [JavaScript-függvénytár](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) jelenleg előzetes verzióban érhető el, és a Microsoft alkalmazottai és külső közreműködői is kezelik. Az összes hozzájárulás, beleértve a forráskódot is, üdvözlendő, és felül lesz bírálva.

## <a name="management-apis"></a>Felügyeleti API-k

A következő táblázat felsorolja az aktuálisan elérhető felügyeleti könyvtárakat. Ezen függvénytárak egyike sem tartalmaz futtatókörnyezeti műveleteket, és kizárólag Event Hubs entitások kezelésének céljára szolgál.

| Nyelv/platform | Felügyeleti csomag | Tárház |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
