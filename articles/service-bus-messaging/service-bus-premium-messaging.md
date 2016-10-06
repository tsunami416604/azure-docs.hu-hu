<properties
    pageTitle="A Service Bus prémium és standard szintű üzenetkezelés tarifacsomagjainak áttekintése | Microsoft Azure"
    description="A Service Bus prémium és standard szintű üzenetkezelés"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2016"
    ms.author="darosa;sethm"/>


# A Service Bus prémium és standard szintű üzenetkezelés szintjei 

A Service Bus üzenettovábbítás, amely az üzenetsorokhoz és témakörökhöz hasonló üzenetkezelési entitásokat is tartalmaz, a vállalati üzenetkezelési képességeket ötvözi a gazdag közzétételi/előfizetési szemantikákkal a felhőbeli skálázással. A Service Bus üzenetkezelés számos kifinomult felhőalapú megoldás kommunikációs vázaként szolgál.

A Service Bus üzenetkezelés *prémium* szintje a skálázással, teljesítménnyel és a legfontosabb alkalmazásokkal kapcsolatos általános ügyfélkérelmeket kezeli. Noha a szolgáltatáskészletek csaknem azonosak, a Service Bus üzenetkezelés két szintje különböző felhasználói esetekhez lett tervezve.

Az alábbi táblázat néhány fontos eltérést emel ki.

| Prémium                               | Standard                       |
|---------------------------------------|--------------------------------|
| Magas teljesítmény                       | Változó teljesítmény            |
| Kiszámítható teljesítmény               | Változó késés               |
| Kiszámítható díjszabás                   | Használatalapú változó díjszabás |
| Lehetőség a munkaterhelés vertikális fel- és leskálázására | N/A                            |
| Üzenet mérete > 256 KB                  | Az üzenet mérete 256 KB          |

A **Service Bus prémium szintű üzenetkezelés** a CPU-ban és a memóriarétegben biztosít erőforrás-elkülönítést, így minden ügyfél számítási feladata elkülönítve fut. Ennek az erőforrás-tárolónak a neve *üzenetkezelési egység*. Legalább egy üzenetkezelési egység van lefoglalva minden prémium névtérhez. Az egyes Service Bus prémium névterekhez 1, 2 vagy 4 üzenetkezelési egység vásárolható. Egyetlen számítási feladat vagy entitás több üzenetkezelési egységre is kiterjedhet, az üzenetkezelési egységek száma pedig tetszés szerint módosítható, bár a számlázás 24 órás vagy napi díjszabás szerint történik. Az eredmény a Service Bus-alapú megoldás kiszámítható és ismételhető teljesítménye.

Nem csak kiszámíthatóbb és nagyobb rendelkezésre állású a teljesítmény, de gyorsabb is. A Service Bus prémium szintű üzenetkezelés az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatásban bemutatott tárolási motorra épít. Prémium szintű üzenetkezelés esetén a csúcsteljesítmény jóval gyorsabb, mint a standard szint esetén.

## Prémium szintű üzenetkezelés – műszaki eltérések

Alább olvasható néhány különbség a prémium és a standard szintű üzenetkezelési szintek között.

### Particionált üzenetsorok és témakörök

A particionált üzenetsorok és témakörök támogatottak a prémium szintű üzenetkezelésben, de nem ugyanúgy működnek, mint a standard és az alapszintű Service Bus üzenetkezelés. A prémium szintű üzenetkezelés nem használ SQL-t az adattároláshoz, és már nem jelentkezik a megosztott platformokhoz társuló erőforrásverseny. Ennek köszönhetően a particionálás nem kötelező. Ezen felül a partíciók száma a standard szint 16 partíciójáról a prémium szinten 2 partícióra változott. A két partíció biztosítja a rendelkezésre állást, és megfelelőbb szám a prémium szintű futtatókörnyezethez. A particionálásra vonatkozó további információkat a [Partitioned queues and topics](service-bus-partitioning.md) (Particionált üzenetsorok és témakörök) című rész tartalmazza.

### Expressz entitások

Mivel a prémium szintű üzenetkezelés teljesen izolált futtatókörnyezetben fut, a prémium szintű névterekben az expressz entitások nem támogatottak. További információkért az expressz szolgáltatásról lásd a [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) tulajdonságot.

## Következő lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

- [Introducing Azure Service Bus Premium messaging (Az Azure Service Bus prémium szintű üzenetkezelés bemutatása) (blogbejegyzés)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducing Azure Service Bus Premium messaging (Az Azure Service Bus prémium szintű üzenetkezelés bemutatása) (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Service Bus messaging overview (A Service Bus üzenetkezelésének áttekintése)](service-bus-messaging-overview.md)
- [Azure Service Bus architectural overview (Az Azure Service Bus architektúrájának áttekintése)](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [How to use Service Bus Queues (A Service Bus-üzenetsorok használata)](service-bus-dotnet-get-started-with-queues.md)



<!--HONumber=Sep16_HO4-->


