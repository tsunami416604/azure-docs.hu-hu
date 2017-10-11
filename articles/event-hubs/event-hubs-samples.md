---
title: "Azure Event Hubs-minták |} Microsoft Docs"
description: "Azure Event Hubs-minták"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: ae9fbd97a1747d8f14c561f247a0973bb11fd039
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="event-hubs-samples"></a>Event Hubs – minták 

Az Azure Event Hubs minták készletét bemutatja a legfontosabb jellemzők [Azure Event Hubs](/azure/event-hubs/). Ez a cikk kategorizálja, és az egyes hivatkozásokkal elérhető mintákat ismerteti.

Az Event Hubs minták írásának időpontjában számos különböző helyeken találhatók:

- [MSDN fejlesztői mintakódok](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

A .NET-keretrendszer különböző verzióival kapcsolatos további információkért lásd: [keretrendszerek és célok](/dotnet/articles/standard/frameworks).

Több minta lesz hozzáadva adott idő alatt, ezért vissza ide gyakran frissítések keresése.

## <a name="net-standard"></a>.NET-szabvány

A következő minták bemutatják, hogyan lehet küldeni és fogadni az eseményeket a [Event Hubs-ügyfél](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) a a [.NET szabványos függvénytár](/dotnet/articles/standard/library).

### <a name="send-events"></a>Események küldése 

A [Ismerkedés küldése](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) minta bemutatja, hogyan írása a .NET Core console alkalmazást, amely események az eseményközpontba.

### <a name="receive-events"></a>Események fogadása 

A [fogadását az Event Processor Host az első lépések](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) minta a .NET Core-Konzolalkalmazás, amely üzeneteket fogad egy eseményközpontot, az Event Processor Host használatával.

## <a name="net-framework"></a>.NET-keretrendszer   

Azure Event hubs célcsoport-kezelési különböző más funkciókat fogunk bemutatni, ezeket a mintákat a [.NET Framework könyvtár](/dotnet/framework/index).
 
### <a name="notify-users-of-events-received"></a>Értesítse a felhasználókat a fogadott események

A [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) minta értesíti a felhasználókat az érzékelők vagy más rendszerek érkező adatokat.

### <a name="get-started-with-event-hubs"></a>Bevezetés az Event Hubs használatába 

A [Event Hubs használatának első lépéseit](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) például létrehoz egy eseményközpontot, események küldése az eseményközpont, és segítségével események felhasználásához az Event Hubs alapvető képességeit mutatja be a [Event Processor Host](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) .

### <a name="scale-out-event-processing"></a>Horizontális felskálázás esemény feldolgozása 

A [esemény feldolgozása kibővítési](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) minta bemutatja, hogyan használható a [Event Processor Host](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) Event Hubs adatfolyam fogyasztás okozott terhelés elosztásához. Azt illusztrálja, hogyan megvalósításához a **EventProcessor** és **EventProcessorFactory** az eseménystream kezelendő objektumokat. 

###  <a name="pull-data-from-sql-into-an-event-hub"></a>SQL eseményközpontnak olvasnak be adatokat

A [húzza SQL adatok](https://github.com/Azure-Samples/event-hubs-dotnet-import-from-sql) minta bemutatja, hogyan olvasnak be adatokat az SQL-táblából, és hogy egy eseményközpontot, az alárendelt analitikus alkalmazásokban bemenetként használandó.

### <a name="pull-web-data-into-an-event-hub"></a>Lekéréses webhely adatok eseményközpontba 

A [adatokat importálhat a webes](https://github.com/Azure-Samples/event-hubs-dotnet-importfromweb) minta bemutatja, hogyan olvasnak be adatokat (például a szállítására részleg forgalom információk hírcsatorna) nyilvános hírcsatornák a, és hogy egy eseményközpontba.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a .NET-keretrendszer-verziók érhetők el a következőket:

- [Keretrendszerek és célok](/dotnet/articles/standard/frameworks)
- [.NET-keretrendszer 4.6 és 4.5](/dotnet/framework/index)

Ön többet is megtudhat az Event Hubs a következő cikkekben:

- [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
- [Eseményközpont létrehozása](event-hubs-create.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)