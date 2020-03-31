---
title: Az Azure Event Hubs API áttekintése | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure Event Hubs szolgáltatás használatához elérhető API-król (futásidejű és felügyeleti).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162225"
---
# <a name="available-event-hubs-apis"></a>Elérhető eseményközpontok API-jai

Ez a cikk az Event Hubs-erőforrások kezeléséhez használható elérhető API-ügyfelek készletét ismerteti.

## <a name="runtime-apis"></a>Futásidejű API-k

A következő szakasz ismerteti az összes jelenleg elérhető Azure Event Hubs futásidejű ügyfelek. Bár ezek közül a könyvtárak közül néhány korlátozott felügyeleti funkciókat is tartalmaz, vannak [speciális kódtárak](#management-apis) is, amelyek a felügyeleti műveletekhez vannak elfoglalva. Ezeknek a könyvtáraknak a központi célja, hogy üzeneteket küldjenek és fogadjanak egy eseményközpontból.

Az egyes futásidejű tárak aktuális állapotáról további információt a további [tudnivalókról](#additional-information)talál.

| Nyelv/platform | Ügyfélcsomag | EventProcessorHost csomag | Adattár |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [Github](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET-keretrendszer | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/A |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [Github](https://github.com/Azure/azure-event-hubs-java) |
| Csomópont | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/A | [Github](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C# | N/A | N/A | [Github](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET

A .NET-ökoszisztéma több futásidővel rendelkezik, így az Event Hubs több .NET-kódtára is van. A .NET Standard könyvtár a .NET Core vagy a .NET Framework használatával futtatható, míg a . A . [framework versions](https://docs.microsoft.com/dotnet/articles/standard/frameworks)

#### <a name="node"></a>Csomópont

A [JavaScript-kódtár](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) jelenleg előzetes verzióban érhető el, és a Microsoft alkalmazottai és külső közreműködők által mellékprojektként karbantartják. Minden hozzájárulást, beleértve a forráskódot is, szívesen fogadjuk, és felül fogjuk vizsgálni.

## <a name="management-apis"></a>Felügyeleti API-k

Az alábbi táblázat az összes jelenleg elérhető felügyeleti-specifikus tárat sorolja fel. Ezek a kódtárak egyike sem tartalmaz futásidejű műveleteket, és kizárólag az Event Hubs entitások kezelésére szolgál.

| Nyelv/platform | Kezelési csomag | Adattár |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [Github](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Eseményközpontok – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
