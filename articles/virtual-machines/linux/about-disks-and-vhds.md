---
title: Körülbelül nem felügyelt (lapblobok) és a managed disks storage a Microsoft Azure Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: További információ a alapjait nem felügyelt (lapblobok) és a managed disks tár Linux rendszerű virtuális gépek az Azure-ban.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.component: disks
ms.openlocfilehash: 53b44d47d9421f1b37aef7ca4d30c2c753b79740
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218321"
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>Az Azure Linux virtuális gépek disks storage-ról
Minden olyan számítógépre, mint az Azure-beli virtuális gépek lemezeket használnak egy olyan hely operációs rendszert, alkalmazásokat és adatokat szeretne tárolni. Az összes Azure-beli virtuális gépek legalább két lemezt – egy Linux operációsrendszer-lemez és a egy ideiglenes lemezzel rendelkezik. Az operációsrendszer-lemez jön létre egy rendszerképből, és az operációsrendszer-lemez és a kép is tárolt virtuális merevlemezek (VHD) egy Azure storage-fiókban. Virtuális gépek is rendelkezhet egy vagy több adatlemezt, is tárolt VHD-ként.

Ebben a cikkben fogja különböző felhasználási módja a lemezeket, és, amelyet majd ismertetjük a különböző típusú is létrehozhat és használhat lemezeket. Ez a cikk érhető el is [Windows virtuális gépek](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Virtuális gépek által használt lemezek

Vessünk egy pillantást a lemezeket a virtuális gépek általi felhasználási módjáról.

## <a name="operating-system-disk"></a>Operációsrendszer-lemez

Minden virtuális gép rendelkezik egy csatlakoztatott operációsrendszer-lemez. Regisztrálva van egy SATA-meghajtót, és a címkével ellátott /dev/sda alapértelmezés szerint. Ez a lemez maximális kapacitása 2048 gigabájt (GB).

## <a name="temporary-disk"></a>Ideiglenes lemez

Minden virtuális gép egy ideiglenes lemezt tartalmaz. Az ideiglenes lemez rövid távú tárolást biztosít az alkalmazások és folyamatok, és csak az adatok, például lapozófájlokat tárolására szolgál. Az ideiglenes lemezen lévő adatok elveszhetnek során egy [karbantartási esemény](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) , vagy ha Ön [egy virtuális gép ismételt üzembe](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Során a virtuális gép normál újraindítása az adatok az ideiglenes meghajtón kell megőrizni. Azonban előfordulhatnak olyan esetek, ahol az adatok nem aktívak maradnak, például egy új gazdagépre helyezi át. Ennek megfelelően az ideiglenes meghajtón lévő adatokat nem lehet, hogy a rendszer kritikus fontosságú adatokat.

A Linux rendszerű virtuális gépek, a lemez általában **/dev/sdb** és formátumú, és csatlakoztatva **/mnt** által az Azure Linux-ügynök. Az ideiglenes lemez méretének a függvénye a virtuális gép méretét. További információkért lásd: [Linux rendszerű virtuális gépek méretei](../windows/sizes.md).

## <a name="data-disk"></a>Adatlemez

Adatlemez egy alkalmazás vagy egyéb adatok továbbra is szeretné tárolni a virtuális géphez csatolt virtuális merevlemez. Adatlemezek SCSI-meghajtók, regisztrálva van, és betűvel, Ön által választott vannak ellátva. Az egyes adatlemezek kapacitása maximum 4095 GB-ot. A virtuális gép mérete határozza meg, hány adatlemez csatolható, és a tárolás típusát használhatja a lemezek.

> [!NOTE]
> Virtuális gépek kapacitások kapcsolatos további információkért lásd: [Linux rendszerű virtuális gépek méretei](./sizes.md).

Az Azure létrehozza az operációsrendszer-lemez egy rendszerképből egy virtuális gép létrehozásakor. Adatlemezek tartalmazó lemezképek használata, ha az Azure az, amikor létrehozza a virtuális gép is létrehoz az adatlemezeket. Ellenkező esetben hozzáadhat adatlemezeket a virtuális gép létrehozása után.

Adhat hozzá adatlemezeket a virtuális gépek bármikor, az **csatolása** a lemezt a virtuális géphez. Egy, amelyet feltöltött, vagy a storage-fiók, vagy olyat, amely az Azure az Ön hozza létre a másolt virtuális Merevlemezt is használhat. A virtuális gép, adatlemez csatolása társítja a VHD-fájlt a címbérlet helyez a VHD-t, így azt nem törölhető a tárolás közben továbbra is csatlakoztatva van.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>További lépések
* [Lemez csatolása](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , adjon hozzá további tárterületet a virtuális géphez.
* [Pillanatkép létrehozása](snapshot-copy-managed-disk.md).
* [Konvertálás felügyelt lemezekké](convert-unmanaged-to-managed-disks.md).