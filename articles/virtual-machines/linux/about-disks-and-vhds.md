---
title: "Hamarosan nem felügyelt (lapblobokat) és a lemezek tárolási felügyelt Microsoft Azure Linux virtuális gépek |} Microsoft Docs"
description: "További tudnivalók a alapjait nem felügyelt (lapblobokat) és a felügyelt Linux virtuális gépek Azure-lemezeket tároló."
services: virtual-machines
author: iainfoulds
manager: jeconnoc
ms.service: virtual-machines
ms.workload: storage
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: iainfou
ms.openlocfilehash: 107e332a0f8c9d5a84a74de685ca458fb29caa8b
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>Lemezek tárolás Azure Linux virtuális gépekhez
Csakúgy, mint bármely más számítógépre az Azure virtuális gépek lemezek használatával egy olyan hely az operációs rendszerek, alkalmazások és adatok tárolására. Minden Azure virtuális gépek legalább két lemezt – a Linux operációs rendszer és egy ideiglenes lemezzel rendelkezik. Az operációs rendszer lemez létrehozása lemezkép, és mind az operációsrendszer-lemez, és a lemezkép ténylegesen tárolt virtuális merevlemezek (VHD) az Azure storage-fiók. Virtuális gépek is rendelkeznek legalább egy adatlemezt, virtuális merevlemezekként is tárolt. 

Ebben a cikkben rendszer szolgáltatással kapcsolatban a lemezek különböző használ, és a különböző típusú lemezek létrehozhat és használhat majd ismertetik. Ez a cikk érhető el is [Windows virtuális gépek](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Virtuális gépek által használt lemezek

Vessen egy pillantást a lemezt a virtuális gépek által használt hogyan.

## <a name="operating-system-disk"></a>Operációsrendszer-lemez
Minden virtuális gép egy csatolt operációsrendszer-lemez van. SATA meghajtóként regisztrálva van, és alapértelmezés szerint /dev/sda lett címkézve. Ezen a lemezen vannak 2048 gigabájt (GB) maximális kapacitását. 

## <a name="temporary-disk"></a>Ideiglenes lemez
Minden virtuális gép ideiglenes lemezt tartalmaz. Az ideiglenes lemezre rövid távú tárolás biztosít az alkalmazások és folyamatok, és csak az adatok, például az oldal vagy swap fájlok tárolására szolgál. Az ideiglenes lemezen lévő adatok elveszhetnek során egy [karbantartási esemény](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) és mikor meg [újratelepíteni a virtuális gépek](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A virtuális gép a szabványos újraindításkor az ideiglenes meghajtón található adatokat kell megőrizni.

A Linux virtuális gépeken, a lemez általában **/dev/sdb** formázott és a csatlakoztatott **/mnt** az Azure Linux ügynök. Változik a ideiglenes lemez méretét, a virtuális gép méretétől függ. További információkért lásd: [Linux virtuális gépek méretei](../windows/sizes.md).

Hogyan Azure használja az ideiglenes lemez a további információkért lásd: [az ideiglenes meghajtón a Microsoft Azure virtuális gépeken ismertetése](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Adatlemez
Adatlemezt tartalmazó virtuális merevlemez csatolva van egy virtuális gép tárolásához, alkalmazás vagy egyéb adatok szüksége. Az adatlemezek SCSI meghajtóként regisztrálva van, és az Ön által betűvel fel van tüntetve. Minden egyes adatlemez 4095 GB maximális kapacitása nem. A virtuális gép mérete határozza meg, hány adatlemezt csatol, és a tároló típusa szerinti használhatja a lemezek.

> [!NOTE]
> Virtuális gépek kapacitások kapcsolatos további tudnivalókért lásd: [Linux virtuális gépek méretei](./sizes.md).
> 

Azure operációsrendszer-lemez hoz létre, amikor egy virtuális gépet hoz létre a lemezkép. Ha adatlemezt tartalmaz egy képet, a Azure is létrehoz az adatlemezek, amikor létrehozza a virtuális gép. Ellenkező esetben az adatlemezek hozzáadása, a virtuális gép létrehozása után.

Adhat hozzá adatlemezt egy virtuális gép bármikor, az **csatolása** a lemezt a virtuális géphez. A tárfiók, vagy egy, az Azure létrehozza, másolni vagy feltöltött virtuális merevlemez is használhatja. A virtuális gép adatlemezt csatol társítja a VHD-fájlt a címbérlet helyez a VHD-t, így azt nem lehet törölni az tárolási amíg továbbra is csatlakoztatva van.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>További lépések
* [A lemez csatolása](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) további tárhely hozzáadása a virtuális gép számára.
* [Pillanatkép létrehozása](snapshot-copy-managed-disk.md).
* [Alakítsa át a felügyelt](convert-unmanaged-to-managed-disks.md).

