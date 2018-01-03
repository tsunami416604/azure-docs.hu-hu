---
title: "Kialakítási szempontok a Azure virtuálisgép-méretezési csoportok |} Microsoft Docs"
description: "További tudnivalók az Azure virtuálisgép-méretezési csoportok kialakítási szempontjai"
keywords: "Linux virtuális gép, virtuálisgép-méretezési beállítása"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: efb9f7f7daa5dbb8cd3120b21ef812106fdc7fb9
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="design-considerations-for-scale-sets"></a>Méretezési csoportok kialakítási szempontjai
A cikk ismerteti a virtuálisgép-méretezési csoportok kialakítási szempontjai. Mik azok a virtuálisgép-méretezési csoportok kapcsolatos információkért tekintse meg [virtuális gépek méretezési készletek áttekintése](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Mikor érdemes használni a skála beállítja a virtuális gépek helyett?
Általában méretezési csoportok hasznosak magas rendelkezésre állású infrastruktúra üzembe helyezéséhez ahol gépek halmaza hasonló konfigurációval rendelkezik. Azonban bizonyos funkciók találhatók csak méretezési csoportok míg egyéb szolgáltatásokat csak a virtuális gépek. Ahhoz, hogy jól informált döntést arról, hogy mikor melyik technológiát használja, meg kell először tekintse meg néhány gyakran használt funkciója által biztosított méretezési csoportok, de nem a virtuális gépek:

### <a name="scale-set-specific-features"></a>Méretezési készlet-specifikus szolgáltatásai

- A méretezési konfigurációs ad meg, miután frissítheti párhuzamosan további virtuális gépek telepítése a "kapacitás" tulajdonság. Ez egy sokkal egyszerűbb, mint a kell levezényelni a párhuzamos sok egyes virtuális gépek telepítését egy parancsfájl írásához.
- Is [Azure automatikus skálázás segítségével automatikusan átméretezi a méretezési](./virtual-machine-scale-sets-autoscale-overview.md) , de nem az egyes virtuális gépek.
- Is [lemezkép-visszaállítási méretezési virtuális gépek](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-a-vm) , de [nem az egyes virtuális gépek](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Is [szükségesnél több erőforrás](./virtual-machine-scale-sets-design-overview.md) méretezési virtuális gépek nagyobb megbízhatóságot és gyorsabb telepítési időpontokat. Nem ehhez az egyes virtuális gépeken kivéve ennek egyéni kód írását, akkor.
- Megadhat egy [házirend frissítése](./virtual-machine-scale-sets-upgrade-scale-set.md) megkezdik frissítések virtuális gépek között a méretezési csoportban lévő megkönnyítése. Az egyes virtuális gépeken meg kell levezényelni a frissítéseket magát.

### <a name="vm-specific-features"></a>VM-specifikus szolgáltatásai

Néhány funkció jelenleg csak a virtuális gépek:

- Az adatlemezek csatolhat adott egyes virtuális gépeken, de csatolt adatlemezek méretezési csoportban lévő összes virtuális gépek vannak konfigurálva.
- Nem üres adatlemez csatolása egyes virtuális gépeken, de nem méretezési csoportban lévő virtuális gépek.
- Egy adott virtuális Gépre, de nem egy Virtuálisgép-méretezési csoportban lévő lehet pillanatkép.
- Az egy adott virtuális Gépre, de nem egy Virtuálisgép-méretezési csoportban lévő lemezkép rögzítheti.
- Telepíthet át egy adott virtuális Gépre natív lemezek felügyelt lemezekre, de nem ehhez a virtuális gépek méretezési csoportban lévő.
- IPv6 nyilvános IP-címek rendelhet az egyes virtuális gép hálózati adapterek, de nem ehhez a virtuális gépek méretezési csoportban lévő. IPv6 nyilvános IP-címek terheléselosztókhoz elé vagy egyedi virtuális gépeket rendelhet, vagy a virtuális gépek méretezési csoportjának virtuális gépek.

## <a name="storage"></a>Tárolás

### <a name="scale-sets-with-azure-managed-disks"></a>Az Azure Managed lemezek méretezési csoportok
Méretezési csoportok hozhatók létre [Azure felügyelt lemezek](../virtual-machines/windows/managed-disks-overview.md) hagyományos Azure storage-fiókok helyett. Felügyelt lemezek előnyei a következők:
- Nincs előzetes létrehozása az Azure storage-fiókokat, a méretezési virtuális gépek számára.
- Megadhat [adatlemezt csatolni](virtual-machine-scale-sets-attached-disks.md) a virtuális gépeket, a méretezési beállítása.
- Méretezési csoportok beállítható úgy, hogy [legfeljebb 1000 virtuális gépek támogatásához egy](virtual-machine-scale-sets-placement-groups.md). 

Ha egy meglévő sablont, is [frissíteni a felügyelt lemezek használt sablon](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Felhasználó által felügyelt tárolási
Egy Azure felügyelt lemezzel nem definiált méretezési támaszkodik a felhasználó által létrehozott tárfiók tárolja a virtuális gépek operációs rendszer lemezén készletében. A tárfiók, vagy kevesebb mint 20 virtuális gép aránya ajánlott maximális IO eléréséhez, és kihasználhatják _elhelyezésétől_ (lásd alább). A tárfiókok neve elején karakterét elosztva az ábécé is ajánlott. Ennek segítségével terhelés elosztva különböző belső rendszerek során. 


## <a name="overprovisioning"></a>Elhelyezésétől
Skálázási készletekben jelenleg az alapértelmezett "elhelyezésétől" virtuális gépeket. Elhelyezésétől-e kapcsolva, a méretezési ténylegesen fordulat további virtuális gépeinek mint kéri be, majd törli az extra virtuális Gépekért, miután sikeresen telepített virtuális gépek a kért számú. Elhelyezésétől növeli a kiépítési sikerességéről, és csökkenti a központi telepítés alkalmával. Nem kell fizetni az extra virtuális Gépekért, és ezek nem számítanak a kvótakorlát felé.

Amíg elhelyezésétől javítása létesítési sikerességéről, okozhat zavaró viselkedést az alkalmazás, amely nem jelenik meg, és ezután eltűnőként extra virtuális Gépekért kezelésére terveztek. Kapcsolja ki a elhelyezésétől, ellenőrizze, hogy a következő karakterláncot a sablonban: `"overprovision": "false"`. További részletek találhatók a [méretezési beállítása REST API-dokumentáció](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Ha a méretezési felhasználó által felügyelt tárolót használ, és elhelyezésétől kikapcsolja, több mint 20 gépek akkor is, de nem javasoljuk, hogy fent IO a teljesítményre vonatkozó megfontolásból 40 lépjen. 

## <a name="limits"></a>Korlátok
A méretezési a Piactéri rendszerkép (más néven platformlemezkép) alapul, és Azure felügyelt lemezek használatára konfigurált legfeljebb 1000 virtuális gépek kapacitású támogatja. Ha az támogatja a több mint 100 virtuális gépek méretezési konfigurál, nem minden esetben azonos (a példa terheléselosztás) működik. További információkért lásd: [nagy virtuálisgép-méretezési csoportok használata](virtual-machine-scale-sets-placement-groups.md). 

A méretezési készletben beállított felhasználó által felügyelt tárfiókokkal célja jelenleg csak 100 virtuális gépek (és a skála ajánlott 5 storage-fiókok).

Egy egyéni lemezkép (egy Ön által létrehozott) épülő méretezési 300 virtuális gépeket, amikor Azure felügyelt lemezzel konfigurált kapacitású lehet. A méretezési felhasználó által felügyelt tárfiókok van beállítva, ha az összes operációs rendszer lemez VHD-k egy tárfiókon belül kell létrehoznia. Ennek eredményeképpen a maximális ajánlott egyéni lemezkép épülő méretezési csoportban lévő virtuális gépek száma és felhasználó által felügyelt tárolási 20. Ha kikapcsolja elhelyezésétől, legfeljebb 40 lépjen.

További virtuális gépek esetén, mint a működés felső korlátjának engedélyezéséhez kell telepítenie a több méretezési csoportok, ahogy az [sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

