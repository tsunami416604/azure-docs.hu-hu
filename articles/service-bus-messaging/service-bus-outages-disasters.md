---
title: "Azure Service Bus-alkalmazások a kimaradások és vészhelyzetek szigetelő |} Microsoft Docs"
description: "Technikák alkalmazások egy Service Bus esetleges leállás elleni védelmét."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fd9fa8ab-f4c4-43f7-974f-c876df1614d4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 7b01412202b5091ad3ae420089049bf456f9a30b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>A Service Bus kimaradások és vészhelyzetek alkalmazások szigetelő ajánlott eljárásai

Kritikus fontosságú alkalmazások folyamatosan, még a nem tervezett leállások vagy vészhelyzetek mellett kell működnie. Ez a témakör ismerteti a Service Bus-alkalmazást egy potenciális szolgáltatáskimaradás vagy katasztrófa elleni védelméhez használható megoldásokat.

Nem tervezett kimaradás az Azure Service Bus elérhetetlenség típusúként van definiálva. A leállás hatással lehet egyes összetevői a Service Bus, például egy üzenetküldési tárolóban, vagy akár a teljes adatközpont. A probléma kijavítása után a Service Bus ismét elérhetővé válik. Nem tervezett kimaradás általában nem okoznak üzenetek vagy egyéb adatok elvesztését. Példa egy összetevő meghibásodása esetén: egy adott üzenetküldési tárolóban elérhetetlensége. Példa egy datacenter kiterjedő leállás: az adatközpontban, vagy egy hibás datacenter hálózati kapcsoló áramszünet esetén. Kimaradás a néhány nap múlva néhány percig is tarthat.

Egy olyan vészhelyzet esetén a végleges adatvesztést egy Service Bus skálázási egység vagy datacenter típusúként van definiálva. A datacenter is, vagy előfordulhat, hogy nem ismét elérhetővé válik. Egy olyan vészhelyzet esetén általában néhány vagy az összes üzenetek vagy egyéb adatok elvesztését okozza. Katasztrófák példák tűz, elárasztás vagy földrengés.

## <a name="current-architecture"></a>Aktuális architektúrája
A Service Bus üzenetsorok és témakörök küldött üzenetek tárolására használja több üzenetküldési tároló. Nem particionált üzenetsor vagy témakör egyetlen üzenetküldési tárolóra van hozzárendelve. Az üzenetküldési tárolóban nem érhető el, ha az adott üzenetsor vagy témakör összes művelet sikertelen lesz.

Az összes Service Bus üzenetküldési entitásokat (üzenetsorok, témakörök, továbbítók) egy névtér, amely tagja-e a datacenter találhatók. A Service Bus most támogatja [ *földrajzi-vész-helyreállítási* és *georeplikáció* ](service-bus-geo-dr.md) a névterek szintjén.

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Üzenetsorok és témakörök üzenetküldési tároló hibák elleni védelme
Nem particionált üzenetsor vagy témakör egyetlen üzenetküldési tárolóra van hozzárendelve. Az üzenetküldési tárolóban nem érhető el, ha az adott üzenetsor vagy témakör összes művelet sikertelen lesz. A particionált várólista, másrészt több töredék áll. Minden egyes töredék egy másik üzenetküldési tárolóban van tárolva. Amikor egy üzenetet küld egy particionált üzenetsor vagy témakör, a Service Bus rendel az üzenet a töredék egyikéhez. A megfelelő üzenetküldési tárolóban nem érhető el, ha a Service Bus ír az üzenet egy másik kódrészletet, ha lehetséges. Particionált entitások kapcsolatos további információkért lásd: [particionált üzenetküldési entitások][Partitioned messaging entities].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Védelem datacenter kimaradások vagy vészhelyzetek ellen
Engedélyezi a feladatátvételre két adatközpont között, hozzon létre egy Service Bus-szolgáltatásnévtér minden adatközpontban. Például a Service Bus-szolgáltatásnévtér **contosoPrimary.servicebus.windows.net** lehet, hogy az Amerikai Egyesült Államok északi/központi régióban található, és **contosoSecondary.servicebus.windows.net** lehet, hogy a US Dél/központi régióban található. Ha egy Service Bus üzenetküldési entitás egy datacenter leállás mellett hozzáférhetőnek kell maradnia, mindkét névtérre is létrehozhat, hogy az entitás.

További információkért lásd: a "Service Bus egy Azure-adatközpontban belül hiba esetén" című szakaszában [aszinkron üzenetkezelési mintákat és magas rendelkezésre állású][Asynchronous messaging patterns and high availability].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Datacenter valamilyen okból kimaradás vagy vészhelyzetek ellen továbbítási végpontok védelme
A továbbítási végpontok georeplikáció lehetővé teszi, hogy egy szolgáltatás, amely elérhetővé teszi a továbbítási végpont mellett a Service Bus kimaradások elérhetőnek kell lennie. A georeplikáció eléréséhez a szolgáltatás létre kell hoznia két továbbítási végpontok különböző névterekben. A névterek különböző adatközpontokban kell lennie, és a két végpont neve nem lehet ugyanaz. Például egy elsődleges végpont elérhető alatt **contosoPrimary.servicebus.windows.net/myPrimaryService**, míg a másodlagos párjukhoz alatt elérhető **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Mindkét végpont követően figyeli a szolgáltatás, és egy ügyfél hívhat meg a szolgáltatás vagy a végpont keresztül. Egy ügyfélalkalmazás véletlenszerűen választja ki a továbbítók egyik elsődleges végpontjának, és a kérést küld az aktív végpontot. A művelet sikertelen, és egy hibakódot, ha ez a hiba azt jelzi, hogy a továbbítási végpont nem érhető el. Az alkalmazás megnyit egy csatornát a biztonsági mentési végpontot, és a kérelem reissues. Ezen a ponton az aktív és a biztonsági mentési végpontok kapcsoló szerepkörök: az ügyfélalkalmazás úgy ítéli meg, a régi aktív végpontja. az új biztonsági mentési végpont és az új aktív végpontot bizonyult a régi biztonsági mentési végpontot kell lennie. Ha mindkét küldött műveletek sikertelenek, a szerepkörök két entitások változatlan marad, és hibát ad vissza.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Üzenetsorok és témakörök datacenter valamilyen okból kimaradás vagy vészhelyzetek ellen védi
A Service Bus eléréséhez rugalmasság datacenter kimaradások szemben ha használatával közvetítőalapú üzenettovábbítás, támogatja a két módszer: *aktív* és *passzív* replikációs. Az egyes módszert használja Ha az adott üzenetsor vagy témakör mellett a adatközpontban szolgáltatáskimaradás hozzáférhetőnek kell maradnia, a hozhatja létre mindkét névtérre. Mindkét entitások lehet-e azonos nevük. Elsődleges queue alatt elérhető például **contosoPrimary.servicebus.windows.net/myQueue**, míg a másodlagos párjukhoz alatt elérhető **contosoSecondary.servicebus.windows.net/myQueue**.

Ha az alkalmazás nem igényli állandó küldő fogadó kommunikáció, az alkalmazás egy tartós ügyféloldali várólista üzenet az adatvesztés elkerülése érdekében és védelmének beállítása a küldőnek a Service Bus átmeneti hiba is létrehozható.

## <a name="active-replication"></a>Aktív replikációs
Aktív replikációs mindkét névtérre entitások minden művelethez használja. Bármely ügyfél által küldött üzenet két példányban ugyanazt az üzenetet küld. Az első másolatot kap az elsődleges entitás (például **contosoPrimary.servicebus.windows.net/sales**), és az üzenet második példányát küldi el a másodlagos entitásra (például **contosoSecondary.servicebus.windows.net/sales**).

Egy ügyfél üzeneteket fogad mindkét várólisták. A fogadó dolgozza fel az üzenet első példányát, és a másodpéldány le van tiltva. Arra, hogy letiltsa a duplikált üzenetek, a küldő minden üzenetet egyedi azonosítóval kell címkét. Az üzenet mindkét másolat ugyanezzel az azonosítóval kell megjelölni. Használhatja a [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] vagy [BrokeredMessage.Label] [ BrokeredMessage.Label] tulajdonságait, vagy az üzenet címkét az egyéni tulajdonsághoz. A fogadó kezelnie kell az üzeneteket, amelyek már kapott.

A [a Service Bus Közvetítőalapú üzenetek georeplikáció] [ Geo-replication with Service Bus Brokered Messages] az üzenetküldési entitások aktív replikációs mutatja be.

> [!NOTE]
> Az aktív replikációs módszerrel megduplázódik műveletek számát, ezért ez a megközelítés költségesebb vezethet.
> 
> 

## <a name="passive-replication"></a>Passzív replikáció
A tartalék szabad esetben passzív replikációs csak egyikét használja a két üzenetküldési entitások. Egy ügyfél az üzenet elküldése az aktív entitás. Az aktív entitáson művelet sikertelen, és egy hibakódot, amely jelzi, lehet, hogy az adatközpont, amelyen az aktív entitás nem érhető el, ha az ügyfél elküldi a biztonsági mentési entitásra az üzenet másolatának elhelyezését. Ezen a ponton az aktív és a biztonsági mentési entitásokat kapcsoló szerepkörök: a küldő ügyfél úgy ítéli meg, a régi aktív entitás új biztonsági mentési személlyel, és a régi biztonsági mentés az új aktív entitás. Ha mindkét küldött műveletek sikertelenek, a szerepkörök két entitások változatlan marad, és hibát ad vissza.

Egy ügyfél üzeneteket fogad mindkét várólisták. Mivel lehetséges, hogy a fogadó két példányt ugyanazt az üzenetet kap, a fogadó kell tiltja le a duplikált. Ismétlődő ugyanúgy tilthatja le az aktív replikációs leírtak szerint.

Passzív replikációs általában gazdaságosabb, mint az aktív replikációs, mert a legtöbb esetben csak egy művelet. A nem replikált forgatókönyv késést, az átviteli sebesség és a pénzügyi költség megegyeznek.

Ha a passzív replikációt használ, a következő esetekben üzenetek néha elvész, vagy kétszer érkezett:

* **Üzenet késleltetés vagy adatvesztés**: feltételezi, hogy a küldő sikeresen elküldte egy üzenet m1 az elsődleges üzenetsorba, és ezután a várólista nem érhető el a címzett megkapja az m1 előtt. A küldő a következő üzenetet m2 küld a másodlagos várólista. Ha az elsődleges várólista átmenetileg nem érhető el, a fogadó m1 kap, után a várólista újra lesz elérhető. Egy vészhelyzet esetén a fogadó soha nem jelenhet meg m1.
* **Ismétlődő fogadása**: azt feltételezik, hogy a küldő egy üzenetet m elsődleges várólistára. A Service Bus sikeresen feldolgozza m, de nem választ küld. Miután a küldési művelet időkorlátja lejár, a küldő m azonos példányának küld a másodlagos várólista. Ha a címzett képes m első példányát fogadja, mielőtt az elsődleges queue elérhetetlenné válik, a fogadó kap mindkét másolat m körülbelül egy időben. Ha a címzett nem képes m első példányát fogadja, mielőtt az elsődleges queue elérhetetlenné válik, a fogadó kezdetben az m csak a második másolatot kap, de m másodpéldányának majd kap, amikor az elsődleges queue elérhetővé válik.

A [georeplikálási a Service Bus közvetítőalapú üzenetek] [ Geo-replication with Service Bus Brokered Messages] az üzenetküldési entitások passzív replikációs mutatja be.

## <a name="geo-replication"></a>Georeplikáció

A Service Bus földrajzi-vész-helyreállítási és georeplikáció, a névterek szintjén támogatja. További információkért lásd: [Azure Service Bus földrajzi-vész-helyreállítási](service-bus-geo-dr.md). A vész helyreállítási szolgáltatás érhető el a [Premium Termékváltozat](service-bus-premium-messaging.md) csak, metaadatok vész-helyreállítási valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterek támaszkodik.

## <a name="next-steps"></a>További lépések
Vész-helyreállítási kapcsolatos további tudnivalókért tekintse meg az alábbi cikkek:

* [Az Azure Service Bus földrajzi-vész-helyreállítási](service-bus-geo-dr.md)
* [Az Azure SQL Database üzletmenet folytonossága][Azure SQL Database Business Continuity]
* [Az Azure-rugalmas alkalmazások tervezése][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency
