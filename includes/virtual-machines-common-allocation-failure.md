---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 2eb7fb82b358d4ec8628bfa546b572ee3cbe47fa
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40209641"
---
Hozzon létre egy virtuális gépet (VM), indítsa újra a leállított (felszabadított) virtuális gépek vagy virtuális gép átméretezése, a Microsoft Azure számítási erőforrásokat, amelyek az előfizetés foglal le. A Microsoft folyamatosan fektet be a további infrastruktúra és a szolgáltatások győződjön meg arról, hogy mindig van összes virtuális gép típusát támogatja az ügyfelek igényei szerint. Azonban alkalmanként tapasztalhat erőforrás foglalási hibák miatt az adott régióban található Azure-szolgáltatások iránti igény példa nélküli növekedését tapasztaltuk. Ez a probléma akkor fordulhat elő, amikor megpróbálja létrehozni, vagy indítsa el a virtuális gépek egy régióban, amíg a virtuális gépek a következő hibakóddal és üzenet megjelenítése:

**Hibakód:**: AllocationFailed vagy ZonalAllocationFailed

**Chybová zpráva**: "a lefoglalás sikertelen. Ebben a régióban nincs elegendő kapacitás a kért Virtuálisgép-méretet. További információ: sikeres javítása http://aka.ms/allocation-guidance"

Ez a cikk ismerteti a gyakori hibák némelyike okait, és lehetséges megoldása javasol.

Ha ebben a cikkben nem képes kezelni az Azure-beli probléma, látogasson el a [Azure az MSDN és Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). A probléma megoldásához e-fórumokon, vagy felteheti @AzureSupport a Twitteren. Ezenkívül fájlt a egy Azure-támogatási kérést a Get-támogatás kiválasztásával a [az Azure-támogatás](https://azure.microsoft.com/support/options/) hely.

Mindaddig, amíg az elsődleges virtuális gép típusát az elsődleges régióban érhető el, azt javasoljuk, hogy ügyfelek, akik üzembe helyezési problémák lépnek fel, fontolja meg az ideiglenes megoldás a következő táblázatban található útmutatást. 

Azonosítsa a forgatókönyvet, amely a legjobban illik az Ön esetében, és ismételje meg a foglalási kérelemben sikeres valószínűségének növelése a megfelelő javasolt megoldás használatával. Azt is megteheti mindig újra később. Ennek oka az, elegendő erőforrással előfordulhat, hogy rendelkezik felszabadult a fürt, régió vagy zóna kérelme befogadásához. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Virtuális gép átméretezése, vagy a virtuális gépeket ad hozzá egy meglévő rendelkezésre állási csoport

### <a name="cause"></a>Ok

Állítsa be a virtuális gép átméretezése, vagy adjon hozzá egy meglévő rendelkezésre állási csoport egy virtuális Gépet kell megpróbálható, az eredeti fürthöz, amelyen a meglévő rendelkezésre állási kérést. A kért Virtuálisgép-méret a fürt által támogatott, de a fürt előfordulhat, hogy jelenleg nem rendelkezik elegendő kapacitással. 

### <a name="workaround"></a>Áthidaló megoldás

Ha a virtuális gép egy másik rendelkezésre állási csoport részeként is szerepelhetnek, hozzon létre egy virtuális Gépet egy másik rendelkezésre állási csoportot (ugyanabban a régióban). Az új virtuális gép ezután lehet hozzáadni ugyanahhoz a virtuális hálózathoz.

Állítsa le (szabadítsa fel) minden virtuális gép egyazon rendelkezésre állási állítsa be, majd indítsa újra a mindegyikhez.
Leállítása: kattintson az erőforráscsoportok > [az erőforráscsoport] > erőforrások > [a rendelkezésre állási csoport] > virtuális gépek > [a virtuális gép] > leállítása.
Miután az összes virtuális gép leállításához válassza ki az első virtuális gép, és válassza az Indítás parancsot.
Ez a lépés biztosítja, hogy, hogy fut-e egy új foglalási kísérlet és, hogy egy új fürtöt választható ki, amelynek elegendő a kapacitása.

## <a name="restart-partially-stopped-deallocated-vms"></a>Részlegesen leállított (felszabadított) virtuális gépek újraindítása

### <a name="cause"></a>Ok

Részleges felszabadítási azt jelenti, hogy Ön leállítva (felszabadított) egy vagy több, de nem az összes virtuális gép egy rendelkezésre állási beállítása. Felszabadíthat egy virtuális Gépet, ha a kapcsolódó erőforrások jelennek meg. Virtuális gépek újraindítása részlegesen felszabadított rendelkezésre állási csoportban megegyezik a virtuális gépeket ad hozzá egy meglévő rendelkezésre állási csoportot. Ezért a foglalási kérelemben, az eredeti fürthöz, hogy a gazdagép a meglévő rendelkezésre állási csoportban, előfordulhat, hogy rendelkezik-e elegendő kapacitással, melynek.

### <a name="workaround"></a>Áthidaló megoldás

Állítsa le (szabadítsa fel) minden virtuális gép egyazon rendelkezésre állási állítsa be, majd indítsa újra a mindegyikhez.
Leállítása: kattintson az erőforráscsoportok > [az erőforráscsoport] > erőforrások > [a rendelkezésre állási csoport] > virtuális gépek > [a virtuális gép] > leállítása.
Miután az összes virtuális gép leállításához válassza ki az első virtuális gép, és válassza az Indítás parancsot.
Ezzel biztosíthatja, hogy fut-e egy új foglalási kísérlet és, hogy egy új fürtöt választható ki, amelynek elegendő a kapacitása.

## <a name="restart-fully-stopped-deallocated-vms"></a>Teljes körűen leállított (felszabadított) virtuális gépek újraindítása

### <a name="cause"></a>Ok

Teljes felszabadítási azt jelenti, hogy az Ön által leállítva (felszabadítva) egy rendelkezésre állási csoport összes virtuális gépére. Ezek a virtuális gépek újraindítását a foglalási kérelemben által megcélzott összes fürt, amely támogatja a kívánt méret, régió vagy zóna belüli. Módosítsa a foglalási kérelemben száma ebben a cikkben a javaslatok, és ismételje meg a kérelmet az esélye, hogy sikeres javítása érdekében. 

### <a name="workaround"></a>Áthidaló megoldás

Ha régebbi Virtuálisgép-sorozatok vagy méretű, például a Dv1, DSv1, Av1, D15v2 vagy DS15v2, érdemes újabb verzióra való áthelyezése. Tekintse meg ezeket a javaslatokat az adott Virtuálisgép-méretek.
Ha nem rendelkezik arra, hogy egy másik Virtuálisgép-méretet használja, próbálja másik ugyanazon a földrajzi régióban üzembe. További információ: minden régióban elérhető Virtuálisgép-méretek https://aka.ms/azure-regions

Ha a rendelkezésre állási zónák használ, próbálja meg, előfordulhat, hogy a kért Virtuálisgép-méret elérhető kapacitás a régión belül egy másik zónában.

Ha a foglalási kérelemben túl nagy (500-nál több mag), tekintse meg a kérelem felbontása kisebb környezetekhez érdekében az alábbi szakaszokban található útmutatást.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Foglalási hibák régebbi Virtuálisgép-méretek (Av1 Dv1, DSv1, D15v2, DS15v2, stb.)

Azure-infrastruktúra bővítésekor üzembe új generációs tervezték, hogy a virtuális gépek legújabb típusainak támogatására. A régebbi sorozatú virtuális gépek némelyike nem futtathatók a új generációs infrastruktúrán. Ezért esetenként tapasztalt hibák ezen régebbi termékváltozatok esetében. Ez a probléma elkerülése érdekében azt javasoljuk, érdemes megfontolni az egyenértékű újabb virtuális gépek száma az alábbi javaslatok a régi sorozatú virtuális gépek használó ügyfeleink: ezekre a virtuális gépekre a legújabb hardverekre vannak optimalizálva, és lehetővé teszi, hogy jobban kihasználhatja díjszabás és teljesítmény. 

|Virtuálisgép-sorozat vagy méretének örökölt|Javasolt Virtuálisgép-sorozat vagy méretének újabb|További információ|
|----------------------|----------------------------|--------------------|
|Av1-sorozat|[Az Av2 sorozat](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 vagy DSv1 sorozat (D1-D5)|[Dv3 és a Dsv3 sorozat](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 vagy DSv1 sorozat (D11 – D14)|[Ev3 vagy az Esv3-adatsorok](../articles/virtual-machines/windows/sizes-memory.md#esv3-series)|
|D15v2 vagy DS15v2|TheResource Manager üzemi modell használatakor a nagyobb Virtuálisgép-méretek kihasználása érdekében érdemes lehet E16v3/E16sv3 vagy E32v3/E32sv3 történő áthelyezését. Ezek a legújabb generációs hardveren futtatott tervezték. Ha a Resource Manager üzemi modell használatával ellenőrizze, hogy a Virtuálisgép-példány egyetlen ügyfél számára dedikált hardverre elkülönített, fontolja meg az új elkülönített Virtuálisgép-méretek, E64i_v3 vagy E64is_v3, amely tervezték, hogy a legújabb generációs hardveren futtatott. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/


## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Foglalási hibák nagyméretű környezetekben (500-nál több mag)

A kért Virtuálisgép-méret példányainak számát csökkentheti, és próbálkozzon újra a központi telepítési művelettel. Ezenkívül nagyobb telepítések esetén előfordulhat, hogy szeretne értékelni [Azure-beli virtuálisgép-méretezési csoportok](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). A Virtuálisgép-példányok száma automatikusan növelheti vagy csökkentheti a pillanatnyi igényeknek megfelelően vagy egy meghatározott ütemezés, és láthatja, hogy nagyobb az esélye annak sikeres központi telepítéseket is több fürt elosztva. 

## <a name="background-information"></a>Háttér-információk
### <a name="how-allocation-works"></a>Foglalási működése
Az Azure-adatközpontokban lévő kiszolgálók fürtökre vannak particionálva. Általában a rendszer több fürtön is próbálkozik egy foglalási kérelemmel, de előfordulhat, hogy a foglalási kérelem bizonyos korlátozásai arra kényszerítik az Azure-platformot, hogy csak egy fürtön próbálkozzon. Ebben a cikkben azt fog hivatkozni erre, "fürt rögzített." 1 alábbi ábra a kis-és a egy normál foglalási, amely több fürtön kísérlet történik. 2. ábra szemlélteti a kis-és a egy elkülönített, amelyek fürt 2 van rögzítve, mert a meglévő felhőalapú szolgáltatás CS_1 vagy rendelkezésre állási készlet üzemeltető.
![Foglalási diagramja](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Miért fordulhat elő, hibák
Egy memóriafoglalási kérelem rögzítve van egy fürthöz, amikor nincs ingyenes forrásokat, mivel a rendelkezésre álló erőforrás-készlet nem magasabb esélyét. Továbbá ha a foglalási kérelem rögzítve van egy fürthöz, de a fürt nem támogatja a kért erőforrás típusát, a kérelem sikertelen lesz akkor is, ha a fürt ingyenes erőforrás. A következő ábra 3 mutatja be az esetben, ahol egy rögzített felosztás meghiúsul, mert az csak jelölt fürtnek nincs szabad erőforrás. 4. ábra mutatja be az esetben, ahol egy rögzített felosztás meghiúsul, mert a csak jelölt fürt nem támogatja a kért Virtuálisgép-méret, annak ellenére, hogy a fürt rendelkezik ingyenes erőforrások.

![Rögzített foglalási hiba](./media/virtual-machines-common-allocation-failure/Allocation2.png)


