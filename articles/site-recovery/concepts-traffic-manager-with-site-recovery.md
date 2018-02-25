---
title: "Az Azure Traffic Manager az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Azure Traffic Manager használata az Azure Site Recovery vész-helyreállítási és áttelepítés"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: manayar
ms.openlocfilehash: e0790bbb9138f3f8b4c72a8210878006192092bf
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Az Azure Traffic Manager az Azure Site Recovery szolgáltatással

Az Azure Traffic Manager lehetővé teszi a forgalom eloszlása vezérelni az alkalmazási végpontokat. A végpont bármilyen internetre-szolgáltatott belül vagy kívül Azure.

A TRAFFIC Manager használ a tartománynévrendszer (DNS) ügyfél kéréseiket a leginkább megfelelő végpont, a forgalom-útválasztási módszer és a végpontok állapota alapján. A TRAFFIC Manager biztosít számos különböző [forgalom-útválasztási módszert](../traffic-manager/traffic-manager-routing-methods.md) és [megfigyelési lehetőségek végpont](../traffic-manager/traffic-manager-monitoring.md) különböző alkalmazásnak és az Automatikus feladatátvétel modellek. Ügyfelek közvetlenül a kijelölt végponthoz kapcsolódni. A TRAFFIC Manager nincs olyan proxy vagy az átjáró, és azt nem látja az ügyfél és a szolgáltatás között áthaladó forgalmat.

Ez a cikk ismerteti, hogyan kombinálhatja a Azure forgalmat figyelő intelligens útválasztási az Azure Site Recovery hatékony vész-helyreállítási és áttelepítési funkcióin.

## <a name="on-premises-to-azure-failover"></a>A helyszíni Azure-feladatátvétel

Az első esetben fontolja meg **a vállalat** , amely rendelkezik az összes, hogy a helyszíni környezetben futó alkalmazás infrastruktúráját. Üzleti folytonosság és megfelelőségi okokból **a vállalat** úgy dönt, hogy az Azure Site Recovery segítségével az alkalmazások védelmét.

**A vállalat** nyilvános végpontok alkalmazások futnak, és szeretne zökkenőmentesen átirányíthatják az adatforgalmat az Azure-bA egy katasztrófa eseményben. A [prioritás](../traffic-manager/traffic-manager-configure-priority-routing-method.md) forgalom-útválasztási módszer az Azure Traffic Managerben lehetővé teszi, hogy a vállalat a feladatátvételi mintát könnyen végrehajtásához.

A telepítő a következőképpen történik:
- **A vállalat** létrehoz egy [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Használatával a **prioritás** útválasztási módszer **a vállalat** hoz létre két végpontot – **elsődleges** a helyszíni és **feladatátvevő** az Azure. **Elsődleges** van hozzárendelve az 1-es prioritást és **feladatátvételi** prioritású virtuális gép 2 van hozzárendelve.
- Mivel a **elsődleges** végpont Azure kívül helyezkedik el, a végpont jön létre egy [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) végpont.
- Az Azure Site Recovery, az Azure hely nem rendelkezik a virtuális gépek vagy a feladatátvétel után már futó alkalmazások. Igen a **feladatátvételi** végpont is létrehoz egy **külső** végpont.
- Alapértelmezés szerint felhasználói forgalomnak a helyszíni alkalmazások irányul, mert a végpont rendelkezik a legmagasabb prioritású társítva. Azure amennyiben sincs forgalom irányítja a rendszer a **elsődleges** végpont állapota kifogástalan.

![A helyszíni-az-Azure-feladatátvétel előtt](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

A vész-események esetén a vállalat elindítható egy [feladatátvevő](site-recovery-failover.md) az Azure-bA és az alkalmazásokhoz az Azure-on. Ha Azure Traffic Manager észleli, hogy a **elsődleges** végpont már nem működik megfelelően, automatikusan használja a **feladatátvételi** végpont a DNS-válasz és a felhasználók csatlakozni az alkalmazást a helyreállított Azure.

![A helyszíni-az-Azure-feladatátvétel után](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Attól függően, hogy az üzleti követelmények **a vállalat** eldönthetik, hogy egy magasabb vagy alacsonyabb [gyakoriság probing](../traffic-manager/traffic-manager-monitoring.md) helyszíni Azure egy katasztrófa eseményben közötti váltás, és minimális állásidő érdekében a felhasználók számára.

Amikor a katasztrófa tartalmazza, **a vállalat** visszaadhatja a feladatokat az Azure-ból a helyszíni környezetbe ([VMware](site-recovery-how-to-failback-azure-to-vmware.md) vagy [Hyper-V](site-recovery-failback-from-azure-to-hyper-v.md)) Azure Site Recovery segítségével. Most, amikor a Traffic Manager észleli, hogy a **elsődleges** végpont újra állapota kifogástalan, automatikusan használja a **elsődleges** végpont a saját DNS-válaszok.

## <a name="on-premises-to-azure-migration"></a>A helyszíni Azure áttelepítése

Vész-helyreállítási mellett is engedélyezi az Azure Site Recovery [áttelepítése az Azure-bA](site-recovery-migrate-to-azure.md). Azure Site Recovery hatékony teszt feladatátvételi lehetőségeket használja, az ügyfelek kiértékelhetik, Azure alkalmazásteljesítmény anélkül, hogy befolyásolná a helyszíni környezetben. Vagy ha az ügyfelek áttelepítését, akkor telepítse át a teljes munkaterhelés együtt, vagy válassza ki az áttelepíteni, a méretezés fokozatosan.

Az Azure Traffic Manager [Weighted](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) útválasztási módszer használható az Azure-bA bejövő forgalom egy része közvetlen közben arra utasíthatja a legtöbb, a helyszíni környezetben. Ezt a módszert segítségével folytathatja a növekvő, több és több, a munkaterhelések áttelepítése az Azure-bA az Azure-bA hozzárendelt súlyozásoknak méretezési teljesítmény megállapításához.

Például **B vállalat** úgy dönt, hogy lépésből áll, néhány alkalmazás környezetének áthelyezni, megtartja a többi helyszíni áttelepíteni. A kezdeti szakaszában a környezetben a legtöbb esetén a helyszíni, nagyobb súlyt hozzá van rendelve a helyszíni környezetben. A TRAFFIC manager végpont számára elérhető végpontok rendelt súlyok alapján adja vissza.

![A helyszíni-az-Azure-áttelepítés](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Az áttelepítés során mindkét végpont aktív, és a forgalom nagy részét a helyszíni környezetben van átirányítva. Az áttelepítés halad, mint nagyobb súlyt hozzárendelhetők legyenek az Azure-végpont, és végül a helyszíni végpont áttelepítés deaktivált után.

## <a name="azure-to-azure-failover"></a>Azure az Azure-feladatátvétel

Az ebben a példában megfontolandó **vállalati C** , amely rendelkezik az összes, hogy fut az Azure alkalmazás infrastruktúráját. Üzleti folytonosság és megfelelőségi okokból **vállalati C** úgy dönt, hogy az Azure Site Recovery segítségével az alkalmazások védelmét.

**Vállalati C** nyilvános végpontok alkalmazások futnak, és szeretne zökkenőmentesen átirányítja a forgalmát egy másik Azure-régió egy katasztrófa utáni esemény. A [prioritás](../traffic-manager/traffic-manager-configure-priority-routing-method.md) forgalom-útválasztási módszer lehetővé teszi, hogy **vállalati C** könnyen megvalósításához a feladatátvételi mintát.

A telepítő a következőképpen történik:
- **Vállalati C** létrehoz egy [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Használatával a **prioritás** útválasztási módszer **vállalati C** hoz létre két végpontot – **elsődleges** a forrás régió (Azure Kelet-Ázsia) és **feladatátvételi** a helyreállítási régió (Azure Délkelet-Ázsiában). **Elsődleges** van hozzárendelve az 1-es prioritást és **feladatátvételi** prioritású virtuális gép 2 van hozzárendelve.
- Mivel a **elsődleges** végpont az Azure-ban található, a végpont lehet, mint egy [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) végpont.
- Az Azure Site Recovery az Azure site recovery nem rendelkezik a virtuális gépek vagy a feladatátvétel után már futó alkalmazások. Igen a **feladatátvételi** végpont hozhatók létre olyan [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) végpont.
- Alapértelmezés szerint felhasználói forgalom van irányítva a régióban (Kelet-Ázsia) alkalmazás adott végpontra a legmagasabb prioritású társítva van. Forgalmat a helyreállítási régióban van irányítva, ha a **elsődleges** végpont állapota kifogástalan.

![Azure-az-Azure-bA feladatátvétel előtt](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

A vészhelyreállítás eseményben **vállalati C** elindítható egy [feladatátvevő](azure-to-azure-tutorial-failover-failback.md) és helyreállításához a helyreállítási az Azure-régió az alkalmazásokhoz. Ha az Azure Traffic Manager azt észleli, hogy az elsődleges végpont már nem működik megfelelően, akkor automatikusan használja a **feladatátvevő** végpont a DNS-válasz és a felhasználók csatlakozni az alkalmazás helyre a helyreállítási Azure-régió, () Délkeleti Ázsia).

![Azure-az-Azure-bA a feladatátvételt követően](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Attól függően, hogy az üzleti követelmények **vállalati C** eldönthetik, hogy egy magasabb vagy alacsonyabb [gyakoriság probing](../traffic-manager/traffic-manager-monitoring.md) forrás- és a helyreállítási régiók közötti váltás, és a felhasználók minimális állásidővel.

Amikor a katasztrófa tartalmazza, **vállalati C** visszaadhatja a feladatokat a helyreállításhoz az Azure-régió a forrás-Azure Site Recovery segítségével az Azure-régió. Most, amikor a Traffic Manager észleli, hogy a **elsődleges** végpont újra állapota kifogástalan, automatikusan használja a **elsődleges** végpont a saját DNS-válaszok.

## <a name="protecting-multi-region-enterprise-applications"></a>Több területi vállalati alkalmazások védelme

Globális vállalatok gyakran felhasználói élmény javítása igazítása – az alkalmazások regionális igényeit. Honosítási és a késés csökkentése régiók-e osztani infrastruktúrát vezethet. A vállalatok egyes területeken regionális adatok törvények által is kötött, és válassza a elkülönítése tartozik a regionális határain belül infrastruktúrát.  

Mérlegeljük, például ha **vállalati D** leválasztási külön kiszolgálására Németország és a világ többi alkalmazás végpontját. **Vállalati D** használja az Azure Traffic Manager [Geographic](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) útválasztási módszer beállítására. A Németországi származó forgalmat van irányítva **1. végpont** és bármely Németország kívülről származó forgalmat van irányítva **végpont 2**.

Ezzel a beállítással a problémát, ha **1. végpont** nem működik a bármilyen okból nem a forgalom átirányítását van **végpont 2**. Németország származó forgalmat továbbra is kéri **1. végpont** függetlenül a végpont állapotát, így a német felhasználók való hozzáférés nélkül **vállalati D**tartozó alkalmazást. Hasonlóképpen ha **végpont 2** kerül offline módban vannak, nem a forgalom átirányítását van **1. végpont**.

![Több területi alkalmazása előtt](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Elkerüléséhez ezt a problémát, és ellenőrizze az alkalmazás rugalmas **vállalati D** használ [Traffic Manager-profilok beágyazott](../traffic-manager/traffic-manager-nested-profiles.md) az Azure Site Recovery szolgáltatással. Egy beágyazott profil beállításaiban forgalom nem irányítja a rendszer az egyes végpontok, hanem más Traffic Manager-profilok. Ez a telepítő működése:
- Földrajzi útválasztási egyedi végpontok használata helyett **vállalati D** földrajzi útválasztási Traffic Manager-profilokat használja.
- Minden gyermek Traffic Manager-profilt használó **prioritás** útválasztási egy elsődleges és egy helyreállítási végpont, ezért beágyazási **prioritás** útválasztási belül **Geographic** útválasztási.
- Ahhoz, hogy az alkalmazás rugalmas, minden munkaterhelés terjesztési használja az Azure Site Recovery számára a feladatátvételt egy vész-esemény esetén alapú helyreállítási régióban.
- Ha a szülő Traffic Manager DNS-lekérdezést kap, a megfelelő gyermek Traffic Manager, amely elérhető a végpont a lekérdezésre van átirányítva.

![Több területi alkalmazás után](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Például ha a végpont az németországi központban sikertelen, az alkalmazás gyorsan állíthatók helyre Németország szerepel. Az új végpont a felhasználók minimális állásidővel Németország származó forgalmat kezeli. Hasonlóképpen Nyugat-Európában végpont kimaradás kezelhetik az alkalmazás munkaterhelés, Észak-Európa, a DNS átirányítja a felhasználókat a elérhető végpontot az Azure Traffic Manager-kezelési helyreállítása.

A fenti telepítő bővíthető szükséges annyi régió és -végpont kombinációja tartalmazza. A TRAFFIC Manager lehetővé teszi, hogy a beágyazott profilok legfeljebb 10 szinteket, és a hurkok belül a beágyazott konfigurációs.

## <a name="recovery-time-objective-rto-considerations"></a>Idő célkitűzés (RTO) kapcsolatos szempontok

A legtöbb szervezetben hozzáadásával vagy módosításával DNS-rekordokat kell kezelni egy külön csapat vagy valaki a szervezeten kívül. Ez lehetővé teszi, hogy a DNS-rekordok nagyon nehéz feladat. Más csapatok vagy szervezetek DNS-infrastruktúra kezelése DNS-rekordok frissítéséhez szükséges idő függ a szervezetben, és hatással van az alkalmazás RTO.

A Traffic Manager felügyelniük frontload DNS-frissítésekhez szükséges munka is. Manuális vagy parancsprogram-alapú intézkedés nem szükséges tényleges feladatátvétel időpontjában. Ez a megközelítés segíti a gyors váltás (és ezáltal csökkentését RTO) valamint elkerülhető az a költséges időigényes DNS-módosítás hibák vész-esemény. A Traffic Managerben, még akkor is a feladat-visszavétel lépés automatizált, amelyeket egyébként külön kellene külön-külön kell kezelni.

A megfelelő beállítás [időköz probing](../traffic-manager/traffic-manager-monitoring.md) keresztül alapszintű vagy gyors időköz egészségügyi ellenőrzések is jelentősen kapcsolja le a RTO a feladatátvétel során és csökkentheti az állásidőt, a felhasználók számára.

Továbbá a DNS-időt élettartam (TTL) értékének a Traffic Manager-profil is optimalizálhatja. Élettartam (TTL), amelyhez a DNS-bejegyzés lesz gyorsítótárazva ügyfél által az érték. A bejegyzés DNS volna nem kérdezhető le kétszer belül a TTL-t. Minden DNS-rekord társítva TTL rendelkezik. Az érték a Traffic Manager további DNS-lekérdezések eredményez, de csökkentheti a RTO kimaradások gyorsabban felderítéséhez.

A TTL-t észlelt az ügyfél által is nem növekszik, ha az ügyfél és a mérvadó DNS-kiszolgáló közötti DNS-feloldókat számát növeli. DNS-feloldókat "számolja" a TTL-t, és csak továbbítására TTL érték, amely tükrözi az eltelt idő, mivel a rekord gyorsítótárazva lett. Ez biztosítja, hogy a DNS-rekord lekérdezi a a TTL-t, függetlenül a DNS-Feloldókat száma a lánc után az ügyfél frissítését.

## <a name="next-steps"></a>További lépések
- A Traffic Manager további [útválasztási metódusait](../traffic-manager/traffic-manager-routing-methods.md).
- További információ [Traffic Manager-profilok beágyazott](../traffic-manager/traffic-manager-nested-profiles.md).
- További információ [végpontmonitoring kijelző](../traffic-manager/traffic-manager-monitoring.md).
- További információ [helyreállítási tervek](site-recovery-create-recovery-plans.md) alkalmazás a feladatátvétel automatizálásához.
