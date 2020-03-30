---
title: Az Azure Service Bus geokatasztrófa-helyreállítása | Microsoft dokumentumok
description: Földrajzi régiók használata feladatátvételhez és vészhelyreállítás végrehajtásához az Azure Service Busban
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259577"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Az Azure Service Bus geokatasztrófa-helyreállítása

Ha teljes Azure-régiók vagy adatközpontok (ha nincs [rendelkezésre állási zónák)](../availability-zones/az-overview.md) állásidőt tapasztal, fontos, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Mint ilyen, *a geo-katasztrófa-helyreállítási* fontos jellemzője minden vállalat számára. Az Azure Service Bus támogatja a földrajzi vészhelyreállítási a névtér szintjén.

A geo-vész-helyreállítási funkció globálisan elérhető a Service Bus Premium Termékváltozat. 

>[!NOTE]
> A geo-katasztrófa-helyreállítás jelenleg csak azt biztosítja, hogy a metaadatok (várólisták, témakörök, előfizetések, szűrők) párosításkor átmásolja az elsődleges névtérből a másodlagos névtérbe.

## <a name="outages-and-disasters"></a>Kimaradások és katasztrófák

Fontos megjegyezni, hogy különbséget kell tenni a "kimaradások" és a "katasztrófák" között. 

A *szolgáltatáskimaradás* az Azure Service Bus ideiglenes elérhetetlensége, és hatással lehet a szolgáltatás egyes összetevőire, például egy üzenettárolóra, vagy akár a teljes adatközpontra. A probléma megoldása után azonban a Service Bus ismét elérhetővé válik. A kimaradás általában nem okoz üzenetek vagy más adatok elvesztését. Egy ilyen kimaradás lehet áramkimaradás az adatközpontban. Egyes kimaradások csak rövid kapcsolat veszteségek átmeneti vagy hálózati problémák miatt. 

A *katasztrófa* definíció szerint a Service Bus-fürt, az Azure-régió vagy az adatközpont állandó vagy hosszabb távú elvesztése. A régió vagy az adatközpont lehet, hogy nem lesz elérhető újra, vagy lehet, hogy le órákig vagy napokig. Ilyen katasztrófák például a tűz, az árvíz vagy a földrengés. Állandóvá válik katasztrófa egyes üzenetek, események vagy egyéb adatok elvesztését okozhatja. A legtöbb esetben azonban nem lehet adatvesztés, és az üzenetek az adatközpont biztonsági csinálása után helyreállnak.

Az Azure Service Bus geo-vészhelyreállítási szolgáltatása vész-helyreállítási megoldás. A jelen cikkben ismertetett fogalmak és munkafolyamatok a katasztrófa-forgatókönyvekre vonatkoznak, nem pedig átmeneti vagy ideiglenes kimaradásokra. A Vészhelyreállítás microsoft Azure-beli részletes ismertet [ebben a cikkben](/azure/architecture/resiliency/disaster-recovery-azure-applications)olvashat.   

## <a name="basic-concepts-and-terms"></a>Alapfogalmak és kifejezések

A vész-helyreállítási szolgáltatás metaadat-vész-helyreállítási, és támaszkodik az elsődleges és másodlagos vész-helyreállítási névterek. Vegye figyelembe, hogy a geo-vész-helyreállítási funkció csak a [prémium szintű termékváltozathoz](service-bus-premium-messaging.md) érhető el. Nem kell módosítania a kapcsolati karakterláncot, mivel a kapcsolat aliason keresztül történik.

A cikk a következő kifejezéseket használja:

-  *Alias*: A létrehozott vész-helyreállítási konfiguráció neve. Az alias egyetlen stabil, teljesen minősített tartománynév (FQDN) kapcsolati karakterláncot biztosít. Az alkalmazások ezt az alias kapcsolati karakterláncot használják a névtérhez való csatlakozáshoz. Alias használatával biztosítja, hogy a kapcsolati karakterlánc változatlan marad, amikor a feladatátvétel aktiválódik.

-  *Elsődleges/másodlagos névtér*: Az aliasnak megfelelő névterek. Az elsődleges névtér "aktív", és üzeneteket fogad (ez lehet meglévő vagy új névtér). A másodlagos névtér "passzív", és nem fogad üzeneteket. A két metaadat szinkronban van, így mindkettő zökkenőmentesen fogadhatja az üzeneteket alkalmazáskód vagy kapcsolati karakterlánc módosítása nélkül. Annak érdekében, hogy csak az aktív névtér fogadjon üzeneteket, az aliast kell használnia. 

-  *Metaadatok:* Entitások, például várólisták, témakörök és előfizetések; és a szolgáltatás névtérhez társított tulajdonságai. Ne feledje, hogy csak az entitások és azok beállításai replikálódnak automatikusan. Az üzenetek replikálása nem ismétlődik.

-  *Feladatátvétel*: A másodlagos névtér aktiválásának folyamata.

## <a name="setup"></a>Telepítés

A következő szakasz áttekintést nyújt a névterek közötti beállításpárosításról.

![1][]

A beállítási folyamat a következő -

1. ***Elsődleges*** szolgáltatásbusz prémium névtere kiépítése.

2. ***Másodlagos*** Service Bus Premium-névtér kiépítése az *elsődleges névtér kiépített helyétől eltérő*régióban. Ez lehetővé teszi a hibák elkülönítését a különböző adatközpont-régiók között.

3. Hozzon létre párosítást az elsődleges névtér és a másodlagos névtér között az ***alias***beszerzéséhez.

    >[!NOTE] 
    > Ha [áttelepítette az Azure Service Bus Standard névterét az Azure Service Bus Premium szolgáltatásba,](service-bus-migrate-standard-premium.md)akkor a már meglévő aliast (azaz a Service Bus Standard névtér kapcsolati karakterláncát) kell használnia a vész-helyreállítási konfiguráció létrehozásához a **PS/CLI** vagy **a REST API-n**keresztül.
    >
    >
    > Ennek az az oka, hogy az áttelepítés során az Azure Service Bus Standard névtér-kapcsolat karakterlánca/DNS-neve maga is az Azure Service Service Premium névtér aliasává válik.
    >
    > Az ügyfélalkalmazásoknak ezt az aliast (azaz az Azure Service Bus Standard névtér kapcsolati karakterláncát) kell használniuk a prémium szintű névtérhez való csatlakozáshoz, ahol a vész-helyreállítási párosítás beállítása történt.
    >
    > Ha a portál segítségével állítsa be a vész-helyreállítási konfiguráció, majd a portál absztrakt ezt a kikötést tőled.


4. A ***alias*** 3. Kezdetben az alias az elsődleges névtérre mutat.

5. [Nem kötelező] Adjon hozzá néhány figyelést, hogy észlelje, ha feladatátvételre van szükség.

## <a name="failover-flow"></a>Feladatátvételi folyamat

A feladatátvételt manuálisan váltja ki az ügyfél (akár explicit módon egy parancs, vagy az ügyfél tulajdonában lévő üzleti logika, amely elindítja a parancsot), és soha nem az Azure által. Ez teljes tulajdonjogot és láthatóságot biztosít az ügyfél számára az Azure gerincén a kimaradás feloldásához.

![4][]

A feladatátvétel aktiválása után -

1. Az ***alias*** kapcsolati karakterlánca frissül, hogy a Másodlagos prémium szintű névtérre mutasson.

2. Az ügyfelek (feladók és fogadók) automatikusan csatlakoznak a másodlagos névtérhez.

3. Az elsődleges és másodlagos prémium szintű névtér közötti meglévő párosítás megszakadt.

A feladatátvétel megkezdése után -

1. Ha egy másik kimaradás történik, azt szeretné, hogy képes legyen átvenni újra. Ezért állítson be egy másik passzív névteret, és frissítse a párosítást. 

2. Üzenetek lekérése a korábbi elsődleges névtérből, amint az ismét elérhetővé válik. Ezt követően használja ezt a névteret a geo-helyreállítási beállításon kívüli rendszeres üzenetküldéshez, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a sikertelen továbbító szemantika támogatott. Ebben az esetben adja át a feladatátvételt, majd párosítsa újra egy új névtérrel. A visszanemében nem támogatott a hiba. például egy SQL-fürtben. 

A feladatátvételt monitorozási rendszerekkel vagy egyéni tervezési megoldásokkal automatizálhatja. Az ilyen automatizálás azonban extra tervezést és munkát igényel, amely kívül esik a cikk hatókörén.

![2][]

## <a name="management"></a>Kezelés

Ha hibázott; például a kezdeti beállítás során nem a megfelelő területeket párosította, bármikor megszakíthatja a két névtér párosítását. Ha a párosított névtereket normál névterekként szeretné használni, törölje az aliast.

## <a name="use-existing-namespace-as-alias"></a>Meglévő névtér használata aliasként

Ha olyan forgatókönyv ehelyzet, amelyben nem módosíthatja a gyártók és a fogyasztók kapcsolatait, újra felhasználhatja a névtér nevét aliasnévként. Lásd a [mintakódot a GitHubon itt.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)

## <a name="samples"></a>Példák

A [GitHub-on lévő minták](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) bemutatják, hogyan állíthatja be és kezdeményezhet feladatátvételt. Ezek a minták a következő fogalmakat mutatják be:

- A .NET minta és a beállításokat, amelyek szükségesek az Azure Active Directoryban az Azure Resource Manager service bus használatával, a geo-katasztrófa utáni helyreállítás beállításához és engedélyezéséhez.
- A mintakód végrehajtásához szükséges lépések.
- Meglévő névtér használata aliasként.
- A geo-vészhelyreállítás PowerShellen vagy CLI-n keresztüli engedélyezésének lépései.
- [Küldés és fogadás](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) az aktuális elsődleges vagy másodlagos névtérből az alias használatával.

## <a name="considerations"></a>Megfontolandó szempontok

Ezt a kiadást vegye figyelembe:

1. A feladatátvétel i. Ha például 15–20 percnél hosszabb ideig veszíti el a kapcsolatot, dönthet úgy, hogy kezdeményezi a feladatátvételt.

2. Az a tény, hogy nincs adat replikálása azt jelenti, hogy a jelenleg aktív munkamenetek nem replikálódik. Emellett előfordulhat, hogy a duplikáltelemek és az ütemezett üzenetek nem működnek. Az új munkamenetek, az új ütemezett üzenetek és az új ismétlődések működni fognak. 

3. Egy összetett elosztott infrastruktúra nem megfelelő feladatát, legalább egyszer el kell [próbálni.](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)

4. Az entitások szinkronizálása eltarthat egy ideig, körülbelül 50–100 entitás percenként. Az előfizetések és a szabályok is entitásoknak számítanak.

## <a name="availability-zones"></a>Rendelkezésre állási zónák

A Service Bus Premium Termékváltozat is támogatja [a rendelkezésre állási zónák,](../availability-zones/az-overview.md)amely hiba-elszigetelt helyek egy Azure-régióban.

> [!NOTE]
> Az Azure Service Bus Premium rendelkezésre állási zónák támogatása csak olyan [Azure-régiókban](../availability-zones/az-overview.md#services-support-by-region) érhető el, ahol rendelkezésre állási zónák vannak jelen.

Csak az Azure Portal használatával engedélyezheti a rendelkezésre állási zónákat az új névterekben. A Service Bus nem támogatja a meglévő névterek áttelepítését. A zónaredundancia nem tiltható le, miután engedélyezte azt a névtérben.

![3][]

## <a name="next-steps"></a>További lépések

- Lásd a geo-vész-helyreállítási [REST API referencia itt](/rest/api/servicebus/disasterrecoveryconfigs).
- Futtassa a geo-vészhelyreállítási [mintát a GitHubon.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)
- Tekintse meg a geo-vészhelyreállítási [mintát, amely üzeneteket küld egy alias .](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi cikkeket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
