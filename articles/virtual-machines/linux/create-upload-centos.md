---
title: "Hozzon létre, és a CentOS-alapú Linux VHD-fájlt feltölti az Azure-ban"
description: "Ismerje meg, létrehozása és feltöltése az Azure virtuális merevlemez (VHD), amely tartalmazza a CentOS-alapú Linux operációs rendszert."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: f649067590dc990c962aa0c9df8c76080fc2a0b8
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>CentOS-alapú virtuális gép előkészítése Azure-beli használatra
* [Az Azure-bA a CentOS 6.x virtuális gép előkészítése](#centos-6x)
* [Az Azure-bA a CentOS 7.0 + virtuális gép előkészítése](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy a CentOS már telepítve van (vagy hasonló származtatott) Linux operációs rendszer virtuális merevlemezre. Több különféle eszköz található .vhd fájlok, például egy például a Hyper-V virtualizálási megoldás létrehozása. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gépek](http://technet.microsoft.com/library/hh846766.aspx).

**Telepítési jegyzetek centOS**

* Is lásd: [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további információkat az Azure Linux előkészítése.
* A VHDX formátum nem támogatott az Azure csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelője vagy a convert-vhd-parancsmag segítségével. VirtualBox rendszer használata esetén ez azt jelenti, hogy kiválasztásával **mérete rögzített** az alapértelmezett, a lemez létrehozásakor dinamikusan kiosztott szemben.
* Ha telepíti a Linux rendszer *ajánlott* LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használja. LVM neve ütközik a klónozott virtuális gépek, így elkerülhető, különösen akkor, ha egy operációsrendszer-lemez legalább egyszer kell hibaelhárítási egy másik, azonos virtuális géphez csatlakoztatható. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezek használható.
* Kernel támogatása UDF fájlrendszerek csatlakoztatására szükség. Azure első rendszerindításkor az üzembe helyezési konfiguráció lett átadva a Linux virtuális gép keresztül UDF formátumú adathordozót, amely csatolva van a Vendég. Az Azure Linux ügynök a UDF fájlrendszerben beolvasni a konfigurációt, és helyezze üzembe a virtuális gép csatlakoztatása képesnek kell lennie.
* Linux kernel verziójánál régebbi 2.6.37 nem támogatott a Hyper-V nagyobb Virtuálisgép-méretek a. A probléma főként hatások régebbi azokat a terjesztéseket használatával a felsőbb rétegbeli Red Hat 2.6.32 kernel és javítását a RHEL 6.6 (kernel-2.6.32-504). Rendszerekre egyéni kernelek régebbi, mint 2.6.37 vagy régebbi RHEL alapú kernelek 2.6.32-504 a rendszerindító paramétert kell beállítani, mint `numa=off` a parancssori grub.conf a kernel. További információ: a Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Ne konfiguráljon egy swap partíciót az operációsrendszer-lemezképet. A Linux-ügynök beállítható úgy, hogy az ideiglenes erőforrás lemezen a lapozófájl létrehozásához.  További információk a megtalálhatók az alábbi lépéseket.
* Összes, a virtuális merevlemezeket kell rendelkeznie, amely többszörösei 1 MB méretű.

## <a name="centos-6x"></a>CentOS 6.x

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Kattintson a **Connect** nyissa meg a konzol ablakot a virtuális gép számára.

3. A CentOS 6 NetworkManager megzavarhatja az Azure Linux ügynök. Távolítsa el a csomagot a következő parancs futtatásával:
   
        # sudo rpm -e --nodeps NetworkManager

4. Hozzon létre vagy szerkessze a fájlt `/etc/sysconfig/network` , és adja hozzá a következő szöveget:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Hozzon létre vagy szerkessze a fájlt `/etc/sysconfig/network-scripts/ifcfg-eth0` , és adja hozzá a következő szöveget:
   
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
   
        # sudo chkconfig network on

8. Ha szeretné használni a OpenLogic tükör, amely az Azure adatközpontjaiban központja üzemelteti, majd cserélje le a `/etc/yum.repos.d/CentOS-Base.repo` fájlt a következő tárházak találhatók.  A művelet továbbá felvesz a **[openlogic]** tárház, például az Azure Linux ügynök további csomagokat tartalmazza:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0
        
        [base]
        name=CentOS-$releasever - Base
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
        
        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
        
        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
        
        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    >[!Note]
    Ez az útmutató többi fogja feltételezni, hogy azok be számára legalább a `[openlogic]` tárház, az alábbi Azure Linux ügynök telepítéséhez használandó.


9. Adja hozzá az alábbi /etc/yum.conf:
    
        http_caching=packages

10. Törölje az aktuális yum metaadatokat, és frissítse a legújabb csomagokat a rendszer a következő parancsot futtassa:
    
        # yum clean all

    Kivéve, ha a régebbi verziójú CentOS egy kép létrehozásakor, javasoljuk, hogy a csomagok frissítése a legújabb verzióra:

        # sudo yum -y update

    A parancs futtatása után a rendszer újraindítása lehet szükség.

11. (Választható) Telepíthetik az illesztőprogramokat a Linux integrációs szolgáltatások (LIS).
   
    >[!IMPORTANT]
    A lépés **szükséges** a CentOS 6.3 és korábbi, és kötelező megadni a későbbi kibocsátásokban megtörténik.

        # sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
        # sudo yum install microsoft-hyper-v

    Azt is megteheti, kövesse a manuális telepítési utasításokat a [LIS letöltési oldalát](https://go.microsoft.com/fwlink/?linkid=403033) , a virtuális gép RPM telepítéséhez.
 
12. Telepítse az Azure Linux ügynök és a függőségek:
    
        # sudo yum install python-pyasn1 WALinuxAgent
    
    A WALinuxAgent csomagot, azzal eltávolítja a NetworkManager és NetworkManager-gnome csomagok Ha nem már eltávolította azokat lásd a 3. lépés.


13. Módosítsa a kernel rendszerindító sor lárvajárat konfigurációs kiegészítő rendszermag paraméterek az Azure-bA felvenni. Ehhez nyissa meg a `/boot/grub/menu.lst` egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel az alábbi paramétereket tartalmazza:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Ez biztosítja az összes konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására.
    
    A fentiek mellett javasoljuk, hogy *eltávolítása* a következő paraméterekkel:
    
        rhgb quiet crashkernel=auto
    
    Grafikus és a csendes rendszerindító egy felhőalapú környezetben, ahol azt szeretnénk, hogy minden a naplókat a soros port küldendő nem hasznosak.  A `crashkernel` beállítás lehet, hogy szükség esetén konfigurálva balra, de vegye figyelembe, hogy ez a paraméter csökkenti a virtuális gépen legalább 128 MB, rendelkezésre álló memória mennyisége kisebb Virtuálisgép-méretek a problematikus esetleg.

    >[!Important]
    CentOS 6.5-ös vagy korábbi is be kell állítani a kernel paraméter `numa=off`. Tekintse meg a Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához.  Ez általában az alapértelmezett beállítás.

15. Ne hozzon létre az operációsrendszer-lemezképet a lapozóterület.
    
    Az Azure Linux ügynök automatikusan konfigurálhatók a lapozóterület használata a helyi erőforrás lemezt a virtuális Géphez csatolt Azure kiépítése után. Vegye figyelembe, hogy a helyi erőforrás lemez egy *ideiglenes* lemezre, és előfordulhat, hogy szerepelnek, ha a virtuális gép van platformelőfizetés. Az Azure Linux ügynök telepítése után (lásd az előző lépésben), módosítsa a következő paramétereket `/etc/waagent.conf` megfelelően:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. A virtuális gép kiosztásának megszüntetése, és előkészíti az Azure-on történő üzembe helyezéséhez a következő parancsok futtatásával:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Kattintson a **művelet le -> leállítási** a Hyper-V kezelőjében. A Linux virtuális merevlemez az Azure-bA feltölteni kívánt készen áll.


- - -
## <a name="centos-70"></a>CentOS 7.0 +
**Változások a CentOS 7 (és hasonló származékai)**

A CentOS 7 virtuális gép előkészítése az Azure-hasonlít a CentOS 6, azonban nincsenek érdemes megjegyezni számos fontos különbség:

* A NetworkManager csomag már nem ütközik az Azure Linux ügynök. Alapértelmezés szerint ez a csomag telepítve van, és azt javasoljuk, hogy nem törli.
* GRUB2 most lesz az alapértelmezett rendszertöltő, az eljárás kernel paraméterek szerkesztésre megváltozott (lásd alább).
* XFS mostantól az alapértelmezett fájlrendszert. A ext4 fájlrendszer továbbra is használható, ha szükséges.

**Konfigurációs lépések**

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Kattintson a **Connect** nyissa meg a konzol ablakot a virtuális gép számára.

3. Hozzon létre vagy szerkessze a fájlt `/etc/sysconfig/network` , és adja hozzá a következő szöveget:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Hozzon létre vagy szerkessze a fájlt `/etc/sysconfig/network-scripts/ifcfg-eth0` , és adja hozzá a következő szöveget:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Az Ethernet-adaptert statikus szabályainak elkerülése érdekében udev szabályok módosítása. Ezek a szabályok problémákat okozhat, ha a Microsoft Azure- vagy Hyper-v virtuális gép klónozása
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Ha szeretné használni a OpenLogic tükör, amely az Azure adatközpontjaiban központja üzemelteti, majd cserélje le a `/etc/yum.repos.d/CentOS-Base.repo` fájlt a következő tárházak találhatók.  A művelet továbbá felvesz a **[openlogic]** tárház, amely az Azure Linux ügynök csomagokat tartalmazza:
   
        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0
        
        [base]
        name=CentOS-$releasever - Base
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        
        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        
        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        
        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

    >[!Note]
    Ez az útmutató többi fogja feltételezni, hogy azok be számára legalább a `[openlogic]` tárház, az alábbi Azure Linux ügynök telepítéséhez használandó.

7. Futtassa a következő parancsot, törölje az aktuális yum metaadatokat, és telepítse a frissítéseket:
   
        # sudo yum clean all

    Kivéve, ha a régebbi verziójú CentOS egy kép létrehozásakor, javasoljuk, hogy a csomagok frissítése a legújabb verzióra:

        # sudo yum -y update

    Lehet, hogy ez a parancs futtatása után a szükséges újraindítás.

8. Módosítsa a kernel rendszerindító sor lárvajárat konfigurációs kiegészítő rendszermag paraméterek az Azure-bA felvenni. Ehhez nyissa meg a `/etc/default/grub` egy szövegszerkesztőben, és a Szerkesztés a `GRUB_CMDLINE_LINUX` paraméter, például:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez biztosítja az összes konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására. Azt is kikapcsolja a CentOS 7 új elnevezési szabályai a hálózati adapterek. A fentiek mellett javasoljuk, hogy *eltávolítása* a következő paraméterekkel:
   
        rhgb quiet crashkernel=auto
   
    Grafikus és a csendes rendszerindító egy felhőalapú környezetben, ahol azt szeretnénk, hogy minden a naplókat a soros port küldendő nem hasznosak. A `crashkernel` beállítás lehet, hogy szükség esetén konfigurálva balra, de vegye figyelembe, hogy ez a paraméter csökkenti a virtuális gépen legalább 128 MB, rendelkezésre álló memória mennyisége kisebb Virtuálisgép-méretek a problematikus esetleg.

9. Miután a szerkesztési `/etc/default/grub` / felett, a következő parancsot, hogy a lárvajárat konfiguráció:
   
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

10. Ha a lemezkép **VMWare, VirtualBox vagy KVM:** ellenőrizze, hogy a Hyper-V-illesztőprogramok a initramfs szerepelnek:
   
   Szerkesztés `/etc/dracut.conf`, adja hozzá a tartalomhoz:
   
        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Építse újra a initramfs:
   
        # sudo dracut –f -v

11. Telepítse az Azure Linux ügynök és a függőségek:

        # sudo yum install python-pyasn1 WALinuxAgent
        # sudo systemctl enable waagent

12. Ne hozzon létre az operációsrendszer-lemezképet a lapozóterület.
   
   Az Azure Linux ügynök automatikusan konfigurálhatók a lapozóterület használata a helyi erőforrás lemezt a virtuális Géphez csatolt Azure kiépítése után. Vegye figyelembe, hogy a helyi erőforrás lemez egy *ideiglenes* lemezre, és előfordulhat, hogy szerepelnek, ha a virtuális gép van platformelőfizetés. Az Azure Linux ügynök telepítése után (lásd az előző lépésben), módosítsa a következő paramétereket `/etc/waagent.conf` megfelelően:
   
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

## <a name="next-steps"></a>További lépések
Most már készen áll a CentOS Linux virtuális merevlemez segítségével új virtuális gépek létrehozása az Azure-ban. Ha az első alkalommal, hogy van-e a .vhd fájl feltöltése az Azure, lásd: [Linux virtuális gép létrehozása az egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd).

