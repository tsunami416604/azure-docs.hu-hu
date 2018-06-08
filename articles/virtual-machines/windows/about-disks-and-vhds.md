---
title: Hamarosan nem felügyelt (lapblobokat) és a lemezek tárolási felügyelt Microsoft Azure Windows virtuális gépek |} Microsoft Docs
description: További tudnivalók a alapjait nem felügyelt (lapblobokat) és a felügyelt Windows virtuális gépek Azure-lemezeket tároló.
services: virtual-machines
author: roygara
manager: jeconnoc
ms.service: virtual-machines
ms.workload: storage
ms.tgt_pltfrm: windows
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.openlocfilehash: e95b5a6037c791f501a04ebe468b1c781805f693
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "32313511"
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>Lemezek tárolás Azure Windows virtuális gépek
Csakúgy, mint bármely más számítógépre az Azure virtuális gépek lemezek használatával egy olyan hely az operációs rendszerek, alkalmazások és adatok tárolására. Minden Azure virtuális gépek legalább két lemezt – a Windows operációs rendszer és egy ideiglenes lemezzel rendelkezik. Az operációs rendszer lemez létrehozása lemezkép, és mind az operációsrendszer-lemez, és a lemezkép virtuális merevlemezeket (VHD) Azure-tárfiók tárolja. Virtuális gépek is rendelkeznek legalább egy adatlemezt, virtuális merevlemezekként is tárolt. 

Ebben a cikkben rendszer szolgáltatással kapcsolatban a lemezek különböző használ, és a különböző típusú lemezek létrehozhat és használhat majd ismertetik. Ez a cikk érhető el is [Linux virtuális gépek](../linux/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Virtuális gépek által használt lemezek

Vessen egy pillantást a lemezt a virtuális gépek által használt hogyan.

### <a name="operating-system-disk"></a>Operációsrendszer-lemez
Minden virtuális gép egy csatolt operációsrendszer-lemez van. SATA meghajtóként regisztrálva van, a C: meghajtóra, alapértelmezés szerint a sorban. Ezen a lemezen vannak 2048 gigabájt (GB) maximális kapacitását. 

### <a name="temporary-disk"></a>Ideiglenes lemez
Minden virtuális gép ideiglenes lemezt tartalmaz. Az ideiglenes lemezre rövid távú tárolás biztosít az alkalmazások és folyamatok, és csak az adatok, például az oldal vagy swap fájlok tárolására szolgál. Az ideiglenes lemezen lévő adatok elveszhetnek során egy [karbantartási esemény](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) és mikor meg [újratelepíteni a virtuális gépek](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A virtuális gép a szabványos újraindításkor az ideiglenes meghajtón található adatokat kell megőrizni.

Az ideiglenes lemez a d meghajtó címkézve alapértelmezett és pagefile.sys tárolására szolgál. Adja meg újból a lemezt egy másik meghajtóbetűjelet, lásd: [a meghajtóbetűjel, a Windows ideiglenes lemez](change-drive-letter.md). Változik a ideiglenes lemez méretét, a virtuális gép méretétől függ. További információkért lásd: [méretek a Windows virtuális gépek](sizes.md).

Hogyan Azure használja az ideiglenes lemez a további információkért lásd: [az ideiglenes meghajtón a Microsoft Azure virtuális gépeken ismertetése](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>Adatlemez
Adatlemezt tartalmazó virtuális merevlemez csatolva van egy virtuális gép tárolásához, alkalmazás vagy egyéb adatok szüksége. Az adatlemezek SCSI meghajtóként regisztrálva van, és az Ön által betűvel fel van tüntetve. Minden egyes adatlemez 4095 GB maximális kapacitása nem. A virtuális gép mérete határozza meg, hány adatlemezt csatol, és a tároló típusa szerinti használhatja a lemezek.

> [!NOTE]
> További információ a virtuális gépek kapacitások: [méretek a Windows virtuális gépek](sizes.md).
> 

Azure operációsrendszer-lemez hoz létre, amikor egy virtuális gépet hoz létre a lemezkép. Ha adatlemezt tartalmaz egy képet, a Azure is létrehoz az adatlemezek, amikor létrehozza a virtuális gép. Ellenkező esetben az adatlemezek hozzáadása, a virtuális gép létrehozása után.

Adhat hozzá adatlemezt egy virtuális gép bármikor, az **csatolása** a lemezt a virtuális géphez. Használja a már feltöltött, illetve a tárfiókhoz másolt virtuális Merevlemezt, vagy egy üres virtuális merevlemez, amely az Azure létrehozza, használja. A virtuális gép adatlemezt csatol társítja a VHD-fájlt a címbérlet helyez a VHD-t, így azt nem lehet törölni az tárolási amíg továbbra is csatlakoztatva van.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Egy utolsó javaslat: használja a nem kezelt szabványos lemezek vágás 

Nem felügyelt standard lemezek (HDD) használatakor vágást kell engedélyeznie. VÁGÁS elveti a nem használt blokkok a lemezen, így csak a ténylegesen használt tárolási kell fizetni. Ez is mentheti költségek Ha nagy fájlok létrehozása, majd törli őket. 

Ez a parancs a vágás beállítás ellenőrzéséhez futtathatja. Nyissa meg a Windows virtuális gép, és írja be a parancssorba:


```
fsutil behavior query DisableDeleteNotify
```

A parancs a 0 értéket adja vissza, vágást engedélyezve van-e megfelelően. Ha a visszaadott érték 1, vágást engedélyezéséhez a következő parancsot:

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> Megjegyzés: A vágás támogatása kezdődik-e a Windows Server 2012 vagy Windows 8 és újabb verzióiban lásd: [új API lehetővé teszi, hogy a mutatók "Vágás és megfeleltetésének törlése" küldendő tárolási adathordozókon alkalmazások](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints).
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>További lépések
* [A lemez csatolása](attach-disk-portal.md) további tárhely hozzáadása a virtuális gép számára.
* [Pillanatkép létrehozása](snapshot-copy-managed-disk.md).
* [Alakítsa át a felügyelt](convert-unmanaged-to-managed-disks.md).


