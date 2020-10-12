---
title: A Linux rendszerű virtuális gép eszköz nevének változásai az Azure-ban – problémamegoldás | Microsoft Docs
description: A cikk azt ismerteti, miért változnak a Linux rendszerű virtuális gépek eszközeinek nevei, és hogyan oldható meg a probléma.
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
ms.openlocfilehash: 31f64a504156134b1d622705d5301d9cd5a5f5b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88756826"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>A Linux rendszerű virtuális gép eszköz nevének módosításainak megoldása

Ez a cikk azt ismerteti, hogy az eszközök nevei miért változnak a Linux rendszerű virtuális gépek újraindítása vagy az adatlemezek újracsatolása után. A cikk a probléma megoldásait is tartalmazza.

## <a name="symptoms"></a>Hibajelenségek
A Linux rendszerű virtuális gépek Microsoft Azureban való futtatásakor a következő problémák merülhetnek fel:

- A virtuális gép újraindítás után nem indul el.
- Ha az adatlemezeket leválasztják és újra csatlakoztatják, a lemezes eszközök nevei módosulnak.
- Egy olyan alkalmazás vagy parancsfájl, amely az eszköz nevével hivatkozik a lemezre, nem sikerül, mert az eszköz neve megváltozott.

## <a name="cause"></a>Ok

A Linux eszköz elérési útjai nem garantáltan konzisztensek az újraindítások között. Az eszközök nevei nagy számokból (betűkből) és kisebb számokból állnak. Ha a Linux Storage-eszköz illesztőprogramja új eszközt észlel, az illesztőprogram a rendelkezésre álló tartományból származó fő és másodlagos számokat rendeli hozzá az eszközhöz. Egy eszköz eltávolításakor az eszközök száma fel lesz szabadítva az újbóli használatra.

A probléma akkor fordul elő, ha a Linux rendszerű eszközök vizsgálatát az SCSI alrendszer aszinkron módon ütemezi. Ennek eredményeképpen az eszköz elérési útjának neve eltérő lehet az újraindítások között.

## <a name="solution"></a>Megoldás

A probléma megoldásához használja az állandó elnevezést. Az állandó elnevezést négy módon lehet használni: a FileSystem Label, az UUID, az ID vagy az Path alapján. Javasoljuk, hogy az Azure Linux rendszerű virtuális gépekhez a fájlrendszer címkéjét vagy az UUID-t használja.

A legtöbb disztribúció a `fstab` **nem hibás** vagy a **nobootwait** paramétert adja meg. Ezek a paraméterek lehetővé teszik a rendszer rendszerindítását, ha a lemez nem csatlakoztatható indításkor. A paraméterekkel kapcsolatos további információkért olvassa el a terjesztési dokumentációt. Arról, hogy hogyan konfigurálhat Linux rendszerű virtuális gépet egy adatlemez hozzáadásakor UUID használatára, olvassa el a [Csatlakozás a Linux rendszerű virtuális géphez az új lemez csatlakoztatása](../linux/add-disk.md#format-and-mount-the-disk)című témakört.

Ha az Azure Linux-ügynök egy virtuális gépre van telepítve, az ügynök udev-szabályokkal hozza létre a szimbolikus hivatkozásokat a/dev/disk/Azure elérési útja alatt. Az alkalmazások és a parancsfájlok udev szabályokat használnak a virtuális géphez csatolt lemezek azonosítására, valamint a lemez típusára és a lemez logikai egységére.

Ha már szerkesztette az fstab-t úgy, hogy a virtuális gép nem indul el, és nem tud SSH-t használni a virtuális géphez, a [virtuális gép soros konzolján](./serial-console-linux.md) megadhatja az [egyfelhasználós üzemmódot](./serial-console-grub-single-user-mode.md) , és módosíthatja az fstab-t.

### <a name="identify-disk-luns"></a>Lemezes LUN azonosítók azonosítása

Az alkalmazások a LUN-ot használják az összes csatlakoztatott lemez megtalálásához, és szimbolikus hivatkozások létrehozásához. Az Azure Linux-ügynök olyan udev-szabályokat tartalmaz, amelyek a LUN-ról az eszközökre mutató szimbolikus hivatkozásokat állítanak be:

```console
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
```

A Linux vendég fiókból származó LUN-adatokat a `lsscsi` vagy egy hasonló eszköz használatával kéri le a rendszer:

```console
$ sudo lsscsi

[1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

[2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

[3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

[5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

[5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd
```

A vendég LUN adatait az Azure-előfizetési metaadatokkal együtt használva megkeresheti az Azure Storage-ban a partíciós adatokat tartalmazó virtuális merevlemezt. Használhatja például a `az` parancssori felületet:

```azurecli
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
```

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Fájlrendszerbeli UUID-azonosítók felderítése a blkid használatával

Az alkalmazások és a parancsfájlok beolvassák az adatok kimenetét, `blkid` vagy hasonló információforrásokat a szimbolikus hivatkozások létrehozásához a/dev elérési útjában. A kimenet megjeleníti a virtuális géphez csatlakoztatott összes lemez UUID-azonosítóját, valamint a hozzájuk tartozó eszköz fájlját:

```console
$ sudo blkid -s UUID

/dev/sr0: UUID="120B021372645f72"
/dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
/dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
/dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"
```

Az Azure Linux-ügynök udev szabályai a/dev/disk/Azure elérési útja alatt a szimbolikus hivatkozások készletét határozzák meg:

```console
$ ls -l /dev/disk/azure

total 0
lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1
```

Az alkalmazások a hivatkozásokkal azonosítják a rendszerindító lemez eszközét és az erőforrás-(ideiglenes) lemezt. Az Azure-ban az alkalmazásoknak a/dev/disk/Azure/root-part1 vagy a/dev/disk/Azure-Resource-part1 elérési utakon kell megkeresniük a partíciók felderítéséhez.

A listából származó további partíciók `blkid` egy adatlemezen találhatók. Az alkalmazások megőrzik az UUID-t ezekhez a partíciókhoz, és egy elérési utat használnak az eszköz nevének felderítéséhez futásidőben:

```console
$ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1
```


### <a name="get-the-latest-azure-storage-rules"></a>A legújabb Azure Storage-szabályok beszerzése

Az Azure Storage legújabb szabályainak beszerzéséhez futtassa a következő parancsokat:

```console
# sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
# sudo udevadm trigger --subsystem-match=block
```

## <a name="see-also"></a>Lásd még

További információkért tekintse át a következő cikkeket:

- [Ubuntu: az UUID használata](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: állandó elnevezés](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: milyen UUID-ket használhat Önnek](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Bevezetés az Eszközkezelőbe egy modern Linux rendszerben](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

