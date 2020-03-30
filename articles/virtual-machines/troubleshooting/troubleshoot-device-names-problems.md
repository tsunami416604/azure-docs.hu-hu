---
title: Linux os virtuális gépnév-változások elhárítása az Azure-ban | Microsoft dokumentumok
description: A linuxos virtuális gép eszköznevének változása és a probléma megoldásának módját ismerteti.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 7d8a7e7e88837214042fb8f1c109c0b93bfe771b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058200"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Linux virtuális gép eszköznév-módosításokkal kapcsolatos hibák elhárítása

Ez a cikk ismerteti, hogy miért változnak az eszköznevek a Linux virtuális gép újraindítása vagy az adatlemezek újbóli csatolása után. A cikk a problémára is megoldást kínál.

## <a name="symptoms"></a>Probléma
A következő problémákléphetnek fel, amikor Linux os virtuális gépeket futtat a Microsoft Azure-ban:

- A virtuális gép újraindítás után nem indul el.
- Az adatlemezek lecserélésekés újracsatlakoztatásakor a lemezeszköz nevei megváltoznak.
- Az eszköznév használatával lemezre hivatkozó alkalmazás vagy parancsfájl nem működik, mert az eszköz neve megváltozott.

## <a name="cause"></a>Ok

A Linuxon lévő eszközelérési utak nem garantáltak, hogy konzisztensek legyenek az újraindítások között. Az eszköznevek nagy számokból (betűkből) és kisebb számokból állnak. Amikor a Linux-tárolóeszköz-illesztőprogram új eszközt észlel, az illesztőprogram nagyobb és kisebb számokat rendel hozzá a rendelkezésre álló tartományból az eszközhöz. Az eszköz eltávolításakor az eszközszámok újrafelhasználásra kerülnek.

A probléma oka az, hogy az SCSI alrendszer aszinkron módon tervezi az eszközbeolvasást Linux alatt. Ennek eredményeképpen az eszköz elérési útja az újraindítások között változhat.

## <a name="solution"></a>Megoldás

A probléma megoldásához használjon állandó elnevezést. Az állandó elnevezés négyféleképpen használható: fájlrendszer-címke, UUID, ID vagy elérési út szerint. Javasoljuk, hogy használja a fájlrendszer címkéjét vagy uuid az Azure Linux virtuális gépek.

A legtöbb disztribúció adja meg a `fstab` **nofail** vagy **nobootwait** paramétereket. Ezek a paraméterek lehetővé teszik a rendszer indítását, ha a lemez indításkor nem csatlakoztatható. Ezekről a paraméterekről további információt a terjesztési dokumentációban talál. Ha tudni szeretné, hogyan konfigurálható a Linux virtuális gép UUID-t adatlemez hozzáadásakor, olvassa el [a Csatlakozás a Linux virtuális géphez az új lemez csatlakoztatásához című témakört.](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk)

Ha az Azure Linux-ügynök telepítve van egy virtuális gépre, az ügynök udev-szabályok at a /dev/disk/azure elérési út alatt szimbolikus hivatkozások at. Az alkalmazások és parancsfájlok Udev-szabályok használatával azonosítják a virtuális géphez csatlakoztatott lemezeket, valamint a lemez típusát és a lemez LUN-ok.

Ha már szerkesztette a fstab oly módon, hogy a virtuális gép nem indul el, és nem tudja, hogy SSH a virtuális gép, használhatja a [virtuális gép soros konzol](./serial-console-linux.md) [egyfelhasználós módban,](./serial-console-grub-single-user-mode.md) és módosítsa a fstab.

### <a name="identify-disk-luns"></a>Lemezlu-ok azonosítása

Az alkalmazások logikai és logikai kapcsolatok at használnak az összes csatlakoztatott lemez megkereséséhez és szimbolikus hivatkozások létrehozásához. Az Azure Linux-ügynök udev-szabályokat tartalmaz, amelyek szimbolikus hivatkozásokat állítanak be a főfelhasználóféláltal az eszközökre:

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

A Linux vendégfiókból származó LUN-adatok at egy hasonló eszköz segítségével `lsscsi` vagy egy hasonló eszközzel kérik le:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

A vendég logikai egység adatait az Azure-előfizetés metaadatai a partícióadatokat tartalmazó Azure Storage-beli virtuális merevlemez megkereséséhez használják. Használhatja például a `az` CLI-t:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>A fájlrendszer UUID-jainak felderítése blkid használatával

Az alkalmazások és parancsfájlok `blkid`a kimenetét vagy hasonló információforrásokat olvasnak be a /dev elérési úton lévő szimbolikus hivatkozások létrehozásához. A kimenet a virtuális géphez és a hozzájuk tartozó eszközfájlhoz csatlakoztatott összes lemez UUI-azonosítóit jeleníti meg:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Az Azure Linux-ügynök Udev-szabályok létre egy sor szimbolikus hivatkozások a /dev/disk/azure elérési út alatt:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Az alkalmazások a hivatkozások segítségével azonosítják a rendszerindító lemezeszközt és az erőforrás (rövid élettartamú) lemezt. Az Azure-ban az alkalmazásoknak meg kell keresniük a /dev/disk/root-part1 vagy a /dev/disk/azure-resource-part1 elérési utakat a partíciók felderítéséhez.

A `blkid` listából származó további partíciók adatlemezen találhatók. Az alkalmazások fenntartják ezeknek a partícióknak az UUID azonosítóját, és egy elérési utat használnak az eszköz nevének felderítéséhez futásidőben:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Az Azure Storage legújabb szabályainak beszereznie

A legújabb Azure Storage-szabályok beszerezéséhez futtassa a következő parancsokat:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Lásd még

További információkért tekintse át a következő cikkeket:

- [Ubuntu: UUID használata](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Állandó elnevezés](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Mit tehetnek önért az UUID-k?](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Bevezetés az eszközkezelésbe egy modern Linux rendszerben](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

