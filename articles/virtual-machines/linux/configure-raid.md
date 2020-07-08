---
title: Szoftveres RAID konfigurálása Linux rendszerű virtuális gépen
description: Ismerje meg, hogyan konfigurálhatja a mdadm a RAID használatára az Azure-ban Linux rendszeren.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 3471ccfa0899f73969c511dea283c2d0d7051af8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84659783"
---
# <a name="configure-software-raid-on-linux"></a>Szoftveres RAID konfigurálása Linuxon
A Linux rendszerű virtuális gépeken az Azure-ban a szoftveres RAID használatával egyetlen RAID-eszközként több csatlakoztatott adatlemezt is be lehet mutatni. Ez általában a teljesítmény javítása és a jobb átviteli sebesség lehetővé tétele, mint a csak egyetlen lemez használata.

## <a name="attaching-data-disks"></a>Adatlemezek csatolása
RAID-eszköz konfigurálásához legalább két üres adatlemez szükséges.  A RAID-eszköz létrehozásának elsődleges oka a lemezes IO teljesítményének növelése.  Az i/o-igények alapján a standard szintű tárolóban tárolt lemezeket csatlakoztathatja, és akár 500 IO/PS-t, akár lemezes, akár 5000 IO/PS-alapú Premium Storage-t is használhat. Ez a cikk nem részletesen ismerteti az adatlemezek Linux rendszerű virtuális géphez való kiépítésének és csatlakoztatásának menetét.  Tekintse meg a Microsoft Azure a [lemez csatlakoztatása](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) című cikket, amely részletesen ismerteti, hogyan lehet üres adatlemezt csatlakoztatni egy linuxos virtuális géphez az Azure-ban.

> [!IMPORTANT]
>Ne keverje a különböző méretű lemezeket, így a raidset teljesítménye a leglassabb lemezre korlátozódik. 

## <a name="install-the-mdadm-utility"></a>A mdadm segédprogram telepítése
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
Ebben a példában egyetlen lemezpartíció-partíciót hozunk létre a/dev/SDC.-on. Az új lemezpartíció neve/dev/sdc1. lesz.

1. `fdisk`A partíciók létrehozásának megkezdése

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

1. Egy **n**EW partíció létrehozásához nyomja meg az "n" billentyűt a parancssorban:

    ```bash
    Command (m for help): n
    ```

1. Ezután a "p" gomb megnyomásával hozzon létre egy **p**-vel elválasztó partíciót:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Az 1. számú partíció kiválasztásához nyomja meg az "1" gombot:

    ```bash
    Partition number (1-4): 1
    ```

1. Válassza ki az új partíció kiindulási pontját, vagy nyomja meg a gombot, `<enter>` hogy fogadja el az alapértelmezett értéket, hogy a partíciót a szabad terület elején helyezze el a meghajtón:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Válassza ki a partíció méretét, például a "+ 10G" típust egy 10 gigabájtos partíció létrehozásához. Vagy nyomja meg a `<enter>` teljes meghajtót átívelő egyetlen partíció létrehozása:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Ezután módosítsa a partíció AZONOSÍTÓját és **t**-típusát az alapértelmezett "83" azonosítóval (Linux) az "FD" azonosítóra (Linux RAID Auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Végül írja a partíciós táblát a meghajtóra, és zárja be az fdisk parancsot:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>A RAID-tömb létrehozása
1. A következő példa a "Stripe" (0. RAID-szint) három partíciót mutat be három különálló adatlemezen (sdc1, sdd1, sde1).  A parancs futtatása után létrejön egy új, **/dev/md127** nevű RAID-eszköz. Azt is vegye figyelembe, hogy ha ezek az adatlemezek korábban egy másik inaktív RAID-tömb részét képezték, akkor szükség lehet a paraméter hozzáadására `--force` a `mdadm` parancshoz:

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
   
    **SLES 11** – a boot.MD engedélyezése és a mdadm. conf létrehozása

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > A módosítások a SUSE rendszereken való végrehajtása után újraindításra lehet szükség. Ez a lépés *nem* szükséges a 12. SLES.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Az új fájlrendszer hozzáadása az/etc/fstab modulhoz
> [!IMPORTANT]
> Az/etc/fstab fájl nem megfelelő szerkesztése nem indítható rendszert eredményezhet. Ha nem biztos a dolgában, a fájl megfelelő szerkesztésével kapcsolatos információkért olvassa el a disztribúció dokumentációját. Azt is javasoljuk, hogy a Szerkesztés előtt hozza létre az/etc/fstab fájl biztonsági másolatát.

1. Hozza létre az új fájlrendszer kívánt csatlakoztatási pontját, például:

    ```bash
    sudo mkdir /data
    ```
1. Az/etc/fstab-fájl szerkesztésekor az **UUID** -t kell használni az eszköz neve helyett a fájlrendszerre való hivatkozáshoz.  A `blkid` segédprogram használatával határozza meg az új fájlrendszer UUID-azonosítóját:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Nyissa meg az/etc/fstab fájlt egy szövegszerkesztőben, és adjon hozzá egy bejegyzést az új fájlrendszerhez, például:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Vagy a **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Ezután mentse és zárjuk le a/etc/fstab.

1. Ellenőrizze, hogy helyesek-e az/etc/fstab bejegyzés:

    ```bash  
    sudo mount -a
    ```

    Ha ez a parancs hibaüzenetet eredményez, ellenőrizze a szintaxist az/etc/fstab fájlban.
   
    A következő parancs futtatásával `mount` Győződjön meg arról, hogy a fájlrendszer csatlakoztatva van:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. Választható Failsafe rendszerindítási paraméterek
   
    **fstab-konfiguráció**
   
    Számos disztribúció tartalmazza az `nobootwait` `nofail` /etc/fstab fájlhoz adható vagy csatlakoztatási paramétereket. Ezek a paraméterek lehetővé teszik a meghibásodást egy adott fájlrendszer csatlakoztatásakor, és lehetővé teszik a Linux rendszernek a rendszerindítás folytatását akkor is, ha nem tudja megfelelően csatlakoztatni a RAID fájlrendszert. A paraméterekkel kapcsolatos további információkért tekintse meg a terjesztés dokumentációját.
   
    Példa (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux rendszerindítási paraméterek**
   
    A fenti paraméterek mellett a (z) "" kernel paraméter a `bootdegraded=true` rendszerindítást is lehetővé teszi, még akkor is, ha a RAID sérült vagy csökkentett teljesítményű, például ha egy adatmeghajtót véletlenül eltávolítottak a virtuális gépről. Ez alapértelmezés szerint nem rendszerindító rendszert is eredményezhet.
   
    A rendszermag paramétereinek megfelelő szerkesztéséhez tekintse meg a disztribúció dokumentációját. Például sok disztribúcióban (CentOS, Oracle Linux, SLES 11) ezeket a paramétereket manuálisan is hozzáadhatja a " `/boot/grub/menu.lst` " fájlhoz.  Ubuntu esetén ez a paraméter a `GRUB_CMDLINE_LINUX_DEFAULT` "/etc/default/grub" változóhoz adható hozzá.


## <a name="trimunmap-support"></a>TRIM/LEKÉPEZÉSÉNEK megszüntetése-támogatás
Egyes linuxos kernelek támogatják a TRIM/LEKÉPEZÉSÉNEK megszüntetése műveleteket a lemezen lévő nem használt blokkok elvetéséhez. Ezek a műveletek elsődlegesen a standard szintű tárolásban hasznosak, hogy tájékoztassák az Azure-t arról, hogy a törölt lapok már nem érvényesek, és el lehet őket dobni. Ha nagyméretű fájlokat hoz létre, és törli őket, a lapok elvetése is megtakaríthatja a költségeket.

> [!NOTE]
> Előfordulhat, hogy a RAID nem ad ki elveti parancsokat, ha a tömb méretének értéke kisebb, mint az alapértelmezett (512 KB nál). Ennek az az oka, hogy a gazdagépen a leképezésének megszüntetése részletessége is 512 KB nál. Ha módosította a tömb méretének méretét a mdadm `--chunk=` paraméterrel, akkor a rendszer a kernel által figyelmen kívül hagyhatja a Trim/leképezésének megszüntetése kérelmeket.

A Linux rendszerű virtuális gépen kétféleképpen engedélyezhető a TRIM-támogatás. A szokásos módon tekintse meg az ajánlott módszert az eloszlásban:

- Használja a `discard` csatlakoztatási lehetőséget a alkalmazásban `/etc/fstab` , például:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Bizonyos esetekben a `discard` beállítás teljesítménybeli következményekkel járhat. Azt is megteheti, hogy manuálisan futtatja a `fstrim` parancsot a parancssorból, vagy hozzáadja azt a crontabhoz, hogy rendszeresen fusson:

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
