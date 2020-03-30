---
title: Az Azure virtuális gépek foglalási hibáinak hibaelhárítása | Microsoft dokumentumok
description: A foglalási hibák elhárítása, amikor virtuális gépeket hoz létre, újraindít vagy átméretez az Azure-ban
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484400"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>A virtuális gépek Azure-beli telepítése, újraindítása vagy átméretezése során jelentkező foglalási hibák elhárítása

Amikor virtuális gépet (VM), újraindítás leállt (felszabadított) virtuális gépeket hoz létre, vagy átméretezi a virtuális gépet, a Microsoft Azure számítási erőforrásokat rendel az előfizetéshez. Folyamatosan beruházunk további infrastruktúrába és funkciókba, hogy mindig minden virtuálisgép-típus elérhető legyen az ügyfelek igényeinek kielégítésére. Előfordulhat azonban, hogy az egyes régiókban az Azure-szolgáltatások iránti kereslet soha nem látott növekedése miatt időnként erőforrás-elosztási hibák léphetnek fel. Ez a probléma akkor fordulhat elő, ha virtuális gépeket próbál létrehozni vagy elindítani egy régióban, miközben a virtuális gépek a következő hibakódot és üzenetet jelenítik meg:

**Hibakód**: A allocationfailed vagy a ZonalAllocationFailed

**A következő hibaüzenet**jelenik meg: "A foglalás nem sikerült. Ebben a régióban nem rendelkezünk elegendő kapacitással a kért virtuális gép méretéhez. További információ a kiosztás sikerének\/valószínűségéről https: /aka.ms/allocation-guidance"

Ez a cikk ismerteti a közös felosztási hibák okait, és lehetséges jogorvoslatokat javasol.

Ha az Azure-probléma nem foglalkozik ebben a cikkben, keresse fel az [Azure-fórumok ON MSDN és stack overflow.](https://azure.microsoft.com/support/forums/) Tudod felad-a kérdés ezeken @AzureSupport a fórumokon, vagy a Twitter. Emellett az Azure-támogatási kérelem beérkeztetése az [Azure támogatási](https://azure.microsoft.com/support/options/) webhelyén a Támogatás kérése lehetőséget választva is nyújthat be.

Amíg a kívánt virtuális gép típusa nem érhető el a kívánt régióban, azt tanácsoljuk az üzembe helyezési problémákkal szembesülő ügyfeleknek, hogy az alábbi táblázatban szereplő útmutatást ideiglenes megoldásként tekintsék. 

Azonosítsa az esetnek leginkább megfelelő forgatókönyvet, majd próbálkozzon újra a foglalási kérelemmel a megfelelő javasolt megoldás használatával a foglalás sikeressearányának növelése érdekében. Azt is megteheti, hogy később újrapróbálkozik. Ennek az az oka, hogy elegendő erőforrás felszabadult a fürtben, régióban vagy zónában a kérés teljesítéséhez. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Virtuális gép átméretezése vagy virtuális gépek hozzáadása egy létező rendelkezésreállási csoporthoz

### <a name="cause"></a>Ok

A virtuális gép átméretezésére vagy egy meglévő rendelkezésre állási csoporthoz való hozzáadására vonatkozó kérést a meglévő rendelkezésre állási csoportot tartalmazó eredeti fürtön kell próbálkozni. A kért virtuális gép méretét a fürt támogatja, de előfordulhat, hogy a fürt jelenleg nem rendelkezik elegendő kapacitással. 

### <a name="workaround"></a>Áthidaló megoldás

Ha a virtuális gép része lehet egy másik rendelkezésre állási csoport, hozzon létre egy virtuális gép egy másik rendelkezésre állási csoport (ugyanabban a régióban). Ez az új virtuális gép ezután hozzáadható ugyanahhoz a virtuális hálózathoz.

Állítsa le (szabadítsa fel) az összes virtuális gépet ugyanabban a rendelkezésre állási csoportban, majd indítsa újra mindegyiket.
Leállítás: Kattintson az Erőforráscsoportok > [az erőforráscsoport] > erőforrások > [a rendelkezésre állási csoport] > a virtuális gépek > [a virtuális gép] > stop.
Miután az összes virtuális gép leáll, jelölje ki az első virtuális gépet, majd kattintson a Start gombra.
Ez a lépés biztosítja, hogy egy új foglalási kísérlet fut, és hogy egy új fürt lehet kiválasztani, amely elegendő kapacitással rendelkezik.

## <a name="restart-partially-stopped-deallocated-vms"></a>Részlegesen leállított (felszabadított) virtuális gépek újraindítása

### <a name="cause"></a>Ok

A részleges feloldás azt jelenti, hogy egy vagy több, de nem az összes virtuális gépet leállított (felszabadított) egy rendelkezésre állási csoportban. Virtuális gép felszabadításakor a kapcsolódó erőforrások felszabadulnak. A virtuális gépek újraindítása egy részlegesen felszabadított rendelkezésre állási csoportban megegyezik a virtuális gépek meglévő rendelkezésre állási csoporthoz való hozzáadásával. Ezért a foglalási kérelmet meg kell próbálni az eredeti fürt, amely a meglévő rendelkezésre állási készlet, amely nem rendelkezik elegendő kapacitással.

### <a name="workaround"></a>Áthidaló megoldás

Állítsa le (szabadítsa fel) az összes virtuális gépet ugyanabban a rendelkezésre állási csoportban, majd indítsa újra mindegyiket.
Leállítás: Kattintson az Erőforráscsoportok > [az erőforráscsoport] > erőforrások > [a rendelkezésre állási csoport] > a virtuális gépek > [a virtuális gép] > stop.
Miután az összes virtuális gép leáll, jelölje ki az első virtuális gépet, majd kattintson a Start gombra.
Ez biztosítja, hogy egy új foglalási kísérlet fut, és hogy egy új fürt lehet kiválasztani, amely elegendő kapacitással rendelkezik.

## <a name="restart-fully-stopped-deallocated-vms"></a>Teljesen leállított (felszabadított) virtuális gépek újraindítása

### <a name="cause"></a>Ok

A teljes feloldás azt jelenti, hogy leállította (felszabadította) az összes virtuális gépet egy rendelkezésre állási csoportban. A virtuális gépek újraindítására vonatkozó foglalási kérelem a régión vagy zónán belül a kívánt méretet támogató összes fürtre irányul. Módosítsa a foglalási kérelmet a cikkben szereplő javaslatok szerint, és próbálkozzon újra a foglalás sikeressek esélyét. 

### <a name="workaround"></a>Áthidaló megoldás

Ha régebbi virtuálisgép-sorozatokat vagy méreteket használ, például Av1, DSv1, Av1, D15v2 vagy DS15v2, fontolja meg az újabb verziókra való áttérést. Tekintse meg ezeket a javaslatokat a virtuális gép méretei.
Ha nem rendelkezik egy másik virtuális gép méretének használatával, próbálja meg telepíteni egy másik régióban ugyanazon a földrajzi területen belül. Az egyes régiókban rendelkezésre álló virtuálisgép-méretekkel kapcsolatos továbbihttps://aka.ms/azure-regions

Ha rendelkezésre állási zónákat használ, próbálkozzon egy másik zónával a régión belül, amely rendelkezhet a kért virtuális gép méretéhez rendelkezésre álló kapacitással.

Ha a foglalási kérelem nagy (több mint 500 mag), tekintse meg a következő szakaszokban a kérelem kisebb üzembe helyezéseket a kérelem felosztása.

Próbálja meg [újratelepíteni a virtuális gép.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/redeploy-to-new-node-windows) A virtuális gép újratelepítése lefoglalja a virtuális gép egy új fürt a régión belül.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Foglalási hibák régebbi virtuálisgép-méretek esetében (Av1, Dv1, DSv1, D15v2, DS15v2 stb.)

Az Azure-infrastruktúra bővítése során újabb generációs hardvereket telepítünk, amelyek et úgy terveztünk, hogy támogassuk a legújabb virtuálisgép-típusokat. Néhány régebbi sorozatú virtuális gépek nem futnak a legújabb generációs infrastruktúra. Emiatt az ügyfelek esetenként előfordulhatnak foglalási hibák ezen örökölt sk-ok esetében. A probléma elkerülése érdekében arra bátorítjuk az örökölt sorozatú virtuális gépeket használó ügyfeleket, hogy a következő javaslatok szerint fontolják meg az egyenértékű újabb virtuális gépekre való áttérést: Ezek a virtuális gépek a legújabb hardverre vannak optimalizálva, és lehetővé teszik a jobb árképzés és a teljesítmény. 

|Örökölt virtuális gépsorozat/méret|Ajánlott újabb VM-sorozat/méret|További információ|
|----------------------|----------------------------|--------------------|
|Av1 sorozat|[Av2-sorozat](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 vagy DSv1 sorozat (D1-Től D5-ig)|[Dv3 vagy DSv3 sorozat](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 vagy DSv1 sorozat (D11-Től D14-ig)|[Ev3 vagy ESv3 sorozat](../ev3-esv3-series.md)|
|D15v2 vagy DS15v2|Ha azErőforrás-kezelő telepítési modelljét használja a nagyobb virtuális gépméretek kihasználása érdekében, fontolja meg a D16v3/DS16v3 vagy a D32v3/DS32v3 rendszerre való áttérést. Ezek úgy vannak kialakítva, hogy a legújabb generációs hardveren fussanak. Ha a Resource Manager központi telepítési modelljét használja annak érdekében, hogy a virtuálisgép-példány egyetlen ügyfélnek dedikált hardverhez legyen elkülönítve, fontolja meg az új elkülönített virtuálisgép-méretek, E64i_v3 vagy E64is_v3, amelyek a legújabb generációs hardveren való futtatásra vannak tervezve. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Nagy méretű (500 magnál többet használó) környezetek foglalási hibái

Csökkentse a kért virtuális gép méretének példányai számát, majd próbálkozzon újra a központi telepítési művelettel. Emellett nagyobb telepítések esetén érdemes kiértékelni az [Azure virtuálisgép-méretezési csoportjait.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) A virtuálisgép-példányok száma automatikusan növelheti vagy csökkentheti az igényre vagy egy meghatározott ütemezésre adott válaszként, és nagyobb esélye van a foglalás sikerére, mivel a központi telepítések több fürt között is eloszthatók. 

## <a name="background-information"></a>Háttér-információk
### <a name="how-allocation-works"></a>A felosztás működése
Az Azure-adatközpontokban lévő kiszolgálók fürtökre vannak particionálva. Általában a rendszer több fürtön is próbálkozik egy foglalási kérelemmel, de előfordulhat, hogy a foglalási kérelem bizonyos korlátozásai arra kényszerítik az Azure-platformot, hogy csak egy fürtön próbálkozzon. Ebben a cikkben ezt "fürthöz rögzítettként" fogjuk hivatkozni. Az alábbi 1. A 2. ábra a fürt 2-höz rögzített foglalás esetét mutatja be, mert a meglévő felhőszolgáltatás CS_1 vagy rendelkezésre állási készlete található.
![Felosztási diagram](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Miért történnek foglalási hibák?
Ha egy foglalási kérelem van rögzítve egy fürthöz, nagyobb az esélye, hogy nem talál szabad erőforrásokat, mivel a rendelkezésre álló erőforráskészlet kisebb. Továbbá, ha a foglalási kérelem egy fürthöz van rögzítve, de a kért erőforrás típusát a fürt nem támogatja, a kérés sikertelen lesz, még akkor is, ha a fürt szabad erőforrásokkal rendelkezik. A következő 3. 4. ábra bemutatja az esetben, ha egy rögzített foglalás sikertelen, mert az egyetlen jelölt fürt nem támogatja a kért virtuális gép méretét, annak ellenére, hogy a fürt szabad erőforrásokkal rendelkezik.

![Rögzített foglalási hiba](./media/virtual-machines-common-allocation-failure/Allocation2.png)


