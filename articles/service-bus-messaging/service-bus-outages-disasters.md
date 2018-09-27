---
title: Leállások és katasztrófák kezelése az Azure Service Bus-alkalmazásfejlesztések szigetelő |} A Microsoft Docs
description: Módszerek a lehetséges a Service Bus kimaradás alkalmazások védelmét.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: spelluru
ms.openlocfilehash: 85481deceeadaf4154659d35fccf777f489bd782
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393707"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Ajánlott eljárások az alkalmazások a Service Bus leállásainak és katasztrófákkal szembeni szigetelő

Alapvető fontosságú alkalmazások folyamatos, még a nem tervezett leállások és katasztrófák folytonosságát kell működniük. Ez a cikk ismerteti a technikák használatával a Service Bus alkalmazások egy esetleges szolgáltatáskimaradás vagy katasztrófa elleni védelmét.

Egy kimaradás az átmeneti elérhetetlensége, valamint az Azure Service Bus definiálható. A szolgáltatáskimaradás hatással lehet egyes összetevői a Service Bus, például az üzenetküldési tárolóban, vagy akár az egész adatközpont. Miután a problémát megoldottuk, Service Bus ismét elérhetővé válik. Egy kimaradás általában nem okoz az üzenetek vagy egyéb adatok elvesztését. Összetevőhiba egyik példája a elérhetetlensége egy adott üzenetküldési tároló. Egy példa egy adatközpontra kiterjedő szolgáltatáskimaradás az adatközpontban vagy egy hibás adatközpont hálózati kapcsoló power hiba történik. Egy kimaradás pár percet vagy is utolsó néhány napra.

A vészhelyreállítási számít, ha a végleges adatvesztést egy Service Bus skálázási egység vagy datacenter. Előfordulhat, hogy az adatközpontban, vagy előfordulhat, hogy nem ismét elérhetővé válik. Általában a vészhelyzet vagy az összes üzenetet, vagy egyéb adatok elvesztését eredményezi. Katasztrófák példák fire, -elárasztás vagy földrengés.

## <a name="current-architecture"></a>Aktuális architektúra
A Service Bus több üzenetküldési tároló üzenetsorokra vagy témakörökbe küldött üzenetek tárolására használ. Egy nem particionált üzenetsor vagy témakör egyetlen üzenetküldési tárolóra van hozzárendelve. Az üzenetküldési tárolóban nem érhető el, ha az üzenetsor vagy témakör összes művelet sikertelen lesz.

Az összes Service Bus üzenetküldési entitások (üzenetsorok, témakörök, továbbítók) szolgáltatásnévtér, amely tagja-e egy adatközpontban található. A Service Bus mostantól támogatja [ *Geo-disaster recovery* és *georeplikációs* ](service-bus-geo-dr.md) a névterek szintjén.

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Üzenetsorok és témakörök üzenetküldési tárolóban hibák elleni védelem
Egy nem particionált üzenetsor vagy témakör egyetlen üzenetküldési tárolóra van hozzárendelve. Az üzenetküldési tárolóban nem érhető el, ha az üzenetsor vagy témakör összes művelet sikertelen lesz. Egy particionált üzenetsorra, másrészt több töredékkel áll. Minden részlet egy különböző üzenetküldési tárolóban tárolja. Ha egy üzenetet küld egy particionált üzenetsorra vagy témakörbe, a Service Bus hozzárendeli az üzenetet a töredék egyikéhez. A megfelelő üzenetküldési tárolóban nem érhető el, ha a Service Bus ír az üzenet egy másik kódrészletet, ha lehetséges. A particionált entitások már nem támogatottak a [prémium szintű Termékváltozat](service-bus-premium-messaging.md). 

Particionált entitások kapcsolatos további információkért lásd: [particionált üzenetküldési entitások][Partitioned messaging entities].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Adatközpont-leállások és katasztrófák elleni védelem
Ahhoz, hogy a feladatátvétel két adatközpont között, létrehozhat egy Service Bus-szolgáltatásnévtér a valamennyi adatközpont. Például a Service Bus-szolgáltatásnévtér **contosoPrimary.servicebus.windows.net** előfordulhat, hogy az Egyesült Államok, északi/középső régió régióban található, és **contosoSecondary.servicebus.windows.net**előfordulhat, hogy USA Dél-India és közép-India a régióban található. Ha egy Service Bus üzenetkezelési entitás zajok mellett egy adatközpont-meghibásodás után hozzáférhetőnek kell maradnia, entitás is létrehozhat a mindkét névtérre.

További információkért tekintse meg a "Service Bus, az Azure-adatközpont meghibásodása" szakaszában [aszinkron üzenetkezelési minták és magas rendelkezésre állás][Asynchronous messaging patterns and high availability].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Adatközpont-leállások és katasztrófák elleni továbbítási végpontok védelme
A továbbítási végpontok georeplikáció lehetővé teszi, hogy egy szolgáltatás, amely a továbbító végpont zajok mellett a Service Bus leállásainak elérhetőnek kell lennie. Georeplikáció elérése érdekében a szolgáltatás létre kell hoznia két továbbítási végpontok különböző névterekben. A névterek kell lennie, különböző adatközpontokban, és a két végpontot nevének eltérőnek kell lennie. Például egy elsődleges végpont érhető el a **contosoPrimary.servicebus.windows.net/myPrimaryService**, míg a másodlagos párjukhoz alatt elérhető **contosoSecondary.servicebus.windows.net /mySecondaryService**.

Ezután figyeli a szolgáltatás mindkét végponton, és a egy ügyfél hívhat meg mindkét végponton keresztül a szolgáltatás. Egy ügyfélalkalmazás véletlenszerűen választja ki a továbbítók egyikét, az elsődleges végpontot, és a kérést küld az aktív végpontot. A művelet egy hibakóddal meghiúsul, ha ez a hiba azt jelzi, hogy a relay-végpont nem érhető el. Az alkalmazás megnyílik a biztonsági mentési végpont egy csatornát, és a kérés reissues. Ezen a ponton az aktív és a biztonsági mentési végpontok váltson szerepkörök: az ügyfélalkalmazás is figyelembe veszi a régi aktív végpontot, hogy az új biztonsági mentési végpont, és a régi biztonsági mentési végpontot, hogy az új aktív végpontnak lennie. Műveletek sikertelenek is küld, ha a szerepköröket a két entitás változatlanok maradnak, és hibát ad vissza.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Az üzenetsoroktól és témaköröktől adatközpont-leállások és katasztrófák elleni védelme
Adatközpont-leállások szembeni rugalmasság elérése érdekében, amikor használatával közvetítőalapú üzenettovábbítás, a Service Bus támogatja a két módszer: *aktív* és *passzív* replikáció. Az egyes módszert használja Ha egy adott üzenetsor vagy témakör zajok mellett az Adatközpont-meghibásodás után hozzáférhetőnek kell maradnia létrehozhatja a mindkét névtérre. Mindkét entitások rendelkezhet ugyanazzal a névvel. Például egy elsődleges queue érhető el a **contosoPrimary.servicebus.windows.net/myQueue**, míg a másodlagos párjukhoz alatt elérhető **contosoSecondary.servicebus.windows.net/myQueue**.

Ha az alkalmazás nem állandó küldő fogadó kommunikációt is igényelnek, az alkalmazás egy tartós ügyféloldali üzenetsor üzenetet az adatvesztés elkerülése érdekében és védelmének beállítása a Service Bus átmeneti hibák a küldőnek valósítható meg.

## <a name="active-replication"></a>A replikálás aktív
Aktív replikációs mindkét névtérre entitások minden művelethez használ. Minden olyan ügyfél, amely egy üzenetet küld az ugyanazon üzenet két példánya küld. Az első másolás küld az elsődleges entitásnál (például **contosoPrimary.servicebus.windows.net/sales**), és az üzenet második példányát küldi el a másodlagos entitást (például  **contosoSecondary.servicebus.windows.net/sales**).

Egy ügyfél mindkét üzenetsorok fogad üzeneteket. A fogadó feldolgozza az üzenetet első példányát, és a második példány le lesz tiltva. Ismétlődő üzenetek le, a küldő fel kell címkéznie az egyes üzenet egyedi azonosítója. Mindkét az üzenet másolata ugyanazzal az azonosítóval kell megcímkézni. Használhatja a [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] vagy [BrokeredMessage.Label] [ BrokeredMessage.Label] tulajdonságok vagy az üzenetek címkézése egyéni tulajdonságot. A fogadó kell üzeneteket, amelyek már megkapta listának a karbantartására.

A [georeplikáció a Service Bus Közvetítőalapú üzenetek] [ Geo-replication with Service Bus Brokered Messages] üzenetküldési entitások active replikálását mutatja be.

> [!NOTE]
> Az aktív replikációs megközelítés megduplázódik műveletek számát, ezért ez a módszer költségesebb vezethet.
> 
> 

## <a name="passive-replication"></a>Passzív replikáció
A tartalék ingyenes esetben passzív-replikáció az csak az egyik a két üzenetküldési entitások. Egy ügyfél az üzenetet küld az aktív entitás. Az aktív entitásra a művelet sikertelen, és egy hibakód, amely azt jelzi, elképzelhető, hogy az adatközpont, amelyen az aktív entitás nem érhető el, ha az ügyfél az üzenet másolatot küld a biztonsági mentési entitás. Ezen a ponton az aktív és a biztonsági mentési entitásokon vált szerepkörök: a küldő ügyfél is figyelembe veszi a régi aktív entitás kell az új biztonsági mentési entitáshoz, és a régi biztonsági mentési entitás az új aktív entitás. Műveletek sikertelenek is küld, ha a szerepköröket a két entitás változatlanok maradnak, és hibát ad vissza.

Egy ügyfél mindkét üzenetsorok fogad üzeneteket. Mivel előfordulhat, hogy a fogadó ugyanazon üzenet két példányt kap, a fogadó ismétlődő üzeneteket kell mellőzése. Ismétlődő ugyanúgy tilthatja le az aktív replikációs leírtak szerint.

Általánosságban elmondható passzív replikáció azért gazdaságosabb, mint az aktív replikációs, mert a legtöbb esetben csak egy művelet végrehajtása után. A nem replikált forgatókönyv késés, átviteli sebesség és a költség megegyeznek.

Passzív replikáció használata esetén a következő esetekben üzenetek néha elvész, vagy kétszer érkezett:

* **Üzenet késése vagy az adatvesztés**: Tegyük fel, hogy a küldő egy üzenetet m1 sikeresen elküldte az elsődleges üzenetsornak, és majd a várólista elérhetetlenné válik a címzett megkapja az m1 előtt. A küldő a következő üzenetet m2 küld a másodlagos üzenetsor. Az elsődleges üzenetsornak átmenetileg nem érhető el, ha a fogadó m1 kap, miután újra elérhetővé a várólista válik. Egy esetleges vészhelyzet esetén a fogadó soha nem jelenhet meg az M1 verzióban.
* **Ismétlődő fogadás**: Tegyük fel, hogy a küldő millió üzenetet küld az elsődleges üzenetsornak. A Service Bus sikeresen m dolgozza fel, de nem tud választ küldeni. Miután a küldési művelet időkorlátja lejár, a küldő m egyforma példányával küld a másodlagos üzenetsor. Ha a fogadó tudja fogadni az m első példányát, mielőtt az elsődleges üzenetsornak elérhetetlenné válik, a fogadó kap mindkét másolat m körülbelül egy időben. Ha a címzett nem tudja fogadni az m első példányát, mielőtt az elsődleges üzenetsornak elérhetetlenné válik, a fogadó csak a második példányt m kezdetben kap, de majd m második másolatot kap, amikor az elsődleges üzenetsornak elérhetővé válik.

A [georeplikáció a Service Bus közvetítőalapú üzenetek] [ Geo-replication with Service Bus Brokered Messages] üzenetküldési entitások passzív replikálását mutatja be.

## <a name="geo-replication"></a>Georeplikáció

A Service Bus Geo-vészhelyreállítás és Georeplikáció útján, a névterek szintjén támogatja. További információkért lásd: [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md). A vész helyreállítási funkció, elérhető a [prémium szintű Termékváltozat](service-bus-premium-messaging.md) csak, metaadatok vész-helyreállítási valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterek támaszkodik.

## <a name="availability-zones-preview"></a>A rendelkezésre állási zónák (előzetes verzió)

A Service Bus prémium szintű Termékváltozat támogatja [rendelkezésre állási zónák](../availability-zones/az-overview.md), így az Azure-régión belüli, meghibásodásoktól elszigetelt helyek. 

> [!NOTE]
> A rendelkezésre állási zónák előzetes verziója csak a támogatott a **USA középső RÉGIÓJA**, **USA keleti RÉGIÓJA 2**, és **közép-Franciaország** régióban.

Engedélyezheti a rendelkezésre állási zónák a csak az új névterek az Azure portal használatával. A Service Bus nem támogatja a meglévő névterek áttelepítésének. Miután engedélyezte a a névtérben nem tiltható le a zone redudancy.

![1][]

## <a name="next-steps"></a>További lépések
Vész-helyreállítási kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [Az Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md)
* [Az Azure SQL Database üzletmenet-folytonossági][Azure SQL Database Business Continuity]
* [Rugalmas alkalmazások tervezése az Azure-hoz][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png