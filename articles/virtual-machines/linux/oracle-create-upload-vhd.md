---
title: "Hozzon létre, és az Oracle Linux virtuális merevlemez feltöltéséhez |} Microsoft Docs"
description: "Ismerje meg, létrehozása és feltöltése az Azure virtuális merevlemez (VHD), amely tartalmazza az Oracle Linux operációs rendszer."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: szark
ms.openlocfilehash: 90df1546ddc70667f6c977afba8078b6dfb0e148
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Oracle Linux-alapú virtuális gép előkészítése Azure-beli használatra
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már telepítette az Oracle Linux operációs rendszer virtuális merevlemezre. Több különféle eszköz található .vhd fájlok, például egy például a Hyper-V virtualizálási megoldás létrehozása. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gépek](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Oracle Linux telepítési megjegyzések
* Is lásd: [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további információkat az Azure Linux előkészítése.
* Red Hat kompatibilis kernel Oracle és azok UEK3 (szoros vállalati Kernel) is támogatott a Hyper-V és az Azure. A legjobb eredmények elérése érdekében adjon ne felejtse el frissíteni a legújabb kernel az Oracle Linux virtuális merevlemez előkészítése során.
* Oracle UEK2 nem támogatott a Hyper-V és az Azure, nem tartalmazza a szükséges illesztőprogramok.
* A VHDX formátum nem támogatott az Azure csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelője vagy a convert-vhd-parancsmag segítségével.
* A Linux rendszer telepítésekor LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használata ajánlott. LVM neve ütközik a klónozott virtuális gépek, így elkerülhető, különösen akkor, ha egy operációsrendszer-lemez legalább egyszer meg kell hibaelhárítási egy másik virtuális géphez csatlakoztatható. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Ha előnyben részesített adatlemezek használható.
* NUMA nagyobb Virtuálisgép-méretek Linux kernel verziójánál régebbi 2.6.37 hiba miatt nem támogatott. A probléma főként hatással van a azokat a terjesztéseket, használja a felsőbb rétegbeli Red Hat 2.6.32 kernel. Az Azure Linux ügynök (waagent) kézi telepítése automatikusan letiltja a NUMA a Linux kernel LÁRVAJÁRAT konfigurációjában. További információk a megtalálhatók az alábbi lépéseket.
* Ne konfiguráljon egy swap partíciót az operációsrendszer-lemezképet. A Linux-ügynök beállítható úgy, hogy az ideiglenes erőforrás lemezen a lapozófájl létrehozásához.  További információk a megtalálhatók az alábbi lépéseket.
* Összes, a virtuális merevlemezeket kell rendelkeznie, amely többszörösei 1 MB méretű.
* Győződjön meg arról, hogy a `Addons` tárház engedélyezve van. A fájl szerkesztése `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) vagy `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux), és módosítsa a sor `enabled=0` való `enabled=1` alatt **[ol6_addons]** vagy **[ol7_addons]** ebben a fájlban.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Adott konfigurációs lépések az operációs rendszerben a virtuális gép az Azure-ban futtatásához szükségesek.

1. A középső ablaktáblán a Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Connect** a virtuális gép ablak megnyitásához.
3. Távolítsa el a NetworkManager a következő parancs futtatásával:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Megjegyzés:** a csomag nincs telepítve, ha ez a parancs a hibaüzenettel meghiúsul. Ez várható.
4. Hozzon létre egy fájlt **hálózati** a a `/etc/sysconfig/` címtárhoz, amely tartalmazza a következő szöveget:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Hozzon létre egy fájlt **ifcfg-eth0** a a `/etc/sysconfig/network-scripts/` címtárhoz, amely tartalmazza a következő szöveget:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Az Ethernet-adaptert statikus szabályainak elkerülése érdekében udev szabályok módosítása. Ezek a szabályok problémákat okozhat, ha a Microsoft Azure- vagy Hyper-v virtuális gép klónozása
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Győződjön meg arról, a hálózati szolgáltatás elkezdi rendszerindítás, a következő parancs futtatásával:
   
        # chkconfig network on
8. Python-pyasn1 telepítse a következő parancs futtatásával:
   
        # sudo yum install python-pyasn1
9. Módosítsa a kernel rendszerindító sor lárvajárat konfigurációs kiegészítő rendszermag paraméterek az Azure-bA felvenni. Ehhez a Megnyitás "/ boot/grub/menu.lst" egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel az alábbi paramétereket tartalmazza:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Ez biztosítja az összes konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására. Ezzel a lépéssel letiltja NUMA Oracle Red Hat kompatibilis kernel programhiba okozza.
   
   A fentiek mellett javasoljuk, hogy *eltávolítása* a következő paraméterekkel:
   
        rhgb quiet crashkernel=auto
   
   Grafikus és a csendes rendszerindító egy felhőalapú környezetben, ahol azt szeretnénk, hogy minden a naplókat a soros port küldendő nem hasznosak.
   
   A `crashkernel` beállítás lehet, hogy szükség esetén konfigurálva balra, de vegye figyelembe, hogy ez a paraméter csökkenti a virtuális gépen legalább 128 MB, rendelkezésre álló memória mennyisége kisebb Virtuálisgép-méretek a problematikus esetleg.
10. Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához.  Ez általában az alapértelmezett beállítás.
11. Az Azure Linux ügynök telepítése a következő parancs futtatásával. A legújabb verzió: 2.0.15-ös.
    
        # sudo yum install WALinuxAgent
    
    Ne feledje, hogy a WALinuxAgent csomag telepítése, azzal eltávolítja a NetworkManager NetworkManager-gnome csomagok Ha nem már eltávolította azokat lásd a 2. lépés.
12. Ne hozzon létre az operációsrendszer-lemezképet a lapozóterület.
    
    Az Azure Linux ügynök automatikusan konfigurálhatók a lapozóterület használata a helyi erőforrás lemezt a virtuális Géphez csatolt Azure kiépítése után. Vegye figyelembe, hogy a helyi erőforrás lemez egy *ideiglenes* lemezre, és előfordulhat, hogy szerepelnek, ha a virtuális gép van platformelőfizetés. Az Azure Linux ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a /etc/waagent.conf a következő paraméterekkel:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. A virtuális gép kiosztásának megszüntetése, és előkészíti az Azure-on történő üzembe helyezéséhez a következő parancsok futtatásával:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Kattintson a **művelet le -> leállítási** a Hyper-V kezelőjében. A Linux virtuális merevlemez az Azure-bA feltölteni kívánt készen áll.

- - -
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Oracle Linux 7 változásai**

Az Oracle Linux 7 virtuális gép előkészítése az Azure-nagyon hasonlít Oracle Linux 6, azonban nincsenek érdemes megjegyezni számos fontos különbség:

* A Red Hat kompatibilis kernel és Oracle UEK3 is támogatottak az Azure-ban.  A UEK3 kernel ajánlott.
* A NetworkManager csomag már nem ütközik az Azure Linux ügynök. Alapértelmezés szerint ez a csomag telepítve van, és azt javasoljuk, hogy nem törli.
* GRUB2 most lesz az alapértelmezett rendszertöltő, az eljárás kernel paraméterek szerkesztésre megváltozott (lásd alább).
* XFS mostantól az alapértelmezett fájlrendszert. A ext4 fájlrendszer továbbra is használható, ha szükséges.

**Konfigurációs lépések**

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Connect** nyissa meg a konzol ablakot a virtuális gép számára.
3. Hozzon létre egy fájlt **hálózati** a a `/etc/sysconfig/` címtárhoz, amely tartalmazza a következő szöveget:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Hozzon létre egy fájlt **ifcfg-eth0** a a `/etc/sysconfig/network-scripts/` címtárhoz, amely tartalmazza a következő szöveget:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Az Ethernet-adaptert statikus szabályainak elkerülése érdekében udev szabályok módosítása. Ezek a szabályok problémákat okozhat, ha a Microsoft Azure- vagy Hyper-v virtuális gép klónozása
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Győződjön meg arról, a hálózati szolgáltatás elkezdi rendszerindítás, a következő parancs futtatásával:
   
        # sudo chkconfig network on
7. A python-pyasn1 telepítéséhez a következő parancs futtatásával:
   
        # sudo yum install python-pyasn1
8. Futtassa a következő parancsot, törölje az aktuális yum metaadatokat, és telepítse a frissítéseket:
   
        # sudo yum clean all
        # sudo yum -y update
9. Módosítsa a kernel rendszerindító sor lárvajárat konfigurációs kiegészítő rendszermag paraméterek az Azure-bA felvenni. Ehhez nyissa meg "/ etc/alapértelmezett/lárvajárat" egy szövegszerkesztőben, és a Szerkesztés a `GRUB_CMDLINE_LINUX` paraméter, például:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez biztosítja az összes konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására. Azt is kikapcsolja új OEL 7 elnevezési szabályai a hálózati adapterek. A fentiek mellett javasoljuk, hogy *eltávolítása* a következő paraméterekkel:
   
       rhgb quiet crashkernel=auto
   
   Grafikus és a csendes rendszerindító egy felhőalapú környezetben, ahol azt szeretnénk, hogy minden a naplókat a soros port küldendő nem hasznosak.
   
   A `crashkernel` beállítás lehet, hogy szükség esetén konfigurálva balra, de vegye figyelembe, hogy ez a paraméter csökkenti a virtuális gépen legalább 128 MB, rendelkezésre álló memória mennyisége kisebb Virtuálisgép-méretek a problematikus esetleg.
10. Miután a szerkesztési "/ etc/alapértelmezett/lárvajárat" / felett, a következő parancsot, hogy a lárvajárat konfiguráció:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához.  Ez általában az alapértelmezett beállítás.
12. Az Azure Linux ügynök telepítése a következő parancs futtatásával:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Ne hozzon létre az operációsrendszer-lemezképet a lapozóterület.
    
    Az Azure Linux ügynök automatikusan konfigurálhatók a lapozóterület használata a helyi erőforrás lemezt a virtuális Géphez csatolt Azure kiépítése után. Vegye figyelembe, hogy a helyi erőforrás lemez egy *ideiglenes* lemezre, és előfordulhat, hogy szerepelnek, ha a virtuális gép van platformelőfizetés. Az Azure Linux ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a /etc/waagent.conf a következő paraméterekkel:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. A virtuális gép kiosztásának megszüntetése, és előkészíti az Azure-on történő üzembe helyezéséhez a következő parancsok futtatásával:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Kattintson a **művelet le -> leállítási** a Hyper-V kezelőjében. A Linux virtuális merevlemez az Azure-bA feltölteni kívánt készen áll.

## <a name="next-steps"></a>További lépések
Most már készen áll az Oracle Linux .vhd használandó új virtuális gépek létrehozása az Azure-ban. Ha az első alkalommal, hogy van-e a .vhd fájl feltöltése az Azure, tekintse meg a 2. és 3 [létrehozása és feltöltése, a Linux operációs rendszert tartalmazó virtuális merevlemez](classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

