---
title: Az Azure Service Bus prémium és standard szintjei
description: Ez a cikk az Azure Service Bus standard és prémium szintjeit ismerteti. Összehasonlítja ezeket a szinteket, és technikai különbségeket biztosít.
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
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774572"
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

A **Service Bus prémium szintű üzenetkezelés** erőforrás-elkülönítést biztosít a CPU és a memória szintjén, így az ügyfél minden számítási feladata elkülönítve fut. Ennek az erőforrás-tárolónak a neve *üzenetkezelési egység*. Legalább egy üzenetkezelési egység van lefoglalva minden prémium névtérhez. Minden Service Bus Premium-névtérhez 1, 2, 4 vagy 8 üzenetküldési egységet vásárolhat. Egyetlen számítási feladat vagy entitás több üzenetküldési egységre is kiterjedhet, és az üzenetküldési egységek száma tetszés szerint módosítható. Az eredmény a Service Bus-alapú megoldás kiszámítható és ismételhető teljesítménye.

Nem csak kiszámíthatóbb és nagyobb rendelkezésre állású a teljesítmény, de gyorsabb is. A Service Bus prémium szintű üzenetkezelés az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatásban bemutatott tárolási motorra épít. Prémium szintű üzenetkezelés esetén a csúcsteljesítmény jóval gyorsabb, mint a standard szinten.

## <a name="premium-messaging-technical-differences"></a>Prémium szintű üzenetkezelés – műszaki eltérések

Az alábbi szakaszokban a prémium és a standard szintű üzenetkezelési szintek közötti egyes különbségek olvashatók.

### <a name="partitioned-queues-and-topics"></a>Particionált üzenetsorok és témakörök

A prémium szintű üzenetkezelés nem támogatja a particionált üzenetsorokat és témaköröket. A particionálásra vonatkozó további információkat a [Partitioned queues and topics](service-bus-partitioning.md) (Particionált üzenetsorok és témakörök) című rész tartalmazza.

### <a name="express-entities"></a>Expressz entitások

Mivel a prémium szintű üzenetkezelés teljesen izolált futtatókörnyezetben fut, a prémium szintű névterek nem támogatják az expressz entitásokat. Az expressz szolgáltatásra vonatkozó további információkért lásd a [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) tulajdonságot.

Ha szabványos üzenetkezelés alatt futtat kódot, és továbbítani szeretné a prémium szintre, ügyeljen arra, hogy az [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) tulajdonság beállítása **false** legyen (ez az alapértelmezett érték).

## <a name="premium-messaging-resource-usage"></a>Prémium üzenetküldési erőforrás-használat
Az entitáson végzett bármely művelet általában processzor- és memóriahasználatot okozhat. Íme néhány ilyen művelet: 

- Felügyeleti műveletek, például CRUD (Create, Beolvasás, Frissítés és Törlés) műveletek várólistákon, témakörökön és előfizetéseken.
- Futásidejű műveletek (üzenetek küldése és fogadása)
- Nyomon követési műveletek és riasztások

A további CPU és a memória használat nem árú továbbá mégis. A prémium üzenetküldési szint esetén az üzenetegység egyetlen árat ért el.

A cpu- és memóriahasználat nyomon követhető, és a következő okok miatt jelenik meg az Ön számára: 

- Átláthatóság biztosítása a rendszer belső
- Ismerje meg a megvásárolt erőforrások kapacitását.
- Kapacitástervezés, amely segít eldönteni, hogy fel-le skálázás.

## <a name="messaging-unit---how-many-are-needed"></a>Üzenetküldő egység - Hány van szükség?

Az Azure Service Bus Premium-névtér kiépítésekor meg kell adni a lefoglalt üzenetküldési egységek számát. Ezek az üzenetkezelési egységek dedikált erőforrások, amelyek a névtérhez vannak rendelve.

A Service Bus Premium névtérhez rendelt üzenetküldési egységek száma **dinamikusan módosítható** a számítási feladatok változásának (növekedésének vagy csökkenésének) tényezőjéhez.

Számos tényezőt kell figyelembe venni az architektúra üzenetkezelő egységeinek számának meghatározásakor:

- Kezdje a névtérhez rendelt ***1 vagy 2 üzenetegységgel.***
- A cpu-használati metrikák a [névtér erőforrás-használati metrikák.](service-bus-metrics-azure-monitor.md#resource-usage-metrics)
    - Ha a processzorhasználat ***20% alatt***van, előfordulhat, hogy ***csökkenteni*** tudja a névtérhez rendelt üzenetküldési egységek számát.
    - Ha a CPU-használat meghaladja a ***70%-ot,*** az alkalmazás számára előnyös lesz a névtérhez rendelt üzenetküldési egységek számának ***növelése.***

A Service Bus-névterekhez rendelt erőforrások méretezése az [Azure Automation Runbookok](../automation/automation-quickstart-create-runbook.md)használatával automatikusan automatizálható.

> [!NOTE]
> A névtérhez rendelt erőforrások **méretezése** lehet megelőző vagy reaktív.
>
>  * **Megelőző:** Ha további számítási feladatok várhatók (szezonalitás vagy trendek miatt), folytathatja további üzenetküldési egységek lefoglalását a névtérhez, mielőtt a számítási feladatok lejönnek.
>
>  * **Reaktív:** Ha további számítási feladatokat azonosítanak az erőforrás-használati metrikák tanulmányozásával, majd további erőforrásokat lehet kiosztani a névtérbe a növekvő kereslet beépítése érdekében.
>
> A Service Bus számlázási mérőjeóránként van. Felskálázás esetén csak a további erőforrásokért kell fizetnie a felhasznált órákért.
>

## <a name="get-started-with-premium-messaging"></a>Ismerkedés a prémium szintű üzenetkezeléssel

A prémium szintű üzenetkezelés használatba vétele egyszerű, a folyamat pedig a standard szintű üzenetkezeléséhez hasonló. Első lépésként [hozzon létre egy névteret](service-bus-create-namespace-portal.md) az [Azure Portalon](https://portal.azure.com). Győződjön meg arról, hogy a **Tarifacsomag** alatt a **Prémium** tarifacsomagot választotta ki. Az egyes tarifacsomagokkal kapcsolatos információk megtekintéséhez kattintson a **Díjszabási részletek megtekintése** elemre.

![create-premium-namespace][create-premium-namespace]

[Az Azure Resource Manager-sablonok használatával is létrehozhat prémium szintű névtereket](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>További lépések

A Service Bus üzenetküldési funkcióival kapcsolatos további információkért tekintse meg a következőket:

* [Az Azure Service Bus prémium üzenetküldésének bemutatása (blogbejegyzés)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Az Azure Service Bus prémium szintű üzenetkezelés bemutatása (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [A Service Bus-üzenetkezelés áttekintése](service-bus-messaging-overview.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
