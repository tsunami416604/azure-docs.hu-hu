---
title: Debian Linux virtuális merevlemez előkészítése
description: Ismerje meg, hogyan hozhat létre Debian vHD-lemezképeket virtuális gépek üzembe helyezéseihez az Azure-ban.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: d54f7a11d929c31fee29a788eb3a2ae2cc8f2703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066718"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Debian virtuális merevlemez előkészítése az Azure-hoz
## <a name="prerequisites"></a>Előfeltételek
Ez a rész feltételezi, hogy már telepítettél egy Debian Linux operációs rendszert egy .iso fájlból, amelyet a [Debian webhelyről](https://www.debian.org/distrib/) töltöttél le egy virtuális merevlemezre. Több eszköz létezik .vhd fájlok létrehozásához; A Hyper-V csak egy példa. A Hyper-V használatával kapcsolatos tudnivalókért olvassa el [a Hyper-V szerepkör telepítése és a Virtuális gép konfigurálása című témakört.](https://technet.microsoft.com/library/hh846766.aspx)

## <a name="installation-notes"></a>Telepítési megjegyzések
* Lásd [még: Általános Linux telepítési megjegyzések](create-upload-generic.md#general-linux-installation-notes) a Linux Azure-ra való előkészítésével kapcsolatos további tippekért.
* Az újabb VHDX formátum ot az Azure nem támogatja. A lemezt A Hyper-V Manager vagy a **convert-vhd** parancsmag segítségével virtuális merevlemez-formátumra konvertálhatja.
* A Linux rendszer telepítésekor ajánlott az LVM helyett szabványos partíciókat használni (ez gyakran sok telepítés alapértelmezett). Ezzel elkerülhető, hogy az LVM-név ütközik a klónozott virtuális gépekkel, különösen akkor, ha hibaelhárítási hibaelhárításhoz egy operációsrendszer-lemezhez kell csatlakoztatni egy másik virtuális géphez. [Az LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [a RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szükség esetén adatlemezeken is használható.
* Ne konfiguráljon lapozópartíciót az operációs rendszer lemezén. Az Azure Linux-ügynök konfigurálható úgy, hogy hozzon létre egy cserefájlt az ideiglenes erőforráslemezen. További információ az alábbi lépésekben található.
* Az Azure-beli virtuális hálózat minden virtuális d-jének 1 MB-hoz igazított virtuális mérettel kell rendelkeznie. Nyers lemezről virtuális merevlemezre való konvertáláskor győződjön meg arról, hogy a nyers lemez mérete a konvertálás előtt az 1 MB többszöröse. További információt a [Linux telepítési megjegyzések című témakörben talál.](create-upload-generic.md#general-linux-installation-notes)

## <a name="use-azure-manage-to-create-debian-vhds"></a>Debian VHD-k létrehozása az Azure-Manage használatával
Vannak olyan eszközök, amelyek debian Virtuálisgépek et hoznak létre az Azure-hoz, például a [Credativ](https://www.credativ.com/) [azure-kezelési](https://github.com/credativ/azure-manage) parancsfájljai. Ez az ajánlott megközelítés, szemben a teljesen új kép létrehozásával. Például egy Debian 8 VHD létrehozásához futtassa a következő parancsokat `azure-manage` `azure_build_image` a segédprogram (és függőségek) letöltéséhez és a parancsfájl futtatásához:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Debian VHD manuális előkészítése
1. A Hyper-V Manager ben válassza ki a virtuális gépet.
2. Kattintson a **Csatlakozás gombra** a virtuális gép konzolablakának megnyitásához.
3. Ha az operációs rendszert ISO-val telepítette, akkor`deb cdrom`a `/etc/apt/source.list`" " in .

4. Módosítsa a `/etc/default/grub` fájlt, és módosítsa a **GRUB_CMDLINE_LINUX** paramétert az alábbiak szerint, hogy az Azure további kernel paramétereit tartalmazza.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Újjáépíteni a grub és fuss:

        # sudo update-grub

6. Add hozzá a Debian Azure-adattárait az /etc/apt/sources.list-hez a Debian 8 vagy 9 esetében:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Nyújtás"**

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

8. A Debian 9+ esetében ajánlott az új Debian Cloud kernel használata az Azure-beli virtuális gépekhez. Az új rendszermag telepítéséhez először hozzon létre egy /etc/apt/preferences.d/linux.pref nevű fájlt a következő tartalommal:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Ezután futtassa a "sudo apt-get install linux-image-cloud-amd64" (sudo apt-get install linux-image-cloud-amd64) című futtassa az új Debian Cloud kernel telepítéséhez.

9. A virtuális gép kiirtása és előkészítése az Azure-beli kiépítésre és futtatásra:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Kattintson **a Művelet** -> Leállítás a Hyper-V Kezelőben parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>További lépések
Most már készen áll arra, hogy a Debian virtuális merevlemezével új virtuális gépeket hozzon létre az Azure-ban. Ha ez az első alkalom, hogy feltölti a .vhd fájlt az Azure-ba, olvassa el a Linux virtuális gép létrehozása egyéni lemezről című [témakört.](upload-vhd.md#option-1-upload-a-vhd)

