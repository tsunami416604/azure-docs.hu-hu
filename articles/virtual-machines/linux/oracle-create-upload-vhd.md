---
title: Az Oracle Linux VHD létrehozása és feltöltése |} A Microsoft Docs
description: Ismerje meg, hozhat létre és töltse fel az Azure virtuális merevlemez (VHD), amely tartalmazza az Oracle Linux operációs rendszert.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: ecd30d30434d91893102ce6ec0df21daa84b677c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276849"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Oracle Linux-alapú virtuális gép előkészítése Azure-beli használatra
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már telepítette az Oracle Linux operációs rendszer virtuális merevlemezre. Több eszköz létezik a .vhd fájlokat, például például a Hyper-V virtualizálási megoldás létrehozása. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gép](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Oracle Linux telepítési jegyzetek
* Tekintse meg a is [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további tippek Linux előkészítése az Azure-hoz.
* Oracle Red Hat-kompatibilis kernel és azok UEK3 (szoros vállalati Kernel) is támogatott Hyper-V és az Azure-on. A legjobb eredmények érdekében kérjük, ne felejtse el frissíteni a legújabb kernel, az Oracle Linux rendszerű virtuális merevlemez előkészítése közben.
* Oracle UEK2 nem támogatott a Hyper-V és az Azure-on, nem tartalmazza a szükséges illesztőprogramokat.
* A VHDX formátum nem támogatott az Azure-ban, csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelőjével vagy a convert-vhd-parancsmag használatával.
* A Linux rendszer telepítésekor LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használata ajánlott. LVM neve nem felel meg a klónozott virtuális gépeket, így elkerülhető, különösen akkor, ha minden eddiginél kell operációsrendszer-lemezt egy másik virtuális Géphez van csatlakoztatva a hibaelhárításhoz. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezekre is használható, ha az előnyben részesített.
* NUMA nagyobb Virtuálisgép-méretek a Linux kernel-verzióknál 2.6.37 alább egy hiba miatt nem támogatott. A probléma elsősorban hatással van a felsőbb rétegbeli használó disztribúciók Red Hat 2.6.32 kernel. Az Azure-beli Linuxos (waagent) ügynök manuális telepítése automatikusan letiltja a NUMA a Linux kernel GRUB konfigurációjában. További információ található a következő lépéseket.
* Az operációsrendszer-lemez nem konfigurál egy lapozó partíciót. A Linux-ügynök beállítható úgy, hogy hozzon létre egy ideiglenes erőforrás lemezen a lapozófájl.  További információ található a következő lépéseket.
* Az Azure-ban minden virtuális merevlemezek rendelkeznie kell egy virtuális méret 1 MB igazítva. A virtuális merevlemez nyers lemezről történő konvertálása során biztosítania kell, hogy a nyers lemez mérete nagyobb-e az átalakítás előtt 1MB többszöröse. Lásd: [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) további információt.
* Győződjön meg arról, hogy a `Addons` adattár engedélyezve van. Szerkessze a fájlt `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) vagy `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7), és módosítsa a sor `enabled=0` való `enabled=1` alatt **[ol6_addons]** vagy **[ol7_addons]** ebben a fájlban.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Az operációs rendszerben a virtuális gép futtatása az Azure-ban a konfigurációs lépéseket kell végrehajtania.

1. A középső ablaktáblán a Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Connect** a virtuális gép ablak megnyitásához.
3. Távolítsa el a NetworkManager a következő parancs futtatásával:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Megjegyzés:** Ha a csomag nem telepítette, ez a parancs egy hiba miatt sikertelen lesz. Ez a várható eredmény.
4. Hozzon létre egy fájlt **hálózati** a a `/etc/sysconfig/` könyvtár, amely tartalmazza a következő szöveget:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Hozzon létre egy fájlt **ifcfg-eth0** a a `/etc/sysconfig/network-scripts/` könyvtár, amely tartalmazza a következő szöveget:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Az Ethernet-adaptert statikus szabályainak elkerülése érdekében, udev szabályok módosítása. Ezek a szabályok problémákat okozhat, ha a Microsoft Azure vagy Hyper-v virtuális gépek klónozásának
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Ellenőrizze, hogy a hálózati szolgáltatás elkezdi rendszerindítás, a következő parancs futtatásával:
   
        # chkconfig network on
8. Telepítse a python-pyasn1 a következő parancs futtatásával:
   
        # sudo yum install python-pyasn1
9. Módosítsa a rendszermag rendszerindítási sorához további kernel paramétereket tartalmazza az Azure-hoz a grub-hibát konfigurációjában. Ehhez a nyílt "/ boot/grub/menu.lst" egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel tartalmazza a következő paraméterekkel:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Ez biztosítja az összes konzol üzenetet küld az első soros port, amely segítheti az Azure támogatási problémák hibakeresésében. Ezzel a lépéssel letiltja NUMA Oracle Red Hat-kompatibilis kernel egy hiba miatt.
   
   A fentiek mellett javasoljuk, hogy *eltávolítása* a következő paraméterekkel:
   
        rhgb quiet crashkernel=auto
   
   Grafikus és a csendes rendszerindító, amelyek nem hasznos, ha egy felhőalapú környezetben, ahol szeretnénk a soros port kell küldeni a naplókat.
   
   A `crashkernel` beállítás lehet bal konfigurálva, ha szükséges, de vegye figyelembe, hogy ez a paraméter csökken a rendelkezésre álló memória a virtuális gépen legalább 128 MB, amely a kisebb Virtuálisgép-méretek problémás lehet.
10. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindítás elindításához.  Ez általában az alapértelmezett érték.
11. Az Azure Linux-ügynök telepítése a következő parancs futtatásával. A legújabb verziója 2.0.15-ös.
    
        # sudo yum install WALinuxAgent
    
    Vegye figyelembe, hogy eltávolítja a WALinuxAgent csomag telepítése a NetworkManager NetworkManager-gnome csomagokat, ha azok nem lettek már eltávolítva leírtak szerint a 2. lépés.
12. Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatják a lapozóterület használata a helyi erőforrás-lemez, amely az Azure-ban üzembe helyezés után a virtuális Géphez van csatlakoztatva. Vegye figyelembe, hogy a helyi erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy ki kell üríteni, ha a virtuális gép. Az Azure Linux-ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a következő paramétereket lévő /etc/waagent.conf:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Futtassa az alábbi parancsokat a virtuális gép megszüntetése és kiépítése az Azure előkészítése:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Kattintson a **művelet le -> Leállítás** a Hyper-V kezelőjében. A Linux rendszerű VHD-t most már készen áll a tölthető fel az Azure-bA.

- - -
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Oracle Linux 7 változásai**

Az Oracle Linux 7 virtuális gép előkészítése Azure-nagyon hasonlít Oracle Linux 6, azonban nincsenek megjegyezni számos fontos különbség:

* A Red Hat-kompatibilis kernel és az Oracle UEK3 támogatottak az Azure-ban.  A UEK3 kernel használata javasolt.
* A NetworkManager csomag már nem ütközik az Azure Linux-ügynök. Ez a csomag alapértelmezés szerint telepítve van, és azt javasoljuk, hogy nem törli.
* GRUB2 most történik, az alapértelmezett rendszerbetöltőt, ezért az eljárás kernel paraméterek szerkesztésre megváltozott (lásd alább).
* XFS már az alapértelmezett fájlrendszer. A ext4 fájlrendszer továbbra is használható, ha szükséges.

**Konfigurációs lépések**

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Connect** , nyisson meg egy konzolablakot, a virtuális gép.
3. Hozzon létre egy fájlt **hálózati** a a `/etc/sysconfig/` könyvtár, amely tartalmazza a következő szöveget:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Hozzon létre egy fájlt **ifcfg-eth0** a a `/etc/sysconfig/network-scripts/` könyvtár, amely tartalmazza a következő szöveget:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Az Ethernet-adaptert statikus szabályainak elkerülése érdekében, udev szabályok módosítása. Ezek a szabályok problémákat okozhat, ha a Microsoft Azure vagy Hyper-v virtuális gépek klónozásának
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Ellenőrizze, hogy a hálózati szolgáltatás elkezdi rendszerindítás, a következő parancs futtatásával:
   
        # sudo chkconfig network on
7. Telepítse a python-pyasn1 csomag a következő parancs futtatásával:
   
        # sudo yum install python-pyasn1
8. Futtassa a következő parancsot az aktuális yum metaadatokat törléséhez, és telepítse a frissítéseket:
   
        # sudo yum clean all
        # sudo yum -y update
9. Módosítsa a rendszermag rendszerindítási sorához további kernel paramétereket tartalmazza az Azure-hoz a grub-hibát konfigurációjában. Ehhez nyissa meg "/ etc/alapértelmezett/grub" egy szövegszerkesztőben, és a Szerkesztés a `GRUB_CMDLINE_LINUX` paramétert, például:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez biztosítja az összes konzol üzenetet küld az első soros port, amely segítheti az Azure támogatási problémák hibakeresésében. Azt is kikapcsolja az új OEL 7 elnevezési konvencióinak hálózati adapterek. A fentiek mellett javasoljuk, hogy *eltávolítása* a következő paraméterekkel:
   
       rhgb quiet crashkernel=auto
   
   Grafikus és a csendes rendszerindító, amelyek nem hasznos, ha egy felhőalapú környezetben, ahol szeretnénk a soros port kell küldeni a naplókat.
   
   A `crashkernel` beállítás lehet bal konfigurálva, ha szükséges, de vegye figyelembe, hogy ez a paraméter csökken a rendelkezésre álló memória a virtuális gépen legalább 128 MB, amely a kisebb Virtuálisgép-méretek problémás lehet.
10. Ha elkészült, szerkesztési "/ etc/alapértelmezett/grub" per felett, a következő parancsot a grub konfigurációs újraépítése:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindítás elindításához.  Ez általában az alapértelmezett érték.
12. Az Azure Linux-ügynök telepítése a következő parancs futtatásával:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatják a lapozóterület használata a helyi erőforrás-lemez, amely az Azure-ban üzembe helyezés után a virtuális Géphez van csatlakoztatva. Vegye figyelembe, hogy a helyi erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy ki kell üríteni, ha a virtuális gép. Az Azure Linux-ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a következő paramétereket lévő /etc/waagent.conf:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Futtassa az alábbi parancsokat a virtuális gép megszüntetése és kiépítése az Azure előkészítése:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Kattintson a **művelet le -> Leállítás** a Hyper-V kezelőjében. A Linux rendszerű VHD-t most már készen áll a tölthető fel az Azure-bA.

## <a name="next-steps"></a>További lépések
Most már készen áll az Oracle Linux .vhd használatával hozzon létre új virtuális gépek az Azure-ban. Ha ez az első alkalommal, hogy a .vhd fájlt videófájl az Azure-ba, tekintse meg a [egy Linux virtuális gép létrehozása egy egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd).

