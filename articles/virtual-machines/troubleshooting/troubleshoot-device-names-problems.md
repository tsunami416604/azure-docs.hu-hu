---
title: Hibaelhárítás a Linux rendszerű virtuális gép eszköznév módosítása az Azure-ban |} A Microsoft Docs
description: Ismerteti, miért a Linux rendszerű virtuális gép eszköz módosítása neveket és hogyan lehet megoldani a problémát.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d636d5f31e78828a518882091af29b25f7219304
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443994"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Hibaelhárítás a Linux rendszerű virtuális gép eszköznév módosítása

Ez a cikk azt ismerteti, miért eszköz nevének módosítása után csatlakoztassa újból az adatlemezek vagy Linux rendszerű virtuális gép újraindítása. A cikk megoldásokat is kínál a probléma.

## <a name="symptoms"></a>Probléma
A következő problémákat tapasztalhat, ha Linuxos virtuális gépek futtatása a Microsoft Azure-ban:

- A virtuális gép az újraindítás után rendszerindító sikertelen lesz.
- Adatlemezek leválasztása és csatolni, ha a lemez eszköznevek módosultak.
- Olyan alkalmazást vagy parancsfájlt, amely egy lemez hivatkozik az eszköznév használatával meghiúsul, mert az eszköz neve megváltozott.

## <a name="cause"></a>Ok

Linux rendszerű eszköz útvonalakat nem garantáltan konzisztens újraindítások között. Eszköz nevének nagyobb számokat (betű) és a kisebb számokat állnak. Amikor a Linux-tároló eszközillesztő észleli az új eszköz, az illesztőprogram rendeli hozzá a fő- és alverzió számok melletti elérhető tartományon az eszközön. Egy eszköz eltávolításakor az eszköz számok felszabadítását ismételt felhasználásra.

A probléma oka, hogy vizsgálata a Linux rendszerű eszköz van ütemezve az SCSI-alrendszer aszinkron módon történik. Ennek eredményeképpen egy eszköz elérési utat újraindítások között változhat.

## <a name="solution"></a>Megoldás

A probléma megoldásához, állandó elnevezési használja. Állandó elnevezési használandó négy módja van: fájlrendszer címke, UUID azonosító, azonosítója vagy elérési út alapján. Az Azure Linux virtuális gépek a fájlrendszer címkét vagy UUID használatát javasoljuk.

A legtöbb disztribúciók adja meg a `fstab` **nofail** vagy **nobootwait** paramétereket. Ezek a paraméterek engedélyezze a rendszert a Ha a lemez indításkor csatlakoztatása nem sikerült. A telepítési dokumentációjából további információt ezekről a paraméterekről. Konfigurálása Linux rendszerű virtuális gép UUID használandó, adatlemez hozzáadásakor a további információkért lásd: [Connect segítségével csatlakoztassa az új lemezt a Linux rendszerű virtuális géphez](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

Az Azure Linux-ügynök telepítve van egy virtuális Gépet, ha az ügynök Udev szabályok segítségével hozhatnak létre szimbolikus hivatkozásokat tartalmaz a /dev/disk/azure elérési úton. Alkalmazások és parancsfájlok Udev szabályok segítségével azonosíthatja a virtuális géphez, valamint a lemez típusát csatlakozó lemezek és a lemez LUN-ok.

Ha már befejezte az fstab oly módon, hogy a virtuális gép nem indítja, és a virtuális géphez SSH nem Ön, használhatja a [virtuális gép soros konzol](./serial-console-linux.md) meg [egyfelhasználós módban](./serial-console-grub-single-user-mode.md) és módosíthatja az fstab.

### <a name="identify-disk-luns"></a>Lemez LUN-ok azonosítása

Alkalmazások keresése a csatlakoztatott lemezeket és hozhatnak létre szimbolikus hivatkozások a logikai egységeket. Az Azure Linux-ügynök beállítása a szimbolikus hivatkozásokat a LUN-t, az eszközök Udev szabályokat tartalmazza:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3

A Linux rendszerű Vendég fiókból Logikaiegység-adatok segítségével lekéri `lsscsi` vagy egy hasonló eszközt:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Vendég Logikaiegység-adatok segítségével az Azure-előfizetés metaadatait a partíció adatait tartalmazó Azure Storage-ban keresse meg a VHD-t. Használhatja például a `az` CLI:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
    [
    {
    "caching": "None",
      "createOption": "empty",
    "diskSizeGb": 1023,
      "image": null,
    "lun": 0,
    "managedDisk": null,
    "name": "testVM-20170619-114353",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
    }
    },
    {
    "caching": "None",
    "createOption": "empty",
    "diskSizeGb": 512,
    "image": null,
    "lun": 1,
    "managedDisk": null,
    "name": "testVM-20170619-121516",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
      }
      }
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Fedezze fel a fájlrendszer az UUID azonosítók blkid használatával

Alkalmazások és parancsfájlok, olvassa el a kimenetét `blkid`, vagy hasonló információforrásokat, /dev elérési szimbolikus hivatkozások létrehozásához. A kimenet az UUID azonosítók, a virtuális gép és a társított eszköz fájl csatlakoztatott minden lemez látható:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Az Azure-beli Linuxos ügynök Udev szabályokat hozhat létre a szimbolikus hivatkozásokat tartalmaz a /dev/disk/azure elérési úton:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Alkalmazások hivatkozások segítségével azonosíthatja a rendszerindító lemez eszköz és az erőforrás (ideiglenes) lemez. Az Azure-ban az alkalmazások felderíteni ezeket a partíciókat /dev/disk/azure/root-part1 vagy /dev/disk/azure-resource-part1 elérési utakat kell kinéznie.

A partíciókat a `blkid` egy adatlemezt tartalmazó lista. Alkalmazások karbantartása az érintett partíciók UUID és elérési utat használjon felderítéséhez futtatáskor az eszköz nevét:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>A legújabb Azure Storage-szabályok beolvasása

A legújabb Azure Storage-szabályok lekéréséhez futtassa a következő parancsokat:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Lásd még

További információkért tekintse át a következő cikkeket:

- [Ubuntu: Használja az UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Állandó elnevezése](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Mi az UUID azonosítók tehet meg](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Bevezetés a kezelés a modern Linux rendszerben](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

