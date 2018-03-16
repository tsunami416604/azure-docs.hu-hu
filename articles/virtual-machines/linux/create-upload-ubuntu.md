---
title: "Hozzon létre, és az Ubuntu Linux VHD-fájlt feltölti az Azure-ban"
description: "Ismerje meg, létrehozása és feltöltése az Azure virtuális merevlemez (VHD), amely tartalmazza az Ubuntu Linux operációs rendszer."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 4c49cbefafe71646ba08dd049baf50ff04463fdc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Ubuntus virtuális gép előkészítése Azure-beli használatra
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Hivatalos Ubuntu felhő lemezképek
Ubuntu most teszi közzé a következő hivatalos Azure VHD [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/). Ha saját speciális Ubuntu rendszerkép létrehozása az Azure-van szüksége, ahelyett, hogy kövesse az alábbi manuális eljárást, mint ajánlott ezeket a VHD-k használata ismert kezdődnie, és igény szerint testre szabhatja. A kép Újdonságok mindig az alábbi helyeken találhatók:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már telepítette az Ubuntu Linux operációs rendszer virtuális merevlemezre. Több különféle eszköz található .vhd fájlok, például egy például a Hyper-V virtualizálási megoldás létrehozása. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gépek](http://technet.microsoft.com/library/hh846766.aspx).

**Ubuntu telepítési megjegyzések**

* Is lásd: [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további információkat az Azure Linux előkészítése.
* A VHDX formátum nem támogatott az Azure csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelője vagy a convert-vhd-parancsmag segítségével.
* A Linux rendszer telepítésekor LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használata ajánlott. LVM neve ütközik a klónozott virtuális gépek, így elkerülhető, különösen akkor, ha egy operációsrendszer-lemez legalább egyszer meg kell hibaelhárítási egy másik virtuális géphez csatlakoztatható. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Ha előnyben részesített adatlemezek használható.
* Ne konfiguráljon egy swap partíciót az operációsrendszer-lemezképet. A Linux-ügynök beállítható úgy, hogy az ideiglenes erőforrás lemezen a lapozófájl létrehozásához.  További információk a megtalálhatók az alábbi lépéseket.
* Minden, az Azure virtuális merevlemez rendelkeznie kell egy virtuális mérete 1MB igazodik. Virtuális merevlemez egy nyers lemezen történő átalakítása meg kell győződnie arról, hogy a nyers lemez mérete 1MB átalakítás előtti többszöröse. Lásd: [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) további információt.

## <a name="manual-steps"></a>Manuális lépések
> [!NOTE]
> Mielőtt megpróbálná a saját egyéni Ubuntu lemezkép létrehozása az Azure-ba, fontolja meg az előre elkészített és tesztelt lemezképekkel [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/) helyette.
> 
> 

1. A középső ablaktáblán a Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Kattintson a **Connect** a virtuális gép ablak megnyitásához.

3. Cserélje le a jelenlegi tárházak találhatók, az Ubuntu tartozó Azure repók használni kívánt lemezkép. A lépések kissé Ubuntu verziójától függően eltérőek.
   
    Szerkesztése előtt `/etc/apt/sources.list`, ajánlott biztonsági másolatot készíteni:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Az Ubuntu Azure lemezképek Mostantól követi a *hardver engedélyezése* (HWE) kernel. Az operációs rendszer frissítése a legújabb kernel a következő parancsok futtatásával:

    Ubuntu 12.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **Lásd még:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Módosítsa a kernel rendszerindító sort lárvajárat kiegészítő rendszermag paraméterek az Azure-bA felvenni. Ehhez a Megnyitás `/etc/default/grub` egy szövegszerkesztőt, keresse meg a változó neve `GRUB_CMDLINE_LINUX_DEFAULT` (vagy felveheti Ön is szükség esetén), és módosítsa az alábbi paramétereket tartalmazza:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Mentse és zárja be ezt a fájlt, és futtassa `sudo update-grub`. Ezzel biztosíthatja, hogy minden konzol küldi el az első soros port, amely segít az Azure technikai támogatást szeretne, a hibakeresés.

6. Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához.  Ez általában az alapértelmezett beállítás.

7. Az Azure Linux ügynök telepítése:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    A `walinuxagent` távolíthatja el a csomagot a `NetworkManager` és `NetworkManager-gnome` a csomagokat, ha telepítve van.

8. A virtuális gép kiosztásának megszüntetése, és előkészíti az Azure-on történő üzembe helyezéséhez a következő parancsok futtatásával:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Kattintson a **művelet le -> leállítási** a Hyper-V kezelőjében. A Linux virtuális merevlemez az Azure-bA feltölteni kívánt készen áll.

## <a name="next-steps"></a>További lépések
Most már készen áll az Ubuntu Linux virtuális merevlemez segítségével új virtuális gépek létrehozása az Azure-ban. Ha az első alkalommal, hogy van-e a .vhd fájl feltöltése az Azure, lásd: [Linux virtuális gép létrehozása az egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd).

## <a name="references"></a>Referencia
Ubuntu hardver engedélyezése (HWE) kernel:

* [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
* [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)

