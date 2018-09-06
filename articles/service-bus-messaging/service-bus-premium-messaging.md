---
title: Az Azure Service Bus prémium és standard szintű üzenetkezelés tarifacsomagjainak áttekintése | Microsoft Docs
description: A Service Bus prémium és standard üzenetkezelési szintjei
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: ''
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/30/2018
ms.author: spelluru
ms.openlocfilehash: 8e53cc5487a64fbd0c3a9ca26fc405756f09ce71
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698733"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>A Service Bus prémium és standard szintű üzenetkezelés szintjei

A Service Bus-üzenetkezelés, amely a várólistákhoz és témakörökhöz hasonló entitásokat is tartalmaz, a vállalati üzenetkezelési képességeket ötvözi a gazdag közzétételi/előfizetési szemantikákkal a felhőbeli skálázással. A Service Bus-üzenetkezelés számos kifinomult felhőalapú megoldás kommunikációs vázaként szolgál.

A Service Bus-üzenetkezelés *prémium* szintje a méretezéssel, teljesítménnyel és a legfontosabb alkalmazásokkal kapcsolatos általános ügyfélkérelmeket kezeli. A prémium szintű csomag éles forgatókönyvekhez ajánlott. Noha a szolgáltatáskészletek csaknem azonosak, a Service Bus-üzenetkezelés két szintje különböző felhasználói esetekhez lett tervezve.

A következő táblázat néhány fontos eltérést emel ki.

| Prémium | Standard |
| --- | --- |
| Magas teljesítmény |Változó teljesítmény |
| Kiszámítható teljesítmény |Változó késés |
| Rögzített díjszabás |Használatalapú változó díjszabás |
| Lehetőség a munkaterhelés vertikális fel- és leskálázására |N/A |
| Legfeljebb 1 MB méretű üzenet |Legfeljebb 256 KB méretű üzenet |

A **Service Bus prémium szintű üzenetkezelés** erőforrás-elkülönítést biztosít a CPU és a memória szintjén, így az ügyfél minden számítási feladata elkülönítve fut. Ennek az erőforrás-tárolónak a neve *üzenetkezelési egység*. Legalább egy üzenetkezelési egység van lefoglalva minden prémium névtérhez. Az egyes Service Bus prémium névterekhez 1, 2 vagy 4 üzenetkezelési egység vásárolható. Egyetlen számítási feladat vagy entitás több üzenetkezelési egységre is kiterjedhet, az üzenetkezelési egységek száma pedig tetszés szerint módosítható, bár a számlázás 24 órás vagy napi díjszabás szerint történik. Az eredmény a Service Bus-alapú megoldás kiszámítható és ismételhető teljesítménye.

Nem csak kiszámíthatóbb és nagyobb rendelkezésre állású a teljesítmény, de gyorsabb is. A Service Bus prémium szintű üzenetkezelés az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatásban bemutatott tárolási motorra épít. Prémium szintű üzenetkezelés esetén a csúcsteljesítmény jóval gyorsabb, mint a standard szinten.

## <a name="premium-messaging-technical-differences"></a>Prémium szintű üzenetkezelés – műszaki eltérések

Az alábbi szakaszokban a prémium és a standard szintű üzenetkezelési szintek közötti egyes különbségek olvashatók.

### <a name="partitioned-queues-and-topics"></a>Particionált üzenetsorok és témakörök

A prémium szintű üzenetkezelés nem támogatja a particionált üzenetsorokat és témaköröket. A particionálásra vonatkozó további információkat a [Partitioned queues and topics](service-bus-partitioning.md) (Particionált üzenetsorok és témakörök) című rész tartalmazza.

### <a name="express-entities"></a>Expressz entitások

Mivel a prémium szintű üzenetkezelés teljesen izolált futtatókörnyezetben fut, a prémium szintű névterek nem támogatják az expressz entitásokat. Az expressz szolgáltatásra vonatkozó további információkért lásd a [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) tulajdonságot.

Ha szabványos üzenetkezelés alatt futtat kódot, és továbbítani szeretné a prémium szintre, ügyeljen arra, hogy az [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) tulajdonság beállítása **false** legyen (ez az alapértelmezett érték).

## <a name="get-started-with-premium-messaging"></a>Ismerkedés a prémium szintű üzenetkezeléssel

A prémium szintű üzenetkezelés használatba vétele egyszerű, a folyamat pedig a standard szintű üzenetkezeléséhez hasonló. Első lépésként [hozzon létre egy névteret](service-bus-create-namespace-portal.md) az [Azure Portalon](https://portal.azure.com). Győződjön meg arról, hogy a **Tarifacsomag** alatt a **Prémium** tarifacsomagot választotta ki. Az egyes tarifacsomagokkal kapcsolatos információk megtekintéséhez kattintson a **Díjszabási részletek megtekintése** elemre.

![create-premium-namespace][create-premium-namespace]

[Az Azure Resource Manager-sablonok használatával is létrehozhat prémium szintű névtereket](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>További lépések

A Service Bus üzenetküldési funkcióival kapcsolatos további információkért tekintse meg a következőket:

* [Az Azure Service Bus prémium szintű üzenetkezelés bemutatása (blogbejegyzés)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Az Azure Service Bus prémium szintű üzenetkezelés bemutatása (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [A Service Bus-üzenetkezelés áttekintése](service-bus-messaging-overview.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
