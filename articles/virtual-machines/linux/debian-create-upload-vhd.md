---
title: Debian Linux rendszerű virtuális merevlemez előkészítése
description: Megtudhatja, hogyan hozhat létre Debian VHD-rendszerképeket az Azure-beli virtuális gépek üzembe helyezéséhez.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: 80272896bd314a1f5f05094afa83568e077ab480
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87368200"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Debian VHD előkészítése az Azure-hoz
## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz azt feltételezi, hogy már telepített egy Debian Linux operációs rendszert a [Debian-webhelyről](https://www.debian.org/distrib/) egy virtuális merevlemezre letöltött. ISO fájlból. Több eszköz létezik a. vhd fájlok létrehozásához; A Hyper-V csak egy példa. A Hyper-V-t használó utasításokért lásd: [a Hyper-v szerepkör telepítése és a virtuális gép konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Telepítési megjegyzések
* Lásd még az [általános linuxos telepítési megjegyzések](create-upload-generic.md#general-linux-installation-notes) című témakört, amely további tippeket nyújt az Azure-hoz készült Linux előkészítéséhez.
* Az újabb VHDX formátum nem támogatott az Azure-ban. A lemezt VHD formátumba konvertálhatja a Hyper-V kezelőjével vagy a **Convert-VHD** parancsmag használatával.
* A Linux rendszer telepítésekor azt javasoljuk, hogy az LVM helyett standard partíciót használjon (általában sok telepítés esetén ez az alapértelmezett beállítás). Ezzel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez kell csatolni a hibaelhárításhoz. Az [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy a [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezeken is használható, ha az előnyben részesített.
* Ne állítson be swap-partíciót az operációsrendszer-lemezen. Az Azure Linux-ügynök beállítható úgy, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén. További információt az alábbi lépésekben talál.
* Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor gondoskodnia kell arról, hogy a nyers lemez mérete a konverzió előtt egy 1MB többszöröse legyen. További információ: Linux- [telepítési megjegyzések](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Debian virtuális merevlemezek létrehozása Azure-Manage használatával
Elérhetők az Azure-hoz készült Debian virtuális merevlemezek, például az [Azure-Credativ-parancsfájlok kezeléséhez](https://github.com/credativ/azure-manage) használható eszközök. [Credativ](https://www.credativ.com/) Ez az ajánlott megközelítés, illetve a rendszerkép teljesen új létrehozása. Ha például egy Debian 8 VHD-t szeretne létrehozni, futtassa a következő parancsokat a `azure-manage` segédprogram (és a függőségek) letöltéséhez, és futtassa a `azure_build_image` szkriptet:

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="manually-prepare-a-debian-vhd"></a>Debian virtuális merevlemez manuális előkészítése
1. A Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Kapcsolódás** elemre a virtuális gép konzoljának megnyitásához.
3. Ha ISO-vel telepítette az operációs rendszert, akkor megjegyzésbe helyezi a (z `deb cdrom` ) "" elemhez kapcsolódó összes sort `/etc/apt/source.list` .

4. Szerkessze a `/etc/default/grub` fájlt, és módosítsa a **GRUB_CMDLINE_LINUX** paramétert az alábbiak szerint, hogy további kernel-paramétereket tartalmazzon az Azure-hoz.

    ```config-grub
    GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"
    ```

5. Hozza létre újra a grub-t, és futtassa a következőket:

    ```console
    # sudo update-grub
    ```

6. Adja hozzá a Debian Azure-tárházait a/etc/apt/sources.list-hez vagy a Debian 8 vagy 9 rendszerhez:

    **Debian 8. x "Jessie"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian jessie main
    deb-src http://debian-archive.trafficmanager.net/debian jessie main
    deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
    deb http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb http://debian-archive.trafficmanager.net/debian jessie-backports main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
    ```

    **Debian 9. x "stretch"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian stretch main
    deb-src http://debian-archive.trafficmanager.net/debian stretch main
    deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-backports main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main
    ```


7. Az Azure Linux-ügynök telepítése:

    ```console
    # sudo apt-get update
    # sudo apt-get install waagent
    ```

8. A Debian 9 + esetében ajánlott az új Debian Cloud kernelt használni az Azure-beli virtuális gépekhez. Az új kernel telepítéséhez először hozzon létre egy/etc/apt/Preferences.d/Linux.pref nevű fájlt a következő tartalommal:

    ```config-pref
    Package: linux-* initramfs-tools
    Pin: release n=stretch-backports
    Pin-Priority: 500
    ```

    Ezután futtassa a "sudo apt-get install Linux-rendszerkép-Cloud-amd64" parancsot az új Debian Cloud kernel telepítéséhez.

9. A virtuális gép kiépítése és előkészítése az Azure-ban való üzembe helyezéshez és futtatásához:

    ```console
    # sudo waagent –force -deprovision
    # export HISTSIZE=0
    # logout
    ```

10. Kattintson a **művelet** – > leállítás a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>További lépések
Most már készen áll a Debian-beli virtuális merevlemez használatára az új virtuális gépek létrehozásához az Azure-ban. Ha első alkalommal tölti fel a. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.
