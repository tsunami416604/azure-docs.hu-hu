---
title: Az Azure-ban, egy Ubuntu Linux VHD létrehozása és feltöltése
description: Ismerje meg, hozhat létre és töltse fel az Azure virtuális merevlemez (VHD), amely az Ubuntu Linux operációs rendszert tartalmazza.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
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
ms.openlocfilehash: 62d17670a068304e0764c85d49da0aa9a736c477
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444433"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Ubuntus virtuális gép előkészítése Azure-beli használatra
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Felhő hivatalos Ubuntu-rendszerképek
Ubuntu most tesz közzé a hivatalos Azure VHD letölthető innen [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/). A saját specializált Ubuntu-rendszerkép létrehozásához az Azure-ban van szüksége, ha inkább kövesse az alábbi manuális eljárást, mint ajánlott indítsa el az ilyen virtuális merevlemezek használata ismert, és igény szerint testreszabhatja. A rendszerkép legújabb kiadásaihoz mindig az alábbi helyeken találhatók:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/cosmic/current/cosmic-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már telepítette az Ubuntu Linux operációs rendszer virtuális merevlemezre. Több eszköz létezik a .vhd fájlokat, például például a Hyper-V virtualizálási megoldás létrehozása. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gép](https://technet.microsoft.com/library/hh846766.aspx).

**Ubuntu telepítési jegyzetek**

* Tekintse meg a is [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további tippek Linux előkészítése az Azure-hoz.
* A VHDX formátum nem támogatott az Azure-ban, csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelőjével vagy a convert-vhd-parancsmag használatával.
* A Linux rendszer telepítésekor LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használata ajánlott. LVM neve nem felel meg a klónozott virtuális gépeket, így elkerülhető, különösen akkor, ha minden eddiginél kell operációsrendszer-lemezt egy másik virtuális Géphez van csatlakoztatva a hibaelhárításhoz. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezekre is használható, ha az előnyben részesített.
* Az operációsrendszer-lemez nem konfigurál egy lapozó partíciót. A Linux-ügynök beállítható úgy, hogy hozzon létre egy ideiglenes erőforrás lemezen a lapozófájl.  További információ található a következő lépéseket.
* Az Azure-ban minden virtuális merevlemezek rendelkeznie kell egy virtuális méret 1 MB igazítva. A virtuális merevlemez nyers lemezről történő konvertálása során biztosítania kell, hogy a nyers lemez mérete nagyobb-e az átalakítás előtt 1MB többszöröse. Lásd: [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) további információt.

## <a name="manual-steps"></a>Manuális lépések
> [!NOTE]
> Mielőtt megkísérelné a saját egyéni Ubuntu-rendszerkép létrehozása az Azure-hoz, fontolja meg az előre elkészített és tesztelt rendszerképekből [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/) helyette.
> 
> 

1. A középső ablaktáblán a Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Kattintson a **Connect** a virtuális gép ablak megnyitásához.

3. Cserélje le a jelenlegi adattárait Azure adattárakkal Ubuntu a használni kívánt lemezkép. A lépések kissé Ubuntu verziójától függően eltérőek.
   
    Szerkesztése előtt `/etc/apt/sources.list`, javasoljuk, hogy készítsen biztonsági másolatot:
   
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

4. Az Ubuntu Azure lemezképek Mostantól követi a *hardver engedélyezésre* (HWE) kernel. Az operációs rendszer frissítése a legújabb kernel a következő parancsok futtatásával:

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


5. Módosítsa a rendszermag rendszerindítási sorához a grub-hibát további kernel paramétereket tartalmazza az Azure-hoz. Ehhez a nyílt `/etc/default/grub` egy szövegszerkesztőbe, keresse meg a nevű változó `GRUB_CMDLINE_LINUX_DEFAULT` (vagy adja hozzá, ha szükséges), és módosítsa az alábbi paramétereket tartalmazza:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Mentse és zárja be ezt a fájlt, és futtassa `sudo update-grub`. Ez biztosítja az összes konzol üzenetet küld az első soros porton, is, ezzel segítve a problémák hibakeresése az Azure műszaki támogatást.

6. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindítás elindításához.  Ez általában az alapértelmezett érték.

7. Az Azure Linux-ügynök telepítése:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    A `walinuxagent` csomagot eltávolíthatja a `NetworkManager` és `NetworkManager-gnome` a csomagokat, ha telepítve vannak.

Ubuntu 18.04/18.10, frissítse az Azure-adatforrás, ez szerkesztése: /etc/cloud/cloud.cfg.d/90-azure.cfg, adja hozzá a kódot a fájl végén:

**Fontos: A kód pontosan, ahogy látható, beleértve a szóközöket is hozzá kell adni.**

```bash
datasource:
   Azure:
     agent_command: [service, walinuxagent, start]
```

8. Futtassa az alábbi parancsokat a virtuális gép megszüntetése és kiépítése az Azure előkészítése:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Kattintson a **művelet le -> Leállítás** a Hyper-V kezelőjében. A Linux rendszerű VHD-t most már készen áll a tölthető fel az Azure-bA.

## <a name="references"></a>Referencia
[Ubuntu hardver lehetővé tétele (HWE) kernel](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>További lépések
Most már készen áll az Ubuntu Linux virtuális merevlemez használatával hozzon létre új virtuális gépek az Azure-ban. Ha ez az első alkalommal, hogy a .vhd fájlt videófájl az Azure-ba, tekintse meg a [egy Linux virtuális gép létrehozása egy egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd).

