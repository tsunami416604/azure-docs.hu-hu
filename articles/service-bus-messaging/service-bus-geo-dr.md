---
title: Azure Service Bus geo-vész-helyreállítás | Microsoft Docs
description: A földrajzi régiók használata feladatátvételhez és a vész-helyreállítás elvégzéséhez Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259577"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geo-vész-helyreállítás

Ha a teljes Azure-régiók vagy-adatközpontok (ha nincsenek használatban [rendelkezésre állási zónák](../availability-zones/az-overview.md) ) a tapasztalatok leállását tapasztalják, kritikus fontosságú, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Ennek megfelelően a *geo-vész-helyreállítás* bármely vállalat számára fontos funkció. Azure Service Bus támogatja a földrajzi katasztrófa utáni helyreállítást a névtér szintjén.

A Geo-vész-helyreállítási funkció globálisan elérhető a Service Bus Premium SKU-hoz. 

>[!NOTE]
> A Geo-vész-helyreállítás jelenleg csak a metaadatokat (várólisták, témakörök, előfizetések, szűrők) másolja át az elsődleges névtérről a másodlagos névtérre, ha párosítva van.

## <a name="outages-and-disasters"></a>Leállások és katasztrófák kezelése

Fontos megjegyzés: a "leállások" és "katasztrófák." megkülönböztetése 

A *leállás* a Azure Service Bus ideiglenes nem érhető el, és hatással lehet a szolgáltatás egyes összetevőire, például az üzenetküldési tárolóra vagy akár a teljes adatközpontra is. A probléma javítása után azonban Service Bus újra elérhetővé válik. Egy kimaradás általában nem okoz az üzenetek vagy egyéb adatok elvesztését. Egy példa az ilyen kimaradás lehet áramszünet esetén az adatközpontban. Néhány leállások veszteségként csak rövid kapcsolat átmeneti vagy hálózati problémák miatt. 

A *katasztrófa* a Service Bus-fürt, az Azure-régió vagy az adatközpont állandó vagy hosszú távú elvesztéseként van meghatározva. A régió vagy az Adatközpont előfordulhat, hogy előfordulhat, hogy nem ismét elérhetővé válik, vagy előfordulhat, hogy nem működik az óra vagy nap. Az ilyen katasztrófák példák fire, -elárasztás vagy földrengés. Olyan állandó válik az egyes üzenetek, események vagy egyéb adatok elvesztését okozhatja. Azonban a legtöbb esetben kell adatvesztés nélküli, és üzeneteket helyreállíthatók legyenek, amint az Adatközpont biztonsági mentése.

A Azure Service Bus geo-vész-helyreállítási funkciója vész-helyreállítási megoldás. A fogalmakat és az ebben a cikkben leírt munkafolyamatot alkalmazni a vészhelyreállítási forgatókönyveket, és nem átmeneti vagy ideiglenes valamilyen okból kimaradás lép. A Microsoft Azure vész-helyreállítási részletes megvitatását [ebben a cikkben](/azure/architecture/resiliency/disaster-recovery-azure-applications)találja.   

## <a name="basic-concepts-and-terms"></a>Alapfogalommal és kifejezéssel

A vész-helyreállítási szolgáltatás metaadatainak vész-helyreállítási valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterek támaszkodik. Vegye figyelembe, hogy a Geo-vész-helyreállítási funkció csak a [Premium SKU](service-bus-premium-messaging.md) esetében érhető el. Nem kell, végezze el a kapcsolati karakterlánc módosításokat, a kapcsolat alias-n keresztül.

Ez a cikk a következő kifejezéseket használjuk:

-  *Alias*: az Ön által beállított vész-helyreállítási konfiguráció neve. Az alias egyetlen stabil teljes tartománynévként (FQDN) kapcsolati karakterláncban biztosít. Alkalmazások ez alias a kapcsolati karakterlánc használatával csatlakozni a névtérhez. Az alias használatával biztosítható, hogy a kapcsolódási sztring változatlan maradjon a feladatátvétel elindításakor.

-  *Elsődleges/másodlagos névtér*: az aliasnak megfelelő névterek. Az elsődleges névtér "aktív", és fogadja az üzeneteket (Ez lehet egy meglévő vagy új névtér). A másodlagos névtérre "passzív", és nem fogadhat üzeneteket. A metaadatok között is szinkronizálva, így mindkettő is zökkenőmentesen fogadja az üzeneteket alkalmazás kódja vagy kapcsolati karakterlánc módosítása nélkül. Győződjön meg arról, hogy csak az aktív névteret fogadja az üzeneteket, az aliast kell használnia. 

-  *Metaadatok*: olyan entitások, mint a várólisták, témakörök és előfizetések; a névtérhez társított szolgáltatás tulajdonságai. Vegye figyelembe, hogy csak az entitások és a beállításaik automatikusan replikálja. Az üzenetek nem replikálódnak.

-  *Feladatátvétel*: a másodlagos névtér aktiválása folyamatban van.

## <a name="setup"></a>Beállítás

A következő szakasz áttekintést nyújt a névterek közötti párosítás beállításáról.

![1][]

A telepítési folyamat a következő:

1. ***Elsődleges*** Service Bus prémium szintű névtér kiépítése.

2. ***Másodlagos*** Service Bus Premium-névtér kiépítése egy olyan régióban, amely *eltér az elsődleges névtér*kiépítésének helyétől. Ez segít a hibák elkülönítésében a különböző adatközpont-régiók között.

3. Hozzon létre párosítást az elsődleges névtér és a másodlagos névtér között az ***alias***beszerzéséhez.

    >[!NOTE] 
    > Ha [áttelepítette a Azure Service Bus standard névteret a prémium szintű Azure Service Busre](service-bus-migrate-standard-premium.md), akkor a vész-helyreállítási konfigurációt a **PS/CLI** vagy a **REST API**használatával kell létrehoznia a korábban meglévő alias (azaz a Service Bus standard névtér-kapcsolódási karakterlánc) segítségével.
    >
    >
    > Ennek az az oka, hogy az áttelepítés során a Azure Service Bus a szabványos névtér-kapcsolódási karakterlánc/DNS-név alias lesz a Azure Service Bus Premium-névtérhez.
    >
    > Az ügyfélalkalmazások ezt az aliast kell használniuk (például Azure Service Bus a szabványos névtér-kapcsolati karakterláncot) ahhoz, hogy csatlakozni tudjanak a prémium szintű névtérhez, ahol a rendszer a vész-helyreállítási párosítást beállította.
    >
    > Ha a portál használatával adja meg a vész-helyreállítási konfigurációt, akkor a portál ezt a kikötést elküldi Önnek.


4. Használja a 3. lépésben beszerzett ***aliast*** az ügyfélalkalmazások a Geo-Dr-kompatibilis elsődleges névtérhez való összekapcsolásához. Az alias kezdetben az elsődleges névtérre mutat.

5. Választható Vegyen fel némi figyelést annak észleléséhez, hogy szükség van-e feladatátvételre.

## <a name="failover-flow"></a>Feladatátvétel folyamatábrája

A feladatátvételt manuálisan indítja el az ügyfél (explicit módon egy parancson keresztül, vagy az ügyfél által birtokolt üzleti logikán keresztül, amely elindítja a parancsot), és soha nem az Azure-ban. Így az ügyfél teljes körű tulajdonosi és láthatósági megoldást nyújt az Azure gerincén.

![4][]

A feladatátvétel elindítása után –

1. Az ***alias*** -kapcsolódási karakterlánc frissült, hogy a másodlagos prémium névtérre mutasson.

2. Az ügyfelek (küldők és fogadók) automatikusan csatlakoznak a másodlagos névtérhez.

3. Az elsődleges és a másodlagos prémium névtér közötti meglévő párosítás megszakadt.

A feladatátvétel kezdeményezése után –

1. Ha egy másik leállás következik be, azt szeretné, hogy újra lehessen adni a feladatátvételt. Ezért egy másik passzív névtér beállítása, és frissítse a párosítást. 

2. Kérje le a korábbi elsődleges névtérből üzeneteket, ha megint elérhetővé válik. Ezt követően, hogy a névtér használhat rendszeres üzenetkezelési kívül a geo-helyreállítási beállításai, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a sikertelen előre szemantika támogatottak. Ebben a forgatókönyvben átadja a feladatokat, és ezután újból párosítsa egy új névteret. Visszavétel nem támogatott. Ha például az SQL-fürt. 

Automatizálhatja a feladatátvétel rendszerek figyelése, vagy a személyre szabott figyelési megoldásokkal. Azonban az ilyen automation vesz igénybe, további tervezési és a munka, amely a jelen cikk hatókörébe esik.

![2][]

## <a name="management"></a>Kezelés

Ha állított be; például, a nem megfelelő régiók párosítva a kezdeti beállítás során, a két névtér párosítási bármikor megszakítható. Ha azt szeretné, a párosított névterek adatokként rendszeres névterek, az alias törlése.

## <a name="use-existing-namespace-as-alias"></a>Meglévő névtér használata aliasként

Ha olyan helyzettel rendelkezik, amelyben nem módosíthatja a termelők és a felhasználók kapcsolatait, akkor a névtér nevét felhasználhatja alias neveként. Tekintse [meg a githubhoz tartozó mintakód itt](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Példák

A [githubon található minták](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) bemutatják, hogyan kell beállítani és kezdeményezni a feladatátvételt. Ezek a minták a következő fogalmakat szemléltetik:

- .NET-minta és-beállítások, amelyek szükségesek ahhoz, hogy a Azure Active Directory Azure Resource Manager a Service Bus használatával a földrajzi katasztrófák helyreállításának beállításához és engedélyezéséhez.
- A mintakód végrehajtásához szükséges lépéseket.
- Meglévő névtér használata aliasként.
- A következő lépésekkel engedélyezheti a földrajzi katasztrófa utáni helyreállítást a PowerShell vagy a parancssori felület használatával.
- [Küldés és fogadás](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) az aktuális elsődleges vagy másodlagos névtérből az alias használatával.

## <a name="considerations"></a>Megfontolások

Vegye figyelembe az alábbi szempontokat figyelembe kell venni ebben a kiadásban:

1. A feladatátvételi tervezés során is figyelembe kell venni az idő tényező. Például ha elveszíti a kapcsolatot a 15-20 percnél hosszabb ideig, dönthet, hogy kezdeményezze a feladatátvételt.

2. Az a tény, hogy az adatok nem replikálódik, az azt jelenti, hogy jelenleg aktív munkamenetek nem lesznek replikálva. Ezenkívül duplikáltelem-észlelési és ütemezett üzenetek előfordulhat, hogy nem működik. Az új munkamenetek, az új ütemezett üzenetek és az új ismétlődések is működni fognak. 

3. Egy összetett elosztott infrastruktúra feladatátvétele legalább egyszer [kipróbálható](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) .

4. Entitások szinkronizálása körülbelül 50-100 entitást percenkénti némi időt is igénybe vehet. Az előfizetések és a szabályok entitásként is számítanak.

## <a name="availability-zones"></a>Rendelkezésre állási zónák

A Service Bus Premium SKU a [Availability Zonest](../availability-zones/az-overview.md)is támogatja, amely az Azure-régióban található, hibátlanul elszigetelt helyszíneket biztosít.

> [!NOTE]
> A prémium szintű Azure Service Bus Availability Zones támogatása csak olyan Azure- [régiókban](../availability-zones/az-overview.md#services-support-by-region) érhető el, ahol rendelkezésre áll a rendelkezésre állási zónák.

Engedélyezheti a rendelkezésre állási zónák a csak az új névterek az Azure portal használatával. A Service Bus nem támogatja a meglévő névterek áttelepítését. Miután engedélyezte a a névtérben nem tiltható le a zone redudancy.

![3][]

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a Geo-vész-helyreállítási [REST API referenciát](/rest/api/servicebus/disasterrecoveryconfigs).
- Futtassa a Geo-vész-helyreállítási [mintát a githubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Tekintse meg a földrajzi katasztrófa utáni helyreállítási [mintát, amely üzeneteket küld egy aliasnak](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
