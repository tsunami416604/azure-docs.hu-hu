---
title: LVM konfigurálása Linux rendszerű virtuális gépen |} A Microsoft Docs
description: Ismerje meg, hogyan LVM konfigurálása az Azure-beli Linux rendszeren.
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: szark
ms.openlocfilehash: 81ee7957c0b26440c064b7f39bc4cfb32b2abd15
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648335"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>LVM konfigurálása az Azure-beli Linuxos virtuális gépre
Ez a dokumentum bemutatja, hogyan lehet logikai kötet-kezelő (LVM) konfigurálása az Azure-beli virtuális gépen. LVM az operációsrendszer-lemez vagy az adatlemezeket az Azure-beli virtuális gépeken is használható, azonban alapértelmezés szerint a legtöbb felhő-rendszerképek nem fog LVM az operációsrendszer-lemez konfigurálva. Az alábbi lépéseket az LVM konfigurálása az adatlemezek összpontosít.

## <a name="linear-vs-striped-logical-volumes"></a>Csíkozott köteteken tárolni logikai és lineáris
LVM használható fizikai lemezek számos egyesítendő egyetlen tárolóköteten. Alapértelmezés szerint LVM általában létrehoz lineáris logikai kötetek, ami azt jelenti, hogy a fizikai tároló együtt összefűzött-e. Ebben az esetben olvasási és írási műveletek általában csak küld egyetlen. Ezzel szemben azt is létrehozhat olvasási és írási terjesztési helyének több lemezhez (RAID0 hasonlóan) a kötet csoportban lévő logikai csíkozott. Teljesítménybeli megfontolások miatt az valószínű, célszerű a logikai kötetek stripe-úgy, hogy olvasási és írási kihasználhassák az összes csatlakoztatott lemez.

Ez a dokumentum azt ismerteti, hogyan, több adatlemezek egyesítése egy kötetet csoportot, és hozzon létre logikai csíkozott kötetek. Az alábbi lépéseket a legtöbb disztribúciók dolgozhat általánosítva vannak. A legtöbb esetben a segédprogramok és munkafolyamatainak kezelése az Azure-ban LVM nem állnak alapvetően más, mint a más környezetben. Szokásos módon is tekintse át a Linux-gyártó dokumentációja és bevált gyakorlatok az LVM használata az adott terjesztési.

## <a name="attaching-data-disks"></a>Adatlemezek csatolására
Az egyik általában érdemes indítsa el a két vagy több üres adatlemezt LVM használatakor. I/o-igényei alapján, ha szeretné, a standard szintű tárhelyre, legfeljebb 500 i/o/ps lemez vagy a prémium szintű storage lemezenkénti legfeljebb 5000 i/o/ps-tárolt lemezt. Ez a cikk nem lépnek részletes történő kiépítéséhez és adatlemezeket csatlakoztathat egy Linux rendszerű virtuális gépet. A Microsoft Azure cikke [lemez csatolása](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) üres adatlemez csatolása az Azure-ban Linux rendszerű virtuális gép részletes útmutatást.

## <a name="install-the-lvm-utilities"></a>Az LVM segédprogramok telepítése
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **& Oracle Linux RHEL, CentOS**

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

    A SLES11, is szerkeszteni kell `/etc/sysconfig/lvm` és `LVM_ACTIVATED_ON_DISCOVERED` "engedélyezése":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>LVM konfigurálása
Ebben az útmutatóban indulunk ki fogja nevezzük, amely három adatlemezek csatolását `/dev/sdc`, `/dev/sdd` és `/dev/sde`. Az elérési utak előfordulhat, hogy nem egyezik a virtuális lemez elérési útja nevekkel. Futtathatja a(z)`sudo fdisk -l`"vagy hasonló paranccsal listát készíthet a rendelkezésre álló lemezek.

1. Készítse elő a fizikai kötetekre:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Hozzon létre egy kötetet. Ebben a példában a kötet csoportnak hívjuk `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Hozza létre a logikai (ek). Alább, hogy a parancs létrehoz egy logikai kötetet nevű `data-lv01` span a teljes kötet csoport, de ne feledje, hogy is megvalósítható a kötet csoportban több logikai kötetek létrehozására.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. A logikai kötet formázása

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > SLES11 használnak `-t ext3` ext4 helyett. SLES11 csak a csak olvasható hozzáférést ext4 fájlrendszereit támogatja.

## <a name="add-the-new-file-system-to-etcfstab"></a>Az új fájlrendszer /etc/fstab hozzáadása
> [!IMPORTANT]
> Nem megfelelően szerkesztése a `/etc/fstab` fájl meghiúsulását eredményezheti. Ha nem tudja biztosan, tekintse meg a telepítési dokumentációban talál információkat megfelelően szerkesztése ezt a fájlt. Is javasoljuk, hogy biztonsági másolatot a `/etc/fstab` fájl jön létre szerkesztése előtt.

1. Például hozza létre az új fájlrendszer, a kívánt csatlakoztatási pontja:

    ```bash  
    sudo mkdir /data
    ```

2. Keresse meg a logikai tárkötet elérési útján

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Nyissa meg `/etc/fstab` egy szövegszerkesztőben, és adja hozzá egy bejegyzés az új fájlrendszer, például:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Ezt követően mentse és zárja be `/etc/fstab`.

4. Ellenőrizze, hogy a `/etc/fstab` bejegyzés értéke helyes:

    ```bash    
    sudo mount -a
    ```

    Ha ez a parancs egy hibaüzenetet eredményez ellenőrizze a szintaxist a `/etc/fstab` fájlt.
   
    Ezután futtassa a `mount` parancsot annak biztosítása érdekében a fájlrendszer csatlakoztatása:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Nem kötelező) FailSafe rendszerindító paraméterek `/etc/fstab`
   
    Terjesztések közé tartozik, vagy a `nobootwait` vagy `nofail` csatlakoztatási paraméterek, amelyeket fel a `/etc/fstab` fájlt. Ezek a paraméterek lehetővé teszi a hibák, amikor az adott operációs rendszer, és lehetővé teszi a Linux rendszer folytatja a rendszerindítást, még akkor is, ha nem tudja megfelelően csatlakoztathatja a RAID fájlrendszert. Tekintse meg a disztribúció dokumentációjában talál további információt ezekről a paraméterekről.
   
    (Ubuntu). például:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>TRIM/UNMAP támogatása
Egyes Linux-kernelek vannak a elveti a nem használt blokkolja a lemez TRIM/UNMAP műveletek támogatásához. Ezek a műveletek elsősorban hasznosak tájékoztatja, hogy az oldalak törlése az Azure már nem érvényesek, és lehet elvetni a standard szintű tárolóban. Oldalak elvetése mentheti a költség, ha nagy méretű fájlokat hoz létre, és törölje őket.

Nincsenek a TRIM engedélyezéséhez kétféleképpen támogatja a Linux rendszerű virtuális gépen. A szokásos módon tekintse meg a terjesztési az ajánlott módszer:

- Használja a `discard` lehetőség a csatlakoztatási `/etc/fstab`, például:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Bizonyos esetekben a `discard` lehetőség is van a teljesítményre. Másik megoldásként futtathatja a `fstrim` manuálisan parancsot a parancssorból, vagy adja hozzá a crontab rendszeresen futtatásához:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
