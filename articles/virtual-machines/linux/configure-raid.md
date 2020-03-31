---
title: Szoftveres RAID konfigurálása Linux os virtuális gépen
description: Ismerje meg, hogyan konfigurálhatja a RAID Linux raid-et az Azure-ban.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 122abda51b907491b322908c3c2c689bc1723e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250256"
---
# <a name="configure-software-raid-on-linux"></a>Szoftveres RAID konfigurálása Linuxon
Gyakori forgatókönyv, hogy az Azure-ban a linuxos virtuális gépeken szoftveres RAID használatával több csatlakoztatott adatlemezt jelenítsen meg egyetlen RAID-eszközként. Ez általában a teljesítmény javítására és a nagyobb átviteli teljesítmény lehetővé teszi, mint egyetlen lemez használatával.

## <a name="attaching-data-disks"></a>Adatlemezek csatolása
A RAID-eszköz konfigurálásához két vagy több üres adatlemezre van szükség.  A RAID-eszköz létrehozásának elsődleges oka a lemez IO teljesítményének javítása.  Az I/O-igények alapján kiválaszthatja, hogy a standard tárolóban tárolt lemezeket csatlakoztatja-e lemezenként akár 500 IO/ps-rel, vagy prémium szintű tárolóval, lemezenként legfeljebb 5000 IO/ps-rel. Ez a cikk nem részletezi, hogyan lehet adatlemezeket kiépíteni és csatolni egy Linux virtuális géphez.  Tekintse meg a Microsoft Azure-cikk [et csatolja a lemezt](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) részletes útmutatást, hogyan csatolja az üres adatlemezt egy Linux virtuális gép az Azure-ban.

> [!IMPORTANT]
>Ne keverje a különböző méretű lemezeket, így a raidset teljesítményét a leglassabb lemezre kellene korlátozni. 

## <a name="install-the-mdadm-utility"></a>Telepítse a mdadm segédprogramot
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS & Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES és openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>A lemezpartíciók létrehozása
Ebben a példában egyetlen lemezpartíciót hozunk létre a /dev/sdc.In this example, we create a single disk partition on /dev/sdc. Az új lemezpartíció neve /dev/sdc1 lesz.

1. Partíciók létrehozásának megkezdése `fdisk`

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

1. Nyomja meg az 'n' gombot a kérdésnél, hogy **n**ew partíciót hozzon létre:

    ```bash
    Command (m for help): n
    ```

1. Ezután nyomja meg a "p" gombot, hogy **hozzon**létre egy p rimary partíciót:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Nyomja meg az "1" gombot az 1-es partíció kiválasztásához:

    ```bash
    Partition number (1-4): 1
    ```

1. Válassza ki az új partíció kezdőpontját, vagy nyomja `<enter>` meg az alapértelmezett értéket, ha a partíciót a szabad terület elejére szeretné helyezni a meghajtón:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Válassza ki a partíció méretét, például írja be a "+10G" parancsot egy 10 gigabájtos partíció létrehozásához. Vagy nyomja `<enter>` meg a következő egyetlen partíciót:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Ezután változtassa meg a partíció azonosítóját és **t**yp azonosítóját az alapértelmezett azonosítóról "83" (Linux) azonosítóra "fd" (Linux raid auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Végül írja be a partíciós táblát a meghajtóra, és lépjen ki az fdisk-ből:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>A RAID-tömb létrehozása
1. A következő példa három különálló adatlemezen (sdc1, sdd1, sde1) található három partícióval "csíkozott" (RAID 0 szint).  A parancs futtatása után létrejön a **/dev/md127** nevű új RAID-eszköz. Azt is vegye figyelembe, hogy ha ezek az adatlemezek korábban egy `--force` másik megszűnt `mdadm` RAID tömb részét képezik, szükség lehet a paraméter hozzáadására a parancshoz:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. A fájlrendszer létrehozása az új RAID-eszközön
   
    **CentOS, Oracle Linux, SLES 12, openSUSE és Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** - lehetővé teszi boot.md és hozzon létre mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > A SUSE-rendszereken végrehajtott módosítások után újra kell indítani a számítógépet. Ez a lépés *nem* szükséges az SLES 12-n.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Az új fájlrendszer hozzáadása az /etc/fstab fájlhoz
> [!IMPORTANT]
> Az /etc/fstab fájl helytelen szerkesztése nem indítható rendszert eredményezhet. Ha nem biztos a dolgában, a fájl megfelelő szerkesztésével kapcsolatos információkért olvassa el a disztribúció dokumentációját. Azt is javasoljuk, hogy az /etc/fstab fájl biztonsági mentése szerkesztés előtt létrejön.

1. Hozza létre a kívánt csatlakoztatási pontot az új fájlrendszerhez, például:

    ```bash
    sudo mkdir /data
    ```
1. Az /etc/fstab szerkesztésekor az **UUID azonosítót** kell használni a fájlrendszerre való hivatkozáshoz az eszköz neve helyett.  A `blkid` segédprogram segítségével határozza meg az új fájlrendszer UUID azonosítóját:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Nyissa meg az /etc/fstab fájlt egy szövegszerkesztőben, és adjon hozzá egy bejegyzést az új fájlrendszerhez, például:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Vagy **SLES 11-en:**

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Ezután mentse és zárja be az /etc/fstab kapcsolót.

1. Ellenőrizze, hogy az /etc/fstab bejegyzés helyes-e:

    ```bash  
    sudo mount -a
    ```

    Ha ez a parancs hibaüzenetet ad, ellenőrizze a szintaxist az /etc/fstab fájlban.
   
    Ezután `mount` futtassa a parancsot a fájlrendszer csatlakoztatásának biztosításához:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Nem kötelező) Hibabiztos rendszerindítási paraméterek
   
    **fstab konfiguráció**
   
    Számos disztribúció tartalmazza az `nobootwait` /etc/fstab fájlhoz adható vagy `nofail` csatlakoztatási paramétereket. Ezek a paraméterek lehetővé teszik a hibákat egy adott fájlrendszer csatlakoztatásakor, és lehetővé teszik a Linux rendszer indítását, még akkor is, ha nem tudja megfelelően csatlakoztatni a RAID fájlrendszert. Ezekről a paraméterekről a disztribúció dokumentációjában talál további információt.
   
    Példa (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux rendszerindítási paraméterek**
   
    A fenti paramétereken kívül a "`bootdegraded=true`" kernel paraméter lehetővé teszi a rendszer indítását, még akkor is, ha a RAID sérültnek vagy korlátozottnak tűnik, például ha egy adatmeghajtót véletlenül eltávolítanak a virtuális gépről. Alapértelmezés szerint ez nem indítható rendszert is eredményezhet.
   
    Kérjük, olvassa el a disztribúció dokumentációját a kernel paramétereinek megfelelő szerkesztéséhez. Például számos disztribúcióban (CentOS, Oracle Linux, SLES 11)`/boot/grub/menu.lst`ezeket a paramétereket manuálisan lehet hozzáadni a " " fájlhoz.  Ubuntu-n ez a paraméter `GRUB_CMDLINE_LINUX_DEFAULT` hozzáadható a változóhoz a "/etc/default/grub" listán.


## <a name="trimunmap-support"></a>TRIM/UNMAP támogatás
Egyes Linux kernelek támogatják a TRIM/UNMAP műveleteket a nem használt blokkok elvetéséhez a lemezen. Ezek a műveletek elsősorban a szabványos tárolás, hogy tájékoztassa az Azure-t, hogy a törölt lapok már nem érvényesek, és elvethetők. Az oldalak elvetése költségmegtakarítást jelenthet, ha nagyfájlokat hoz létre, majd törli őket.

> [!NOTE]
> Előfordulhat, hogy a RAID nem ad ki elvetési parancsokat, ha a tömb adattömbmérete kisebb, mint az alapértelmezett (512 KB). Ennek az az oka, hogy a gazdagép térképének feltérképezése is 512 KB. Ha a tömb adattömbméretét a mdadm `--chunk=` paraméterén keresztül módosította, akkor a RENDSZERMag figyelmen kívül hagyhatja a TRIM/unmap kérelmeket.

A LINUX virtuális gépben kétféleképpen engedélyezheti a TRIM-támogatást. Mint mindig, konzultáljon a forgalmazás az ajánlott megközelítés:

- Használja `discard` a csatlakoztatási lehetőséget a alkalmazásban, `/etc/fstab`például:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Bizonyos esetekben `discard` a lehetőség nek teljesítménybeli következményei lehetnek. Azt is megteheti, `fstrim` hogy a parancsot manuálisan futtatja a parancssorból, vagy hozzáadja a crontab-hoz, hogy rendszeresen fusson:

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
