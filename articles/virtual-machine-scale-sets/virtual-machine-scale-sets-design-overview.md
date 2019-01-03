---
title: Kialakítási szempontok az Azure Virtual Machine Scale Sets |} A Microsoft Docs
description: További tudnivalók az Azure Virtual Machine Scale Sets kapcsolatos kialakítási szempontok
keywords: Linux rendszerű virtuális gép, virtuálisgép-méretezési csoportok
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.openlocfilehash: 67bbad7e73f33d73d4c3f1d4f7e5599d2ef914e3
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791047"
---
# <a name="design-considerations-for-scale-sets"></a>Kialakítási szempontok a méretezési csoportokhoz
Ez a cikk ismerteti a Virtual Machine Scale Sets kapcsolatos kialakítási szempontok. Mik azok a Virtual Machine Scale Sets kapcsolatos információkért tekintse meg [virtuálisgép-méretezési csoportok – áttekintés](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Mikor érdemes használni a méretezési csoportok virtuális gépek helyett?
Általában a méretezési csoportok hasznosak a magas rendelkezésre állású infrastruktúra telepítése ahol gépekről hasonló konfigurációval rendelkezik. Azonban bizonyos funkciók csak érhető el a méretezési közben egyéb funkciók a virtuális gépek csak érhetők el. Annak érdekében, hogy tájékozott döntést arról, mikor melyik technológiát használja, először tekintse meg a méretezési csoportok azonban nem a virtuális gépek gyakran használt funkcióit kell tennie:

### <a name="scale-set-specific-features"></a>Scale set-specifikus szolgáltatásai

- Miután megadta a méretezési csoport konfigurációs, frissítheti a *kapacitás* tulajdonság további virtuális gépek párhuzamos üzembe helyezése. Ez a folyamat jobb, mint írása egy parancsfájl történő párhuzamos számos, az egyes virtuális gépek üzembe helyezéséhez.
- Is [a méretezési csoport automatikus méretezése az Azure automatikus méretezési funkciójával használatával](./virtual-machine-scale-sets-autoscale-overview.md) , de nem az egyéni virtuális gépeket.
- Is [reimage méretezési csoport virtuális gépeinek](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) , de [nem az egyes virtuális gépek](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Is [overprovision](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) méretezési csoport virtuális gépeinek a nagyobb megbízhatóság és gyorsabb üzembe helyezéshez szükséges idő. Az egyes virtuális gépek nem overprovision, kivéve, ha ez a művelet végrehajtásához egyéni kód írása.
- Megadhat egy [frissítési szabályzatának](./virtual-machine-scale-sets-upgrade-scale-set.md) megkönnyíti a frissítés bevezetése virtuális gépek között a méretezési csoportban lévő. Az egyes virtuális gépekhez meg kell levezényelni saját magának.

### <a name="vm-specific-features"></a>Virtuálisgép-specifikus szolgáltatásai

Néhány funkció jelenleg csak virtuális gépeken érhető el:

- Egy adott virtuális Gépre, de nem a méretezési csoportban lévő virtuális gép olyan lemezképét rögzítheti.
- Telepíthet át egy adott virtuális Gépre natív lemezeket a managed Disks szolgáltatásba, de egy méretezési csoportban lévő Virtuálisgép-példányok nem telepíthetők át.
- IPv6-alapú nyilvános IP-címeket rendelhet az egyes virtuális gép virtuális hálózati adapterek (NIC), de nem tudja megtenni, a méretezési csoportban lévő Virtuálisgép-példányokhoz. IPv6-alapú nyilvános IP-címek vagy az egyes virtuális gépek elé terheléselosztókhoz rendelhet, vagy a méretezési csoport virtuális gépeinek.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Az Azure által felügyelt lemezekkel rendelkező méretezési csoportok
A méretezési csoportok hozhatók [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) hagyományos Azure storage-fiókokkal szemben. A felügyelt lemezek előnyei a következők:
- Nem kell előre létrehozhat egy csoportot az Azure storage-fiókok esetében a méretezési csoport virtuális gépeinek.
- Definiálhat [csatlakoztatott adatlemezekkel](virtual-machine-scale-sets-attached-disks.md) állítsa be a méretezési csoportban lévő virtuális gépek.
- A méretezési csoportok beállítható úgy, hogy [támogatja a legfeljebb 1000 virtuális gépet egy](virtual-machine-scale-sets-placement-groups.md). 

Ha rendelkezik egy meglévő sablont, akkor is [a sablont frissítését a felügyelt lemezek használata](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Felhasználó által felügyelt tárolási
Egy méretezési csoportot, amely nincs definiálva az Azure Managed Disks támaszkodik a felhasználó által létrehozott storage-fiókokat, a virtuális gépek operációsrendszer-lemezeinek tárolásához a készletben. Szélesség-magasság arányban tárfiókonként vagy kevesebb mint 20 virtuális gép ajánlott maximális i/o eléréséhez, és kihasználhatja _túlzott_ (lásd alább). A tárfiókok nevének kezdő karaktereit elosztva az ábécé is ajánlott. Ennek során, így segít a terhelés elosztását belső különböző rendszerekből. 


## <a name="overprovisioning"></a>Túlzott
A méretezési csoportok jelenleg az alapértelmezett "túlzott mértékű kiépítést" virtuális gépek. Túlzott van kapcsolva, a méretezési csoport beállítása ténylegesen terveket a jövőre mentése adnia a több virtuális gépet, majd törli a további virtuális gépek után sikeresen megtörténik a virtuális gépek kért száma. Túlzott növeli a kiépítési sikerességi arányokat, és csökkenti a telepítési idejét. Az extra virtuális Gépekért nem díjköteles, és azok nem számít bele a kvótakorlát felé.

Túlzott javítása kiépítési sikerességi arányokat, amíg azt a az alkalmazás, amely nem jelennek meg, és ezután eltűnőként további virtuális gépek kezelésére zavaró viselkedést okozhat. Kapcsolja ki túlzott, győződjön meg arról hogy a következő karakterláncot a sablonban: `"overprovision": "false"`. További részletek találhatók a [méretezési állítsa be a REST API dokumentációja](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Ha a méretezési felhasználó által kezelt tárolót használ, és kikapcsolja túlzott, tárfiókonként legfeljebb csak 20 virtuális géppel rendelkezhet, de nem javasoljuk, hogy nyissa meg a fenti 40 i/o-teljesítmény javítása érdekében. 

## <a name="limits"></a>Korlátok
Egy méretezési csoportot (más néven platformlemezkép) Piactéri lemezképet épül, és az Azure Managed Disks használatára konfigurált támogatja a legfeljebb 1000 virtuális gépet kapacitását. Ha a méretezési csoport több mint 100 virtuális gép támogatására konfigurálja, nem minden esetben azonos (a példában terheléselosztás) működik. További információkért lásd: [nagyméretű virtuálisgép-méretezési csoportok](virtual-machine-scale-sets-placement-groups.md). 

Egy méretezési csoportot konfigurált, a felhasználó által felügyelt storage-fiókok jelenleg legfeljebb 100 virtuális gép (és 5 tárfiókok használata javasolt a skála).

Egy méretezési csoportot egy egyéni rendszerképet (egy Ön által készített) épülő rendelkezhet legfeljebb 600 virtuális gépek az Azure Managed disks használatával konfigurálásakor kapacitását. Ha a méretezési csoportban van konfigurálva, a felhasználó által felügyelt tárfiókokban, annak létre kell hoznia az összes operációs rendszer lemez VHD-k egy tárfiókon belül. Ennek eredményeképpen a maximális ajánlott épülő egyéni rendszerkép méretezési csoportban lévő virtuális gépek számát és felhasználó által felügyelt tárolási 20. Ha kikapcsolja a túlzott, 40 léphet.

További virtuális gépek esetében, mint hogy ezeket a korlátokat, üzembe kell helyeznie a több méretezési, ahogyan [ezzel a sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

