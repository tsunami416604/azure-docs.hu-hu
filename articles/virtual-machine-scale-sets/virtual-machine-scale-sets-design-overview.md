---
title: Az Azure virtuálisgép-méretezési készletek tervezési szempontjai
description: Ismerje meg az Azure virtuálisgép-méretezési készletek tervezési szempontjait. Hasonlítsa össze a méretezési csoportok funkcióit a virtuális gép funkcióival.
keywords: linux virtuális gép, virtuális gép méretezési készletek
author: mayanknayar
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: b427319fdba634ea3c61681baa30547450709dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250776"
---
# <a name="design-considerations-for-scale-sets"></a>Méretezési csoportok tervezési szempontjai
Ez a cikk a virtuálisgép-méretezési csoportok tervezési szempontjait ismerteti. A virtuálisgép-méretezési készletekről a [Virtuálisgép-méretezési csoportok áttekintése című témakörben](virtual-machine-scale-sets-overview.md)olvashat.

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Mikor kell használni a méretezési készletek helyett a virtuális gépek?
A méretezési csoportok általában akkor hasznosak, ha magas rendelkezésre állású infrastruktúrát telepít, ahol a gépek hasonló konfigurációval rendelkeznek. Egyes funkciók azonban csak méretezési csoportokban érhetők el, míg más funkciók csak a virtuális gépeken érhetők el. Annak érdekében, hogy megalapozott döntést hozhasson az egyes technológiák használatáról, először tekintse meg a méretezési csoportokban elérhető, de a virtuális gépekben elérhető, általánosan használt funkciókat:

### <a name="scale-set-specific-features"></a>Készletspecifikus funkciók méretezése

- Miután megadta a méretezési csoport *capacity* konfigurációját, frissítheti a kapacitástulajdonságot, hogy párhuzamosan több virtuális gépet telepítsen. Ez a folyamat jobb, mint egy parancsfájl írása számos egyedi virtuális gép párhuzamos üzembe helyezéséhez.
- Az [Azure automatikus skálázás segítségével automatikusan skálázhatja a méretezési készlet,](./virtual-machine-scale-sets-autoscale-overview.md) de nem az egyes virtuális gépek.
- A [méretezési készletes virtuális gépeket újrafellehet képzni,](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) de [az egyes virtuális gépeket nem.](https://docs.microsoft.com/rest/api/compute/virtualmachines)
- A nagyobb megbízhatóság és a gyorsabb üzembe helyezési idő érdekében [túlépítheti](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) a méretezési készletes virtuális gépeket. Az egyes virtuális gépek et csak akkor lehet túlépíteni, ha egyéni kódot ír a művelet végrehajtásához.
- Megadhat egy [frissítési szabályzatot,](./virtual-machine-scale-sets-upgrade-scale-set.md) amely megkönnyíti a frissítések bevezetését a méretezési készlet virtuális gépek között. Az egyes virtuális gépek, saját kezűleg kell vezénylnie a frissítéseket.

### <a name="vm-specific-features"></a>Virtuális gép-specifikus funkciók

Egyes funkciók jelenleg csak a virtuális gépeken érhetők el:

- Egy képet rögzíthet egy adott virtuális gépről, de nem egy méretezési készletben lévő virtuális gépről.
- Az egyes virtuális gépek áttelepítheti a natív lemezek ről felügyelt lemezekre, de nem telepítheti át a virtuálisgép-példányokat egy méretezési csoportban.
- IPv6 nyilvános IP-címeket rendelhet az egyes virtuális gép virtuális hálózati csatolókártyákhoz, de a méretezési csoport virtuálisgép-példányai esetében ez nem. IPv6 nyilvános IP-címeket rendelhet a terheléselosztókhoz az egyes virtuális gépek vagy a méretezési csoport virtuális gépek előtt.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Készletek méretezése az Azure felügyelt lemezeivel
A scale-készletek a hagyományos Azure storage-fiókok helyett [az Azure felügyelt lemezekkel hozhatók](../virtual-machines/windows/managed-disks-overview.md) létre. A felügyelt lemezek a következő előnyökkel járnak:
- Nem kell előre létrehoznia egy Azure storage-fiókok készletét a méretezési csoport virtuális gépekhez.
- A méretezési csoportban lévő virtuális [gépekhez csatolt adatlemezeket](virtual-machine-scale-sets-attached-disks.md) definiálhat.
- A méretezési csoportok beállíthatók [úgy, hogy egy készletben legfeljebb 1000 virtuális gépet támogassanak.](virtual-machine-scale-sets-placement-groups.md) 

Ha van egy meglévő sablonja, [frissítheti a sablont a Felügyelt lemezek használatához.](virtual-machine-scale-sets-convert-template-to-md.md)

### <a name="user-managed-storage"></a>Felhasználó által felügyelt tárhely
Az Azure felügyelt lemezekkel nem definiált méretezési csoport a felhasználó által létrehozott tárfiókokra támaszkodik a virtuális gépek operációs rendszerlemezeinek készletben való tárolásához. A maximális Io eléréséhez és a _túlterhelés_ (lásd alább) érdekében storage-fiókonként 20 virtuális gép vagy kevesebb arányajánlott. Azt is javasoljuk, hogy a tárfiók nevek kezdő karaktereit az ábécé között elosztja. Ezzel segít a terhelés különböző belső rendszerek között. 


## <a name="overprovisioning"></a>Túlépítés
Jelenleg az alapértelmezett készleteket "túlterhelés" virtuális gépekre méretezi. Ha a túlterhelés be van kapcsolva, a méretezési csoport ténylegesen több virtuális gépet hoz létre, mint amennyit kért, majd törli az extra virtuális gépeket, miután a kért számú virtuális gép sikeresen kivan építve. A túlépítés javítja a kiépítési sikerességi arányt, és csökkenti a telepítési időt. A további virtuális gépek ért nem kell fizetnie, és nem számítanak bele a kvótakorlátokba.

Bár a túlépítés javítja a kiépítés sikerességi arányát, zavaró viselkedést okozhat egy olyan alkalmazás számára, amely nem úgy van kialakítva, hogy kezelje a megjelenő, majd eltűnő további virtuális gépeket. A túlterhelés kikapcsolásához győződjön meg arról, `"overprovision": "false"`hogy a sablonban a következő karakterlánc található: . További részletek a [Rest API méretezése API dokumentációjában](/rest/api/virtualmachinescalesets/create-or-update-a-set)találhatók.

Ha a méretezési csoport felhasználó által felügyelt tárolót használ, és kikapcsolja a túlterhelést, tárfiókonként több mint 20 virtuális géppel rendelkezhet, de nem ajánlott 40 fölé menni az IO teljesítményének okamiatt. 

## <a name="limits"></a>Korlátok
A Marketplace-lemezképre (más néven platformlemezre) épülő és Az Azure Managed Disks használatára konfigurált méretezési készlet legfeljebb 1000 virtuális gép kapacitását támogatja. Ha úgy konfigurálja a méretezési csoportot, hogy 100-nál több virtuális gépet támogasson, nem minden forgatókönyv működik ugyanazzal (például terheléselosztás). További információ: [Nagy méretű virtuálisgép-méretezési csoportok kidolgozása.](virtual-machine-scale-sets-placement-groups.md) 

A felhasználó által felügyelt tárfiókokkonfigurált méretezési csoport jelenleg 100 virtuális gépre van korlátozva (és 5 tárfiók ajánlott ehhez a skálázáshoz).

Az egyéni lemezképre épülő méretezési készlet (az Ön által készített) kapacitása akár 600 virtuális gép is lehet, ha Azure Felügyelt lemezekkel konfigurálva van. Ha a méretezési csoport felhasználó által kezelt tárfiókok, létre kell hoznia az összes operációsrendszer-lemez Virtuálismerevlemezek egy tárfiókon belül. Ennek eredményeképpen az egyéni lemezképre és a felhasználó által felügyelt tárolóra épülő méretezési készletben a virtuális gépek ajánlott maximális ajánlott száma 20. Ha kikapcsolja a túlterhelést, akár 40-ig is megléphet.

Ha több virtuális gép jelenik meg, mint amennyit ezek a korlátok megengednek, több méretezési csoportban kell üzembe helyeznie az ebben a [sablonban](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)látható módon.

