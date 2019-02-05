---
title: Leállások és katasztrófák kezelése az Azure Service Bus-alkalmazásfejlesztések szigetelő |} A Microsoft Docs
description: Módszerek a lehetséges a Service Bus kimaradás alkalmazások védelmét.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: ccf0ddfd42d6617634ce4a230ba88b76552afef3
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692843"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Ajánlott eljárások az alkalmazások a Service Bus leállásainak és katasztrófákkal szembeni szigetelő

Alapvető fontosságú alkalmazások folyamatos, még a nem tervezett leállások és katasztrófák folytonosságát kell működniük. Ez a cikk ismerteti a technikák használatával a Service Bus alkalmazások egy esetleges szolgáltatáskimaradás vagy katasztrófa elleni védelmét.

Egy kimaradás az átmeneti elérhetetlensége, valamint az Azure Service Bus definiálható. A szolgáltatáskimaradás hatással lehet egyes összetevői a Service Bus, például az üzenetküldési tárolóban, vagy akár az egész adatközpont. Miután a problémát megoldottuk, Service Bus ismét elérhetővé válik. Egy kimaradás általában nem okoz az üzenetek vagy egyéb adatok elvesztését. Összetevőhiba egyik példája a elérhetetlensége egy adott üzenetküldési tároló. Egy példa egy adatközpontra kiterjedő szolgáltatáskimaradás az adatközpontban vagy egy hibás adatközpont hálózati kapcsoló power hiba történik. Egy kimaradás pár percet vagy is utolsó néhány napra.

A vészhelyreállítási számít, ha a végleges adatvesztést egy Service Bus skálázási egység vagy datacenter. Előfordulhat, hogy az adatközpontban, vagy előfordulhat, hogy nem ismét elérhetővé válik. Általában a vészhelyzet vagy az összes üzenetet, vagy egyéb adatok elvesztését eredményezi. Katasztrófák példák fire, -elárasztás vagy földrengés.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Leállások és katasztrófák kezelése – a Service Bus prémium szintű elleni védelme
Az Azure Service Bus prémium szintű, ugyanabban a régióban (keresztül a rendelkezésre állási zónák) egyaránt be van építve a magas rendelkezésre állás és a vész-helyreállítási fogalmak és (a Geo-Disaster Recovery) keresztül különböző régiók között.

### <a name="geo-disaster-recovery"></a>Georeplikációs vészhelyreállítás

Prémium szintű Service Bus Geo-disaster recovery, a névterek szintjén támogatja. További információkért lásd: [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md). A vész helyreállítási funkció, elérhető a [prémium szintű Termékváltozat](service-bus-premium-messaging.md) csak, metaadatok vész-helyreállítási valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterek támaszkodik.

### <a name="availability-zones"></a>Rendelkezésre állási zónák

A Service Bus prémium szintű Termékváltozat támogatja [rendelkezésre állási zónák](../availability-zones/az-overview.md), így az azonos Azure-régión belüli, meghibásodásoktól elszigetelt helyek.

> [!NOTE]
> Az Azure Service Bus prémium szintű rendelkezésre állási zónák támogatása csak érhető el a [Azure-régiók](../availability-zones/az-overview.md#regions-that-support-availability-zones) ahol jelen-e rendelkezésre állási zónák.

Engedélyezheti a rendelkezésre állási zónák a csak az új névterek az Azure portal használatával. A Service Bus nem támogatja a meglévő névterek áttelepítésének. Miután engedélyezte a a névtérben nem tiltható le a zone redudancy.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Leállások és katasztrófák kezelése – a standard szintű Service Bus elleni védelme
Adatközpont-leállások szembeni rugalmasság elérése, a standard tarifacsomag üzenetkezelés használata esetén, a Service Bus támogatja a két módszer: *aktív* és *passzív* replikáció. Az egyes módszert használja Ha egy adott üzenetsor vagy témakör zajok mellett az Adatközpont-meghibásodás után hozzáférhetőnek kell maradnia létrehozhatja a mindkét névtérre. Mindkét entitások rendelkezhet ugyanazzal a névvel. Például egy elsődleges queue érhető el a **contosoPrimary.servicebus.windows.net/myQueue**, míg a másodlagos párjukhoz alatt elérhető **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> A **aktív replikációs** és **passzív replikációs** telepítő, általános célú megoldások és a Service Bus nem adott funkcióit. A replikációs logikát, (2 különböző névterekben küldött) él küldő alkalmazás és a fogadó kell duplikáltelem-észlelési egyéni logikát rendelkezik.

Ha az alkalmazás nem állandó küldő fogadó kommunikációt is igényelnek, az alkalmazás egy tartós ügyféloldali üzenetsor üzenetet az adatvesztés elkerülése érdekében és védelmének beállítása a Service Bus átmeneti hibák a küldőnek valósítható meg.

### <a name="active-replication"></a>A replikálás aktív
Aktív replikációs mindkét névtérre entitások minden művelethez használ. Minden olyan ügyfél, amely egy üzenetet küld az ugyanazon üzenet két példánya küld. Az első másolás küld az elsődleges entitásnál (például **contosoPrimary.servicebus.windows.net/sales**), és az üzenet második példányát küldi el a másodlagos entitást (például  **contosoSecondary.servicebus.windows.net/sales**).

Egy ügyfél mindkét üzenetsorok fogad üzeneteket. A fogadó feldolgozza az üzenetet első példányát, és a második példány le lesz tiltva. Ismétlődő üzenetek le, a küldő fel kell címkéznie az egyes üzenet egyedi azonosítója. Mindkét az üzenet másolata ugyanazzal az azonosítóval kell megcímkézni. Használhatja a [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] vagy [BrokeredMessage.Label] [ BrokeredMessage.Label] tulajdonságok vagy az üzenetek címkézése egyéni tulajdonságot. A fogadó kell üzeneteket, amelyek már megkapta listának a karbantartására.

A [a Service Bus Standard szintű georeplikáció] [ Geo-replication with Service Bus Standard Tier] üzenetküldési entitások active replikálását mutatja be.

> [!NOTE]
> Az aktív replikációs megközelítés megduplázódik műveletek számát, ezért ez a módszer költségesebb vezethet.
> 
> 

### <a name="passive-replication"></a>Passzív replikáció
A tartalék ingyenes esetben passzív-replikáció az csak az egyik a két üzenetküldési entitások. Egy ügyfél az üzenetet küld az aktív entitás. Az aktív entitásra a művelet sikertelen, és egy hibakód, amely azt jelzi, elképzelhető, hogy az adatközpont, amelyen az aktív entitás nem érhető el, ha az ügyfél az üzenet másolatot küld a biztonsági mentési entitás. Ezen a ponton az aktív és a biztonsági mentési entitásokon vált szerepkörök: a küldő ügyfél is figyelembe veszi a régi aktív entitás kell az új biztonsági mentési entitáshoz, és a régi biztonsági mentési entitás az új aktív entitás. Műveletek sikertelenek is küld, ha a szerepköröket a két entitás változatlanok maradnak, és hibát ad vissza.

Egy ügyfél mindkét üzenetsorok fogad üzeneteket. Mivel előfordulhat, hogy a fogadó ugyanazon üzenet két példányt kap, a fogadó ismétlődő üzeneteket kell mellőzése. Ismétlődő ugyanúgy tilthatja le az aktív replikációs leírtak szerint.

Általánosságban elmondható passzív replikáció azért gazdaságosabb, mint az aktív replikációs, mert a legtöbb esetben csak egy művelet végrehajtása után. A nem replikált forgatókönyv késés, átviteli sebesség és a költség megegyeznek.

Passzív replikáció használata esetén a következő esetekben üzenetek néha elvész, vagy kétszer érkezett:

* **Üzenet késése vagy az adatvesztés**: Tegyük fel, hogy a küldő egy üzenetet m1 sikeresen elküldte az elsődleges üzenetsornak, és ezután a várólista elérhetetlenné válik a címzett megkapja az m1 előtt. A küldő a következő üzenetet m2 küld a másodlagos üzenetsor. Az elsődleges üzenetsornak átmenetileg nem érhető el, ha a fogadó m1 kap, miután újra elérhetővé a várólista válik. Egy esetleges vészhelyzet esetén a fogadó soha nem jelenhet meg az M1 verzióban.
* **Ismétlődő fogadás**: Tegyük fel, hogy a küldő millió üzenetet küld az elsődleges üzenetsornak. A Service Bus sikeresen m dolgozza fel, de nem tud választ küldeni. Miután a küldési művelet időkorlátja lejár, a küldő m egyforma példányával küld a másodlagos üzenetsor. Ha a fogadó tudja fogadni az m első példányát, mielőtt az elsődleges üzenetsornak elérhetetlenné válik, a fogadó kap mindkét másolat m körülbelül egy időben. Ha a címzett nem tudja fogadni az m első példányát, mielőtt az elsődleges üzenetsornak elérhetetlenné válik, a fogadó csak a második példányt m kezdetben kap, de majd m második másolatot kap, amikor az elsődleges üzenetsornak elérhetővé válik.

A [a Service Bus Standard szintű georeplikáció] [ Geo-replication with Service Bus Standard Tier] üzenetküldési entitások passzív replikálását mutatja be.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Adatközpont-leállások és katasztrófák elleni továbbítási végpontok védelme
A továbbítási végpontok georeplikáció lehetővé teszi, hogy egy szolgáltatás, amely a továbbító végpont zajok mellett a Service Bus leállásainak elérhetőnek kell lennie. Georeplikáció elérése érdekében a szolgáltatás létre kell hoznia két továbbítási végpontok különböző névterekben. A névterek kell lennie, különböző adatközpontokban, és a két végpontot nevének eltérőnek kell lennie. Például egy elsődleges végpont érhető el a **contosoPrimary.servicebus.windows.net/myPrimaryService**, míg a másodlagos párjukhoz alatt elérhető **contosoSecondary.servicebus.windows.net /mySecondaryService**.

Ezután figyeli a szolgáltatás mindkét végponton, és a egy ügyfél hívhat meg mindkét végponton keresztül a szolgáltatás. Egy ügyfélalkalmazás véletlenszerűen választja ki a továbbítók egyikét, az elsődleges végpontot, és a kérést küld az aktív végpontot. A művelet egy hibakóddal meghiúsul, ha ez a hiba azt jelzi, hogy a relay-végpont nem érhető el. Az alkalmazás megnyílik a biztonsági mentési végpont egy csatornát, és a kérés reissues. Ezen a ponton az aktív és a biztonsági mentési végpontok váltson szerepkörök: az ügyfélalkalmazás is figyelembe veszi a régi aktív végpontot, hogy az új biztonsági mentési végpont, és a régi biztonsági mentési végpontot, hogy az új aktív végpontnak lennie. Műveletek sikertelenek is küld, ha a szerepköröket a két entitás változatlanok maradnak, és hibát ad vissza.

## <a name="next-steps"></a>További lépések
Vész-helyreállítási kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [Az Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md)
* [Az Azure SQL Database üzletmenet-folytonossági][Azure SQL Database Business Continuity]
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
