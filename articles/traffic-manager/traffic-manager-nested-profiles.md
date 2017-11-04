---
title: "Traffic Manager-profilokat a beágyazott |} Microsoft Docs"
description: "Ez a cikk ismerteti a beágyazott profilok szolgáltatást az Azure Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 1ac4ec2775ca9f690f5adf4f939908f8cee3f715
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="nested-traffic-manager-profiles"></a>Beágyazott Traffic Manager-profilok

A TRAFFIC Manager forgalom-útválasztási módszereket, amelyek segítségével szabályozhatja, hogyan Traffic Manager úgy dönt, hogy melyik végponthoz forgalmat mindegyik végfelhasználói kell fogadni számos tartalmazza. További információkért lásd: [Traffic Manager forgalom-útválasztási módszert](traffic-manager-routing-methods.md).

Minden egyes Traffic Manager-profil megadja egy forgalom-útválasztási módszert. Vannak azonban forgatókönyv esetén van szükség, kifinomultabb a forgalom útválasztásához, mint egyetlen Traffic Manager-profil által biztosított útválasztását. Traffic Manager-profilok egynél több forgalom-útválasztási módszer előnyei egyesítése ágyazhatja be. Beágyazott profilok lehetővé teszik a támogatási nagyobb és összetettebb alkalmazások központi telepítésének alapértelmezett Traffic Manager működés felülbírálásához.

A következő példák bemutatják a különböző forgatókönyvekben beágyazott Traffic Manager-profilok használatával.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>1. példa: Kombinálásával "Teljesítmény" és "Weighted" forgalom-Útválasztás

Tegyük fel, hogy telepítette-e az alkalmazás a következő Azure-régiók: USA nyugati régiója, Nyugat-Európában és Kelet-Ázsia. A Traffic Manager "Teljesítmény" forgalom-útválasztási módszer használatával terjesztheti a forgalmat a felhasználó legközelebb eső régiót.

![Egy Traffic Manager-profil][4]

Most tegyük fel, mielőtt további széles körben egy frissítést adunk ki a szolgáltatás tesztelni kívánt. A "súlyozott" forgalom-útválasztási módszer át tudja irányítani a teszt telepítéséhez forgalom csekély használni kívánt. Nyugat-Európában beállítása mellett a meglévő éles környezet üzembe helyezési teszt.

Nem lehet kombinálni mindkét Weighted és "teljesítmény forgalom-útválasztási egyetlen profilban. A forgatókönyv támogatása érdekében hozzon létre egy Traffic Manager-profil, Nyugat-Európában végpontokat és a "Weighted" forgalom-útválasztási módszer használatával. Ezt követően adja hozzá a "child" profil végpontjaként a "parent" profil. A szülő profil továbbra is a teljesítmény forgalom-útválasztási módszert használja, és a globális központilag olyan végpontként tartalmazza.

Az alábbi ábrán ez a példa látható:

![Beágyazott Traffic Manager-profilok][2]

Ebben a konfigurációban keresztül a szülő profil irányított forgalom osztja el a forgalmat különböző régiókban általában. Nyugat-Európában, belül az egymásba ágyazott profilelérési osztja el a forgalmat a termelési és tesztelési végpontokra a hozzárendelt súlyozások szerint.

Ha a szülő-profil a "Teljesítmény" forgalom-útválasztási módszert használ, minden egyes végpont hozzá kell rendelni egy helyre. A hely van hozzárendelve, amikor konfigurálja a végpontot. Válassza ki a központi telepítés legközelebbi Azure-régiót. Az Azure-régiók a a Internet késés tábla által támogatott értékei. További információkért lásd: ["Teljesítmény" Traffic Manager forgalom-útválasztási módszer](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>2. példa: A Végpontmonitoring kijelző a beágyazott profilok

A TRAFFIC Manager aktívan az egyes szolgáltatási végpont állapotát figyeli. A végpont nem kifogástalan, ha a Traffic Manager arra utasítja a felhasználók alternatív végpontokkal való megőrzéséhez a szolgáltatás rendelkezésre állását. A figyelés és a feladatátvételi végpontviselkedéshez összes forgalom-útválasztási módszer vonatkozik. További információkért lásd: [Traffic Manager-végpont figyelés](traffic-manager-monitoring.md). Végpontmonitoring kijelző eltérő módon működik a beágyazott profilok. Beágyazott profilok a szülő profil nem ellenőrzi a állapota a gyermek közvetlenül. Ehelyett a gyermek profil végpontok állapotát rendszer kiszámítja a gyermek profil általános állapotát. Ez állapottal kapcsolatos adatok mentése az egymásba ágyazott profilelérési hierarchia propagálja. A szülő profil ez összesített állapotát határozza meg, hogy át tudja irányítani a forgalmat gyermek profilt használ. Tekintse meg a [gyakran ismételt kérdések](traffic-manager-FAQs.md#traffic-manager-nested-profiles) a beágyazott profilok állapotfigyelését teljes leírását.

Visszatérés az előző példában, tegyük fel, Nyugat-Európában az éles központi telepítése sikertelen. Alapértelmezés szerint a "child" profil irányítja a teszttelepítés bejövő forgalmát. Ha a teszt telepítése is sikertelen, a a szülő profil határozza meg, hogy a gyermek profil nem kell kapnia forgalom, mivel az összes gyermek-végpontok a nem megfelelő. A szülő profil szétosztja a más régiókban forgalmat.

![Beágyazott profil feladatátvevő (alapértelmezés)][3]

Előfordulhat, hogy ezzel az elrendezéssel elégedett. Vagy lehet, hogy összes forgalmat a Nyugat-Európában most fog az üzembe helyezési teszt helyett egy korlátozott részhalmaza forgalom érintett. Üzembe helyezési teszt állapotát, függetlenül kívánt átveheti a más régiókban az éles környezet Nyugat-Európában meghibásodása esetén. Ahhoz, hogy a feladatátvétel, a szülő profil végpontjaként a gyermek-profil konfigurálásakor megadhatja a "MinChildEndpoints" paraméter. A paraméter a gyermek profil számára elérhető végpontok minimális számát határozza meg. Az alapértelmezett érték: "1". Ebben a forgatókönyvben a MinChildEndpoints érték 2 beállítani. Az ezen küszöbérték alatti a szülő profil úgy ítéli meg, a teljes gyermek profil nem érhető el, és forgalmát átirányítja a többi végpont.

Az alábbi ábra az ebben a konfigurációban:

![A beágyazott "MinChildEndpoints" profil feladatátvétel = 2][4]

> [!NOTE]
> A "Priority" forgalom-útválasztási módszer elosztása az összes forgalmat egy végpontot. Így nincs kevés célja a MinChildEndpoints beállítás csak az "1" egy gyermek profil.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>3. példa: Rangsorolt feladatátvételi régiók "Teljesítmény" forgalom-Útválasztás

A "Teljesítmény" forgalom-útválasztási módszer az alapértelmezett működést célja, hogy túlzott betöltése a következő végpont legközelebbi és okozó kaszkádolt sorozatos hibák elkerülése érdekében. A végpont nem sikerül, ha összes forgalom az adott végpontra átirányítva volna egyenletesen vannak elosztva a többi végpont a minden egyes között.

![Útválasztás és alapértelmezett feladatátvétel "Teljesítmény" forgalom][5]

Előfordulhat azonban hogy inkább a Nyugat-Európában forgalom feladatátvételének USA nyugati régiója, és más régiókban forgalmat csak irányítani, ha mindkét végpont nem érhetők el. Ez a megoldás gyermek profilt használ, a "Priority" forgalom-útválasztási módszerrel hozhat létre.

!["Teljesítmény" forgalom-útválasztási kedvezményes feladatátvételi][6]

Mivel a Nyugat-Európában végpont csak a magasabb prioritású, mint az USA nyugati régiója végpont, az összes forgalmat elküldi a Nyugat-Európában végpont mindkét végpont online állapotba kerülnek. Nyugat-Európában nem sikerül, ha a forgalom USA nyugati régiója van átirányítva. A beágyazott profillal forgalom Kelet-Ázsia irányul, csak akkor, ha mind a Nyugat-Európában, és az USA nyugati régiója nem.

Minden régió esetében ismételje meg az ebben a mintában. Cserélje le a szülő-profilban szereplő minden három végpontok három alárendelt profilok, egyes a rangsorolt feladatátvételi feladatütemezési biztosítása.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>4. példa: A "Teljesítmény" a forgalom útválasztásához ugyanabban a régióban több végpontok közötti vezérlése

Tegyük fel, hogy a "teljesítmény" forgalom-útválasztási módszert használ, amely több végpont rendelkezik az adott profil. Alapértelmezés szerint a régióba irányított forgalmat is terjeszthető egyenletesen az adott régióban elérhető végpontjai.

!["Teljesítmény" forgalom-útválasztási régió a forgalom eloszlása (alapértelmezés)][7]

Több végpont hozzáadása a Nyugat-Európában, helyett ezekre a végpontokra egy külön gyermek profil parancsfájlblokkjában találhatók. A gyermek profil hozzá lesz adva a szülő Nyugat-Európában csak végpontjának. A gyermek profiljának beállításait szabályozhatja a forgalom eloszlása, Nyugat-Európa engedélyezése a prioritásalapú vagy súlyozott forgalom-útválasztás adott régión belül.

!["Teljesítmény" forgalom-útválasztási az egyéni terület a forgalom eloszlása][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>5. példa: A végpont figyelési beállításokat

Tegyük fel, hogy a Traffic Manager segítségével zökkenőmentesen áttelepítése egy örökölt érkező forgalmat a helyi webhely egy új, Azure-ban üzemeltetett felhőalapú verzióra. A régi helyhez használni kívánt a kezdőlap URI hely állapotának figyeléséhez. Új felhőalapú verzióhoz, egy egyéni figyelési lapjának készül olyanokat, de (elérési út "/ monitor.aspx"), amely tartalmazza a további ellenőrzéseket.

![Traffic Manager-végpont figyelése (alapértelmezés)][9]

A figyelési beállításokat a Traffic Manager-profil alkalmazása egyetlen profilon belüli összes végponton. Beágyazott profilok segítségével egy másik alárendelt profil minden helyen különböző figyelési beállításainak megadása.

![Figyelés végpont beállítások a TRAFFIC Manager-végpontot][10]

## <a name="next-steps"></a>Következő lépések

További információ [Traffic Manager-profilok](traffic-manager-overview.md)

Megtudhatja, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)

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
