---
title: Az Azure Service Bus prémium és standard szintű üzenetkezelés tarifacsomagjainak áttekintése | Microsoft Docs
description: A Service Bus prémium és standard üzenetkezelési szintjei
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 7565ce24199dd8f86f756f01f66aa79e764a1a12
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72752191"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>A Service Bus prémium és standard szintű üzenetkezelés szintjei

A Service Bus-üzenetkezelés, amely a várólistákhoz és témakörökhöz hasonló entitásokat is tartalmaz, a vállalati üzenetkezelési képességeket ötvözi a gazdag közzétételi/előfizetési szemantikákkal a felhőbeli skálázással. A Service Bus-üzenetkezelés számos kifinomult felhőalapú megoldás kommunikációs vázaként szolgál.

A Service Bus-üzenetkezelés *prémium* szintje a méretezéssel, teljesítménnyel és a legfontosabb alkalmazásokkal kapcsolatos általános ügyfélkérelmeket kezeli. A prémium szintű csomag éles forgatókönyvekhez ajánlott. Noha a szolgáltatáskészletek csaknem azonosak, a Service Bus-üzenetkezelés két szintje különböző felhasználói esetekhez lett tervezve.

A következő táblázat néhány fontos eltérést emel ki.

| Prémium | Standard |
| --- | --- |
| Nagy adatforgalom kezelése |Változó teljesítmény |
| Kiszámítható teljesítmény |Változó késés |
| Rögzített díjszabás |Használatalapú változó díjszabás |
| Lehetőség a munkaterhelés vertikális fel- és leskálázására |– |
| Legfeljebb 1 MB méretű üzenet |Legfeljebb 256 KB méretű üzenet |

A **Service Bus prémium szintű üzenetkezelés** erőforrás-elkülönítést biztosít a CPU és a memória szintjén, így az ügyfél minden számítási feladata elkülönítve fut. Ennek az erőforrás-tárolónak a neve *üzenetkezelési egység*. Legalább egy üzenetkezelési egység van lefoglalva minden prémium névtérhez. Az egyes Service Bus prémium szintű névterekhez 1, 2, 4 vagy 8 üzenetküldési egység is megvásárolható. Egyetlen munkaterhelés vagy entitás több üzenetkezelési egységre is kiterjedhet, és az üzenetkezelési egységek száma a következő időpontban módosítható:. Az eredmény a Service Bus-alapú megoldás kiszámítható és ismételhető teljesítménye.

Nem csak kiszámíthatóbb és nagyobb rendelkezésre állású a teljesítmény, de gyorsabb is. A Service Bus prémium szintű üzenetkezelés az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatásban bemutatott tárolási motorra épít. Prémium szintű üzenetkezelés esetén a csúcsteljesítmény jóval gyorsabb, mint a standard szinten.

## <a name="premium-messaging-technical-differences"></a>Prémium szintű üzenetkezelés – műszaki eltérések

Az alábbi szakaszokban a prémium és a standard szintű üzenetkezelési szintek közötti egyes különbségek olvashatók.

### <a name="partitioned-queues-and-topics"></a>Particionált üzenetsorok és témakörök

A prémium szintű üzenetkezelés nem támogatja a particionált üzenetsorokat és témaköröket. A particionálásra vonatkozó további információkat a [Partitioned queues and topics](service-bus-partitioning.md) (Particionált üzenetsorok és témakörök) című rész tartalmazza.

### <a name="express-entities"></a>Expressz entitások

Mivel a prémium szintű üzenetkezelés teljesen izolált futtatókörnyezetben fut, a prémium szintű névterek nem támogatják az expressz entitásokat. Az expressz szolgáltatásra vonatkozó további információkért lásd a [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) tulajdonságot.

Ha szabványos üzenetkezelés alatt futtat kódot, és továbbítani szeretné a prémium szintre, ügyeljen arra, hogy az [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) tulajdonság beállítása **false** legyen (ez az alapértelmezett érték).

## <a name="premium-messaging-resource-usage"></a>Prémium szintű üzenetkezelési erőforrás-használat
Általánosságban elmondható, hogy az entitások bármely művelete CPU-és memóriahasználat okozhatja a használatot. Íme néhány ilyen művelet: 

- Felügyeleti műveletek, például a szifilisz (létrehozás, lekérés, frissítés és törlés) a várólisták, témakörök és előfizetések műveleteihez.
- Futásidejű műveletek (üzenetek küldése és fogadása)
- Figyelési műveletek és riasztások

A további CPU-és memóriahasználat azonban nem számítunk fel. A prémium szintű üzenetkezelési szint esetében az üzenet egységének egyetlen ára van.

A CPU és a memóriahasználat nyomon követhető, és a következő okok miatt jelenik meg: 

- Átláthatóság biztosítása a belső rendszerek számára
- Ismerje meg a megvásárolt erőforrások kapacitását.
- A kapacitás megtervezése, amely segít a vertikális felskálázásban.

## <a name="messaging-unit---how-many-are-needed"></a>Üzenetkezelési egység – hányra van szükség?

Azure Service Bus prémium szintű névtér kiosztásakor meg kell adni a lefoglalt üzenetküldési egységek számát. Ezek az üzenetkezelési egységek a névtérhez lefoglalt dedikált erőforrások.

A Service Bus Premium névtérhez lefoglalt üzenetküldési egységek száma **dinamikusan módosítható** a munkaterhelések változásának (növekedésének vagy csökkenésének) a felszámításához.

Az architektúrához tartozó üzenetkezelési egységek számának meghatározásakor figyelembe kell venni néhány tényezőt:

- Kezdje a névtérhez rendelt ***1 vagy 2 üzenetküldési egységgel*** .
- Tanulmányozza a CPU használati metrikáit a névtér [Erőforrás-használati metrikái](service-bus-metrics-azure-monitor.md#resource-usage-metrics) között.
    - Ha a CPU-használat ***20%***-nál kisebb, akkor lehetséges, hogy ***le tudja méretezni*** a névtérhez lefoglalt üzenetküldési egységek számát.
    - Ha a CPU-használat ***meghaladja a 70%-ot***, az alkalmazása kihasználhatja a névtérhez lefoglalt üzenetküldési egységek számának ***növelését*** .

A Service Bus névterek számára lefoglalt erőforrások méretezésének folyamata [Azure Automation runbookok](../automation/automation-quickstart-create-runbook.md)használatával automatizálható.

> [!NOTE]
> A névtérhez lefoglalt erőforrások **skálázása** lehet preemptív karbantartással vagy reaktív.
>
>  * **Preemptív karbantartással**: Ha további számítási feladatok várhatók (az évszakok vagy trendek miatt), a számítási feladatok elvégzése előtt további üzenetkezelési egységek is lefoglalhatók a névtérbe.
>
>  * **Reaktív**: Ha a további munkaterheléseket az erőforrás-használati metrikák tanulmányozásával azonosítja, a rendszer további erőforrásokat is kioszthat a névtérbe a növekvő igények kielégítése érdekében.
>
> A Service Bus számlázási mérőszáma óránként történik. Vertikális felskálázás esetén csak a felhasznált órák esetében kell fizetnie a további erőforrásokhoz.
>

## <a name="get-started-with-premium-messaging"></a>Ismerkedés a prémium szintű üzenetkezeléssel

A prémium szintű üzenetkezelés használatba vétele egyszerű, a folyamat pedig a standard szintű üzenetkezeléséhez hasonló. Első lépésként [hozzon létre egy névteret](service-bus-create-namespace-portal.md) az [Azure Portalon](https://portal.azure.com). Győződjön meg arról, hogy a **Tarifacsomag** alatt a **Prémium** tarifacsomagot választotta ki. Az egyes tarifacsomagokkal kapcsolatos információk megtekintéséhez kattintson a **Díjszabási részletek megtekintése** elemre.

![create-premium-namespace][create-premium-namespace]

[Az Azure Resource Manager-sablonok használatával is létrehozhat prémium szintű névtereket](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Következő lépések

A Service Bus üzenetküldési funkcióival kapcsolatos további információkért tekintse meg a következőket:

* [Az Azure Service Bus prémium szintű üzenetkezelés bemutatása (blogbejegyzés)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Az Azure Service Bus prémium szintű üzenetkezelés bemutatása (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [A Service Bus-üzenetkezelés áttekintése](service-bus-messaging-overview.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
