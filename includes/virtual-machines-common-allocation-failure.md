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
ms.openlocfilehash: 24d89b617c347bc9443b437c92cb034acb3e05cb
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33901329"
---
Hozzon létre egy virtuális gépet (VM), indítsa újra a leállított (felszabadított) virtuális gépek vagy átméretezni egy virtuális Gépet, a Microsoft Azure előfizetéshez számítási erőforrásokat foglal le. Azt is folyamatosan terhelésnél további infrastruktúrával, és győződjön meg arról, hogy mindig minden virtuális gép esetében keresletének támogató szolgáltatások. Egyedülálló növekszik az Azure meghatározott régióiba-szolgáltatások igény miatt azonban erőforrás pufferallokációs hibák alkalmanként tapasztalhatja. Ez a probléma akkor fordulhat elő, létrehozásához, vagy indítsa el a virtuális gépek régióban közben a virtuális gépek a következő hibakód és üzenet megjelenítése közben:

**Hibakód**: AllocationFailed vagy ZonalAllocationFailed

**Hibaüzenet**: "a lefoglalás sikertelen. Ebben a régióban jelenleg nem rendelkezik elegendő kapacitással a kért Virtuálisgép-méretet a. További információk: foglalási sikeres valószínűségét javítása http://aka.ms/allocation-guidance"

Ez a cikk ismerteti az egyes a közös pufferallokációs hibák okait, és megkísérelheti javasol.

Ha az Azure nem problémával az ebben a cikkben, látogasson el a [MSDN és a Stack Overflow Azure fórumok](https://azure.microsoft.com/support/forums/). A probléma, beküldheti, ezek fórumokban, vagy @AzureSupport a Twitteren. Emellett fájlt a az Azure támogatási kérelmet választva módosíthatja a Get-támogatás a [az Azure támogatási](https://azure.microsoft.com/support/options/) hely.

Az elsődleges virtuális gép típusát az elsődleges régióban érhető el, amíg az ügyfelek, akik a telepítési problémák figyelembe kell venni az útmutató a következő táblázat ideiglenes megoldásként ajánlott. 

A forgatókönyvet, amely a legjobban illik az Ön esetében azonosítása, majd próbálkozzon újra a memóriafoglalási kérelem foglalási sikeres valószínűségének növelésére a megfelelő javasolt megoldás segítségével. Azt is megteheti hogy mindig újra később. Ennek az az oka erőforrásokkal előfordulhat, hogy rendelkezik felszabadult a fürt, a régióban, vagy a zónát, hogy megfeleljen a kérést. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Méretezze át a virtuális gépek vagy virtuális gépek felvétele a rendelkezésre állási készlet

### <a name="cause"></a>Ok

Kérelem méretezze át egy virtuális Gépet, vagy adja hozzá a virtuális gépek rendelkezésre állási készlet kell próbálkozik a rendszer az eredeti fürt, amelyen a meglévő rendelkezésre állási, állítsa be. A kért Virtuálisgép-méretet a fürt által támogatott, de a fürt jelenleg nincs elegendő kapacitással. 

### <a name="workaround"></a>Áthidaló megoldás

A virtuális gép egy másik rendelkezésre állási készlet része lehet, ha egy másik rendelkezésre állási csoport (ugyanabban a régióban) a virtuális gép létrehozása. Új virtuális gép felvehető ugyanahhoz a virtuális hálózathoz.

Állítsa le (felszabadítása) minden virtuális gép ugyanabban a rendelkezésre állási állítsa be, majd indítsa újra a minden egyes.
Leállítása: kattintson az erőforráscsoportok > [az erőforráscsoport] > erőforrások > [a rendelkezésre állási csoport] > virtuális gépek > [a virtuális gép] > leállítása.
Után minden virtuális gép leállításához jelölje ki az első virtuális gép, és válassza az Indítás parancsot.
Ez a lépés biztosítja, hogy fut-e egy új foglalási kísérlet és, hogy egy új fürt választható ki, amely rendelkezik elegendő kapacitással.

## <a name="restart-partially-stopped-deallocated-vms"></a>Indítsa újra a részlegesen leállított (felszabadított) virtuális gépek

### <a name="cause"></a>Ok

Részleges felszabadítás azt jelenti, hogy (felszabadított) egy vagy több leállt, de nem minden, a rendelkezésre állási virtuális gépeket. Ha egy virtuális gép felszabadítani a kapcsolódó erőforrások kiadásakor. Részlegesen felszabadított rendelkezésre állási csoportban lévő virtuális gépek újraindításával megegyezik a virtuális gépek hozzáadása egy meglévő rendelkezésre állási csoportot. Ezért az eredeti fürt, hogy a gazdagép a meglévő rendelkezésre állási csoport, amely előfordulhat, hogy rendelkezik-e elegendő kapacitással a próbált kell a memóriafoglalási kérelem.

### <a name="workaround"></a>Áthidaló megoldás

Állítsa le (felszabadítása) minden virtuális gép ugyanabban a rendelkezésre állási állítsa be, majd indítsa újra a minden egyes.
Leállítása: kattintson az erőforráscsoportok > [az erőforráscsoport] > erőforrások > [a rendelkezésre állási csoport] > virtuális gépek > [a virtuális gép] > leállítása.
Után minden virtuális gép leállításához jelölje ki az első virtuális gép, és válassza az Indítás parancsot.
Ezzel biztosíthatja, hogy fut-e egy új foglalási kísérlet és, hogy egy új fürt választhat ki, amely rendelkezik elegendő kapacitással.

## <a name="restart-fully-stopped-deallocated-vms"></a>Indítsa újra a teljes leállított (felszabadított) virtuális gépek

### <a name="cause"></a>Ok

Teljes felszabadítás azt jelenti, hogy leállította (felszabadítva) minden virtuális gép rendelkezésre állási csoportba. A virtuális gépek újraindítására a memóriafoglalási kérelem által megcélzott összes fürt, amely támogatja a kívánt méretet, a régiót vagy zóna belül. A cikkben szereplő javaslatok / memóriafoglalási kérelem módosítsa, majd ismételje meg a kérelmet a eséllyel sikerül, felosztási javítása érdekében. 

### <a name="workaround"></a>Áthidaló megoldás

Ha a régebbi Virtuálisgép-sorozat vagy méretek, például a d.v.1, DSv1, Av1, D15v2 vagy DS15v2, fontolja meg újabb verzióit. Lásd: ezek a javaslatok, az adott VM-méretek.
Ha nem is használhat egy másik Virtuálisgép-méretet, próbálja másik belül az azonos földrajzi régióban üzembe helyezni. További információ az elérhető Virtuálisgép-méretek minden régióban: https://aka.ms/azure-regions

Ha a rendelkezésre állási zónákat használ, próbálja meg, amelyek a kért Virtuálisgép-méret szükséges kapacitás régión belül egy másik zónában.

Ha a memóriafoglalási kérelem nagy (több mint 500 magok), lásd az alábbi szakaszok azokat a kisebb telepítésekhez a kérelmet.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Pufferallokációs hibák az régebbi Virtuálisgép-méretek (Av1 d.v.1, DSv1, D15v2, DS15v2, stb.)

Azt bontsa ki az Azure-infrastruktúra, mivel azt telepíteni van kialakítva, hogy támogatja a legújabb virtuális gépek típusainak újabb generációs hardvert. A régebbi adatsorozat virtuális gépek némelyike nem futnak a legújabb generációs infrastruktúra. Emiatt alkalmanként tapasztalt pufferallokációs hibák az ezeket a hagyományos SKU. Ez a probléma elkerülése érdekében javasoljuk, fontolja meg a megfelelő újabb virtuális gépek száma a következők javaslatok az örökölt adatsorozat virtuális gépek használó ügyfelek: ezek virtuális gépek vannak optimalizálva, a legújabb hardveres, és lehetővé teszi, hogy jobb kihasználása árak és teljesítmény. 

|Virtuálisgép-sorozat vagy méretének örökölt|Virtuálisgép-sorozat vagy méretének újabb ajánlott|További információ|
|----------------------|----------------------------|--------------------|
|Av1-sorozat|[Av2-sorozat](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|D.v.1 vagy DSv1-sorozat (D5 a D1)|[Dv3 vagy DSv3-sorozat](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|D.v.1 vagy DSv1-sorozat (a D14 D11)|[Ev3 vagy ESv3-sorozat](../articles/virtual-machines/windows/sizes-memory.md#ev3-series)|
|D15v2 vagy DS15v2|Ha a nagyobb Virtuálisgép-méretek kihasználása érdekében theResource Manager telepítési modellt használ, fontolja meg D16v3/DS16v3 vagy D32v3/DS32v3. Ezek a legújabb generációs hardver futtatásra tervezték. Ha a Resource Manager üzembe helyezési modellel segítségével ellenőrizze, hogy a Virtuálisgép-példány az egyetlen ügyfél számára dedikált hardverre elkülönített, fontolja meg az új elszigetelt Virtuálisgép-méretek, E64i_v3 vagy E64is_v3, amely a legújabb generációs hardverre futnak. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Pufferallokációs hibák nagy méretű telepítéséhez (több mint 500 mag)

Adjon meg kevesebb a kért Virtuálisgép-méret példányait, és ismételje meg a központi telepítési műveletet. Emellett nagyobb telepítések esetén érdemes lehet kiértékelni [Azure virtuálisgép-méretezési csoportok](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). A Virtuálisgép-példányok száma automatikusan növelhető és csökkenthető igény szerint vagy egy meghatározott ütemezés, és egy nagyobb eséllyel sikerül, lemezfoglalási rendelkezik, mert a központi telepítéseket is elosztva több fürt. 

## <a name="background-information"></a>Háttér-információkat
### <a name="how-allocation-works"></a>Foglalási működése
Az Azure-adatközpontokban lévő kiszolgálók fürtökre vannak particionálva. Általában a rendszer több fürtön is próbálkozik egy foglalási kérelemmel, de előfordulhat, hogy a foglalási kérelem bizonyos korlátozásai arra kényszerítik az Azure-platformot, hogy csak egy fürtön próbálkozzon. Ebben a cikkben kifejezés ez szerint "fürtre van rögzítve." 1. ábra alatt a kis-és a normál foglalási több fürt megkísérlő mutatja be. 2. ábrán látható, a kis-és memóriakiosztást, mert, ahol a felhőalapú szolgáltatás CS_1 vagy rendelkezésre állási készlet tárolása fürt 2 van rögzítve.
![Foglalási diagramja](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Miért fordulhat elő, pufferallokációs hibák
Egy memóriafoglalási kérelem fürtre van rögzítve, nincs szabad erőforrások keresésére, mivel a rendelkezésre álló erőforráskészlet kisebb sikertelen magasabb esélyét. Továbbá ha a memóriafoglalási kérelem fürtre van rögzítve, de az adott fürt nem támogatja a kért erőforrás típusát, a kérelem sikertelen lesz akkor is, ha a fürt szabad erőforrást. A következő Diagram 3 mutatja be, hogy az esetet, ahol egy rögzített foglalás sikertelen lesz, mivel az egyetlen jelölt fürtnek nincs szabad erőforrást. 4. ábrán látható, az esetet, ahol egy rögzített foglalás sikertelen lesz, mivel az egyetlen jelölt fürt nem támogatja a kért Virtuálisgép-méret, annak ellenére, hogy a fürt ingyenes erőforrással rendelkezik.

![Rögzített memóriafoglalási hiba](./media/virtual-machines-common-allocation-failure/Allocation2.png)


