---
title: Az Azure-ban Debian Linux rendszerű virtuális merevlemez előkészítése |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre a központi telepítés Debian VHD lemezképek az Azure-ban.
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
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: ce2b9811baffea85cfa9a542fb5f93652daf39c8
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976450"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Debian-alapú VHD előkészítése Azure-beli használatra
## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz azt feltételezi, hogy már telepítette a Debian Linux operációs rendszer ISO-fájlból származó letöltött a [Debian webhely](https://www.debian.org/distrib/) egy virtuális merevlemezre. Több eszköz létezik; .vhd-fájlok létrehozása A Hyper-V csak egy példa. Hyper-v-T használó utasításokért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gép](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Telepítési jegyzetek
* Lásd még: [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további tippek Linux előkészítése az Azure-hoz.
* Az újabb VHDX formátum nem támogatott az Azure-ban. A lemez konvertálása Hyper-V kezelője segítségével VHD formátumú, vagy a **convert-vhd** parancsmagot.
* A Linux rendszer telepítésekor LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használata ajánlott. LVM neve nem felel meg a klónozott virtuális gépeket, így elkerülhető, különösen akkor, ha minden eddiginél kell operációsrendszer-lemezt egy másik virtuális Géphez van csatlakoztatva a hibaelhárításhoz. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezekre is használható, ha az előnyben részesített.
* Az operációsrendszer-lemez nem konfigurál egy lapozó partíciót. Az Azure Linux-ügynök beállítható úgy, hogy hozzon létre egy ideiglenes erőforrás lemezen a lapozófájl. További információ található a következő lépéseket.
* Az Azure-ban minden virtuális merevlemezek rendelkeznie kell egy virtuális méret 1 MB igazítva. Nyers lemezről történő konvertáláskor a virtuális merevlemez, ha biztosítania kell, hogy a nyers lemez mérete nagyobb-e az átalakítás előtt 1MB többszöröse. További információkért lásd: [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Használja a Debian virtuális merevlemezek létrehozása az Azure-kezelése
Eszközök is elérhetők az Azure-hoz, a Debian virtuális merevlemezek létrehozásának például a [azure-kezelése](https://github.com/credativ/azure-manage) parancsfájlokat [Credativ](http://www.credativ.com/). Ez a teljesen új lemezkép létrehozása és az ajánlott eljárás. Például hozhat létre egy Debian 8 virtuális merevlemez futtassa a következő parancsok töltse le a `azure-manage` segédprogram (és a függőségek), és futtassa a `azure_build_image` parancsfájlt:

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
2. Kattintson a **Connect** , nyisson meg egy konzolablakot, a virtuális gép.
3. Ha az operációs rendszert egy ISO használatával telepítette, majd tegye megjegyzésbe minden olyan sor vonatkozó "`deb cdrom`" a `/etc/apt/source.list`.

4. Szerkessze a `/etc/default/grub` fájlt, és módosítsa a **GRUB_CMDLINE_LINUX** paramétert a következő további kernel paramétereket tartalmazzák az Azure-hoz.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Építse újra a grub-hibát, és futtassa:

        # sudo update-grub

6. Adja hozzá a Debian Azure tárházak /etc/apt/sources.list Debian 8 és 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Az Azure Linux-ügynök telepítése:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. A Debian 9 + ajánlott az új Debian felhőalapú kernel használata virtuális gépek az Azure-ban való használatra. Az új rendszermag telepítéséhez először létre kell hoznia a következő tartalommal /etc/apt/preferences.d/linux.pref nevű fájlba:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Futtassa "sudo apt-get install linux-lemezkép-felhő-amd64" az új Debian felhőalapú kernel telepítéséhez.

9. A virtuális gép megszüntetése és kiépítése az Azure előkészítése, és futtassa:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Kattintson a **művelet** -> Leállítás le a Hyper-V kezelőjében. A Linux rendszerű VHD-t most már készen áll a tölthető fel az Azure-bA.

## <a name="next-steps"></a>További lépések
Most már készen áll a Debian virtuális merevlemez használatával hozzon létre új virtuális gépek az Azure-ban. Ha ez az első alkalommal, hogy a .vhd fájlt videófájl az Azure-ba, tekintse meg a [egy Linux virtuális gép létrehozása egy egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd).

