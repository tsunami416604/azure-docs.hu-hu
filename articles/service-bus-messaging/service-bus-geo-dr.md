---
title: Azure Service Bus geo-vész-helyreállítás | Microsoft Docs
description: A földrajzi régiók használata feladatátvételhez és a vész-helyreállítás elvégzéséhez Azure Service Bus
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: fcdeb499b8ebecc4ecddbfcbe32b812ce7e3efe5
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85341483"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geo-vész-helyreállítás

Ha a teljes Azure-régiók vagy-adatközpontok (ha nincsenek használatban [rendelkezésre állási zónák](../availability-zones/az-overview.md) ) a tapasztalatok leállását tapasztalják, kritikus fontosságú, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Ennek megfelelően a *geo-vész-helyreállítás* bármely vállalat számára fontos funkció. Azure Service Bus támogatja a földrajzi katasztrófa utáni helyreállítást a névtér szintjén.

A Geo-vész-helyreállítási funkció globálisan elérhető a Service Bus Premium SKU-hoz. 

>[!NOTE]
> A Geo-vész-helyreállítás jelenleg csak a metaadatokat (várólisták, témakörök, előfizetések, szűrők) másolja át az elsődleges névtérről a másodlagos névtérre, ha párosítva van.

## <a name="outages-and-disasters"></a>Kimaradások és katasztrófák

Fontos megjegyezni az "kimaradások" és a "katasztrófák" közötti különbséget. 

A *leállás* a Azure Service Bus ideiglenes nem érhető el, és hatással lehet a szolgáltatás egyes összetevőire, például az üzenetküldési tárolóra vagy akár a teljes adatközpontra is. A probléma javítása után azonban Service Bus újra elérhetővé válik. A leállás általában nem okoz üzenetet vagy más adatvesztést. Ilyen kimaradás például áramkimaradás lehet az adatközpontban. Bizonyos kimaradások átmeneti vagy hálózati problémák miatt csak rövid kapcsolódási veszteségek. 

A *katasztrófa* a Service Bus-fürt, az Azure-régió vagy az adatközpont állandó vagy hosszú távú elvesztéseként van meghatározva. Előfordulhat, hogy a régió vagy az adatközpont újra elérhetővé válik, vagy akár órákig, akár napokig nem. Ilyen katasztrófák például a tűz, az árvíz vagy a földrengés. Az állandó vészhelyzet miatt előfordulhat, hogy bizonyos üzenetek, események vagy egyéb adatvesztést okoznak. Azonban a legtöbb esetben nem lehet adatvesztés, és az adatközpont biztonsági mentése után az üzenetek nem állíthatók helyre.

A Azure Service Bus geo-vész-helyreállítási funkciója vész-helyreállítási megoldás. Az ebben a cikkben ismertetett fogalmak és munkafolyamatok a katasztrófa-forgatókönyvekre, és nem átmeneti vagy átmeneti kimaradásokra vonatkoznak. A Microsoft Azure vész-helyreállítási részletes megvitatását [ebben a cikkben](/azure/architecture/resiliency/disaster-recovery-azure-applications)találja.   

## <a name="basic-concepts-and-terms"></a>Alapvető fogalmak és kifejezések

A vész-helyreállítási funkció a metaadatok vész-helyreállítását valósítja meg, és az elsődleges és másodlagos vész-helyreállítási névterekre támaszkodik. Vegye figyelembe, hogy a Geo-vész-helyreállítási funkció csak a [Premium SKU](service-bus-premium-messaging.md) esetében érhető el. Nem kell módosítania a kapcsolódási karakterláncot, mert a kapcsolatok aliason keresztül történnek.

A cikk a következő kifejezéseket használja:

-  *Alias*: az Ön által beállított vész-helyreállítási konfiguráció neve. Az alias egyetlen stabil teljes tartománynevet (FQDN) tartalmazó adatkarakterláncot biztosít. Az alkalmazások ezt az alias kapcsolati karakterláncot használják a névtérhez való kapcsolódáshoz. Az alias használatával biztosítható, hogy a kapcsolódási sztring változatlan maradjon a feladatátvétel elindításakor.

-  *Elsődleges/másodlagos névtér*: az aliasnak megfelelő névterek. Az elsődleges névtér "aktív", és fogadja az üzeneteket (ez lehet egy meglévő vagy egy új névtér). A másodlagos névtér "passzív", és nem fogad üzeneteket. A kettő közötti metaadatok szinkronban vannak, így mindkét alkalmazás kód vagy kapcsolati karakterlánc módosítása nélkül is zökkenőmentesen fogadhat üzeneteket. Annak biztosítása érdekében, hogy csak az aktív névtér kapjon üzeneteket, az aliast kell használnia. 

-  *Metaadatok*: olyan entitások, mint a várólisták, témakörök és előfizetések; a névtérhez társított szolgáltatás tulajdonságai. Vegye figyelembe, hogy csak az entitások és azok beállításai lesznek automatikusan replikálva. Az üzenetek nem replikálódnak.

-  *Feladatátvétel*: a másodlagos névtér aktiválása folyamatban van.

## <a name="setup"></a>Telepítés

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

## <a name="failover-flow"></a>Feladatátvételi folyamat

A feladatátvételt manuálisan indítja el az ügyfél (explicit módon egy parancson keresztül, vagy az ügyfél által birtokolt üzleti logikán keresztül, amely elindítja a parancsot), és soha nem az Azure-ban. Így az ügyfél teljes körű tulajdonosi és láthatósági megoldást nyújt az Azure gerincén.

![4][]

A feladatátvétel elindítása után –

1. Az ***alias*** -kapcsolódási karakterlánc frissült, hogy a másodlagos prémium névtérre mutasson.

2. Az ügyfelek (küldők és fogadók) automatikusan csatlakoznak a másodlagos névtérhez.

3. Az elsődleges és a másodlagos prémium névtér közötti meglévő párosítás megszakadt.

A feladatátvétel kezdeményezése után –

1. Ha egy másik leállás következik be, azt szeretné, hogy újra lehessen adni a feladatátvételt. Ezért állítson be egy másik passzív névteret, és frissítse a párosítást. 

2. A korábbi elsődleges névtérből származó üzenetek lekérése, ha ismét elérhetővé válik. Ezt követően használja ezt a névteret a normál üzenetküldéshez a Geo-helyreállítási beállításon kívül, vagy törölje a régi elsődleges névteret.

> [!NOTE]
> Csak a meghiúsult továbbítási szemantika támogatott. Ebben a forgatókönyvben feladatátvételt hajt végre, majd egy új névtérrel újra párosítja. A sikertelen visszaállítás nem támogatott; például egy SQL-fürtben. 

A feladatátvételt a figyelési rendszerekkel vagy a testreszabott figyelési megoldásokkal automatizálhatja. Az ilyen automatizálás azonban további tervezést és munkát is igénybe vesz, amely nem tartozik a jelen cikk hatálya alá.

![2][]

## <a name="management"></a>Kezelés

Ha hibát vétett; Előfordulhat például, hogy a kezdeti beállítás során nem a megfelelő régiókat párosítja, hanem bármikor megszakíthatja a két névtér párosítását. Ha a párosított névtereket normál névtérként szeretné használni, törölje az aliast.

## <a name="use-existing-namespace-as-alias"></a>Meglévő névtér használata aliasként

Ha olyan helyzettel rendelkezik, amelyben nem módosíthatja a termelők és a felhasználók kapcsolatait, akkor a névtér nevét felhasználhatja alias neveként. Tekintse [meg a githubhoz tartozó mintakód itt](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Példák

A [githubon található minták](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) bemutatják, hogyan kell beállítani és kezdeményezni a feladatátvételt. Ezek a minták a következő fogalmakat szemléltetik:

- .NET-minta és-beállítások, amelyek szükségesek ahhoz, hogy a Azure Active Directory Azure Resource Manager a Service Bus használatával a földrajzi katasztrófák helyreállításának beállításához és engedélyezéséhez.
- A mintakód végrehajtásához szükséges lépések.
- Meglévő névtér használata aliasként.
- A következő lépésekkel engedélyezheti a földrajzi katasztrófa utáni helyreállítást a PowerShell vagy a parancssori felület használatával.
- [Küldés és fogadás](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) az aktuális elsődleges vagy másodlagos névtérből az alias használatával.

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következő szempontokat, hogy ne feledje a jelen kiadást:

1. A feladatátvétel tervezése során az időtényezőt is figyelembe kell venni. Ha például megszakad a kapcsolat a 15 – 20 percnél hosszabb ideig, dönthet úgy, hogy kezdeményezi a feladatátvételt.

2. Az a tény, hogy nem replikálódnak az adathalmazok, a jelenleg aktív munkamenetek nem replikálódnak. Emellett előfordulhat, hogy a duplikált észlelés és az ütemezett üzenetek nem működnek. Az új munkamenetek, az új ütemezett üzenetek és az új ismétlődések is működni fognak. 

3. Egy összetett elosztott infrastruktúra feladatátvétele legalább egyszer [kipróbálható](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) .

4. Az entitások szinkronizálása hosszabb időt is igénybe vehet, körülbelül 50-100 entitást percenként. Az előfizetések és a szabályok entitásként is számítanak.

## <a name="availability-zones"></a>Rendelkezésre állási zónák

A Service Bus Premium SKU a [Availability Zonest](../availability-zones/az-overview.md)is támogatja, amely az Azure-régióban található, hibátlanul elszigetelt helyszíneket biztosít.

> [!NOTE]
> A prémium szintű Azure Service Bus Availability Zones támogatása csak olyan Azure- [régiókban](../availability-zones/az-region.md) érhető el, ahol rendelkezésre áll a rendelkezésre állási zónák.

A Availability Zones csak az új névtereken engedélyezheti, a Azure Portal használatával. A Service Bus nem támogatja a meglévő névterek áttelepítését. A zóna redundancia nem tiltható le, miután engedélyezte azt a névtérben.

![3][]

## <a name="private-endpoints"></a>Privát végpontok
Ez a szakasz további szempontokat tartalmaz, ha a Geo-vész-helyreállítást privát végpontokat használó névterekkel használja. Ha többet szeretne megtudni a Service Bustel rendelkező privát végpontok használatáról, tekintse meg a [Azure Service Bus integrálása az Azure privát kapcsolattal](private-link-service.md)című témakört.

### <a name="new-pairings"></a>Új párosítások
Ha egy privát végponttal rendelkező elsődleges névtér és egy privát végpont nélküli másodlagos névtér között próbál létrehozni egy párosítást, akkor a párosítás sikertelen lesz. A párosítás csak akkor lesz sikeres, ha az elsődleges és a másodlagos névterek magánhálózati végpontokkal is rendelkeznek. Azt javasoljuk, hogy ugyanazokat a konfigurációkat használja az elsődleges és másodlagos névtereken, valamint azokon a virtuális hálózatokon, amelyeken a magánhálózati végpontok létre lettek hozva. 

> [!NOTE]
> Ha az elsődleges névteret privát végponttal és a másodlagos névtérrel próbálja párosítani, az érvényesítési folyamat csak azt ellenőrzi, hogy létezik-e privát végpont a másodlagos névtérben. Nem vizsgálja, hogy a végpont működik-e, vagy a feladatátvétel után fog működni. Az Ön felelőssége annak biztosítása, hogy a titkos végponttal rendelkező másodlagos névtér a feladatátvételt követően is a várt módon működjön.
>
> Annak ellenőrzéséhez, hogy a magánhálózati végponti konfigurációk azonosak-e, küldjön egy [Get Queues](/rest/api/servicebus/queues/get) kérést a másodlagos névtérnek a virtuális hálózaton kívülről, és ellenőrizze, hogy hibaüzenetet kap-e a szolgáltatástól.

### <a name="existing-pairings"></a>Meglévő párosítások
Ha az elsődleges és a másodlagos névtér közötti párosítás már létezik, akkor az elsődleges névtéren a magánhálózati végpont létrehozása sikertelen lesz. A megoldáshoz először hozzon létre egy privát végpontot a másodlagos névtérben, majd hozzon létre egyet az elsődleges névtérhez.

> [!NOTE]
> Noha a másodlagos névtérhez csak olvasási hozzáférést engedélyezünk, a magánhálózati végpontok konfigurációjának frissítései engedélyezve vannak. 

### <a name="recommended-configuration"></a>Ajánlott konfiguráció
Az alkalmazás és a Service Bus vész-helyreállítási konfigurációjának létrehozásakor saját végpontokat kell létrehoznia az elsődleges és a másodlagos Service Bus névterekhez az alkalmazás elsődleges és másodlagos példányait üzemeltető virtuális hálózatokon.

Tegyük fel, hogy két virtuális hálózattal rendelkezik: VNET-1, VNET-2 és ezek az elsődleges és második névterek: ServiceBus-Namespace1-Primary, ServiceBus-Namespace2-másodlagos. A következő lépéseket kell elvégeznie: 

- A ServiceBus-Namespace1-Primary-ben hozzon létre két privát végpontot, amely a VNET-1 és a VNET-2 alhálózatokat használja.
- ServiceBus – Namespace2 – másodlagos, hozzon létre két privát végpontot, amelyek ugyanazt az alhálózatot használják, mint a VNET-1 és a VNET-2 

![Magánhálózati végpontok és virtuális hálózatok](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


Ennek a megközelítésnek az az előnye, hogy a feladatátvétel a Service Bus névtértől független alkalmazási rétegben történhet. Vegyük példaként a következő forgatókönyveket: 

**Csak alkalmazáson belüli feladatátvétel:** Itt az alkalmazás nem létezik a VNET-1 helyen, de a VNET-2 értékre lép. Mivel a magánhálózati végpontok mind a VNET-1, mind a VNET-2 esetében mind az elsődleges, mind a másodlagos névtér esetében konfigurálva vannak, az alkalmazás csak működni fog. 

**Service Bus csak névtér feladatátvétele**: itt újra, mivel mindkét magánhálózati végpont mind az elsődleges, mind a másodlagos névterek esetében mindkét virtuális hálózaton konfigurálva van, az alkalmazás csak működni fog. 

> [!NOTE]
> A virtuális hálózatok földrajzi katasztrófa utáni helyreállításával kapcsolatos útmutatásért lásd: [Virtual Network – üzletmenet folytonossága](../virtual-network/virtual-network-disaster-recovery-guidance.md).

## <a name="next-steps"></a>További lépések

- Tekintse meg a Geo-vész-helyreállítási [REST API referenciát](/rest/api/servicebus/disasterrecoveryconfigs).
- Futtassa a Geo-vész-helyreállítási [mintát a githubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Tekintse meg a földrajzi katasztrófa utáni helyreállítási [mintát, amely üzeneteket küld egy aliasnak](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
