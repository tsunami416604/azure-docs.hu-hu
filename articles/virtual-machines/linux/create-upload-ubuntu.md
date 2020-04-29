---
title: Ubuntu Linux VHD létrehozása és feltöltése az Azure-ban
description: Megtudhatja, hogyan hozhat létre és tölthet fel egy Ubuntu Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: guybo
ms.openlocfilehash: 5fa3415d8663f358bf0ae48be46ac52b8f8b4b06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066733"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Ubuntu rendszerű virtuális gép előkészítése az Azure-beli használatra


Az Ubuntu mostantól közzéteszi a hivatalos Azure VHD [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)-ket a letöltéshez. Ha saját, speciális Ubuntu-lemezképet kell létrehoznia az Azure-hoz, és nem az alábbi manuális eljárást szeretné használni, érdemes elindítani ezeket az ismert munkavhd-ket, és szükség szerint testre szabnia azokat. A legújabb rendszerkép-verziók mindig a következő helyeken találhatók:

* Ubuntu 12.04/Precise: [Ubuntu-12,04-Server-cloudimg-amd64-Disk1. vhd. zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/megbízható: [Ubuntu-14,04-Server-cloudimg-amd64-Disk1. vhd. zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenia: [Ubuntu-16,04-Server-cloudimg-amd64-Disk1. VMDK](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-amd64. VMDK](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/kozmikus: [Cosmic-Server-cloudimg-amd64. vhd. zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már telepített egy Ubuntu Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik a. vhd fájlok létrehozásához, például egy virtualizációs megoldáshoz, például a Hyper-V-hez. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és a virtuális gép konfigurálása](https://technet.microsoft.com/library/hh846766.aspx).

**Ubuntu telepítési megjegyzések**

* A Linux for Azure előkészítésével kapcsolatos további tippeket a [Linux általános telepítési megjegyzései](create-upload-generic.md#general-linux-installation-notes) című témakörben talál.
* A VHDX formátuma nem támogatott az Azure-ban, csak a **rögzített VHD**.  A lemezt VHD formátumba konvertálhatja a Hyper-V kezelőjével vagy a convert-VHD parancsmag használatával.
* A Linux rendszer telepítésekor azt javasoljuk, hogy az LVM helyett standard partíciót használjon (ez általában számos telepítés esetében). Ezzel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez kell csatolni a hibaelhárításhoz. Az [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy a [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezeken is használható, ha az előnyben részesített.
* Ne állítson be swap-partíciót az operációsrendszer-lemezen. A Linux-ügynök úgy konfigurálható, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén.  Erről további információt az alábbi lépésekben találhat.
* Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor gondoskodnia kell arról, hogy a nyers lemez mérete a konverzió előtt egy 1MB többszöröse legyen. További információért lásd a [Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>Manuális lépések
> [!NOTE]
> Mielőtt saját Ubuntu-lemezképet hozna létre az Azure-hoz, érdemes [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) inkább az előre elkészített és tesztelt lemezképeket használni.
> 
> 

1. A Hyper-V kezelőjének középső ablaktábláján válassza ki a virtuális gépet.

2. Kattintson a **Kapcsolódás** gombra a virtuális gép ablakának megnyitásához.

3. Cserélje le a rendszerkép aktuális tárházait az Ubuntu Azure-tárházának használatára. A lépések kis mértékben változnak az Ubuntu verziójától függően.
   
    A Szerkesztés `/etc/apt/sources.list`előtt ajánlott biztonsági másolatot készíteni:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Az Ubuntu Azure-lemezképek mostantól a *hardveres engedélyezés* (HWE) kernelt követik. A következő parancsok futtatásával frissítse az operációs rendszert a legújabb kernelre:

    Ubuntu 12,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    Ubuntu-18.04.04:
    
        # sudo apt-get update
        # sudo apt-get install --install-recommends linux-generic-hwe-18.04 xserver-xorg-hwe-18.04
        # sudo apt-get install --install-recommends linux-cloud-tools-generic-hwe-18.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    **Lásd még:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Módosítsa a grub kernel-rendszerindítási sorát, hogy további kernel-paramétereket tartalmazzon az Azure-hoz. Ehhez nyissa meg `/etc/default/grub` egy szövegszerkesztőben, keresse meg a nevű `GRUB_CMDLINE_LINUX_DEFAULT` változót (vagy adja hozzá, ha szükséges), és szerkessze úgy, hogy tartalmazza a következő paramétereket:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Mentse és zárda be ezt a fájlt, `sudo update-grub`majd futtassa a parancsot. Ezzel biztosítható, hogy az összes konzol üzenetei az első soros porton legyenek elküldve, amely segítséget nyújt az Azure technikai támogatásához hibakeresési problémák esetén.

6. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.

7. Az Azure Linux-ügynök telepítése:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  Előfordulhat `walinuxagent` , hogy a csomag `NetworkManager` eltávolítja `NetworkManager-gnome` a és a csomagokat, ha azok telepítve vannak.


1. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="references"></a>Referencia
[Ubuntu hardveres engedélyezés (HWE) kernel](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>További lépések
Most már készen áll a Ubuntu Linux virtuális merevlemez használatára az Azure-beli új virtuális gépek létrehozásához. Ha első alkalommal tölti fel a. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.

