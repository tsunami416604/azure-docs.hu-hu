---
title: Azure-beli virtuális gépek foglalási hibáinak elhárítása | Microsoft Docs
description: Az Azure-beli virtuális gépek létrehozásakor, újraindításakor vagy átméretezésével kapcsolatos foglalási hibák elhárítása
services: virtual-machines
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 04/13/2018
ms.author: cjiang
ms.openlocfilehash: b4750ad9fdfa214aa4d7b6a0355c319e7eb1d9c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77484400"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>A virtuális gépek Azure-beli telepítése, újraindítása vagy átméretezése során jelentkező foglalási hibák elhárítása

Ha virtuális gépet (VM) hoz létre, indítsa újra a leállított (felszabadított) virtuális gépeket, vagy átméretezi a virtuális gépet, Microsoft Azure a számítási erőforrásokat lefoglalja az előfizetéséhez. Folyamatosan fejlesztünk további infrastruktúrát és szolgáltatásokat annak érdekében, hogy mindig minden virtuálisgép-típus elérhető legyen az ügyfelek igényeinek kielégítése érdekében. Alkalmanként azonban előfordulhat, hogy az erőforrás-lefoglalási hibák miatt az Azure-szolgáltatások igény szerinti növekedése az egyes régiókban nem tapasztalt növekedést eredményezett. Ez a probléma akkor fordulhat elő, ha virtuális gépeket próbál létrehozni vagy elindítani egy régióban, miközben a virtuális gépek a következő hibakódot és üzenetet jelenítik meg:

**Hibakód**: AllocationFailed vagy ZonalAllocationFailed

**Hibaüzenet**: "a foglalás nem sikerült. Nem áll rendelkezésre elegendő kapacitás a kért VM-mérethez ebben a régióban. További információ a foglalás sikerességének valószínűségéről a https:\//aka.MS/Allocation-Guidance címen

Ez a cikk ismerteti a gyakori lefoglalási hibák okait, és javaslatot tesz a lehetséges jogorvoslatokra.

Ha az Azure-beli probléma nem szerepel ebben a cikkben, látogasson el az [MSDN webhelyen található Azure-fórumokra, és stack overflow](https://azure.microsoft.com/support/forums/). A problémát ezen fórumokon vagy @AzureSupport a Twitteren teheti közzé. Emellett Azure-támogatási kérelmet is benyújthat, ha a támogatás beszerzése lehetőséget választja az [Azure támogatási](https://azure.microsoft.com/support/options/) webhelyén.

Amíg az előnyben részesített virtuálisgép-típus nem érhető el az előnyben részesített régióban, javasoljuk, hogy az üzembe helyezési problémákkal rendelkező ügyfeleinknek ideiglenes megkerülő megoldásként vegye figyelembe az alábbi táblázatban szereplő útmutatást. 

Azonosítsa az adott esethez legjobban illő forgatókönyvet, majd próbálja megismételni a foglalási kérést a megfelelő javasolt megkerülő megoldással, hogy növelje a kiosztás sikerességének valószínűségét. Azt is megteheti, hogy később újra próbálkozik. Ennek az az oka, hogy elegendő erőforrást lehet a fürtben, régióban vagy zónában megszabadítani a kérelem elfogadásához. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Virtuális gép átméretezése vagy virtuális gépek hozzáadása egy létező rendelkezésreállási csoporthoz

### <a name="cause"></a>Ok

A virtuális gép átméretezésére vagy a virtuális gép meglévő rendelkezésre állási csoportba való felvételére vonatkozó kérést a meglévő rendelkezésre állási készletet üzemeltető eredeti fürtön kell megpróbálkozni. A fürt a kért virtuálisgép-méretet támogatja, de a fürt jelenleg nem rendelkezik elegendő kapacitással. 

### <a name="workaround"></a>Áthidaló megoldás

Ha a virtuális gép egy másik rendelkezésre állási csoport része lehet, hozzon létre egy virtuális gépet egy másik rendelkezésre állási készletben (ugyanabban a régióban). Ezt követően az új virtuális gép hozzáadhatók ugyanahhoz a virtuális hálózathoz.

Állítson le (szabadítson fel) minden virtuális gépet ugyanabban a rendelkezésre állási készletben, majd indítsa újra az összeset.
Leállítás: kattintson az erőforráscsoportok > [az erőforráscsoport] > erőforrások > [a rendelkezésre állási csoport] > Virtual Machines > [a virtuális gép] > leállítás lehetőségre.
Az összes virtuális gép leállítása után válassza ki az első virtuális gépet, majd kattintson az Indítás gombra.
Ez a lépés gondoskodik arról, hogy a rendszer új kiosztási kísérletet futtasson, és egy új fürtöt is kiválaszthat, amely elegendő kapacitással rendelkezik.

## <a name="restart-partially-stopped-deallocated-vms"></a>Részlegesen leállított (felszabadított) virtuális gépek újraindítása

### <a name="cause"></a>Ok

A részleges felszabadítás azt jelenti, hogy a rendelkezésre állási csoportokban egy vagy több, de nem az összes virtuális gép leállt (fel van foglalva). Egy virtuális gép felszabadításakor a rendszer felszabadítja a kapcsolódó erőforrásokat. A virtuális gépek egy részlegesen lefoglalt rendelkezésre állási csoportba való újraindítása megegyezik a virtuális gépek meglévő rendelkezésre állási csoportba való hozzáadásával. Ezért a foglalási kérelmet azon az eredeti fürtön kell megpróbálkozni, amelyen a meglévő rendelkezésre állási csoport nem rendelkezik elegendő kapacitással.

### <a name="workaround"></a>Áthidaló megoldás

Állítson le (szabadítson fel) minden virtuális gépet ugyanabban a rendelkezésre állási készletben, majd indítsa újra az összeset.
Leállítás: kattintson az erőforráscsoportok > [az erőforráscsoport] > erőforrások > [a rendelkezésre állási csoport] > Virtual Machines > [a virtuális gép] > leállítás lehetőségre.
Az összes virtuális gép leállítása után válassza ki az első virtuális gépet, majd kattintson az Indítás gombra.
Ezzel biztosíthatja, hogy a rendszer új kiosztási kísérletet futtasson, és egy új fürtöt is kiválaszthat, amely elegendő kapacitással rendelkezik.

## <a name="restart-fully-stopped-deallocated-vms"></a>Teljesen leállított (felszabadított) virtuális gépek újraindítása

### <a name="cause"></a>Ok

A teljes felszabadítás azt jelenti, hogy egy rendelkezésre állási csoportba tartozó összes virtuális gép leállt (fel van foglalva). A virtuális gépek újraindítására vonatkozó lefoglalási kérelem minden olyan fürtöt céloz meg, amely támogatja a kívánt méretet a régión vagy a zónán belül. Módosítsa a foglalási kérelmet a jelen cikk javaslatai alapján, és próbálja megismételni a kérést, hogy javítsa a kiosztások sikerességének esélyét. 

### <a name="workaround"></a>Áthidaló megoldás

Ha régebbi virtuálisgép-sorozatot vagy-méreteket használ, például a Dv1, a DSv1, a Av1, a D15v2 vagy a DS15v2, érdemes áthelyeznie az újabb verziókat. Tekintse meg ezeket a javaslatokat adott virtuálisgép-méretekhez.
Ha nincs lehetősége más virtuálisgép-méret használatára, próbáljon meg egy másik régióba üzembe helyezni ugyanazon a földrajzi területen belül. További információ az egyes régiókban elérhető virtuálisgép-méretekről:https://aka.ms/azure-regions

Ha rendelkezésre állási zónákat használ, próbáljon meg egy másik zónát a régión belül, amely a kért virtuálisgép-mérethez rendelkezésre álló kapacitással rendelkezhet.

Ha a kiosztási kérelem nagy (több mint 500 mag), tekintse meg a következő részekben ismertetett útmutatást, hogy a kérést kisebb telepítésekre lehessen bontani.

Próbálja meg újból [üzembe helyezni a virtuális gépet](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/redeploy-to-new-node-windows). A virtuális gép újbóli üzembe helyezése a virtuális gépet a régión belüli új fürthöz rendeli.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Foglalási hibák régebbi virtuálisgép-méretek esetében (Av1, Dv1, DSv1, D15v2, DS15v2 stb.)

Az Azure-infrastruktúra kibővítésekor üzembe helyezünk újabb generációs hardvereket, amelyeket a legújabb virtuálisgép-típusok támogatásához terveztek. A régebbi sorozatú virtuális gépek némelyike nem a legújabb generációs infrastruktúrán fut. Ezért előfordulhat, hogy az ügyfelek időnként kiosztási hibákat tapasztalhatnak ezen örökölt SKU-okra vonatkozóan. A probléma elkerülése érdekében javasoljuk, hogy az örökölt sorozatú virtuális gépeket használó ügyfeleinknek a következő javaslatok alapján vegyék fontolóra az egyenértékű újabb virtuális gépekre való áttérést: ezek a virtuális gépek a legújabb hardverre vannak optimalizálva, és lehetővé teszik a jobb díjszabás és teljesítmény előnyeit. 

|Örökölt VM-sorozat/méret|Ajánlott újabb VM-sorozat/méret|További információ|
|----------------------|----------------------------|--------------------|
|Av1 sorozat|[Av2-sorozat](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 vagy DSv1 sorozat (D1 – D5)|[Dv3 vagy DSv3 sorozat](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 vagy DSv1 sorozat (D11 – D14)|[Ev3 vagy ESv3 sorozat](../ev3-esv3-series.md)|
|D15v2 vagy DS15v2|Ha a theResource Manager-alapú üzemi modellt használja a nagyobb méretű virtuálisgép-méretek kihasználása érdekében, érdemes áthelyeznie a D16v3/DS16v3 vagy a D32v3/DS32v3. Ezek úgy lettek kialakítva, hogy a legújabb generációs hardveren fussanak. Ha a Resource Manager-alapú üzemi modellt használja annak biztosítására, hogy a virtuálisgép-példány egyetlen ügyfél számára dedikált hardveren legyen elkülönítve, érdemes lehet áthelyezni az új elkülönített virtuálisgép-méretekre, E64i_v3 vagy E64is_v3re, amelyeket a legújabb generációs hardveren való futtatásra terveztek. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Nagy méretű (500 magnál többet használó) környezetek foglalási hibái

Csökkentse a kért VM-méret példányainak számát, majd próbálja megismételni a telepítési műveletet. Emellett nagyobb telepítések esetén érdemes lehet kiértékelni az [Azure virtuálisgép-méretezési csoportokat](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). A virtuálisgép-példányok száma automatikusan növelheti vagy csökkentheti az igény szerinti vagy egy meghatározott ütemezésre adott válaszokat, és nagyobb eséllyel lehet kiosztani, mivel a központi telepítések több fürtön is elterjedhetnek. 

## <a name="background-information"></a>Háttér-információk
### <a name="how-allocation-works"></a>A foglalás működése
Az Azure-adatközpontokban lévő kiszolgálók fürtökre vannak particionálva. Általában a rendszer több fürtön is próbálkozik egy foglalási kérelemmel, de előfordulhat, hogy a foglalási kérelem bizonyos korlátozásai arra kényszerítik az Azure-platformot, hogy csak egy fürtön próbálkozzon. Ebben a cikkben a következőképpen fogunk hivatkozni: "fürtre rögzítve". Az alábbi ábra egy olyan normál foglalást mutat be, amely több fürtön próbálkozik. A 2. diagram azt szemlélteti, hogy a 2. fürthöz rögzített foglalások esetében a meglévő felhőalapú szolgáltatás CS_1 vagy rendelkezésre állási csoport fut.
![Foglalási diagram](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>A foglalási hibák elhárítása
Ha egy foglalási kérelem egy fürtre van rögzítve, nagyobb eséllyel nem talál szabad erőforrásokat, mert a rendelkezésre álló erőforráskészlet kisebb. Továbbá, ha a foglalási kérelem egy fürthöz van rögzítve, de a kért erőforrás típusa nem támogatott az adott fürtben, a kérés sikertelen lesz, még akkor is, ha a fürt rendelkezik szabad erőforrásokkal. A következő ábra azt az esetet mutatja be, amikor egy rögzített foglalás meghiúsul, mert az egyetlen jelölt fürtnek nincs szabad erőforrása. A 4. ábra azt szemlélteti, hogy a rögzített foglalás meghiúsul, mert az egyetlen jelölt fürt nem támogatja a kért virtuálisgép-méretet, annak ellenére, hogy a fürt rendelkezik szabad erőforrásokkal.

![Rögzített foglalási hiba](./media/virtual-machines-common-allocation-failure/Allocation2.png)


