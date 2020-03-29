---
title: Beágyazott forgalomkezelő-profilok az Azure-ban
titleSuffix: Azure Traffic Manager
description: Ez a cikk ismerteti az Azure Traffic Manager "Beágyazott profilok" funkcióját
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: rohink
ms.openlocfilehash: 282099cb274c1ea872a0df9c2753a939ef31421f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938570"
---
# <a name="nested-traffic-manager-profiles"></a>Beágyazott Traffic Manager-profilok

A Traffic Manager egy sor forgalom-útválasztási módszert tartalmaz, amelyek lehetővé teszik annak szabályozását, hogy a Traffic Manager hogyan választja ki, hogy melyik végpontnak kell fogadnia a forgalmat az egyes végfelhasználóktól. További információ: [Traffic Manager traffic-routing methods.](traffic-manager-routing-methods.md)

Minden Traffic Manager-profil egyetlen forgalom-útválasztási módszert határoz meg. Vannak azonban olyan forgatókönyvek, amelyek kifinomultabb forgalom-útválasztást igényelnek, mint az egyetlen Traffic Manager-profil által biztosított útválasztás. A Traffic Manager-profilok egymásba ágyazhatók egynél több forgalom-útválasztási módszer előnyeinek kombinálásával. A beágyazott profilok lehetővé teszik az alapértelmezett Traffic Manager-viselkedés felülbírálását a nagyobb és összetettebb alkalmazástelepítések támogatása érdekében.

Az alábbi példák bemutatják, hogyan használható beágyazott Traffic Manager-profilok különböző esetekben.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>1. példa: A "Teljesítmény" és a "Súlyozott" forgalomútválasztás kombinálása

Tegyük fel, hogy egy alkalmazást a következő Azure-régiókban telepített: USA nyugati régióiban, Nyugat-Európában és Kelet-Ázsiában. A Traffic Manager "Teljesítmény" forgalom-útválasztási módszerével forgalmat osztanak a felhasználóhoz legközelebb eső régióban.

![Egy forgalomkezelő-profil][4]

Most tegyük fel, hogy szeretné tesztelni a szolgáltatás frissítését, mielőtt szélesebb körben bevezeten. A "súlyozott" forgalom-útválasztási módszert szeretné használni a forgalom kis százalékának a tesztüzembe irányításához. A tesztüzembe helyezést a meglévő nyugat-európai éles környezet mellett állíthatja be.

A "Súlyozott" és a "Teljesítményforgalom-útválasztás" nem kombinálható egyetlen profilban. A forgatókönyv támogatásához hozzon létre egy Traffic Manager-profilt a két nyugat-európai végpont és a "Súlyozott" forgalom-útválasztási módszer használatával. Ezután adja hozzá ezt a "gyermek" profilt végpontként a "szülő" profilhoz. A szülőprofil továbbra is a Performance traffic-routing metódust használja, és a többi globális központi telepítést végpontként tartalmazza.

Az alábbi ábra ezt a példát mutatja be:

![Beágyazott Traffic Manager-profilok][2]

Ebben a konfigurációban a szülőprofilon keresztül irányított forgalom a szokásos módon osztja el a forgalmat a régiók között. Nyugat-Európán belül a beágyazott profil a forgalmat a termelési és tesztelési végpontok szerint osztja el a hozzárendelt súlyok.

Ha a szülőprofil a "Teljesítmény" forgalom-útválasztási módszert használja, minden végponthoz hozzá kell rendelni egy helyet. A hely a végpont konfigurálásakor van hozzárendelve. Válassza ki a központi telepítéshez legközelebb eső Azure-régiót. Az Azure-régiók az internetes késési táblázat által támogatott helyértékek. További információ: [Traffic Manager "Performance" traffic-routing method](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>2. példa: Végpontfigyelés beágyazott profilokban

A Traffic Manager aktívan figyeli az egyes szolgáltatásvégpontok állapotát. Ha egy végpont nem megfelelő állapotú, a Traffic Manager alternatív végpontokra irányítja a felhasználókat a szolgáltatás rendelkezésre állásának megőrzése érdekében. Ez a végpontfigyelési és feladatátvételi viselkedés az összes forgalom-útválasztási módszerre vonatkozik. További információ: [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md). A végpontfigyelés a beágyazott profilok esetében eltérően működik. Beágyazott profilok, a szülő profil nem végez állapotellenőrzést a gyermek közvetlenül. Ehelyett a gyermekprofil végpontjainak állapotát a rendszer a gyermekprofil általános állapotának kiszámításához használja. Ez az állapotinformáció propagálva jelenik meg a beágyazott profilhierarchiában. A szülőprofil ezt az összesített állapotot használja annak meghatározására, hogy a forgalmat a gyermekprofilra irányítsa-e. A beágyazott profilok állapotfigyelésével kapcsolatos gyakori kérdésekben a [GYIK-ben](traffic-manager-FAQs.md#traffic-manager-nested-profiles) talál részletes információt.

Visszatérve az előző példához, tegyük fel, hogy a nyugat-európai éles környezet nem működik. Alapértelmezés szerint a "gyermek" profil irányítja az összes forgalmat a teszt központi telepítés. Ha a teszt központi telepítése is sikertelen, a szülőprofil megállapítja, hogy a gyermekprofil nem fogadhat forgalmat, mivel az összes gyermekvégpont nem kifogástalan. Ezután a szülőprofil forgalmat oszt ki a többi régióban.

![Beágyazott profil feladatátvétel (alapértelmezett viselkedés)][3]

Lehet, hogy elégedett leszel ezzel a megállapodással. Vagy lehet, hogy aggódik amiatt, hogy a Nyugat-Európa összes forgalma most a teszttelepítésre kerül, nem pedig egy korlátozott részhalmazú forgalomra. Függetlenül ateszt-üzembe helyezés állapotától, szeretné átvenni a feladatát a többi régióban, ha az éles telepítés Nyugat-Európában sikertelen. A feladatátvétel engedélyezéséhez megadhatja a "MinChildEndpoints" paramétert, amikor a gyermekprofilt végpontként konfigurálja a szülőprofilban. A paraméter határozza meg a gyermekprofilban elérhető végpontok minimális számát. Az alapértelmezett érték "1". Ebben a forgatókönyvben a MinChildEndpoints értéket 2-re állítja. Ez alatt a küszöbérték alatt a szülőprofil úgy véli, hogy a teljes gyermekprofil nem érhető el, és a forgalmat a többi végpontra irányítja.

Az alábbi ábra ezt a konfigurációt mutatja be:

![Beágyazott profil feladatátvétele 'MinChildEndpoints' = 2][4]

> [!NOTE]
> A "Prioritás" forgalom-útválasztási módszer az összes forgalmat egyetlen végpontra osztja el. Így kevés célja van a MinChildEndpoints beállítás eltérő "1" egy gyermek profilt.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>3. példa: Rangsorolt feladatátvételi régiók a "Teljesítmény" forgalomútválasztásban

A "Teljesítmény" forgalom-útválasztási módszer alapértelmezett viselkedése az, ha a végpontok különböző földrajzi helyeken vannak, a végfelhasználók a legalacsonyabb hálózati késés szempontjából a "legközelebbi" végponthoz vannak irányítva.

Azonban tegyük fel, hogy inkább a nyugat-európai forgalom feladatátvételi az USA nyugati régiójában, és csak a közvetlen forgalmat más régiókba, ha mindkét végpont nem érhető el. Ezt a megoldást a "Prioritás" forgalom-útválasztási módszerrel rendelkező gyermekprofil használatával hozhatja létre.

!["Teljesítmény" forgalom-útválasztás kedvezményes feladatátvételsel][6]

Mivel a Nyugat-Európa végpont magasabb prioritású, mint a nyugat-amerikai végpont, minden forgalom a nyugat-európai végpontra kerül, ha mindkét végpont online állapotban van. Ha Nyugat-Európa nem sikerül, a forgalom az USA nyugati felé irányul. A beágyazott profillal a forgalom csak akkor irányul Kelet-Ázsiába, ha mind Nyugat-Európa, mind az USA nyugati része kudarcot vall.

Ezt a mintát minden régióban megismételheti. Cserélje le a szülőprofil mindhárom végpontját három gyermekprofilra, amelyek mindegyike rangsorolt feladatátvételi sorrendet biztosít.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>4. példa: A "Teljesítmény" forgalom útválasztásának szabályozása több végpont között ugyanabban a régióban

Tegyük fel, hogy a "Teljesítmény" forgalom-útválasztási módszert egy olyan profilban használja, amely egy adott régióban egynél több végpontot használ. Alapértelmezés szerint az adott régióra irányuló forgalom egyenletesen oszlik el az adott régióösszes elérhető végpontja között.

!["Teljesítmény" forgalomútválasztás régióbeli forgalom eloszlása (alapértelmezett viselkedés)][7]

Ahelyett, hogy több végpontot ad hozzá Nyugat-Európában, ezek a végpontok egy külön gyermekprofilba vannak zárva. A gyermekprofil hozzáadódik a szülőhöz, mint az egyetlen végpont Nyugat-Európában. A gyermekprofil beállításai szabályozhatják a forgalom eloszlását Nyugat-Európával, ha engedélyezi a prioritásalapú vagy súlyozott forgalom-útválasztást az adott régióban.

!["Teljesítmény" forgalom-útválasztás egyéni régióbeli forgalomelosztással][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>5. példa: Végpontonkénti figyelési beállítások

Tegyük fel, hogy a Traffic Manager használatával zökkenőmentesen migrálhatja a forgalmat egy örökölt helyszíni webhelyről egy új, az Azure-ban üzemeltetett felhőalapú verzióra. Az örökölt hely esetén a kezdőlap URI-ját szeretné használni a webhely állapotának figyelésére. De az új felhőalapú verzió, egy egyéni figyelési lapot (elérési út "/monitor.aspx"), amely további ellenőrzéseket tartalmaz.

![Traffic Manager végpontfigyelés (alapértelmezett viselkedés)][9]

A Traffic Manager-profil figyelési beállításai egy profilon belüli összes végpontra vonatkoznak. Beágyazott profilok esetén webhelyenként eltérő gyermekprofilt használhat a különböző figyelési beállítások meghatározásához.

![A Traffic Manager végpontfigyelése végpontonkénti beállításokkal][10]

## <a name="faqs"></a>Gyakori kérdések

* [Hogyan konfigurálhatom a beágyazott profilokat?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [Hány réteg beágyazási nem Traffic Manger támogatja?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [Keverhetek más végponttípusokat beágyazott gyermekprofilokkal ugyanabban a Traffic Manager-profilban?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Hogyan vonatkozik a számlázási modell a beágyazott profilokra?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [A beágyazott profilok teljesítményre gyakorolt hatása van?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [Hogyan számítja ki a Traffic Manager a szülőprofil beágyazott végpontjának állapotát?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>További lépések

További információ a [Traffic Manager-profilokról](traffic-manager-overview.md)

További információ a [Traffic Manager-profil létrehozásáról](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png
