---
title: Oracle Linux VHD létrehozása és feltöltése
description: Megtudhatja, hogyan hozhat létre és tölthet fel egy Oracle Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 16f3bc9e70f8fac6ab28318e1654742a2c3b76a1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035373"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Oracle Linux-alapú virtuális gép előkészítése Azure-beli használatra
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már telepített egy Oracle Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik a. vhd fájlok létrehozásához, például egy virtualizációs megoldáshoz, például a Hyper-V-hez. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és a virtuális gép konfigurálása](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Oracle Linux telepítési megjegyzések
* A Linux for Azure előkészítésével kapcsolatos további tippeket a [Linux általános telepítési megjegyzései](create-upload-generic.md#general-linux-installation-notes) című témakörben talál.
* A Hyper-V és az Azure egyaránt támogatja az Oracle Red hat-kompatibilis kernelét és a UEK3 (nem törhető vállalati kernel). A legjobb eredmények érdekében frissítsen a legújabb kernelre a Oracle Linux VHD előkészítése során.
* Az Oracle UEK2 nem támogatott a Hyper-V-ben és az Azure-ban, mert nem tartalmazza a szükséges illesztőprogramokat.
* A VHDX formátuma nem támogatott az Azure-ban, csak a **rögzített VHD**.  A lemezt VHD formátumba konvertálhatja a Hyper-V kezelőjével vagy a convert-VHD parancsmag használatával.
* A Linux rendszer telepítésekor azt javasoljuk, hogy az LVM helyett standard partíciót használjon (ez általában számos telepítés esetében). Ezzel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez kell csatolni a hibaelhárításhoz. Az [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy a [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezeken is használható, ha az előnyben részesített.
* A NUMA nem támogatott nagyobb méretű virtuálisgép-méretekhez, mert a 2.6.37 alatti Linux kernel-verziókban hiba fordul elő. Ez a probléma elsősorban a felsőbb rétegbeli Red Hat 2.6.32 kernelt használó disztribúciókat befolyásolja. Az Azure Linux-ügynök (waagent) manuális telepítése automatikusan letiltja a NUMA-t a Linux kernel GRUB-konfigurációjában. Erről további információt az alábbi lépésekben találhat.
* Ne állítson be swap-partíciót az operációsrendszer-lemezen. A Linux-ügynök úgy konfigurálható, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén.  Erről további információt az alábbi lépésekben találhat.
* Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor gondoskodnia kell arról, hogy a nyers lemez mérete a konverzió előtt egy 1MB többszöröse legyen. További információért lásd a [Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes) .
* Győződjön meg arról, hogy a `Addons` adattár engedélyezve van. Szerkessze a fájlt `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) vagy `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7), és módosítsa a sort `enabled=0` a következőre: **[`enabled=1`]** vagy **[ol6_addons]** .

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Az Azure-ban való futtatáshoz a virtuális gép operációs rendszerében meghatározott konfigurációs lépéseket kell végrehajtania.

1. A Hyper-V kezelőjének középső ablaktábláján válassza ki a virtuális gépet.
2. Kattintson a **Kapcsolódás** gombra a virtuális gép ablakának megnyitásához.
3. A következő parancs futtatásával távolítsa el a hálózatkezelő:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Megjegyzés:** Ha a csomag még nincs telepítve, akkor a parancs hibaüzenettel meghiúsul. Ez a várható eredmény.
4. Hozzon létre egy **hálózat** nevű fájlt a `/etc/sysconfig/` könyvtárban, amely a következő szöveget tartalmazza:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Hozzon létre egy **ifcfg-eth0** nevű fájlt a `/etc/sysconfig/network-scripts/` könyvtárban, amely a következő szöveget tartalmazza:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Módosítsa a udev-szabályokat úgy, hogy ne generáljon statikus szabályokat az Ethernet-adapter (ek) számára. Ezek a szabályok problémákat okozhatnak a Microsoft Azure vagy Hyper-V-ben lévő virtuális gépek klónozásakor:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. A következő parancs futtatásával gondoskodjon arról, hogy a hálózati szolgáltatás a rendszerindítás során induljon el:
   
        # chkconfig network on
8. Telepítse a Python-pyasn1 a következő parancs futtatásával:
   
        # sudo yum install python-pyasn1
9. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/boot/grub/menu.lst" szöveget egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely segítséget nyújt az Azure támogatásához hibakeresési problémák esetén. Ezzel letiltja a NUMA-t az Oracle Red hat-kompatibilis kernel hibája miatt.
   
   A fentiek mellett ajánlott *eltávolítani* a következő paramétereket:
   
        rhgb quiet crashkernel=auto
   
   A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra.
   
   Előfordulhat, hogy a `crashkernel` beállítás meg van adva, ha szükséges, de vegye figyelembe, hogy ez a paraméter a virtuális gépen rendelkezésre álló memória mennyiségét legfeljebb 128 MB-kal csökkenti, ami problémát okozhat a kisebb virtuálisgép-méretekben.
10. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.
11. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával. A legújabb verzió a 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Vegye figyelembe, hogy a WALinuxAgent csomag telepítése eltávolítja a hálózatkezelő és a hálózatkezelő-GNOME csomagokat, ha azok még nem lettek eltávolítva a 2. lépésben leírtak szerint.
12. Ne hozzon létre lapozófájlt az operációsrendszer-lemezen.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez az Azure-ban való üzembe helyezést követően csatlakozó helyi erőforrás lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy *ideiglenes* lemez, és a virtuális gép kiépítésekor kiürítésre kerülhet. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) a/etc/waagent.conf megfelelően módosítsa a következő paramétereket:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

---
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Változások a Oracle Linux 7**

Egy Oracle Linux 7 virtuális gép Azure-hoz való előkészítése nagyon hasonlít a Oracle Linux 6-hoz, azonban számos fontos eltérést érdemes megjegyezni:

* A Red hat-kompatibilis kernel és az Oracle UEK3 is támogatottak az Azure-ban.  A UEK3 kernel használata javasolt.
* A hálózatkezelő csomag már nem ütközik az Azure Linux-ügynökkel. Ez a csomag alapértelmezés szerint telepítve van, ezért azt javasoljuk, hogy ne távolítsa el.
* A GRUB2 mostantól alapértelmezett rendszerbetöltőként van használatban, így a kernel-paraméterek szerkesztésének eljárása módosult (lásd alább).
* A XFS mostantól az alapértelmezett fájlrendszer. Az ext4 fájlrendszer továbbra is használható, ha szükséges.

**Konfigurációs lépések**

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Kapcsolódás** elemre a virtuális gép konzoljának megnyitásához.
3. Hozzon létre egy **hálózat** nevű fájlt a `/etc/sysconfig/` könyvtárban, amely a következő szöveget tartalmazza:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Hozzon létre egy **ifcfg-eth0** nevű fájlt a `/etc/sysconfig/network-scripts/` könyvtárban, amely a következő szöveget tartalmazza:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Módosítsa a udev-szabályokat úgy, hogy ne generáljon statikus szabályokat az Ethernet-adapter (ek) számára. Ezek a szabályok problémákat okozhatnak a Microsoft Azure vagy Hyper-V-ben lévő virtuális gépek klónozásakor:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. A következő parancs futtatásával gondoskodjon arról, hogy a hálózati szolgáltatás a rendszerindítás során induljon el:
   
        # sudo chkconfig network on
7. Telepítse a Python-pyasn1 csomagot a következő parancs futtatásával:
   
        # sudo yum install python-pyasn1
8. Futtassa a következő parancsot az aktuális yum-metaadatok törléséhez és a frissítések telepítéséhez:
   
        # sudo yum clean all
        # sudo yum -y update
9. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/etc/default/grub" kifejezést egy szövegszerkesztőben, és szerkessze a `GRUB_CMDLINE_LINUX` paramétert, például:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely segítséget nyújt az Azure támogatásához hibakeresési problémák esetén. Emellett kikapcsolja a hálózati adapterek új OEL 7 elnevezési konvencióit is. A fentiek mellett ajánlott *eltávolítani* a következő paramétereket:
   
       rhgb quiet crashkernel=auto
   
   A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra.
   
   Előfordulhat, hogy a `crashkernel` beállítás meg van adva, ha szükséges, de vegye figyelembe, hogy ez a paraméter a virtuális gépen rendelkezésre álló memória mennyiségét legfeljebb 128 MB-kal csökkenti, ami problémát okozhat a kisebb virtuálisgép-méretekben.
10. Miután befejezte a "/etc/default/grub" szerkesztését, futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.
12. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Ne hozzon létre lapozófájlt az operációsrendszer-lemezen.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez az Azure-ban való üzembe helyezést követően csatlakozó helyi erőforrás lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy *ideiglenes* lemez, és a virtuális gép kiépítésekor kiürítésre kerülhet. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) megfelelően módosítsa a következő paramétereket a/etc/waagent.conf megfelelő módon:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>Következő lépések
Most már készen áll a Oracle Linux. vhd fájl használatára az Azure-beli új virtuális gépek létrehozásához. Ha első alkalommal tölti fel a. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.

