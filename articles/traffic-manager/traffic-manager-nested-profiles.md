---
title: Beágyazott Traffic Manager-profilok |} A Microsoft Docs
description: Ez a cikk ismerteti a beágyazott profilok funkció az Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: kumud
ms.openlocfilehash: 876305c7195a186671c30c4bdd9bb0c6b5331e9a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648598"
---
# <a name="nested-traffic-manager-profiles"></a>Beágyazott Traffic Manager-profilok

A TRAFFIC Manager forgalom-útválasztási módszer, amely lehetővé teszi, hogy hogyan Traffic Manager úgy dönt, hogy melyik végponthoz forgalmat kell kapnia, minden végfelhasználónak a foglal magában. További információkért lásd: [a Traffic Manager forgalom-útválasztási módszerei](traffic-manager-routing-methods.md).

Minden egyes Traffic Manager-profil megadja egy forgalom-útválasztási módszert. Vannak azonban olyan kifinomultabb forgalom-útválasztást egyetlen Traffic Manager-profil által kínált útválasztását esetekben. Traffic Manager-profilok úgy, hogy egynél több forgalom-útválasztási módszer előnyei ágyazhatja be. Beágyazott profilok segítségével felülírhatja az alapértelmezett a Traffic Manager viselkedés támogatási nagyobb és összetettebb alkalmazások központi telepítésének engedélyezése.

Az alábbi példák bemutatják, hogyan beágyazott Traffic Manager-profilok használatához különböző helyzetekben.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>1. példa: Kombinálásával "Teljesítmény" és "Súlyozott" forgalom-útválasztást

Tegyük fel, hogy telepített egy alkalmazást a következő Azure-régiókban: USA nyugati RÉGIÓJA, Nyugat-Európa és Kelet-Ázsia. Traffic Manager "Teljesítmény" forgalom-útválasztási módszer használatával a felhasználó legközelebb eső régiót forgalom elosztása.

![Egy Traffic Manager-profil][4]

Most tegyük fel, mielőtt további széles körben egy frissítést a szolgáltatás a tesztelni kívánt. Szeretné irányítani a forgalmat a tesztelési üzembe helyezés egy kis százalékát a "súlyozott" forgalom-útválasztási módszert használja. Nyugat-Európában beállítása mellett a meglévő éles környezet üzembe helyezési teszt.

Nem lehet kombinálni mindkét súlyozott és a "teljesítmény forgalom-útválasztási egyetlen profilban. Ez a forgatókönyv támogatása érdekében hozzon létre Traffic Manager-profil a Nyugat-Európa végpontokat és a "Súlyozott" forgalom-útválasztási módszer használatával. Következő lépésként hozzá a "gyermek" profil-végpontként a "parent" profil. A szülő profil továbbra is a teljesítmény forgalom-útválasztási módszert használja, és a többi globális üzemelő példányokat tartalmaz Végpontokként.

Az alábbi ábra az ebben a példában:

![Beágyazott Traffic Manager-profilok][2]

Ebben a konfigurációban a szülő profil keresztül irányított forgalom elosztja a forgalmat régióban megfelelően. Nyugat-Európa, belül az egymásba ágyazott profilelérési elosztja a forgalmat a termelési és tesztelési végpontokhoz a hozzárendelt súlyok megfelelően.

Amikor a szülő-profilt a "Teljesítmény" forgalom-útválasztási módszert használja, minden végpont hozzá kell rendelni egy helyre. A hely van hozzárendelve, amikor konfigurálja a végpontot. Válassza ki a legközelebb áll az üzembe helyezés az Azure-régióban. Az Azure-régiók értékei helyét az interneten késés tábla által támogatott. További információkért lásd: [Traffic Manager "Teljesítmény" forgalom-útválasztási módszer](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>2. példa:-Végpont monitorozása a beágyazott profilok

Traffic Manager folyamatosan figyeli az egyes Szolgáltatásvégpontok állapotát. A végpont állapota nem kifogástalan, ha a Traffic Manager arra utasítja a felhasználók alternatív végpontokat a szolgáltatás rendelkezésre állásának fenntartása. Ez a végpont monitorozása és feladatátvétele viselkedés vonatkozik az összes forgalom-útválasztási módszer. További információkért lásd: [Traffic Manager végpont figyelése](traffic-manager-monitoring.md). Végpont-monitorozás eltérően működik a beágyazott profilok. A beágyazott profilok a szülő-profil nem állapotellenőrzéseket hajthat végre az alárendelt közvetlenül. Ehelyett a gyermek profil végpontok állapotát rendszer kiszámítja a gyermek profil általános állapotát. Az egészségügyi információk propagálja felfelé a beágyazott profilhierarchiában. A szülő-profil az összesített állapota határozza meg, hogy a gyermek profil forgalmat használ. Tekintse meg a [– gyakori kérdések](traffic-manager-FAQs.md#traffic-manager-nested-profiles) szolgáltatásállapot-figyelést a beágyazott profilok teljes részleteiért.

Térjen vissza az előző példával, tegyük fel, Nyugat-Európában az éles üzembe helyezés sikertelen lesz. Alapértelmezés szerint a "gyermek" profil arra utasítja a tesztelési környezet felé irányuló összes forgalma. A tesztelési üzembe helyezés akkor is sikertelen, ha a szülő profil határozza meg, hogy a gyermek-profil nem kell kapnia forgalmat, mivel az összes gyermek-végpont nem megfelelő állapotú. Ezt követően a szülő profil elosztja a forgalmat a más régiók.

![Beágyazott profilok feladatátvétel (alapértelmezés)][3]

Előfordulhat, hogy forduljon hozzánk, ezzel az elrendezéssel. Vagy lehet, hogy minden forgalom a Nyugat-Európa most intézni fog egy korlátozott részhalmaza forgalom helyett üzembe helyezési teszt érintett. A tesztelési üzembe helyezés állapotát, függetlenül szeretné átadja a feladatokat más régiókban a Nyugat-Európában az éles központi telepítésének hibája esetén. Ahhoz, hogy a feladatátvétel, a szülő-profilban végpontként a gyermek-profil konfigurálásakor megadhatja a minchildendpoints "tulajdonság" paraméter. A paraméter meghatározza, hogy a gyermek-profil számára elérhető végpontok minimális számát. Az alapértelmezett értéke "1". Ebben a forgatókönyvben a minchildendpoints tulajdonság értékét 2 beállítani. Az ezen küszöbérték alatti a szülő-profil nem érhető el, a teljes gyermek profilt is figyelembe veszi, és irányítja a forgalmat a többi végpont.

Az alábbi ábra ezt a konfigurációt mutatja be:

![A minchildendpoints "tulajdonság" profil feladatátvételi beágyazott = 2][4]

> [!NOTE]
> A "Priority" forgalom-útválasztási módszer egyetlen végpont felé irányuló összes forgalma osztja el. Így nincs kis célú a MinChildEndpoints beállítás csak az "1" gyermekhely profilok.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>3. példa: Rangsorolt feladatátvételi régiója "Teljesítmény" forgalom-Útválasztás

Az alapértelmezett viselkedést, a "Teljesítmény" forgalom-útválasztási módszer van, ha végpontok különböző földrajzi helyeken, a végfelhasználók számára a rendszer a legalacsonyabb hálózati késéssel szempontjából a "legközelebbi" végpontra irányítja.

Előfordulhat azonban hogy inkább a Nyugat-Európa forgalom feladatátvételének USA nyugati Régiójában, és csak irányítja a forgalmat a régiók felé, ha mindkét végpont sem érhető el. Ez a megoldás a "Priority" forgalom-útválasztási módszer egy gyermek profil használatával hozhat létre.

!["Teljesítmény" forgalom-útválasztási kedvezményes feladatátvétellel][6]

A Nyugat-Európa-végpont, mint az USA nyugati RÉGIÓJA végpont magasabb prioritással bír, mivel minden forgalom a Nyugat-Európa-végpontra zajlik, ha mindkét végpont online állapotban. Ha nem sikerül a Nyugat-Európa, USA nyugati RÉGIÓJA átirányítja a forgalmat a. A beágyazott profillal átirányítja a forgalmat Kelet-Ázsia csak akkor, ha a Nyugat-Európa és az USA nyugati RÉGIÓJA sikertelen.

Minden régió esetében ismételje meg az ezt a mintát. Cserélje le a szülő-profilban szereplő összes három végpontok három alárendelt profilok, minden egyes biztosít egy rangsorolt feladatátvételi sorrendjét.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>4. példa: A "Teljesítmény" forgalom ugyanabban a régióban több végpontok közötti útválasztás vezérlése

Tegyük fel, hogy a "teljesítmény" forgalom-útválasztási módszer egy profilt, amely egy adott régióban több végpont már használatban van. Alapértelmezés szerint a régióhoz irányított forgalom egyenletesen legyen elosztva az összes elérhető végpontok az adott régióban.

!["Teljesítmény" forgalom-útválasztási forgalomelosztás régión belüli (alapértelmezés)][7]

Több végpont hozzáadása a Nyugat-Európában, helyett azokat a végpontokat egy külön gyermek profil parancsfájlblokkjában találhatók. A gyermek profil hozzá lesz adva a szülő és a Nyugat-Európában csak végpontnak. A gyermek profil beállításait szabályozhatja az adatforgalom eloszlása a Nyugat-Európa engedélyezése a prioritásalapú vagy súlyozott forgalom-útválasztást, hogy a régión belül.

!["Teljesítmény" forgalom-útválasztási az egyéni régión belüli forgalomelosztás][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>5. példa:-Endpoint figyelési beállítások

Tegyük fel, hogy a Traffic Manager segítségével zökkenőmentesen áttelepítése egy örökölt érkező forgalmat a helyszíni webhely egy új, Azure-ban üzemeltetett felhőalapú verzióra. A régi helyhez használni kívánt a kezdőlap URI hely állapotának figyeléséhez. De az új felhőalapú verzió valósít meg egy egyéni monitorozási oldal (elérési út "/ monitor.aspx"), amely tartalmaz további ellenőrzéseket.

![A TRAFFIC Manager végpont figyelése (alapértelmezés)][9]

Egyetlen profilban lévő összes végpont a Traffic Manager-profil a figyelési beállításokat alkalmazni. A beágyazott profilok használatával egy másik alárendelt profil webhelyenként eltérő figyelési beállítások megadása.

![A TRAFFIC Manager végpont figyelése végpont beállításokkal][10]

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Traffic Manager-profilok](traffic-manager-overview.md)

Ismerje meg, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)

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
