---
title: Szoftveres RAID a Linux rendszerű virtuális gép konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan mdadm használatával Linux rendszeren RAID konfigurálása az Azure-ban.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.component: disks
ms.openlocfilehash: 3627891df429745c66bb85aed5182ff934268027
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475424"
---
# <a name="configure-software-raid-on-linux"></a>Szoftveres RAID konfigurálása Linuxban
Egy általános forgatókönyv szoftveres RAID Linux rendszerű virtuális gépeken használhatja az Azure több csatlakoztatott adatlemezt nyújtjuk egyetlen RAID eszközként. Általában ez segítségével javíthatja a teljesítményt, és csak egyetlen lemez használatához képest nagyobb átviteli sebességet teszi lehetővé.

## <a name="attaching-data-disks"></a>Adatlemezek csatolására
Két vagy több üres adatlemezt a RAID-eszköz konfigurálásához szükségesek.  RAID eszköz létrehozásának elsődleges oka, a lemez i/o-teljesítmény javítása érdekében.  I/o-igényei alapján, ha szeretné, a standard szintű tárhelyre, legfeljebb 500 i/o/ps lemez vagy a prémium szintű storage lemezenkénti legfeljebb 5000 i/o/ps-tárolt lemezt. Ez a cikk nem lépnek részletes történő kiépítéséhez és adatlemezeket csatlakoztathat egy Linux rendszerű virtuális gépet.  A Microsoft Azure cikke [lemez csatolása](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) üres adatlemez csatolása az Azure-ban Linux rendszerű virtuális gép részletes útmutatást.

## <a name="install-the-mdadm-utility"></a>A mdadm segédprogram telepítéséhez
* **Ubuntu**
```bash
sudo apt-get update
sudo apt-get install mdadm
```

* **CentOS és Oracle Linux-környezetekkel**
```bash
sudo yum install mdadm
```

* **SLES és openSUSE**
```bash  
zypper install mdadm
```

## <a name="create-the-disk-partitions"></a>A lemez partíciók létrehozása
Ebben a példában a /dev/sdc hozunk létre egy egyetlen lemezpartíció. Az új lemez partíció /dev/sdc1 fogja meghívni.

1. Indítsa el `fdisk` partíciók létrehozásának megkezdéséhez

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. Nyomja le a parancssorba létrehozása ennyi egy **n**PPA partíció:

    ```bash
    Command (m for help): n
    ```

1. Ezután nyomja le az "p" hozhat létre egy **p**lsődleges partíció:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Kattintson ide "1" a partíciószám 1 kiválasztása:

    ```bash
    Partition number (1-4): 1
    ```

1. Válassza ki az új partíciót, vagy nyomja meg a kiindulási pont `<enter>` , fogadja el az alapértelmezett helyezi el a partíciót a szabad lemezterület a meghajtón a elején:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Válassza ki a partíció méretét, írja be például a "+10G" 10 gigabájt partíció létrehozásához. Vagy nyomja meg az `<enter>` hozzon létre egy olyan partíciót a teljes meghajtót is:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Ezután módosítsa az Azonosítót és **t**típusa a partíció az alapértelmezett azonosítójú "83" (Linux) "fd" (Linux raid automatikus) azonosító:

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Végül a partíciós táblán írni a meghajtó, és lépjen ki a fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>A RAID-tömb létrehozása
1. A következő példa lesz "stripe" (a RAID 0. szint) három partíció található, három különálló adatok lemezen (sdc1, sdd1, sde1).  Hívása a parancs egy új RAID eszköz futtatása után **/dev/md127** jön létre. Vegye figyelembe, hogy ha ezeket az adatokat lemezek azt korábban már egy másik működőképes RAID-tömb részeként szükség lehet hozzáadni a `--force` paramétert a `mdadm` parancsot:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. A fájlrendszer létrehozása az új RAID-eszközön
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE, and Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** – boot.md engedélyezése és mdadm.conf létrehozása

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > A SUSE-rendszerek módosítások elvégzése után újraindítás lehet szükség. Ez a lépés *nem* SLES 12 szükséges.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Az új fájlrendszer /etc/fstab hozzáadása
> [!IMPORTANT]
> Helytelen a az /etc/fstab fájl szerkesztése meghiúsulását eredményezheti. Ha nem tudja biztosan, tekintse meg a telepítési dokumentációban talál információkat megfelelően szerkesztése ezt a fájlt. Emellett javasoljuk, hogy a /etc/fstab fájl biztonsági másolata jön létre szerkesztése előtt.

1. Például hozza létre az új fájlrendszer, a kívánt csatlakoztatási pontja:

    ```bash
    sudo mkdir /data
    ```
1. /Etc/fstab, szerkesztésekor a **UUID** segítségével az eszköz nevét, hanem a fájlrendszer hivatkozhat.  Használja a `blkid` segítségével határozza meg, az új fájlrendszer UUID:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Nyissa meg a /etc/fstab egy szövegszerkesztőben, és adjon hozzá egy bejegyzés az új fájlrendszer, például:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Vagy a **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Ezt követően mentse, és zárja be a /etc/fstab.

1. Ellenőrizze, hogy helyesen szerepel-e az/etc/fstab bejegyzést:

    ```bash  
    sudo mount -a
    ```

    Ha ez a parancs egy hibaüzenetet eredményez, ellenőrizze a szintaxist a /etc/fstab fájlban.
   
    Ezután futtassa a `mount` parancsot annak biztosítása érdekében a fájlrendszer csatlakoztatása:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Nem kötelező) FailSafe rendszerindítási paramétereinek
   
    **fstab-konfiguráció**
   
    Terjesztések közé tartozik, vagy a `nobootwait` vagy `nofail` csatlakoztatási paraméterek, amelyeket az/etc/fstab fájl adhatók hozzá. Ezek a paraméterek lehetővé teszi a hibák, amikor az adott operációs rendszer, és lehetővé teszi a Linux rendszer folytatja a rendszerindítást, még akkor is, ha nem tudja megfelelően csatlakoztathatja a RAID fájlrendszert. Tekintse meg a disztribúció dokumentációjában talál további információt ezekről a paraméterekről.
   
    (Ubuntu). például:

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux rendszerindítási paramétereinek**
   
    A fenti paraméterek, a kernel paraméter mellett "`bootdegraded=true`" engedélyezheti a rendszer akkor is, ha a RAID által tapasztalt sérült vagy csökkentett teljesítményű, a példaként, ha egy adatmeghajtó véletlenül eltávolítják a virtuális gép. Alapértelmezés szerint ez is eredményezhet egy rendszerindításra képtelen rendszer.
   
    Tekintse meg a telepítési dokumentációban megfelelően a kernel paraméterek szerkesztése. Például a terjesztések (CentOS, Oracle Linux, SLES 11) ezeket a paramétereket vehetők fel manuálisan a "`/boot/grub/menu.lst`" fájl.  Ubuntu rendszeren ez a paraméter lehet hozzáadni a `GRUB_CMDLINE_LINUX_DEFAULT` változó a "/ etc/alapértelmezett/grub".


## <a name="trimunmap-support"></a>TRIM/UNMAP támogatása
Egyes Linux-kernelek vannak a elveti a nem használt blokkolja a lemez TRIM/UNMAP műveletek támogatásához. Ezek a műveletek elsősorban hasznosak tájékoztatja, hogy az oldalak törlése az Azure már nem érvényesek, és lehet elvetni a standard szintű tárolóban. Oldalak elvetése mentheti a költség, ha nagy méretű fájlokat hoz létre, és törölje őket.

> [!NOTE]
> RAID előfordulhat, hogy a elvetési parancsok nem ad meg, ha a tömb az adatrészlet méretének értéke kisebb, mint az alapértelmezett (512KB). Ez azért, mert a gazdagépen a unmap granularitási is 512KB. Ha a tömb adattömbméret keresztül mdadm a módosított `--chunk=` paramétert, majd a kérelmek TRIM/leképezésének megszüntetése figyelmen kívül hagyhatja a kernel.

Nincsenek a TRIM engedélyezéséhez kétféleképpen támogatja a Linux rendszerű virtuális gépen. A szokásos módon tekintse meg a terjesztési az ajánlott módszer:

- Használja a `discard` lehetőség a csatlakoztatási `/etc/fstab`, például:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Bizonyos esetekben a `discard` lehetőség is van a teljesítményre. Másik megoldásként futtathatja a `fstrim` manuálisan parancsot a parancssorból, vagy adja hozzá a crontab rendszeresen futtatásához:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
