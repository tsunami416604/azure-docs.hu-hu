---
title: "LVM konfigurálása a Linux rendszerű virtuális gép |} Microsoft Docs"
description: "Megtudhatja, hogyan LVM konfigurálása Linux az Azure-ban."
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: timlt
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 7926627aaa3f0da935131f491d927ab5cb4b35c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>A Linux virtuális gép az Azure-ban LVM konfigurálása
Ez a dokumentum bemutatja az logikai kötet Manager (LVM) konfigurálása az Azure virtuális gépen. Is megvalósítható LVM konfigurálása a virtuális géphez csatolt lemezen, alapértelmezés szerint a legtöbb felhő lemezképek nem lesz konfigurálva az operációsrendszer-lemezképet a LVM. Ez a duplikált kötet csoportok problémák elkerülése érdekében, ha az operációs rendszer lemezének valaha is egy másik virtuális Géphez van csatolva az azonos terjesztési és típusa, azaz a helyreállítás során. Ezért ajánlott, csak az adatlemezek LVM használandó.

## <a name="linear-vs-striped-logical-volumes"></a>Csíkozott köteteken tárolni logikai és lineáris
A fizikai lemezek számát egyesítése egyetlen tárolóköteten LVM használható. Alapértelmezés szerint LVM általában létrehoz lineáris logikai kötetek, ami azt jelenti, hogy a fizikai tároló együtt összefűzendő-e. Ebben az esetben olvasási/írási műveletek általában csak kapnak egyetlen lemezre. Ezzel szemben azt is létrehozhat ahol olvasási és írási elosztott több olyan lemezt, a kötet (azaz a RAID0 hasonlóan) csoportban lévő logikai csíkozott. A teljesítményre vonatkozó megfontolásból valószínűleg érdemes a logikai kötetek paritásos, hogy az olvasások és írások használják a mellékelt adatok lemezek.

Ez a dokumentum azt ismerteti, hogyan több adatlemezek egyesítése egyetlen köteten csoport, majd hozza létre a logikai csíkozott kötetek. Az alábbi lépéseket történő együttműködésre a legtöbb terjesztéseket némileg általánosítva vannak. A legtöbb esetben a segédprogramok és munkafolyamatok kezelése az Azure-on LVM nincsenek alapvetően más, mint más környezetekben. Szokásos módon is tekintse át a Linux forgalmazójával dokumentációk és ajánlott eljárások az adott terjesztési LVM használatával.

## <a name="attaching-data-disks"></a>Adatlemez csatlakoztatása
Egy általában érdemes legalább két üres adatlemezekkel rendelkező start LVM használatakor. IO igényei szerint, ha szeretné, csatlakoztassa a szabványos tároló, a legfeljebb 500 IO/ps / lemez vagy a prémium szintű storage, legfeljebb 5000 IO/ps lemezenként tárolt lemezeket. Ez a cikk nem kerül részletes kiépíteni, és a Linux virtuális gép adatlemezt csatolni. A Microsoft Azure cikke [lemezt csatlakoztatni](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) egy üres adatlemezt csatolni egy Linux virtuális gépet az Azure részletes útmutatást.

## <a name="install-the-lvm-utilities"></a>Telepítse a LVM segédeszközöket
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

    A SLES11 is szerkeszteni kell `/etc/sysconfig/lvm` és `LVM_ACTIVATED_ON_DISCOVERED` az "Engedélyezés":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>LVM konfigurálása
A jelen útmutató indulunk ki csatolt adatok három lemezt, amely lesz lesz az `/dev/sdc`, `/dev/sdd` és `/dev/sde`. Előfordulhat, hogy ezek nem mindig a azonos útvonalneveket a virtuális gépen. Futtathatja a(z)`sudo fdisk -l`"vagy hasonló paranccsal listát készíthet a rendelkezésre álló lemezek.

1. Készítse elő a fizikai kötetekre:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Hozzon létre egy kötet csoportot. Ebben a példában a kötet csoport hívjuk `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. A logikai kötetek létrehozása. A parancs azt alább hoz létre a következő nevű egyetlen logikai kötet `data-lv01` span a teljes kötet csoport, de vegye figyelembe, hogy azt is megvalósítható a több logikai köteteket a kötet csoport létrehozásához.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. A logikai kötet formázása

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > SLES11 használatával `-t ext3` ext4 helyett. SLES11 csak olvasási hozzáférést ext4 fájlrendszerek támogatja.

## <a name="add-the-new-file-system-to-etcfstab"></a>Az új fájlrendszer /etc/fstab hozzáadása
> [!IMPORTANT]
> Nem megfelelő módosítása a `/etc/fstab` fájl meghiúsulását eredményezheti. Ha nem ismeri, olvassa el a telepítési dokumentációban talál információkat arról a fájl megfelelően szerkesztése. Is javasolt biztonsági másolatot a `/etc/fstab` fájl szerkesztése előtt jön létre.

1. Létrehozhat például az új fájlrendszer, a megfelelő csatlakozási pont:

    ```bash  
    sudo mkdir /data
    ```

2. Keresse meg a logikai elérési útjával

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Nyissa meg `/etc/fstab` egy szövegszerkesztőben, és adja hozzá a bejegyzést az új fájlrendszer, például:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Ezt követően mentse és zárja be `/etc/fstab`.

4. Tesztelhető, hogy a `/etc/fstab` bejegyzés helyességét:

    ```bash    
    sudo mount -a
    ```

    Ha ezt a parancsot egy hibaüzenet ellenőrizze a szintaxist a `/etc/fstab` fájlt.
   
    Ezután futtassa a `mount` parancsot annak biztosítására, hogy a fájlrendszer van csatlakoztatva:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Választható) A FailSafe rendszerindító paraméterek`/etc/fstab`
   
    Terjesztések valamelyikét tartalmazza a `nobootwait` vagy `nofail` csatlakoztatási fel paraméterek a `/etc/fstab` fájlt. Ezek a paraméterek hibák engedélyezése, ha egy adott fájlrendszer csatlakoztatása és a Linux rendszer akkor is, ha az nem csatolható fel a RAID fájlrendszer megfelelően elindulni. További információt ezekről a paraméterekről a terjesztési dokumentációjában tájékozódhat.
   
    (Ubuntu). példa:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>TRIM/UNMAP támogatása
Egyes Linux kernelek támogatja a vágás/UNMAP műveleteket elveti a nem használt blokkok a lemezen. Ezek a műveletek elsősorban hasznosak standard tárolási tájékoztatja Azure törölt lapok már nem érvényesek, és is elvesznek. Lapok elvetése költség mentheti, ha nagy fájlok létrehozása, majd törli őket.

Két módon vágást engedélyezése támogatja a Linux virtuális gép van. A szokásos módon olvassa el a terjesztési az ajánlott módszer:

- Használja a `discard` lehetőség a csatlakoztatási `/etc/fstab`, például:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Bizonyos esetekben a `discard` lehetőség is van a teljesítményre. Alternatív megoldásként futtathatja a `fstrim` manuálisan parancsot a parancssorból, vagy adja hozzá a crontab rendszeresen futtatásához:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL vagy CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
