---
title: Azure Traffic Manager az Azure Site Recovery szolgáltatással | Microsoft dokumentumok
description: Az Azure Traffic Manager és az Azure Site Recovery használata a vészhelyreállításhoz és az áttelepítéshez
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60947802"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager az Azure Site Recovery-vel

Az Azure Traffic Manager lehetővé teszi, hogy szabályozhatja a forgalom eloszlását az alkalmazás végpontjai között. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás.

A Traffic Manager a DNS -t használja az ügyfélkérelmek nek a legmegfelelőbb végpontra történő irányítására, egy forgalom-útválasztási módszer és a végpontok állapota alapján. A Traffic Manager különböző [forgalom-útválasztási módszereket](../traffic-manager/traffic-manager-routing-methods.md) és [végpont-monitorozási lehetőségeket](../traffic-manager/traffic-manager-monitoring.md) biztosít, hogy megfeleljen a különböző alkalmazások igényeinek és az automatikus feladatátvételi modelleknek. Az ügyfelek közvetlenül a kiválasztott végponthoz kapcsolódnak. A Traffic Manager nem proxy vagy átjáró, és nem látja az ügyfél és a szolgáltatás közötti forgalmat.

Ez a cikk bemutatja, hogyan kombinálhatja az Azure Traffic Monitor intelligens útválasztását az Azure Site Recovery hatékony vész-helyreállítási és áttelepítési képességeivel.

## <a name="on-premises-to-azure-failover"></a>Helyszíni azure-feladatátvétel

Az első forgatókönyv, fontolja meg **az A vállalat,** amely az összes alkalmazás-infrastruktúra fut a helyszíni környezetben. Az üzletmenet folytonossága és megfelelőségi okokmiatt **az "A" vállalat** úgy dönt, hogy az Azure Site Recovery használatával védi alkalmazásait.

**Az "A" vállalat** nyilvános végpontokkal rendelkező alkalmazásokat futtat, és azt szeretné, hogy a forgalom zökkenőmentesen átirányítsa a forgalmat az Azure-ba egy katasztrófa esemény esetén. Az Azure Traffic Manager [priority](../traffic-manager/traffic-manager-configure-priority-routing-method.md) traffic-routing metódusa lehetővé teszi, hogy az A vállalat egyszerűen megvalósítsa ezt a feladatátvételi mintát.

A beállítás a következő:
- **Az "A" vállalat** létrehoz egy [Traffic Manager-profilt.](../traffic-manager/traffic-manager-create-profile.md)
- A **prioritásos** útválasztási módszer használatával az **A vállalat** két végpontot hoz létre – **elsődleges** a helyszíni és az Azure-beli **feladatátvétel.** **Az elsődleges** prioritás 1-es, **a feladatátvétel** pedig a 2-es prioritás.
- Mivel az **elsődleges** végpont az Azure-on kívül található, a végpont [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) végpontként jön létre.
- Az Azure Site Recovery, az Azure-webhely nem rendelkezik a feladatátvétel előtt futó virtuális gépek vagy alkalmazások. Így a **feladatátvételi** végpont is létrejön **egy külső** végpontként.
- Alapértelmezés szerint a felhasználói forgalom a helyszíni alkalmazás, mert a végpont a legmagasabb prioritást társítva. Nincs forgalom az Azure-ba, ha az **elsődleges** végpont kifogástalan.

![Helyszíni Azure-ba feladatátvétel előtt](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

Katasztrófa esetén az "A" vállalat elindíthatja az Azure-ba [irányuló feladatátvételt,](site-recovery-failover.md) és helyreállíthatja alkalmazásait az Azure-ban. Ha az Azure Traffic Manager azt észleli, hogy az **elsődleges** végpont már nem kifogástalan állapotú, automatikusan használja a **feladatátvételi** végpontot a DNS-válaszban, és a felhasználók csatlakoznak az Azure-ban helyreállított alkalmazáshoz.

![Helyszíni Azure-ba feladatátvétel után](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Az üzleti követelményektől függően **az "A" vállalat** választhat egy magasabb vagy alacsonyabb [proktálási gyakoriságot,](../traffic-manager/traffic-manager-monitoring.md) hogy a helyszíni azure-ok között katasztrófa esetén váltson, és minimális állásidőt biztosíthat a felhasználók számára.

A katasztrófa esetén az **"A" vállalat** feladat-visszavételt hozhat az Azure-ból a helyszíni környezetbe[(VMware](vmware-azure-failback.md) vagy [Hyper-V)](hyper-v-azure-failback.md)az Azure Site Recovery használatával. Most, amikor a Traffic Manager észleli, hogy az **elsődleges** végpont ismét kifogástalan állapotú, automatikusan használja az **elsődleges** végpont a DNS-válaszokban.

## <a name="on-premises-to-azure-migration"></a>Helyszíni azure-áttelepítés

A vészhelyreállítás mellett az Azure Site Recovery lehetővé teszi [az Azure-ba való áttelepítést](migrate-overview.md)is. Az Azure Site Recovery hatékony tesztfeladat-átvételi képességeinek használatával az ügyfelek anélkül értékelhetik az alkalmazások teljesítményét az Azure-ban, hogy ez hatással lenne a helyszíni környezetükre. És amikor az ügyfelek készen állnak az áttelepítésre, választhatnak a teljes számítási feladatok együtt, vagy úgy dönt, hogy migrálja és fokozatosan skálázhatja.

Az Azure Traffic Manager [súlyozott](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) útválasztási módszer segítségével irányíthatja a bejövő forgalom egy részét az Azure-ba, miközben a többség a helyszíni környezetben. Ez a megközelítés segíthet a méretezési teljesítmény felmérésében, mivel továbbra is növelheti az Azure-hoz rendelt súlyt, ahogy egyre több számítási feladatot telepít át az Azure-ba.

A **B vállalat** például úgy dönt, hogy fázisokban telepíti át, és az alkalmazási környezet egy részét áthelyezi, miközben a többi marad a helyszínen. A kezdeti szakaszokban, amikor a legtöbb a környezet a helyszínen, nagyobb súlyt rendelnek a helyszíni környezetben. A Forgalomkezelő egy végpontot ad vissza a rendelkezésre álló végpontokhoz rendelt súlyok alapján.

![Helyszíni Azure-áttelepítés](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Az áttelepítés során mindkét végpont aktív, és a forgalom nagy része a helyszíni környezetbe irányul. Az áttelepítés előrehaladtával nagyobb súlyt rendelhet az Azure-beli végponthoz, és végül a helyszíni végpont inaktiválható az áttelepítés után.

## <a name="azure-to-azure-failover"></a>Azure-azure-beli feladatátvétel

Ebben a példában vegye figyelembe a **C vállalat,** amely rendelkezik az összes alkalmazás-infrastruktúra Azure-t futtató. Az üzletmenet folytonossága és megfelelőségi okokmiatt **a C vállalat** úgy dönt, hogy az Azure Site Recovery használatával védi alkalmazásait.

**C vállalat** nyilvános végpontokkal rendelkező alkalmazásokat futtat, és azt szeretné, hogy egy katasztrófa esetén zökkenőmentesen irányítsa át a forgalmat egy másik Azure-régióba. A [prioritási](../traffic-manager/traffic-manager-configure-priority-routing-method.md) forgalom-útválasztási módszer lehetővé teszi a **C vállalat** számára, hogy könnyen megvalósítsa ezt a feladatátvételi mintát.

A beállítás a következő:
- **C vállalat** létrehoz egy [Traffic Manager-profilt.](../traffic-manager/traffic-manager-create-profile.md)
- A **prioritásos** útválasztási módszer használatával a **C vállalat** két végpontot hoz létre : **elsődleges** a forrásrégióhoz (Azure East Asia) és **feladatátvétel** a helyreállítási régióhoz (Azure Délkelet-Ázsia). **Az elsődleges** prioritás 1-es, **a feladatátvétel** pedig a 2-es prioritás.
- Mivel az **elsődleges** végpont az Azure-ban található, a végpont [azure-végpontként](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) is lehet.
- Az Azure Site Recovery, a helyreállítási Azure-webhely nem rendelkezik a feladatátvétel előtt futó virtuális gépek vagy alkalmazások. Így a **feladatátvételi** végpont [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) végpontként hozható létre.
- Alapértelmezés szerint a felhasználói forgalom a forrásrégióba (Kelet-Ázsia) irányul, mivel a végpont hoz a legmagasabb prioritást társítva. Nincs forgalom a helyreállítási régióba, ha az **elsődleges** végpont kifogástalan.

![Azure-ból az Azure-ba feladatátvétel előtt](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

Katasztrófa esetén a **C vállalat** elindíthatja a [feladatátvételt,](azure-to-azure-tutorial-failover-failback.md) és helyreállíthatja alkalmazásait a helyreállítási Azure-régióban. Ha az Azure Traffic Manager azt észleli, hogy az elsődleges végpont már nem kifogástalan állapotú, automatikusan használja a **feladatátvételi** végpontot a DNS-válaszban, és a felhasználók csatlakoznak a helyreállítási Azure-régióban (Délkelet-Ázsia) helyreállított alkalmazáshoz.

![Azure-ból Az Azure-ba feladatátvétel után](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Az üzleti követelményektől függően **a C vállalat** választhat egy magasabb vagy alacsonyabb [proktálási gyakoriságot](../traffic-manager/traffic-manager-monitoring.md) a forrás- és helyreállítási régiók közötti váltáshoz, és minimális állásidőt biztosíthat a felhasználók számára.

A katasztrófa esetén a **C vállalat** feladat-visszavételt hozhat a helyreállítási Azure-régióból a forrás Azure-régióba az Azure Site Recovery használatával. Most, amikor a Traffic Manager észleli, hogy az **elsődleges** végpont ismét kifogástalan állapotú, automatikusan használja az **elsődleges** végpont a DNS-válaszokban.

## <a name="protecting-multi-region-enterprise-applications"></a>Többrégiós vállalati alkalmazások védelme

A globális vállalatok gyakran javítják az ügyfélélményt azáltal, hogy alkalmazásaikat a regionális igényekkielégítéséhez igazítják. A lokalizáció és a késés csökkentése az alkalmazásinfrastruktúra régiók közötti felosztásához vezethet. A vállalatokat bizonyos területeken a regionális adattörvények is kötik, és úgy döntenek, hogy alkalmazási infrastruktúrájuk egy részét regionális határokon belül elkülönítik.  

Vegyünk egy példát, ahol **a D vállalat** megosztotta az alkalmazás végpontjait, hogy külön szolgálja Németországot és a világ többi részét. **A D vállalat** az Azure Traffic Manager [földrajzi](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) útválasztási módszerét használja ennek beállításához. A Németországból származó bármely forgalom az **1.** **Endpoint 2**

A probléma ezzel a beállítással az, hogy ha **az 1.** **Endpoint 2** A Németországból származó forgalom továbbra is az **1.** **Company D** Hasonlóképpen, ha **a 2.** **Endpoint 1**

![Többrégiós alkalmazás előtt](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

A probléma elkerülése és az alkalmazások rugalmasságának biztosítása érdekében **a D vállalat** beágyazott Traffic [Manager-profilokat](../traffic-manager/traffic-manager-nested-profiles.md) használ az Azure Site Recovery szolgáltatással. A beágyazott profil beállításaiban a forgalom nem az egyes végpontokra, hanem más Traffic Manager-profilokra irányul. A beállítás a következőképpen működik:
- Ahelyett, hogy földrajzi útválasztást használna az egyes végpontokkal, **a D vállalat** földrajzi útválasztást használ a Traffic Manager-profilokkal.
- Minden gyermek Traffic Manager-profil elsődleges és helyreállítási végpontdal használja **a prioritási** útválasztást, így **a prioritási** útválasztást **a földrajzi** útválasztásba ágyazva.
- Az alkalmazások rugalmasságának engedélyezéséhez minden számítási feladatok elosztása az Azure Site Recovery használatával feladatátvételt egy vészesemény alapján alapuló helyreállítási régióba.
- Amikor a szülő Traffic Manager dns-lekérdezést kap, a rendszer a megfelelő gyermek Traffic Manager, amely válaszol a lekérdezésre egy elérhető végpont.

![Többrégiós alkalmazás után](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Ha például a németországi központi végpont meghibásodik, az alkalmazás gyorsan helyreáll Németország északkeleti részén. Az új végpont a Németországból származó forgalmat a felhasználók számára minimális állásidővel kezeli. Hasonlóképpen egy nyugat-európai végpontkimaradás is kezelhető az észak-európai alkalmazásszámítási feladatok helyreállításával, az Azure Traffic Manager kezeli a DNS-átirányításokat a rendelkezésre álló végpontra.

A fenti beállítás kibontható annyi régió- és végpontkombinációval, amely szükséges. A Traffic Manager legfeljebb 10 beágyazott profilszintet engedélyez, és nem engedélyezi a hurkokat a beágyazott konfiguráción belül.

## <a name="recovery-time-objective-rto-considerations"></a>Helyreállítási idő célkitűzés (RTO) szempontjai

A legtöbb szervezetben a DNS-rekordok hozzáadását vagy módosítását külön csoport vagy a szervezeten kívüli személy kezeli. Ez nagyon megnehezíti a DNS-rekordok módosításának feladatát. A DNS-infrastruktúrát kezelő más csoportok vagy szervezetek DNS-rekordjainak frissítéséhez szükséges idő szervezetenként változik, és hatással van az alkalmazás RTO-jára.

A Traffic Manager segítségével előretöltheti a DNS-frissítésekhez szükséges munkát. Nincs szükség manuális vagy parancsfájlalapú műveletre a tényleges feladatátvétel időpontjában. Ez a megközelítés segít a gyors váltásban (és ezáltal az RTO csökkentésében), valamint a költséges időigényes DNS-módosítási hibák elkerülésében egy katasztrófa esemény esetén. A Traffic Manager, még a feladat-visszavételi lépés automatikus, amely et egyébként külön kell kezelni.

A megfelelő [szondázási időköz](../traffic-manager/traffic-manager-monitoring.md) beállítása az alap- vagy gyors intervallumállapot-ellenőrzések révén jelentősen csökkentheti az RTO-t a feladatátvétel során, és csökkentheti a felhasználók állásidejét.

Ezenkívül optimalizálhatja a DNS Time to Live (TTL) értékét a Traffic Manager profilhoz. A TTL az az érték, amelyhez az ügyfél gyorsítótárazná a DNS-bejegyzést. Rekord esetén a DNS nem kérdezhető le kétszer a TTL tartományon belül. Minden DNS-rekordhoz tartozik egy TTL. Ennek az értéknek a csökkentése több DNS-lekérdezést eredményez a Traffic Manager számára, de csökkentheti az RTO-t a kimaradások gyorsabb felderítésével.

Az ügyfél által tapasztalt TTL akkor sem növekszik, ha az ügyfél és a mérvadó DNS-kiszolgáló között nő a DNS-feloldók száma. A DNS-feloldók "visszaszámolják" a TTL-t, és csak olyan TTL értéket adnak át, amely a rekord gyorsítótárazása óta eltelt időt tükrözi. Ez biztosítja, hogy a DNS-rekord frissüljön az ügyfélnél a TTL után, függetlenül a láncban lévő DNS-feloldók számától.

## <a name="next-steps"></a>További lépések
- További információ a Traffic Manager [útválasztási módszereiről.](../traffic-manager/traffic-manager-routing-methods.md)
- További információ a [beágyazott Traffic Manager-profilokról.](../traffic-manager/traffic-manager-nested-profiles.md)
- További információ a [végpontfigyelésről.](../traffic-manager/traffic-manager-monitoring.md)
- További információ az alkalmazásfeladat-átvétel automatizálását célzó [helyreállítási tervekről.](site-recovery-create-recovery-plans.md)
