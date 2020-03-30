---
title: LVM konfigurálása Linux ot futtató virtuális gépen
description: Ismerje meg, hogyan konfigurálhatja az LVM-et Linuxon az Azure-ban.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: guybo
ms.subservice: disks
ms.openlocfilehash: 7f560a1e6266b5f2452bf9442d2d4c983de1236e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066795"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>LVM konfigurálása Linux os virtuális gépen az Azure-ban
Ez a dokumentum ismerteti, hogyan konfigurálhatja a logikai kötetkezelőt (LVM) az Azure virtuális gépén. LvM lehet használni az operációs rendszer lemezén vagy adatlemezek az Azure virtuális gépeken, azonban alapértelmezés szerint a legtöbb felhőalapú lemezképek nem lvm konfigurálva az operációs rendszer lemezén. Az alábbi lépések az LVM adatlemezekhez való konfigurálására összpontosítanak.

## <a name="linear-vs-striped-logical-volumes"></a>Lineáris és csíkozott logikai kötetek
Az LVM segítségével több fizikai lemezt egyesíthet egyetlen tárolókötetben. Alapértelmezés szerint az LVM általában lineáris logikai köteteket hoz létre, ami azt jelenti, hogy a fizikai tároló összevan fűzve. Ebben az esetben az olvasási/írási műveletek általában csak egyetlen lemezre lesznek elküldve. Ezzel szemben csíkozott logikai köteteket is létrehozhatunk, ahol az olvasások és írások a kötetcsoportban található több lemezre kerülnek (hasonlóan a RAID0-hoz). A teljesítmény miatt valószínűleg a logikai köteteket úgy szeretné csíkozni, hogy az olvasások és írások az összes csatolt adatlemezt használják.

Ez a dokumentum leírja, hogyan egyesíthet több adatlemezt egyetlen kötetcsoportba, majd hogyan hozhat létre csíkozott logikai kötetet. Az alábbi lépések általánosak a legtöbb disztribúcióval való munkához. A legtöbb esetben az LVM Azure-beli felügyeletére szolgáló segédprogramok és munkafolyamatok alapvetően nem különböznek más környezetektől. Mint mindig, is konzultáljon a Linux-szállító dokumentációés gyakorlati lvm az adott disztribúció használatával.

## <a name="attaching-data-disks"></a>Adatlemezek csatolása
Az LVM használata esetén az egyik általában két vagy több üres adatlemezzel szeretne kezdeni. Az I/O-igények alapján kiválaszthatja, hogy a standard tárolóban tárolt lemezeket csatlakoztatja-e lemezenként akár 500 IO/ps-rel, vagy prémium szintű tárolóval, lemezenként legfeljebb 5000 IO/ps-rel. Ez a cikk nem részletezi, hogyan lehet adatlemezeket csatlakoztatni és csatolni egy Linux virtuális géphez. Tekintse meg a Microsoft Azure-cikk [et csatolja a lemezt](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) részletes útmutatást, hogyan csatolja az üres adatlemezt egy Linux virtuális gép az Azure-ban.

## <a name="install-the-lvm-utilities"></a>Az LVM segédprogramok telepítése
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS & Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 és openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    Az SLES11-en is `/etc/sysconfig/lvm` szerkeszteni kell, és be kell állítania `LVM_ACTIVATED_ON_DISCOVERED` az "engedélyezés" beállítást:

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Az LVM konfigurálása
Ebben az útmutatóban feltételezzük, hogy három adatlemezt csatolt, `/dev/sdc` `/dev/sdd` amelyeket a , és `/dev/sde`. Előfordulhat, hogy ezek az elérési utak nem egyeznek meg a virtuális gép lemezelérési útjának nevével. Futtathatja a`sudo fdisk -l`' ' vagy hasonló parancsot a rendelkezésre álló lemezek listázásához.

1. Készítse elő a fizikai térfogatokat:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Kötetcsoport létrehozása. Ebben a példában hívjuk `data-vg01`a kötetcsoport:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Hozza létre a logikai kötet(eke)t. Az alábbi parancs egyetlen logikai `data-lv01` kötetet hoz létre, amelynek célja a teljes kötetcsoport átterjedése, de vegye figyelembe, hogy a kötetcsoportban több logikai kötet is létrehozható.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Logikai kötet formázása

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Az SLES11 `-t ext3` használata ext4 helyett. Az SLES11 csak az ext4 fájlrendszerekhez való írásvédett hozzáférést támogatja.

## <a name="add-the-new-file-system-to-etcfstab"></a>Az új fájlrendszer hozzáadása az /etc/fstab fájlhoz
> [!IMPORTANT]
> Az `/etc/fstab` fájl nem megfelelő szerkesztése esetén előfordulhat, hogy a rendszer nem tud elindulni. Ha nem biztos a dolgában, a fájl megfelelő szerkesztésével kapcsolatos információkért olvassa el a disztribúció dokumentációját. Azt is javasoljuk, hogy `/etc/fstab` a szerkesztés előtt készítsen biztonsági másolatot a fájlról.

1. Hozza létre a kívánt csatlakoztatási pontot az új fájlrendszerhez, például:

    ```bash  
    sudo mkdir /data
    ```

2. A logikai kötet elérési útjának megkeresése

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Nyissa `/etc/fstab` meg a szövegszerkesztőben, és adjon hozzá egy bejegyzést az új fájlrendszerhez, például:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Ezután mentse `/etc/fstab`és zárja be a .

4. Ellenőrizze, `/etc/fstab` hogy a bejegyzés helyes-e:

    ```bash    
    sudo mount -a
    ```

    Ha ez a parancs hibaüzenetet ad, `/etc/fstab` ellenőrizze a fájlszintaxisát.
   
    Ezután `mount` futtassa a parancsot a fájlrendszer csatlakoztatásának biztosításához:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Nem kötelező) Failsafe rendszerindítási paraméterek`/etc/fstab`
   
    Számos disztribúció tartalmazza a `nobootwait` `nofail` `/etc/fstab` fájlhoz adható vagy csatlakoztatási paramétereket. Ezek a paraméterek lehetővé teszik a hibákat egy adott fájlrendszer csatlakoztatásakor, és lehetővé teszik a Linux rendszer indítását, még akkor is, ha nem tudja megfelelően csatlakoztatni a RAID fájlrendszert. Ezekről a paraméterekről a disztribúció dokumentációjában talál további információt.
   
    Példa (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>TRIM/UNMAP támogatás
Egyes Linux kernelek támogatják a TRIM/UNMAP műveleteket a nem használt blokkok elvetéséhez a lemezen. Ezek a műveletek elsősorban a szabványos tárolás, hogy tájékoztassa az Azure-t, hogy a törölt lapok már nem érvényesek, és elvethetők. Az oldalak elvetése költségmegtakarítást jelenthet, ha nagyfájlokat hoz létre, majd törli őket.

A LINUX virtuális gépben kétféleképpen engedélyezheti a TRIM-támogatást. Mint mindig, konzultáljon a forgalmazás az ajánlott megközelítés:

- Használja `discard` a csatlakoztatási lehetőséget a alkalmazásban, `/etc/fstab`például:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Bizonyos esetekben `discard` a lehetőség nek teljesítménybeli következményei lehetnek. Azt is megteheti, `fstrim` hogy a parancsot manuálisan futtatja a parancssorból, vagy hozzáadja a crontab-hoz, hogy rendszeresen fusson:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL, CentOS & Oracle Linux**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
