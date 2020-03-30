---
title: Oracle Linux vHD létrehozása és feltöltése
description: Ismerje meg, hogyan hozhat létre és tölthet fel egy Oracle Linux operációs rendszert tartalmazó Virtuális Azure-merevlemezt.Learn to create and upload an Azure virtual hard disk (VHD) that contains a Oracle Linux operating system.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: 784d6c01125a9fd6ec291f32e989e4b22e7607af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066579"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Oracle Linux virtuális gép előkészítése az Azure-hoz

Ez a cikk feltételezi, hogy már telepített egy Oracle Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik .vhd fájlok létrehozásához, például egy virtualizációs megoldás, például a Hyper-V. További információt [a Hyper-V szerepkör telepítése és a Virtuális gép konfigurálása című témakörben talál.](https://technet.microsoft.com/library/hh846766.aspx)

## <a name="oracle-linux-installation-notes"></a>Oracle Linux telepítési megjegyzések
* A Linux Azure-ra való előkészítésével kapcsolatos további tippekért tekintse meg [az általános Linux-telepítési megjegyzéseket](create-upload-generic.md#general-linux-installation-notes) is.
* A Hyper-V és az Azure támogatja az Oracle Linuxot a Törhetetlen vállalati kernellel (UEK) vagy a Red Hat-kompatibilis kernellel.
* Az Oracle UEK2 nem támogatott a Hyper-V és az Azure, mivel nem tartalmazza a szükséges illesztőprogramokat.
* A VHDX formátum nem támogatott az Azure-ban, csak **a rögzített virtuális merevlemez**.  A lemezt A Hyper-V Manager vagy a convert-vhd parancsmag segítségével virtuális merevlemez-formátumra konvertálhatja.
* A Linux rendszer telepítésekor ajánlott az LVM helyett szabványos partíciókat használni (ez gyakran sok telepítés alapértelmezett). Ezzel elkerülhető, hogy az LVM-név ütközik a klónozott virtuális gépekkel, különösen akkor, ha hibaelhárítási hibaelhárításhoz egy operációsrendszer-lemezhez kell csatlakoztatni egy másik virtuális géphez. [Az LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [a RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szükség esetén adatlemezeken is használható.
* Linux kernel verziók korábbi, mint 2.6.37 nem támogatja a NUMA a Hyper-V nagyobb virtuális gép mérete. Ez a probléma elsősorban az upstream Red Hat 2.6.32 kernelt használó régebbi disztribúciákat érinti, és az Oracle Linux 6.6-os és újabb verzióiban javították.
* Ne konfiguráljon lapozópartíciót az operációs rendszer lemezén. A Linux-ügynök beállítható úgy, hogy hozzon létre egy cserefájlt az ideiglenes erőforráslemezen.  Erről további információt az alábbi lépésekben talál.
* Az Azure-beli virtuális hálózat minden virtuális d-jének 1 MB-hoz igazított virtuális mérettel kell rendelkeznie. Nyers lemezről virtuális merevlemezre történő konvertáláskor győződjön meg arról, hogy a nyers lemez mérete a konvertálás előtt az 1 MB többszöröse. További információt a [Linux telepítési megjegyzések című témakörben](create-upload-generic.md#general-linux-installation-notes) talál.
* Győződjön meg `Addons` arról, hogy a tárház engedélyezve van. Módosítsa a `/etc/yum.repos.d/public-yum-ol6.repo`fájlt (Oracle Linux `/etc/yum.repos.d/public-yum-ol7.repo`6) vagy (Oracle `enabled=0` Linux `enabled=1` 7), és módosítsa a sort **[ol6_addons]** vagy **[ol7_addons]** alatt ebben a fájlban.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 és újabb
A virtuális gép Azure-beli futtatásához az operációs rendszer meghatározott konfigurációs lépéseit kell végrehajtania.

1. A Hyper-V Manager középső ablaktábláján válassza ki a virtuális gépet.
2. Kattintson a **Csatlakozás gombra** a virtuális gép ablakának megnyitásához.
3. Távolítsa el a NetworkManager programot a következő parancs futtatásával:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Megjegyzés:** Ha a csomag még nincs telepítve, a parancs hibaüzenettel sikertelen lesz. Ez a várható eredmény.
4. Hozzon létre egy `/etc/sysconfig/` nevű **fájlt** a könyvtárban, amely a következő szöveget tartalmazza:
   
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
6. Módosítsa az udev-szabályokat, hogy ne hozzon létre statikus szabályokat az Ethernet-csatoló(k)hoz. Ezek a szabályok problémákat okozhatnak a virtuális gépek Microsoft Azure vagy Hyper-V szolgáltatásban történő klónozása során:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul a következő parancs futtatásával:
   
        # chkconfig network on
8. Telepítse a python-pyasn1-et a következő parancs futtatásával:
   
        # sudo yum install python-pyasn1
9. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/boot/grub/menu.lst" fájlt egy szövegszerkesztőben, és győződjön meg arról, hogy a kernel a következő paramétereket tartalmazza:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Ez biztosítja, hogy az összes konzolüzenet az első soros portra kerüljön, amely segítséget nyújthat az Azure-támogatáshibakeresési problémák megoldásához.
   
   A fentieken kívül ajánlott a következő paraméterek *eltávolítása:*
   
        rhgb quiet crashkernel=auto
   
   Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port.
   
   A `crashkernel` lehetőség lehet hagyni konfigurálva, ha szükséges, de vegye figyelembe, hogy ez a paraméter csökkenti a rendelkezésre álló memória a virtuális gép 128 MB vagy több, ami problémás lehet a kisebb virtuális gép mérete.
10. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el.  Ez általában az alapértelmezett.
11. Telepítse az Azure Linux-ügynök a következő parancs futtatásával. A legutolsó változat van 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Vegye figyelembe, hogy a WALinuxAgent csomag telepítése eltávolítja a NetworkManager és a NetworkManager-gnome csomagokat, ha azokat még nem távolította el a 2.
12. Ne hozzon létre csereterületet az operációs rendszer lemezén.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterület a helyi erőforráslemez, amely csatlakozik a virtuális géphez kiépítése után az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy *ideiglenes* lemez, és lehet, hogy kiüríti, ha a virtuális gép megszüntetése. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) megfelelően módosítsa a következő paramétereket az /etc/waagent.conf fájlban:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Kattintson **a Művelet -> Leállítás a** Hyper-V Kezelőben parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 és újabb verziók
**Változások az Oracle Linux 7-ben**

Az Oracle Linux 7 virtuális gép előkészítése az Azure-hoz nagyon hasonlít az Oracle Linux 6-hoz, de számos fontos különbség van, amelyeket érdemes megjegyezni:

* Az Azure támogatja az Oracle Linuxot a Törhetetlen vállalati kernellel (UEK) vagy a Red Hat-kompatibilis kernellel. Oracle Linux UEK ajánlott.
* A NetworkManager-csomag már nem ütközik az Azure Linux-ügynökkel. Ez a csomag alapértelmezés szerint telepítve van, és azt javasoljuk, hogy ne távolítsuk el.
* A GRUB2 mostantól alapértelmezett rendszertöltőként használatos, így a kernel paramétereinek szerkesztési eljárása megváltozott (lásd alább).
* Az XFS mostantól az alapértelmezett fájlrendszer. Az ext4 fájlrendszer szükség esetén továbbra is használható.

**Konfigurációs lépések**

1. A Hyper-V Manager ben válassza ki a virtuális gépet.
2. Kattintson a **Csatlakozás gombra** a virtuális gép konzolablakának megnyitásához.
3. Hozzon létre egy `/etc/sysconfig/` nevű **fájlt** a könyvtárban, amely a következő szöveget tartalmazza:
   
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
5. Módosítsa az udev-szabályokat, hogy ne hozzon létre statikus szabályokat az Ethernet-csatoló(k)hoz. Ezek a szabályok problémákat okozhatnak a virtuális gépek Microsoft Azure vagy Hyper-V szolgáltatásban történő klónozása során:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul a következő parancs futtatásával:
   
        # sudo chkconfig network on
7. Telepítse a python-pyasn1 csomagot a következő parancs futtatásával:
   
        # sudo yum install python-pyasn1
8. Az aktuális yum metaadatok törléséhez és a frissítések telepítéséhez futtassa a következő parancsot:
   
        # sudo yum clean all
        # sudo yum -y update
9. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. Ehhez nyissa meg az "/etc/default/grub" szövegszerkesztőt, és szerkesztse a `GRUB_CMDLINE_LINUX` paramétert, például:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez azt is biztosítja, hogy az összes konzolüzenet az első soros portra kerüljön, amely segítséget nyújthat az Azure-támogatásnak a hibakeresési problémák megoldásában. Emellett kikapcsolja az Oracle Linux 7 hálózati adapterek elnevezési konvencióit a Törhetetlen enterprise kernelnel. A fentieken kívül ajánlott a következő paraméterek *eltávolítása:*
   
       rhgb quiet crashkernel=auto
   
   Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port.
   
   A `crashkernel` lehetőség lehet hagyni konfigurálva, ha szükséges, de vegye figyelembe, hogy ez a paraméter csökkenti a rendelkezésre álló memória a virtuális gép 128 MB vagy több, ami problémás lehet a kisebb virtuális gép mérete.
10. Miután végzett szerkesztési "/ etc / alapértelmezett / grub" a fenti, futtassa a következő parancsot, hogy újjáépíteni a grub konfiguráció:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el.  Ez általában az alapértelmezett.
12. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Ne hozzon létre csereterületet az operációs rendszer lemezén.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterület a helyi erőforráslemez, amely csatlakozik a virtuális géphez kiépítése után az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy *ideiglenes* lemez, és lehet, hogy kiüríti, ha a virtuális gép megszüntetése. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) megfelelően módosítsa a következő paramétereket az /etc/waagent.conf fájlban:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Kattintson **a Művelet -> Leállítás a** Hyper-V Kezelőben parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>További lépések
Most már készen áll az Oracle Linux .vhd használatával új virtuális gépek létrehozásához az Azure-ban. Ha ez az első alkalom, hogy feltölti a .vhd fájlt az Azure-ba, olvassa el a Linux virtuális gép létrehozása egyéni lemezről című [témakört.](upload-vhd.md#option-1-upload-a-vhd)

