---
title: "A Service Bus prémium és standard szintű üzenetkezelés tarifacsomagjainak áttekintése | Microsoft Docs"
description: "A Service Bus prémium és standard szintű üzenetkezelés"
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: 
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/21/2016
ms.author: darosa,sethm
translationtype: Human Translation
ms.sourcegitcommit: d36b40444af4ba68b016351f9ff016351e9fe58c
ms.openlocfilehash: a4ccfdbc079a989477a80af7ac701dc77dce5a4f


---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>A Service Bus prémium és standard szintű üzenetkezelés szintjei
A Service Bus-üzenetkezelés, amely a várólistákhoz és témakörökhöz hasonló üzenetkezelési entitásokat is tartalmaz, a vállalati üzenetkezelési képességeket ötvözi a gazdag közzétételi/előfizetési szemantikákkal a felhőbeli skálázással. A Service Bus-üzenetkezelés számos kifinomult felhőalapú megoldás kommunikációs vázaként szolgál.

A Service Bus-üzenetkezelés *prémium* szintje a méretezéssel, teljesítménnyel és a legfontosabb alkalmazásokkal kapcsolatos általános ügyfélkérelmeket kezeli. Noha a szolgáltatáskészletek csaknem azonosak, a Service Bus-üzenetkezelés két szintje különböző felhasználói esetekhez lett tervezve.

A következő táblázat néhány fontos eltérést emel ki.

| Prémium | Standard |
| --- | --- |
| Magas teljesítmény |Változó teljesítmény |
| Kiszámítható teljesítmény |Változó késés |
| Kiszámítható díjszabás |Használatalapú változó díjszabás |
| Lehetőség a munkaterhelés vertikális fel- és leskálázására |N/A |
| Üzenet mérete > 256 KB |Az üzenet mérete 256 KB |

A **Service Bus prémium szintű üzenetkezelés** a CPU-ban és a memóriarétegben biztosít erőforrás-elkülönítést, így minden ügyfél számítási feladata elkülönítve fut. Ennek az erőforrás-tárolónak a neve *üzenetkezelési egység*. Legalább egy üzenetkezelési egység van lefoglalva minden prémium névtérhez. Az egyes Service Bus prémium névterekhez 1, 2 vagy 4 üzenetkezelési egység vásárolható. Egyetlen számítási feladat vagy entitás több üzenetkezelési egységre is kiterjedhet, az üzenetkezelési egységek száma pedig tetszés szerint módosítható, bár a számlázás 24 órás vagy napi díjszabás szerint történik. Az eredmény a Service Bus-alapú megoldás kiszámítható és ismételhető teljesítménye.

Nem csak kiszámíthatóbb és nagyobb rendelkezésre állású a teljesítmény, de gyorsabb is. A Service Bus prémium szintű üzenetkezelés az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatásban bemutatott tárolási motorra épít. Prémium szintű üzenetkezelés esetén a csúcsteljesítmény jóval gyorsabb, mint a standard szinten.

## <a name="premium-messaging-technical-differences"></a>Prémium szintű üzenetkezelés – műszaki eltérések
Alább olvasható néhány különbség a prémium és a standard szintű üzenetkezelési szintek között.

### <a name="partitioned-queues-and-topics"></a>Particionált üzenetsorok és témakörök
A particionált üzenetsorok és témakörök támogatottak a prémium szintű üzenetkezelésben, de nem ugyanúgy működnek, mint a standard és az alapszintű Service Bus-üzenetkezelés. A prémium szintű üzenetkezelés nem használ SQL-t az adattároláshoz, és már nem jelentkezik a megosztott platformokhoz társuló erőforrásverseny. Ennek köszönhetően a particionálás nem kötelező. Ezen felül a partíciók száma a standard szint 16 partíciójáról a prémium szinten 2 partícióra változott. A két partíció biztosítja a rendelkezésre állást, és megfelelőbb szám a prémium szintű futtatókörnyezethez. A particionálásra vonatkozó további információkat a [Partitioned queues and topics](service-bus-partitioning.md) (Particionált üzenetsorok és témakörök) című rész tartalmazza.

### <a name="express-entities"></a>Expressz entitások
Mivel a prémium szintű üzenetkezelés teljesen izolált futtatókörnyezetben fut, a prémium szintű névterekben az expressz entitások nem támogatottak. Az expressz szolgáltatásra vonatkozó további információkért lásd a [QueueDescription.EnableExpress](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) tulajdonságot.

## <a name="next-steps"></a>Következő lépések
A Service Bus-üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [Az Azure Service Bus prémium szintű üzenetkezelés bemutatása (blogbejegyzés)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Az Azure Service Bus prémium szintű üzenetkezelés bemutatása (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [A Service Bus-üzenetkezelés áttekintése](service-bus-messaging-overview.md)
* [A Service Bus-üzenetsorok használata](service-bus-dotnet-get-started-with-queues.md)




<!--HONumber=Dec16_HO4-->


