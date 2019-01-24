---
title: Az Azure Service Bus Geo-disaster recovery |} A Microsoft Docs
description: Feladatátvétel földrajzi régiót használnak, és hajtsa végre a vészhelyreállítás az Azure Service Busban
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: d98ff2c5b9d18c36e7d16ec19d3e136be03b8d4c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848002"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Az Azure Service Bus Geo-disaster recovery

Ha a teljes Azure-régióban, vagy az adatközpontok (Ha nincs [rendelkezésre állási zónák](../availability-zones/az-overview.md) használt) leállás következik be, kritikus fontosságú adatok feldolgozásához, eltérő régióban vagy datacenter továbbra is. Emiatt a *Geo-disaster recovery* és *georeplikációs* bármely vállalat számára fontos funkciók. Az Azure Service Bus geo-vészhelyreállítás és georeplikáció útján, a névterek szintjén is támogatja. 

A Geo-disaster recovery funkció érhető el globálisan a Service Bus prémium szintű Termékváltozat. 

## <a name="outages-and-disasters"></a>Leállások és katasztrófák kezelése

Fontos megjegyzés: a "leállások" és "katasztrófák." megkülönböztetése Egy *szolgáltatáskimaradás* az átmeneti elérhetetlensége, valamint az Azure Service Bus, és hatással lehetnek egyes összetevői a szolgáltatás, például egy üzenetküldési tárolóban, vagy akár az egész adatközpontot. Azonban Miután a problémát, a Service Bus ismét elérhetővé válik. Egy kimaradás általában nem okoz az üzenetek vagy egyéb adatok elvesztését. Egy példa az ilyen kimaradás lehet áramszünet esetén az adatközpontban. Néhány leállások veszteségként csak rövid kapcsolat átmeneti vagy hálózati problémák miatt. 

A *vészhelyreállítási* számít, ha egy Service Bus fürt, az Azure-régió vagy datacenter állandó, illetve a hosszabb távú elvesztését. A régió vagy az Adatközpont előfordulhat, hogy előfordulhat, hogy nem ismét elérhetővé válik, vagy előfordulhat, hogy nem működik az óra vagy nap. Az ilyen katasztrófák példák fire, -elárasztás vagy földrengés. Olyan állandó válik az egyes üzenetek, események vagy egyéb adatok elvesztését okozhatja. Azonban a legtöbb esetben kell adatvesztés nélküli, és üzeneteket helyreállíthatók legyenek, amint az Adatközpont biztonsági mentése.

A Geo-disaster recovery funkció az Azure Service Bus egy vész-helyreállítási megoldást. A fogalmakat és az ebben a cikkben leírt munkafolyamatot alkalmazni a vészhelyreállítási forgatókönyveket, és nem átmeneti vagy ideiglenes valamilyen okból kimaradás lép. A Microsoft Azure-ban vész-helyreállítási részletes tárgyalását lásd: [Ez a cikk](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Alapfogalommal és kifejezéssel

A vész-helyreállítási szolgáltatás metaadatainak vész-helyreállítási valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterek támaszkodik. Vegye figyelembe, hogy a Geo-disaster recovery funkció érhető el a [prémium szintű Termékváltozat](service-bus-premium-messaging.md) csak. Nem kell, végezze el a kapcsolati karakterlánc módosításokat, a kapcsolat alias-n keresztül.

Ez a cikk a következő kifejezéseket használjuk:

-  *Alias*: Az Ön által beállított vészhelyreállítási konfiguráció neve. Az alias egyetlen stabil teljes tartománynévként (FQDN) kapcsolati karakterláncban biztosít. Alkalmazások ez alias a kapcsolati karakterlánc használatával csatlakozni a névtérhez. 

-  *Elsődleges és másodlagos névtér*: A névterek, amelyek megfelelnek a címre. Az elsődleges névtér "aktív", és fogadja az üzeneteket (Ez lehet egy meglévő vagy új névtér). A másodlagos névtérre "passzív", és nem fogadhat üzeneteket. A metaadatok között is szinkronizálva, így mindkettő is zökkenőmentesen fogadja az üzeneteket alkalmazás kódja vagy kapcsolati karakterlánc módosítása nélkül. Győződjön meg arról, hogy csak az aktív névteret fogadja az üzeneteket, az aliast kell használnia. 

-  *metaadatok*: Entitások, például az üzenetsorok, témakörök és előfizetések; és azok tulajdonságait a névtérhez társított szolgáltatás. Vegye figyelembe, hogy csak az entitások és a beállításaik automatikusan replikálja. Üzenetek nem lesznek replikálva. 

-  *Feladatátvétel*: Folyamat aktiválása a másodlagos névtér.

## <a name="setup-and-failover-flow"></a>A telepítő és a feladatátvételi folyamat

A következő szakaszban áttekintjük a feladatátvételi folyamat, és azt ismerteti, hogyan állítható be a kezdeti feladatátvétel. 

![1][]

### <a name="setup"></a>Beállítás

Először létrehoz vagy egy meglévő elsődleges névtér és a egy új másodlagos névteret, majd párosítsa a két. A párosítás kínál, amellyel csatlakozni alias. Alias használata, mert nem rendelkezik a kapcsolati karakterláncok módosítása. Csak új névteret a párosítás feladatátvételi lehet hozzáadni. Végül, hozzá kell adnia bizonyos figyelőket észleli, ha szükség-e a feladatátvételt. A legtöbb esetben a szolgáltatás kiterjedt ökoszisztémáját egy részét képezi, így az automatikus feladatátvételt is ritkán lehetségesek, például nagyon gyakran feladatátvételt kell végrehajtani szinkronban vannak a többi alrendszer vagy infrastruktúra.

### <a name="example"></a>Példa

Ebben a forgatókönyvben egy példa fontolja meg egy értékesítés pont (POS) megoldás, amely az üzenetek vagy a eseményeket bocsát ki. A Service Bus események néhány leképezési vagy a megoldást, amely ezután továbbítja a megfeleltetett adatokat további feldolgozás céljából egy másik rendszerre újraformázást továbbítja. Ezen a ponton az összes ezekben a rendszerekben előfordulhat, hogy futhat ugyanazon Azure-régióban. A döntést, mikor és milyen része a feladatátvételt az infrastruktúra adatáramlásra függ. 

Automatizálhatja a feladatátvétel rendszerek figyelése, vagy a személyre szabott figyelési megoldásokkal. Azonban az ilyen automation vesz igénybe, további tervezési és a munka, amely a jelen cikk hatókörébe esik.

### <a name="failover-flow"></a>Feladatátvétel folyamatábrája

Kezdeményezze a feladatátvételt, ha két lépésre szükség:

1. Egy másik kimaradás során, érdemes tudni átadja a feladatokat újra. Ezért egy másik passzív névtér beállítása, és frissítse a párosítást. 

2. Kérje le a korábbi elsődleges névtérből üzeneteket, ha megint elérhetővé válik. Ezt követően, hogy a névtér használhat rendszeres üzenetkezelési kívül a geo-helyreállítási beállításai, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a sikertelen előre szemantika támogatottak. Ebben a forgatókönyvben átadja a feladatokat, és ezután újból párosítsa egy új névteret. Visszavétel nem támogatott. Ha például az SQL-fürt. 

![2][]

## <a name="management"></a>Kezelés

Ha állított be; például, a nem megfelelő régiók párosítva a kezdeti beállítás során, a két névtér párosítási bármikor megszakítható. Ha azt szeretné, a párosított névterek adatokként rendszeres névterek, az alias törlése.

## <a name="use-existing-namespace-as-alias"></a>Használja a meglévő névtér vagy alias

Ha rendelkezik egy olyan forgatókönyvet, amelyben a kapcsolatok előállítók és fogyasztók nem módosítható, aliasneve, felhasználhatja a névtér nevét. Tekintse meg a [mintakód a Githubon Itt](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Példák

A [példák a Githubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) bemutatják, hogyan állíthatja be, és a feladatátvétel. Ezek a minták a következő fogalmakat mutatják be:

- Egy .NET-minta és Azure Resource Manager használata a Service Bus, és engedélyezze a Geo-disaster recovery az Azure Active Directory szükséges beállításokat.
- A mintakód végrehajtásához szükséges lépéseket.
- Hogyan használható egy meglévő névtér-alias.
- Másik lehetőségként a Geo-disaster recovery PowerShell vagy parancssori felület engedélyezésének lépései.
- [Küldési és fogadási](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) az aliast használni aktuális elsődleges vagy másodlagos névtér.

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe az alábbi szempontokat figyelembe kell venni ebben a kiadásban:

1. A feladatátvételi tervezés során is figyelembe kell venni az idő tényező. Például ha elveszíti a kapcsolatot a 15-20 percnél hosszabb ideig, dönthet, hogy kezdeményezze a feladatátvételt. 
 
2. Az a tény, hogy az adatok nem replikálódik, az azt jelenti, hogy jelenleg aktív munkamenetek nem lesznek replikálva. Ezenkívül duplikáltelem-észlelési és ütemezett üzenetek előfordulhat, hogy nem működik. Az új munkamenetek, új ütemezett üzenetek és új ismétlődések fog működni. 

3. Egy összetett elosztott infrastruktúra feladatátadás kell [kipróbálni](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) legalább egyszer. 

4. Entitások szinkronizálása körülbelül 50-100 entitást percenkénti némi időt is igénybe vehet. Az előfizetések és -szabályok is entitásokat is számítanak. 

## <a name="availability-zones-preview"></a>A rendelkezésre állási zónák (előzetes verzió)

A Service Bus prémium szintű Termékváltozat is támogatja a [rendelkezésre állási zónák](../availability-zones/az-overview.md), egy Azure-régión belüli, meghibásodásoktól elszigetelt helyek megadása. 

> [!NOTE]
> A rendelkezésre állási zónák előzetes verziója csak a támogatott a **USA középső RÉGIÓJA**, **USA keleti RÉGIÓJA 2**, és **közép-Franciaország** régióban.

Engedélyezheti a rendelkezésre állási zónák a csak az új névterek az Azure portal használatával. A Service Bus nem támogatja a meglévő névterek áttelepítésének. Miután engedélyezte a a névtérben nem tiltható le a zone redudancy.

![3][]

## <a name="next-steps"></a>További lépések

- Tekintse meg a Geo-disaster recovery [REST API-referenciában találhat](/rest/api/servicebus/disasterrecoveryconfigs).
- Futtassa a Geo-disaster recovery [mintát a Githubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Tekintse meg a Geo-disaster recovery [minta, amely üzeneteket küld egy aliast](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

További információ a Service Bus-üzenetkezelés, tekintse meg a következő cikkeket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST API-val](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png