---
title: Ubuntu Linux virtuális merevlemez létrehozása és feltöltése az Azure-ban
description: Ismerje meg, hogyan hozhat létre és tölthet fel egy Ubuntu Linux operációs rendszert tartalmazó Virtuális Azure-merevlemezt (VHD).
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: guybo
ms.openlocfilehash: 5fa3415d8663f358bf0ae48be46ac52b8f8b4b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066733"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Ubuntu rendszerű virtuális gép előkészítése az Azure-beli használatra


Az Ubuntu most antól közzéteszi a hivatalos [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)Azure VHD-ket a letöltéshez a. Ha saját speciális Ubuntu-lemezképet kell készítenie az Azure-hoz, ahelyett, hogy az alábbi manuális eljárást használná, javasoljuk, hogy kezdje ezekkel az ismert működő virtuális számítógépekkel, és szükség szerint testre szabja. A legújabb képkiadások mindig a következő helyeken találhatók:

* Ubuntu 12.04/Pontos: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Megbízható: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [bionikus-szerver-cloudimg-amd64.vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már telepített egy Ubuntu Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik .vhd fájlok létrehozásához, például egy virtualizációs megoldás, például a Hyper-V. További információt [a Hyper-V szerepkör telepítése és a Virtuális gép konfigurálása című témakörben talál.](https://technet.microsoft.com/library/hh846766.aspx)

**Ubuntu telepítési megjegyzések**

* A Linux Azure-ra való előkészítésével kapcsolatos további tippekért tekintse meg [az általános Linux-telepítési megjegyzéseket](create-upload-generic.md#general-linux-installation-notes) is.
* A VHDX formátum nem támogatott az Azure-ban, csak **a rögzített virtuális merevlemez**.  A lemezt A Hyper-V Manager vagy a convert-vhd parancsmag segítségével virtuális merevlemez-formátumra konvertálhatja.
* A Linux rendszer telepítésekor ajánlott az LVM helyett szabványos partíciókat használni (ez gyakran sok telepítés alapértelmezett). Ezzel elkerülhető, hogy az LVM-név ütközik a klónozott virtuális gépekkel, különösen akkor, ha hibaelhárítási hibaelhárításhoz egy operációsrendszer-lemezhez kell csatlakoztatni egy másik virtuális géphez. [Az LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [a RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szükség esetén adatlemezeken is használható.
* Ne konfiguráljon lapozópartíciót az operációs rendszer lemezén. A Linux-ügynök beállítható úgy, hogy hozzon létre egy cserefájlt az ideiglenes erőforráslemezen.  Erről további információt az alábbi lépésekben talál.
* Az Azure-beli virtuális hálózat minden virtuális d-jének 1 MB-hoz igazított virtuális mérettel kell rendelkeznie. Nyers lemezről virtuális merevlemezre történő konvertáláskor győződjön meg arról, hogy a nyers lemez mérete a konvertálás előtt az 1 MB többszöröse. További információt a [Linux telepítési megjegyzések című témakörben](create-upload-generic.md#general-linux-installation-notes) talál.

## <a name="manual-steps"></a>Manuális lépések
> [!NOTE]
> Mielőtt megpróbálna saját egyéni Ubuntu-lemezképet létrehozni az Azure-hoz, [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) fontolja meg az előre elkészített és tesztelt lemezképek használatát.
> 
> 

1. A Hyper-V Manager középső ablaktábláján válassza ki a virtuális gépet.

2. Kattintson a **Csatlakozás gombra** a virtuális gép ablakának megnyitásához.

3. Cserélje le a rendszerkép aktuális adattárak az Ubuntu Azure-tárház ának használatához. A lépések kissé eltérnek az Ubuntu verziójától függően.
   
    Szerkesztés `/etc/apt/sources.list`előtt a biztonsági mentés ajánlott:
   
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

4. Az Ubuntu Azure-rendszerképek most követik a *hardveres enablement* (HWE) kernelt. Frissítse az operációs rendszert a legújabb rendszermagra a következő parancsok futtatásával:

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
    
    Ubuntu 18.04.04:
    
        # sudo apt-get update
        # sudo apt-get install --install-recommends linux-generic-hwe-18.04 xserver-xorg-hwe-18.04
        # sudo apt-get install --install-recommends linux-cloud-tools-generic-hwe-18.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    **Lásd még:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Módosítsa a Grub kernelrendszer-rendszerindító sorát, hogy az tartalmazzon további kernelparamétereket az Azure-hoz. Ha ezt `/etc/default/grub` meg szeretné nyitni egy szövegszerkesztőben, keresse meg a nevű változót `GRUB_CMDLINE_LINUX_DEFAULT` (vagy szükség esetén adja hozzá), és végezze el úgy, hogy a következő paramétereket tartalmazza:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Mentse és zárja be a `sudo update-grub`fájlt, majd futtassa a programot. Ez biztosítja, hogy az összes konzolüzenet az első soros portra kerüljön, amely segítséget nyújthat az Azure technikai támogatásának a hibakeresési problémák megoldásában.

6. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el.  Ez általában az alapértelmezett.

7. Az Azure Linux-ügynök telepítése:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  A `walinuxagent` csomag eltávolíthatja `NetworkManager-gnome` a és a `NetworkManager` csomagokat, ha telepítve vannak.


1. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Kattintson **a Művelet -> Leállítás a** Hyper-V Kezelőben parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="references"></a>Referencia
[Ubuntu hardveres enablement (HWE) kernel](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>További lépések
Most már készen áll az Ubuntu Linux virtuális merevlemezének használatára, hogy új virtuális gépeket hozzon létre az Azure-ban. Ha ez az első alkalom, hogy feltölti a .vhd fájlt az Azure-ba, olvassa el a Linux virtuális gép létrehozása egyéni lemezről című [témakört.](upload-vhd.md#option-1-upload-a-vhd)

