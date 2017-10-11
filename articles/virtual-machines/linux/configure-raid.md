---
title: "Szoftveres RAID Linuxot futtató virtuális gépen konfigurálása |} Microsoft Docs"
description: "Útmutató mdadm Linux RAID konfigurálása az Azure-ban."
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: timlt
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
ms.openlocfilehash: 12f540a700fbf85e579e8aadc9f6def039299ff7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="configure-software-raid-on-linux"></a>Szoftveres RAID konfigurálása Linuxban
Egy általános forgatókönyv szoftveres RAID Linux virtuális gépeken használatára az Azure-ban van több csatolt adatlemezek egyetlen RAID eszközként is. Általában ez is használható a teljesítmény javítása, és nagyobb átviteli sebesség eléréséhez csak egyetlen lemez használatával engedélyezheti.

## <a name="attaching-data-disks"></a>Adatlemez csatlakoztatása
Két vagy több üres adatlemezek RAID eszköz beállítása van szükség.  Az elsődleges RAID eszköz létrehozása oka, hogy a lemez IO teljesítmény javítása érdekében.  IO igényei szerint, ha szeretné, csatlakoztassa a szabványos tároló, a legfeljebb 500 IO/ps / lemez vagy a prémium szintű storage, legfeljebb 5000 IO/ps lemezenként tárolt lemezeket. Ez a cikk nem halad részletes kiépíteni, és a Linux virtuális gép adatlemezt csatolni.  A Microsoft Azure cikke [lemezt csatlakoztatni](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) egy üres adatlemezt csatolni egy Linux virtuális gépet az Azure részletes útmutatást.

## <a name="install-the-mdadm-utility"></a>Telepítse a mdadm segédprogram
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

## <a name="create-the-disk-partitions"></a>Hozzon létre a lemezpartíció
Ebben a példában /dev/sdc egyetlen lemezt a partíciót létrehozni azt. Új tároló lemezpartíción /dev/sdc1 neve.

1. Start `fdisk` partíciók létrehozásának megkezdéséhez

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

2. Nyomja meg az "n" létrehozásához a parancssorban a  **n** új partíció:

    ```bash
    Command (m for help): n
    ```

3. Nyomja meg az "p" létrehozásához egy **p**lsődleges partíció:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. Nyomja meg az "1" partíciószám 1 kiválasztásához:

    ```bash
    Partition number (1-4): 1
    ```

5. Válassza ki az új partíciót, vagy nyomja meg a kiindulási pont `<enter>` elfogadja az alapértelmezett helyezhető el a partíció a szabad lemezterület a meghajtón elején:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. Válassza ki a partíció méretét, írja be például a "+10G" 10 gigabájt partíció létrehozásához. Vagy nyomja le az ENTER `<enter>` létrehoz egy olyan partíciót a teljes meghajtót is:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. Ezután módosítsa az Azonosítót és **t**típusa a partíció a "83" alapértelmezett-azonosító (Linux) azonosító "fd" (Linux raid automatikus):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. Végezetül a partíciós táblán írni a meghajtót, és zárja be a fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>A RAID tömb létrehozása
1. A következő példa fog "paritásos" (RAID 0. szint) három lemezeken található három különböző adatokhoz (sdc1, sdd1, sde1).  A parancs új RAID eszköz futtatása után **/dev/md127** jön létre. Vegye figyelembe azt is, hogy ha ezeket az adatokat lemezek azt korábban egy másik inaktív RAID tömb része lehet kell hozzáadnia a `--force` paramétert a `mdadm` parancs:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. A fájlrendszer létrehozása az új RAID-eszközön
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE és Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** - boot.md engedélyezése és mdadm.conf létrehozása

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > A SUSE rendszereken módosítások végrehajtását követően újraindításra lehet szükség. Ez a lépés *nem* SLES 12 szükséges.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Az új fájlrendszer /etc/fstab hozzáadása
> [!IMPORTANT]
> Helytelen a az /etc/fstab fájl szerkesztése meghiúsulását eredményezheti. Ha nem ismeri, tekintse meg a telepítési dokumentációban talál információkat arról a fájl megfelelően szerkesztése. Ajánlott továbbá, hogy létrejött-e a fájl biztonsági másolata /etc/fstab szerkesztése előtt.

1. Létrehozhat például az új fájlrendszer, a megfelelő csatlakozási pont:

    ```bash
    sudo mkdir /data
    ```
2. /Etc/fstab, szerkesztésekor a **UUID** használatával hivatkozik, az eszköz neve helyett a fájlrendszerben.  Használja a `blkid` segítségével határozza meg, az új fájlrendszer UUID:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. Nyissa meg a /etc/fstab egy szövegszerkesztőben, és adja hozzá a bejegyzést az új fájlrendszer, például:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Vagy a **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Ezt követően mentse, és zárja be a /etc/fstab.

4. Tesztelje, hogy helyesek-e az/etc/fstab bejegyzést:

    ```bash  
    sudo mount -a
    ```

    Ha ezt a parancsot egy hibaüzenet, ellenőrizze a szintaxist a /etc/fstab fájlban.
   
    Ezután futtassa a `mount` parancsot annak biztosítására, hogy a fájlrendszer van csatlakoztatva:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (Választható) FailSafe rendszerindító paraméterek
   
    **fstab konfiguráció**
   
    Terjesztések valamelyikét tartalmazza a `nobootwait` vagy `nofail` csatlakoztatási paramétereket, előfordulhat, hogy hozzá lehet adni a/etc/fstab fájlhoz. Ezek a paraméterek hibák engedélyezése, ha egy adott fájlrendszer csatlakoztatása és a Linux rendszer akkor is, ha az nem csatolható fel a RAID fájlrendszer megfelelően elindulni. Tekintse meg a terjesztési dokumentációjában talál további információt ezekről a paraméterekről.
   
    (Ubuntu). példa:

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux rendszerindító paraméterek**
   
    A fenti paraméterek, a kernel paraméter mellett "`bootdegraded=true`" megtehetik, hogy a rendszer akkor is, ha a RAID érzékelt sérült vagy csökkent, a példaként, ha egy adatmeghajtó véletlenül eltávolítják a virtuális gépről. Alapértelmezés szerint ez is vezethet a nem rendszerindító rendszer.
   
    Tekintse meg a terjesztési dokumentációja megfelelően a kernel paraméterek szerkesztése. Például terjesztések (CentOS, Oracle Linux SLES 11.) a következő paraméterek vehetők manuálisan a "`/boot/grub/menu.lst`" fájl.  Ubuntu az ezzel a paraméterrel adhatók hozzá a `GRUB_CMDLINE_LINUX_DEFAULT` változó a "/ etc/alapértelmezett/lárvajárat".


## <a name="trimunmap-support"></a>TRIM/UNMAP támogatása
Egyes Linux kernelek támogatja a vágás/UNMAP műveleteket elveti a nem használt blokkok a lemezen. Ezek a műveletek elsősorban hasznosak standard tárolási tájékoztatja Azure törölt lapok már nem érvényesek, és is elvesznek. Lapok elvetése költség mentheti, ha nagy fájlok létrehozása, majd törli őket.

> [!NOTE]
> RAID nem lehetséges, hogy ki elvetési parancsok, ha a tömb az adatrészlet méretének értéke kisebb, mint az alapértelmezett (512KB). Ez azért, mert a gazdagépen unmap granularitása is 512KB. Ha a tömb adatrészlet méretének keresztül mdadm által módosított `--chunk=` paramétert, majd a vágás/megfeleltetésének törlése kérelmek előfordulhat, hogy figyelmen kívül lesz hagyva a rendszermag által.

Két módon vágást engedélyezése támogatja a Linux virtuális gép van. A szokásos módon olvassa el a terjesztési az ajánlott módszer:

- Használja a `discard` lehetőség a csatlakoztatási `/etc/fstab`, például:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Bizonyos esetekben a `discard` lehetőség is van a teljesítményre. Alternatív megoldásként futtathatja a `fstrim` manuálisan parancsot a parancssorból, vagy adja hozzá a crontab rendszeresen futtatásához:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL vagy CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
