---
title: Az Azure Site Recovery az Azure Traffic Manager |} A Microsoft Docs
description: Az Azure Traffic Manager használata az Azure Site Recovery vész-helyreállítási és áttelepítés
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788749"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager az Azure Site Recovery-vel

Az Azure Traffic Manager lehetővé teszi, hogy az alkalmazás végpontok közötti forgalom elosztását. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás.

A TRAFFIC Manager a tartománynévrendszer (DNS) használatával a leginkább megfelelő végpontra, a forgalom-útválasztási módszer és a végpontok állapota alapján az ügyfélkéréseket. A Traffic Manager különböző [forgalom-útválasztási módszereket](../traffic-manager/traffic-manager-routing-methods.md) és [végpont-monitorozási lehetőségeket](../traffic-manager/traffic-manager-monitoring.md) biztosít, hogy megfeleljen a különböző alkalmazások igényeinek és az automatikus feladatátvételi modelleknek. Az ügyfelek közvetlenül kapcsolódni a kiválasztott végpont. A TRAFFIC Manager nem a proxy- vagy egy átjárót, és nem jelenik meg a forgalom az ügyfél és a szolgáltatás között.

Ez a cikk bemutatja, hogyan kombinálhatja az Azure a forgalmat figyelő intelligens útválasztás az Azure Site Recovery hatékony vészhelyreállítás és migrálási képességeken.

## <a name="on-premises-to-azure-failover"></a>Helyszíniről Azure-feladatátvétel

Az első forgatókönyv, fontolja meg a **a vállalat** , amely rendelkezik az összes, hogy a helyszíni környezetben futó alkalmazás infrastruktúráját. Üzleti folytonosság és a megfelelőségi okokból **a vállalat** úgy dönt, hogy a rajtuk található alkalmazások védelme az Azure Site Recovery használatával.

**A vállalat** nyilvános végpontokkal rendelkező alkalmazásokat futtat, és zökkenőmentesen forgalom átirányítása az Azure-bA az olyan vész-helyreállítási esemény szeretne. A [prioritású](../traffic-manager/traffic-manager-configure-priority-routing-method.md) forgalom-útválasztási módszer az Azure Traffic Manager lehetővé teszi, hogy a vállalat egyszerűen implementálni a feladatátvételi minta.

A telepítő a következőképpen történik:
- **A vállalat** létrehoz egy [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Használatával a **prioritású** -útválasztási módszert **a vállalat** létrehoz két végpontot – **elsődleges** a helyszíni és **feladatátvételi** az Azure-hoz. **Elsődleges** hozzá van rendelve a prioritása 1 és **feladatátvételi** prioritású 2 van hozzárendelve.
- Mivel a **elsődleges** szolgáltatásvégpontot futtató Azure-on kívülről, a végpont a létrehozás- [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) végpont.
- Az Azure Site Recovery az Azure site nem rendelkezik minden olyan virtuális gépeket vagy feladatátvétel előtt futó alkalmazások. Így a **feladatátvételi** végpont is létrejön egy **külső** végpont.
- Alapértelmezés szerint a felhasználói adatforgalmat a helyszíni alkalmazás irányul, mert, hogy a végpont rendelkezik a legmagasabb prioritású társítva. Nincs forgalom irányul, ha az Azure a **elsődleges** végpont állapota kifogástalan.

![A – helyszíni – Azure feladatátvétel előtt](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

Az olyan vész-helyreállítási esemény a vállalat aktiválhat egy [feladatátvételi](site-recovery-failover.md) az Azure-ba, és helyreállítja a rajtuk található alkalmazások az Azure-ban. Ha az Azure Traffic Manager észleli, hogy a **elsődleges** végpont már nem kifogástalan, automatikusan használja a **feladatátvételi** végpont a DNS-válasz és a felhasználók az alkalmazást a helyreállított csatlakozni Azure-t.

![A – helyszíni – Azure feladatátvétel után](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Üzleti követelményeitől függően **a vállalat** kiválaszthatja, hogy egy magasabb vagy alacsonyabb [tesztelés gyakorisága](../traffic-manager/traffic-manager-monitoring.md) váltani a helyszínen az Azure-bA egy vész-helyreállítási esemény között, és győződjön meg, hogy a felhasználók minimális üzemen kívüli idővel.

Ha a vészhelyreállítási megtalálható, **a vállalat** történő feladat-visszavétel az Azure-ból a helyszíni környezetben is ([VMware](vmware-azure-failback.md) vagy [Hyper-V](hyper-v-azure-failback.md)) Azure Site Recovery használatával. Most, ha a Traffic Manager észleli, hogy a **elsődleges** végpont újra állapota kifogástalan, automatikusan használja a **elsődleges** végpont a saját DNS-válaszok.

## <a name="on-premises-to-azure-migration"></a>Helyszíniről Azure-migrálás

Vész-helyreállítási mellett az Azure Site Recovery lehetővé teszi a [migrálását az Azure-](migrate-overview.md). Azure Site Recovery hatékony tesztelési feladatátvételt képességek használata esetén a ügyfelek alkalmazások teljesítménye az Azure-ban anélkül, hogy befolyásolná a saját helyszíni környezetükben értékelhet. Vagy ha ügyfelek áttelepítését, azok, együtt a teljes számítási feladatok migrálása, vagy válasszon át, és fokozatosan skálázhatja.

Az Azure Traffic Manager [súlyozott](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) esetén használt útválasztási módszer segítségével közvetlen az Azure-bA a bejövő forgalom egy része során a helyszíni környezetet a legtöbb irányítja. Ez a megközelítés segíthet a méretezési teljesítmény értékeléséhez, szerint növekvő folyamokhoz hozzárendelt Azure-ba, a migrálás több és több, a számítási feladatok Azure-ban továbbra is.

Ha például **vállalati B** úgy dönt, hogy fázisban történik, a többi helyszíni megőrzése áthelyezése néhány az alkalmazási környezet migrálása. A kezdeti szakaszában a helyszínen, a környezetben a legtöbb esetén nagyobb súly hozzá van rendelve a helyszíni környezetben. A TRAFFIC manager végpont elérhető végpontok rendelt súlyok alapján ad vissza.

![A – helyszíni – Azure-migrálás](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Az áttelepítés során mindkét végpont aktívak, és a forgalom a legtöbb van irányítva a helyszíni környezetben. A migrálás végrehajtása egy nagyobb súly hozzárendelheti az Azure-ban a végpont és végül a helyi végpont lehet-e a migrálás után inaktív.

## <a name="azure-to-azure-failover"></a>Azure-bA feladatátvétel

Ebben az esetben érdemes lehet **vállalati C** , amely rendelkezik az összes, az Azure futtató alkalmazás infrastruktúra. Üzleti folytonosság és a megfelelőségi okokból **vállalati C** úgy dönt, hogy a rajtuk található alkalmazások védelme az Azure Site Recovery használatával.

**Vállalati C** nyilvános végpontokkal rendelkező alkalmazásokat futtat, és zökkenőmentesen forgalom átirányítása egy másik Azure-régióban egy vész-helyreállítási esemény elvégezhessék. A [prioritású](../traffic-manager/traffic-manager-configure-priority-routing-method.md) forgalom-útválasztási módszer lehetővé teszi, hogy **vállalati C** egyszerűen implementálni a feladatátvételi minta.

A telepítő a következőképpen történik:
- **Vállalati C** létrehoz egy [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Használatával a **prioritás** -útválasztási módszert **vállalati C** létrehoz két végpontot – **elsődleges** a forrás régió (Azure Kelet-Ázsia) és **feladatátvételi** a helyreállítási régióban (az Azure Délkelet-Ázsia). **Elsődleges** hozzá van rendelve a prioritása 1 és **feladatátvételi** prioritású 2 van hozzárendelve.
- Mivel a **elsődleges** végpont az Azure-ban üzemel, a végpont is, mint egy [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) végpont.
- Az Azure Site Recovery az Azure site recovery nem rendelkezik minden olyan virtuális gépeket vagy feladatátvétel előtt futó alkalmazások. Így a **feladatátvételi** végpont hozható létre egy [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) végpont.
- Alapértelmezés szerint felhasználói átirányítja a forgalmat a régió (Kelet-Ázsia) forrásalkalmazás, hogy a végpont rendelkezik a legmagasabb prioritású társítva. Nincs forgalom irányul a helyreállítási régióban, ha a **elsődleges** végpont állapota kifogástalan.

![Azure – Azure feladatátvétel előtt](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

Az olyan vész-helyreállítási esemény **vállalati C** elindíthat egy [feladatátvételi](azure-to-azure-tutorial-failover-failback.md) és a rajtuk található alkalmazások a helyreállítási Azure-régió helyreállítása. Ha az Azure Traffic Manager észleli, hogy az elsődleges végpont már nem kifogástalan állapotú, akkor automatikusan használja a **feladatátvételi** végpont a DNS-válasz és a felhasználók kapcsolódnak az alkalmazáshoz az Azure-régiót (helyreállítási helyre Délkelet-Ázsiában).

![Azure – Azure feladatátvétel után](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Üzleti követelményeitől függően **vállalati C** kiválaszthatja, hogy egy magasabb vagy alacsonyabb [tesztelés gyakorisága](../traffic-manager/traffic-manager-monitoring.md) forrás- és a helyreállítási régiók közötti váltás, és győződjön meg, hogy a felhasználók minimális üzemen kívüli idővel.

Ha a vészhelyreállítási megtalálható, **vállalati C** visszaadhatja a feladatokat a helyreállításból az Azure-régióban a forrás Azure Site Recovery használatával az Azure-régióban. Most, ha a Traffic Manager észleli, hogy a **elsődleges** végpont újra állapota kifogástalan, automatikusan használja a **elsődleges** végpont a saját DNS-válaszok.

## <a name="protecting-multi-region-enterprise-applications"></a>Többrégiós vállalati alkalmazások védelme

Globális vállalatok gyakran javíthatja testreszabható alkalmazásaikat regionális igényeinek kiszolgálása érdekében. A honosítás és a késés csökkentése régióban osztott alkalmazás-infrastruktúra vezethet. A vállalatok egyes területeken regionális adatainak törvények által is kötve vannak, és elkülöníteni egy részét az alkalmazás-infrastruktúra regionális határain belül válassza.  

Vegyünk egy példát, **vállalati D** felosztott annak végpontjainak Németország és a világ többi részén a külön-külön kiszolgálása érdekében. **Vállalati D** már használja az Azure Traffic Manager [Geographic](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) útválasztási módszer beállítására. Németország területéről származó forgalmat van irányítva **végpont 1** , és átirányítja a Németországi kívülről származó minden forgalmat **végpont 2**.

Ezt a problémát, ha **1. végpont** leáll, ha valamilyen okból nincs érkező forgalom átirányítása van **végpont 2**. Németország területéről származó forgalmat a rendszer továbbra is **1. végpont** függetlenül a végpont állapotát, és a német felhasználók való hozzáférés nélkül **vállalati D**a kérelmet. Hasonlóképpen ha **végpont 2** offline, kerül a van nem érkező forgalom átirányítása **végpont 1**.

![Többrégiós alkalmazás előtt](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Kerülje ezt a problémát, és győződjön meg, hogy a rugalmas alkalmazások **vállalati D** használ [beágyazott Traffic Manager-profilok](../traffic-manager/traffic-manager-nested-profiles.md) az Azure Site Recoveryvel. Egy beágyazott profil beállításaiban nem átirányítja a forgalmat az egyes végpontokat, hanem más Traffic Manager-profilok. Itt van ez a beállítás működéséről:
- Ahelyett, hogy az egyes végpontokat, földrajzi útválasztásának használó **vállalati D** földrajzi útválasztásának Traffic Manager-profilokat használja.
- Minden egyes gyermek Traffic Manager-profilt használja **prioritású** útválasztás az elsődleges és a egy helyreállítási végpont, ezért beágyazása **prioritású** útválasztási belül **Geographic** útválasztási.
- Ahhoz, hogy a rugalmas alkalmazások, az egyes számítási feladatok terjesztési történő feladatátvételt egy vész-helyreállítási esemény esetén-alapú helyreállítási régióba az Azure Site Recovery használja.
- Ha a szülő Traffic Manager DNS-lekérdezést kap, a kapcsolódó gyermek, amely válaszol a lekérdezésre elérhető végpontot a Traffic Manager van átirányítva.

![Többrégiós alkalmazás után](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Például ha nem sikerül a végpontot a közép-Németország, az alkalmazás gyorsan helyreállíthatók legyenek, Északkelet-Németország. Az új végpont kezeli a felhasználók minimális állásidővel Németország területéről származó forgalmat. Hasonlóképpen Nyugat-Európában végpont kimaradás tudja kezelni helyreállítása DNS átirányítja a felhasználót az elérhető végpontok Azure Traffic Manager kezelése az Észak-Európa, az alkalmazás feladata.

A fenti beállítás is bővíthetők ki szükséges annyi régió és -végpont kombinációja. Traffic Manager lehetővé teszi, hogy a beágyazott profilok legfeljebb 10 szintjét, és a hurkok belül a beágyazott konfigurációját.

## <a name="recovery-time-objective-rto-considerations"></a>A helyreállítási időre vonatkozó célkitűzés (RTO) kapcsolatos szempontok

A szervezetek többségében hozzáadásával vagy módosításával a DNS-rekordok kezelése egy külön csapat vagy valaki a szervezeten kívül. Ez teszi a nagy kihívást DNS-rekordok módosítása. Más csapatok vagy szervezetek DNS-infrastruktúra kezelésére DNS-rekordok frissítéséhez szükséges idő változó a szervezetben, és hatással van a az alkalmazás RTO.

A Traffic Manager használatával is frontload DNS-frissítésekhez szükséges munkát. Manuális vagy szkriptalapú semmit nem kell a tényleges feladatátvétel időpontjában. Ez a megközelítés segít a gyors váltás (és ezáltal emelő RTO), valamint kerülje a költséges időigényes DNS módosítása hibáinak egy vész-helyreállítási esemény. A Traffic Managerben, még a feladat-visszavétel lépés automatizált, amelyeket egyébként külön kellene külön.

A megfelelő beállítás [mintavételi időköz](../traffic-manager/traffic-manager-monitoring.md) alap- vagy gyors időköz health segítségével ellenőrzéseket is jelentősen feladatátvétel során a RTO leállásához és csökkenthető a leállások a felhasználók számára.

Emellett optimalizálhatja az élettartam (TTL) értéke a Traffic Manager-profil DNS időt. Élettartam az érték, amelynek egy DNS-bejegyzést lenne gyorsítótárazható ügyfelek számára. Egy rekord DNS lenne nem kérdezhető le kétszer a vizualizált Élettartamon belül. Minden DNS-rekord társítva TTL rendelkezik. Ez az érték csökkentése a Traffic Manager több DNS-lekérdezéseket eredményez, de csökkentheti a RTO gyorsabban derítse valamilyen okból kimaradás lép fel.

Az élettartam-ügyfél által tapasztalt is nem növeli az ügyfél és a mérvadó DNS-kiszolgáló közötti DNS feloldók számának növekedésével. DNS feloldók: count "Élettartama, és csak egy TTL-értéket, amely tükrözi az eltelt idő, mivel az a rekord gyorsítótárazva lett adja át. Ez biztosítja, hogy a DNS-rekord frissül az ügyfél az élettartam, attól függetlenül, a lánc DNS feloldók száma után.

## <a name="next-steps"></a>További lépések
- További tudnivalók a Traffic Manager [útválasztási metódusait](../traffic-manager/traffic-manager-routing-methods.md).
- Tudjon meg többet [beágyazott Traffic Manager-profilok](../traffic-manager/traffic-manager-nested-profiles.md).
- Tudjon meg többet [végpont-monitorozás](../traffic-manager/traffic-manager-monitoring.md).
- Tudjon meg többet [helyreállítási tervek](site-recovery-create-recovery-plans.md) alkalmazás a feladatátvétel automatizálásához.
