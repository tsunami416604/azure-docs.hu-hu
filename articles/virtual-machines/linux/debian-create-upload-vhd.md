---
title: Az Azure-ban Debian Linux virtuális merevlemez előkészítése |} Microsoft Docs
description: Útmutató a Debian 7 & központi telepítési 8 VHD-fájlok létrehozása az Azure-ban.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: f8e98ae823d03dae475efca48a4ce32f27317882
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Debian-alapú VHD előkészítése Azure-beli használatra
## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz azt feltételezi, hogy már telepítette a Debian Linux operációs rendszer ISO-fájlból le: a [Debian webhely](https://www.debian.org/distrib/) egy virtuális merevlemezre. Több különféle eszköz található .vhd fájlok; létrehozása Hyper-V csak egy példaként szolgál. A Hyper-v-vel utasításokért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gépek](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>A telepítéssel kapcsolatos megjegyzések
* Is lásd: [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további információkat az Azure Linux előkészítése.
* Az újabb VHDX formátum nem támogatott az Azure-ban. A lemez VHD formátumú Hyper-V kezelőjével konvertálhatja vagy a **convert-vhd** parancsmag.
* A Linux rendszer telepítésekor LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használata ajánlott. LVM neve ütközik a klónozott virtuális gépek, így elkerülhető, különösen akkor, ha egy operációsrendszer-lemez legalább egyszer meg kell hibaelhárítási egy másik virtuális géphez csatlakoztatható. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Ha előnyben részesített adatlemezek használható.
* Ne konfiguráljon egy swap partíciót az operációsrendszer-lemezképet. Az Azure Linux ügynök beállítható úgy, hogy az ideiglenes erőforrás lemezen a lapozófájl létrehozásához. További információk a megtalálhatók az alábbi lépéseket.
* Minden, az Azure virtuális merevlemez rendelkeznie kell egy virtuális mérete 1MB igazodik. Virtuális merevlemez egy nyers lemezen történő átalakítása meg kell győződnie arról, hogy a nyers lemez mérete 1MB átalakítás előtti többszöröse. Lásd: [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) további információt.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Debian VHD-k létrehozásához használja a Azure-kezelése
Eszközök is elérhetők generálásához. az Azure-ba, Debian virtuális merevlemezeket, mint a [azure-kezelése](https://github.com/credativ/azure-manage) parancsfájl [credativ](http://www.credativ.com/). Ez az az ajánlott módszer, és teljesen új lemezkép létrehozása. Például létrehozásához futtassa az alábbi parancsokat letöltése Debian 8 virtuális merevlemez azure-kezelése (és függőségeinek) és a azure_build_image parancsprogrammal:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Manuálisan a Debian virtuális merevlemez előkészítése
1. A Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Connect** nyissa meg a konzol ablakot a virtuális gép számára.
3. A vonal megjegyzésbe **deb cdrom** a `/etc/apt/source.list` Ha úgy konfigurálja a virtuális gép ISO-fájl alapján.
4. Szerkessze a `/etc/default/grub` fájlt, és módosítsa a **GRUB_CMDLINE_LINUX** paraméter a következő kiegészítő rendszermag paramétereket tartalmazza az Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. Építse újra a lárvajárat, majd futtassa:
   
        # sudo update-grub
6. Vegye fel a Debian tartozó Azure adattárak /etc/apt/sources.list Debian 7 vagy 8:
   
    **Debian 7.x "Wheezy"**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Az Azure Linux ügynök telepítése:
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. Debian 7 a wheezy backports tárházból 3.16-alapú kernel futtatásához szükség. Először létre kell hoznia a következő tartalommal /etc/apt/preferences.d/linux.pref nevű fájlba:
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    Ezután futtassa "sudo apt get telepítése linux-lemezkép-amd64" az új kernel telepítéséhez.
3. A virtuális gép kiosztásának megszüntetése és előkészíti az Azure-on történő üzembe helyezéséhez, és futtassa:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. Kattintson a **művelet** -> leállítási le a Hyper-V kezelőjében. A Linux virtuális merevlemez az Azure-bA feltölteni kívánt készen áll.

## <a name="next-steps"></a>További lépések
Most már készen áll a Debian virtuális merevlemez segítségével új virtuális gépek létrehozása az Azure-ban. Ha az első alkalommal, hogy van-e a .vhd fájl feltöltése az Azure, lásd: [Linux virtuális gép létrehozása az egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd).

