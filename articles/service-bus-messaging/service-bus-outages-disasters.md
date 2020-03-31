---
title: Az Azure Service Bus-alkalmazások elszigetelése a kimaradások és katasztrófák ellen
description: Ez a cikk az alkalmazások potenciális Azure Service Bus-kimaradás elleni védelmét ismerteti.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 2a7f5d5eacb2d03e64ae95d34e1cf0bd37bbc7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259252"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Ajánlott eljárások az alkalmazások elszigeteléséhez a Service Bus leállásaival és katasztrófáival szemben

A kritikus fontosságú alkalmazásoknak folyamatosan kell működniük, még nem tervezett leállások vagy katasztrófák esetén is. Ez a cikk a Service Bus-alkalmazások potenciális szolgáltatáskimaradásvagy katasztrófa elleni védelmére használható technikákat ismerteti.

A szolgáltatáskimaradás az Azure Service Bus ideiglenes elérhetetlensége. A szolgáltatáskimaradás hatással lehet a Service Bus egyes összetevőire, például egy üzenettárolóra, vagy akár a teljes adatközpontra. A probléma megoldása után a Service Bus ismét elérhetővé válik. A kimaradás általában nem okoz üzenetek vagy más adatok elvesztését. Egy összetevő meghibásodásának egyik példája egy adott üzenettároló elérhetetlensége. Egy adatközpont-szintű kimaradás egy áramkimaradás az adatközpont, vagy egy hibás adatközpont hálózati kapcsoló. A leállás néhány perctől néhány napig tarthat.

A katasztrófa a Service Bus méretezési egységének vagy adatközpontjának végleges elvesztése. Előfordulhat, hogy az adatközpont nem lesz elérhető újra. A katasztrófa általában néhány vagy az összes üzenet vagy más adat elvesztését okozza. Katasztrófák például tűz, árvíz vagy földrengés.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Védelem a kimaradások és katasztrófák ellen - Service Bus Premium
A magas rendelkezésre állásés a vészhelyreállítás fogalmak közvetlenül az Azure Service Bus premium szintbe vannak beépítve, mind ugyanabban a régióban (a rendelkezésre állási zónákon keresztül) és a különböző régiókban (a geo-katasztrófa-helyreállítási keresztül).

### <a name="geo-disaster-recovery"></a>Geo-katasztrófa helyreállítása

A Service Bus Premium támogatja a földrajzi vészhelyreállítást a névtér szintjén. További információ: [Azure Service Bus Geo-katasztrófa-helyreállítási.](service-bus-geo-dr.md) A vész-helyreállítási funkció, csak a [prémium szintű termékváltozat,](service-bus-premium-messaging.md) metaadat-vész-helyreállítási, és az elsődleges és másodlagos vész-helyreállítási névterek támaszkodik.

### <a name="availability-zones"></a>Rendelkezésre állási zónák

A Service Bus Premium Termékváltozat támogatja [a rendelkezésre állási zónák,](../availability-zones/az-overview.md)amely hiba-elszigetelt helyek ugyanabban az Azure-régióban.

> [!NOTE]
> Az Azure Service Bus Premium rendelkezésre állási zónák támogatása csak olyan [Azure-régiókban](../availability-zones/az-overview.md#services-support-by-region) érhető el, ahol rendelkezésre állási zónák vannak jelen.

Csak az Azure Portal használatával engedélyezheti a rendelkezésre állási zónákat az új névterekben. A Service Bus nem támogatja a meglévő névterek áttelepítését. A zónaredundancia nem tiltható le, miután engedélyezte azt a névtérben.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Védelem a kimaradások és katasztrófák ellen - Service Bus Standard
Az adatközpont kimaradások ellen a szabványos üzenetkezelési tarifacsomag használata esetén a Service Bus két megközelítést támogat: *aktív* és *passzív* replikáció. Minden egyes megközelítés, ha egy adott várólista vagy témakör elérhetőnek kell maradnia egy adatközpont-kimaradás, létrehozhatja azt mindkét névterekben. Mindkét entitásnak ugyanaz a neve. Például az elsődleges várólista **contosoPrimary.servicebus.windows.net/myQueue**alatt érhető el, míg másodlagos megfelelője **contosoSecondary.servicebus.windows.net/myQueue**alatt érhető el.

>[!NOTE]
> Az **aktív replikáció** és a passzív **replikáció** beállítása általános célú megoldások, nem pedig a Service Bus speciális szolgáltatásai. A replikációs logika (2 különböző névtérbe küldése) a küldő alkalmazásokon él, és a fogadónak egyéni logikával kell rendelkeznie a duplikáltelem-észleléshez.

Ha az alkalmazás nem igényel állandó feladó-címzett kommunikációt, az alkalmazás egy tartós ügyféloldali várólistát valósíthat meg az üzenetvesztés megelőzése és a feladó védelme az átmeneti Service Bus hibáktól.

### <a name="active-replication"></a>Aktív replikáció
Az aktív replikáció mindkét névtérben használ entitásokat minden művelethez. Minden olyan ügyfél, amely üzenetet küld, ugyanannak az üzenetnek a két példányát küldi el. Az első példányt a rendszer elküldi az elsődleges entitásnak (például **contosoPrimary.servicebus.windows.net/sales),** az üzenet második példányát pedig a másodlagos entitásnak (például **contosoSecondary.servicebus.windows.net/sales).**

Az ügyfél mindkét várólistából fogad üzeneteket. A fogadó feldolgozza az üzenet első példányát, és a második példány le van tiltva. Az ismétlődő üzenetek letiltásához a feladónak minden üzenetet egyedi azonosítóval kell elkészítenie. Az üzenet mindkét példányát ugyanazzal az azonosítóval kell címkézni. Használhatja a [BrokeredMessage.MessageId][BrokeredMessage.MessageId] vagy [BrokeredMessage.Label][BrokeredMessage.Label] tulajdonságok, vagy egy egyéni tulajdonság az üzenet címkézésére. A fogadónak karban kell tartania a már fogadott üzenetek listáját.

A [Geo-replikáció a Service Bus standard szintű][Geo-replication with Service Bus Standard Tier] minta bemutatja az üzenetküldő entitások aktív replikációját.

> [!NOTE]
> Az aktív replikációs megközelítés megduplázza a műveletek számát, ezért ez a megközelítés magasabb költségekhez vezethet.
> 
> 

### <a name="passive-replication"></a>Passzív replikáció
A hibamentes esetben a passzív replikáció csak az egyik a két üzenetküldő entitások. Az ügyfél elküldi az üzenetet az aktív entitásnak. Ha az aktív entitáson végzett művelet sikertelen, egy hibakóddal, amely azt jelzi, hogy az aktív entitást tároló adatközpont nem érhető el, az ügyfél elküldi az üzenet másolatát a biztonsági másolat entitásnak. Ezen a ponton az aktív és a biztonsági mentési entitások szerepköröket váltanak: a küldő ügyfél a régi aktív entitást tekinti az új biztonsági mentési entitásnak, a régi biztonsági mentési entitás pedig az új aktív entitás. Ha mindkét küldési művelet sikertelen, a két entitás szerepkörei változatlanok maradnak, és hibaüzenetet ad vissza.

Az ügyfél mindkét várólistából fogad üzeneteket. Mivel előfordulhat, hogy a címzett ugyanannak az üzenetnek két példányát kapja meg, a fogadónak le kell nyomnia az ismétlődő üzeneteket. Az ismétlődéseket ugyanúgy letilthatja, mint az aktív replikációesetében leírtakszerint.

A passzív replikáció általában gazdaságosabb, mint az aktív replikáció, mivel a legtöbb esetben csak egy műveletet hajt végre. A késés, az átviteli és a pénzbeli költségek megegyeznek a nem replikált forgatókönyvvel.

Passzív replikáció használata esetén a következő esetekben az üzenetek kétszer elveszhetnek vagy fogadhatók:

* **Üzenet késleltetése vagy elvesztése**: Tegyük fel, hogy a feladó sikeresen elküldött egy m1-es üzenetet az elsődleges várólistába, majd a várólista elérhetetlenné válik, mielőtt a fogadó m1-et kapna. A feladó egy további m2-es üzenetet küld a másodlagos várólistának. Ha az elsődleges várólista átmenetileg nem érhető el, a fogadó az m1-et kapja, miután a várólista ismét elérhetővé válik. Katasztrófa esetén előfordulhat, hogy a vevő készülék soha nem kapja meg az M1-et.
* **Duplikált vétel**: Tegyük fel, hogy a feladó m üzenetet küld az elsődleges várólistának. A Service Bus sikeresen feldolgozza az m-et, de nem küld választ. Miután a küldési művelet idővel ki, a feladó elküldi az m azonos példányát a másodlagos várólistába. Ha a fogadó képes fogadni az m első példányát, mielőtt az elsődleges várólista elérhetetlenné válik, a fogadó körülbelül ugyanabban az időben fogadja az m mindkét példányát. Ha a fogadó nem tudja fogadni az m első példányát, mielőtt az elsődleges várólista elérhetetlenné válik, a fogadó először csak az m második példányát kapja meg, de ezután megkapja az m második példányát, amikor az elsődleges várólista elérhetővé válik.

A [Geo-replikáció a Service Bus Standard Tier][Geo-replication with Service Bus Standard Tier] minta bemutatja az üzenetküldő entitások passzív replikációját.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>A továbbítási végpontok védelme az adatközpont kimaradásai és a katasztrófák ellen
Az Azure [Relay-végpontok](../service-bus-relay/relay-what-is-it.md) georeplikációja lehetővé teszi, hogy egy továbbítóvégpontot elérhetővé tegye egy szolgáltatás a Service Bus-kimaradások jelenlétében. A georeplikáció eléréséhez a szolgáltatásnak két továbbítási végpontot kell létrehoznia a különböző névterekben. A névtereknek különböző adatközpontokban kell elhelyezkedniük, és a két végpontnak különböző nevűnek kell lennie. Például az elsődleges végpont **contosoPrimary.servicebus.windows.net/myPrimaryService**alatt érhető el, míg másodlagos megfelelője **contosoSecondary.servicebus.windows.net/mySecondaryService**alatt érhető el.

A szolgáltatás ezután mindkét végponton figyel, és az ügyfél bármelyik végponton keresztül meghívhatja a szolgáltatást. Az ügyfélalkalmazás véletlenszerűen kiválasztja az egyik relét elsődleges végpontként, és elküldi a kérését az aktív végpontnak. Ha a művelet hibakóddal sikertelen, ez a hiba azt jelzi, hogy a továbbítási végpont nem érhető el. Az alkalmazás megnyit egy csatornát a biztonsági mentési végponthoz, és újra kiadja a kérelmet. Ezen a ponton az aktív és a biztonsági mentési végpontok szerepkörök: az ügyfélalkalmazás úgy véli, a régi aktív végpont az új biztonsági mentési végpont, és a régi biztonsági mentési végpont az új aktív végpont. Ha mindkét küldési művelet sikertelen, a két entitás szerepkörei változatlanok maradnak, és hibaüzenetet ad vissza.

## <a name="next-steps"></a>További lépések
A vészhelyreállításról az alábbi cikkekben olvashat bővebben:

* [Az Azure Service Bus geokatasztrófa-helyreállítása](service-bus-geo-dr.md)
* [Az Azure SQL Database üzletmenetének folytonossága][Azure SQL Database Business Continuity]
* [Rugalmas alkalmazások tervezése az Azure-hoz][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
