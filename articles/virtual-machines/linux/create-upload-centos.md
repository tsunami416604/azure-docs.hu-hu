---
title: Az Azure-ban a CentOS-alapú Linux rendszerű VHD létrehozása és feltöltése
description: Ismerje meg, hozhat létre és töltse fel az Azure virtuális merevlemez (VHD), amely egy Linux CentOS-alapú operációs rendszert tartalmazza.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: szark
ms.openlocfilehash: a46f2b4ed1bb3fc5fff65a627bd3d808ed85ffce
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967282"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>CentOS-alapú virtuális gép előkészítése Azure-beli használatra

* [CentOS 6.x virtuális gép előkészítése Azure-beli használatra](#centos-6x)
* [CentOS 7.0 + virtuális gép előkészítése Azure-beli használatra](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy a CentOS már telepítve van (vagy hasonló származtatott) Linux operációs rendszer virtuális merevlemezre. Több eszköz létezik a .vhd fájlokat, például például a Hyper-V virtualizálási megoldás létrehozása. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gép](https://technet.microsoft.com/library/hh846766.aspx).

**CentOS telepítési jegyzetek**

* Tekintse meg a is [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további tippek Linux előkészítése az Azure-hoz.
* A VHDX formátum nem támogatott az Azure-ban, csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelőjével vagy a convert-vhd-parancsmag használatával. VirtualBox használatakor ez azt jelenti, hogy kiválasztja **rögzített méretű** dinamikusan kiosztott lemez létrehozásakor az alapértelmezett helyett.
* A Linux rendszer telepítése esetén *ajánlott* LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciókat használni. LVM neve nem felel meg a klónozott virtuális gépeket, így elkerülhető, különösen akkor, ha minden eddiginél kell operációsrendszer-lemezt egy másik, azonos virtuális Géphez van csatlakoztatva a hibaelhárításhoz. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) használnak, az adatlemezeket.
* Kernel támogatása csatlakoztatni UDF fájlrendszerek megadása kötelező. Első rendszerindításkor az Azure-ban az üzembe helyezési konfiguráció átadódik a Linux rendszerű virtuális gép UDF-formátumú adathordozót, amely csatolva van a Vendég-n keresztül. Az Azure Linux-ügynök csatlakoztatása az UDF-fájlrendszer, olvassa el a konfigurációját, és a virtuális gép képesnek kell lennie.
* Linux-kernel verziók alatt 2.6.37 támogatott Hyper-v, nagyobb Virtuálisgép-mérettel. A probléma elsősorban hatással van a felsőbb rétegbeli használó régebbi disztribúciók Red Hat 2.6.32 kernel, és kijavítottuk RHEL 6.6 (kernel-2.6.32-504). Rendszert futtató egyéni 2.6.37 régebbi kernelekkel, vagy régebbi RHEL-alapú kernelekkel 2.6.32-504 a rendszerindító paramétert kell beállítani, mint `numa=off` a parancssori grub.conf a kernel. További információ: Red Hat [KB-os 436883](https://access.redhat.com/solutions/436883).
* Az operációsrendszer-lemez nem konfigurál egy lapozó partíciót. A Linux-ügynök beállítható úgy, hogy hozzon létre egy ideiglenes erőforrás lemezen a lapozófájl.  További információ található a következő lépéseket.
* Az Azure-ban minden virtuális merevlemezek rendelkeznie kell egy virtuális méret 1 MB igazítva. A virtuális merevlemez nyers lemezről történő konvertálása során biztosítania kell, hogy a nyers lemez mérete nagyobb-e az átalakítás előtt 1MB többszöröse. Lásd: [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) további információt.

## <a name="centos-6x"></a>CentOS 6.x

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Kattintson a **Connect** , nyisson meg egy konzolablakot, a virtuális gép.

3. CentOS 6 NetworkManager zavarhatja az Azure Linux-ügynök. Távolítsa el ezt a csomagot a következő parancs futtatásával:

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

6. Az Ethernet-adaptert statikus szabályainak elkerülése érdekében, udev szabályok módosítása. Ezek a szabályok problémákat okozhat, ha a Microsoft Azure vagy Hyper-v virtuális gépek klónozásának

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Ellenőrizze, hogy a hálózati szolgáltatás elkezdi rendszerindítás, a következő parancs futtatásával:

        # sudo chkconfig network on

8. Ha szeretné használni az OpenLogic tükör, amely az Azure-adatközpontokban üzemelteti, majd cserélje le a `/etc/yum.repos.d/CentOS-Base.repo` fájlt a következő tárházakban.  A művelet továbbá felvesz a **[openlogic]** , például az Azure Linux-ügynök további csomagokat tartalmazó tárházban:

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

    > [!Note]
    > Ez az útmutató többi használandó használja legalább az `[openlogic]` adattár, az alábbi Azure Linux-ügynök telepítéséhez használandó.

9. Adja hozzá a következő sort a /etc/yum.conf:

        http_caching=packages

10. Futtassa a következő parancsot az aktuális yum metaadatokat törléséhez, és frissítse a rendszer a legújabb csomagok:

        # yum clean all

    Hacsak nem hoz létre a CentOS egy régebbi verzióját egy kép, javasoljuk, hogy minden a csomagok frissítése a legújabb verzióra:

        # sudo yum -y update

    A parancs futtatása után újraindítás lehet szükséges.

11. (Nem kötelező) Telepítse az illesztőprogramokat a Linux Integration Services (LIS).

    > [!IMPORTANT]
    > A lépés **szükséges** a CentOS 6.3 és a korábbi és újabb verziók esetén nem kötelező.

        # sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
        # sudo yum install microsoft-hyper-v

    Másik lehetőségként kövesse a manuális telepítési utasításait a [LIS letöltési oldalát](https://go.microsoft.com/fwlink/?linkid=403033) az RPM alakzatot a virtuális gép telepítéséhez.

12. Az Azure Linux-ügynök és a függőségek telepítése:

        # sudo yum install python-pyasn1 WALinuxAgent

    A WALinuxAgent csomag eltávolítja a NetworkManager és NetworkManager-gnome csomagokat, ha azok nem lettek már eltávolítva leírt módon a 3. lépés.

13. Módosítsa a rendszermag rendszerindítási sorához további kernel paramétereket tartalmazza az Azure-hoz a grub-hibát konfigurációjában. Ehhez nyissa meg a `/boot/grub/menu.lst` egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel tartalmazza a következő paraméterekkel:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Ez biztosítja az összes konzol üzenetet küld az első soros port, amely segítheti az Azure támogatási problémák hibakeresésében.

    A fentiek mellett javasoljuk, hogy *eltávolítása* a következő paraméterekkel:

        rhgb quiet crashkernel=auto

    Grafikus és a csendes rendszerindító, amelyek nem hasznos, ha egy felhőalapú környezetben, ahol szeretnénk a soros port kell küldeni a naplókat.  A `crashkernel` beállítás lehet bal konfigurálva, ha szükséges, de vegye figyelembe, hogy ez a paraméter csökken a rendelkezésre álló memória a virtuális gépen legalább 128 MB, amely a kisebb Virtuálisgép-méretek problémás lehet.

    > [!Important]
    > CentOS 6.5-ös vagy régebbi is be kell állítania a kernel paraméter `numa=off`. Tekintse meg a Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindítás elindításához.  Ez általában az alapértelmezett érték.

15. Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez.

    Az Azure Linux-ügynök automatikusan konfigurálhatják a lapozóterület használata a helyi erőforrás-lemez, amely az Azure-ban üzembe helyezés után a virtuális Géphez van csatlakoztatva. Vegye figyelembe, hogy a helyi erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy ki kell üríteni, ha a virtuális gép. Az Azure Linux-ügynök telepítése után (lásd az előző lépésben), módosítsa a következő paramétereket lévő `/etc/waagent.conf` megfelelően:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Futtassa az alábbi parancsokat a virtuális gép megszüntetése és kiépítése az Azure előkészítése:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Kattintson a **művelet le -> Leállítás** a Hyper-V kezelőjében. A Linux rendszerű VHD-t most már készen áll a tölthető fel az Azure-bA.

- - -

## <a name="centos-70"></a>CentOS 7.0 +

**Változások a CentOS 7 (és hasonló származékai)**

A CentOS 7 virtuális gép előkészítése Azure-hasonlít a CentOS 6, azonban nincsenek megjegyezni számos fontos különbség:

* A NetworkManager csomag már nem ütközik az Azure Linux-ügynök. Ez a csomag alapértelmezés szerint telepítve van, és azt javasoljuk, hogy nem törli.
* GRUB2 most történik, az alapértelmezett rendszerbetöltőt, ezért az eljárás kernel paraméterek szerkesztésre megváltozott (lásd alább).
* XFS már az alapértelmezett fájlrendszer. A ext4 fájlrendszer továbbra is használható, ha szükséges.

**Konfigurációs lépések**

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Kattintson a **Connect** , nyisson meg egy konzolablakot, a virtuális gép.

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

5. Az Ethernet-adaptert statikus szabályainak elkerülése érdekében, udev szabályok módosítása. Ezek a szabályok problémákat okozhat, ha a Microsoft Azure vagy Hyper-v virtuális gépek klónozásának

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Ha szeretné használni az OpenLogic tükör, amely az Azure-adatközpontokban üzemelteti, majd cserélje le a `/etc/yum.repos.d/CentOS-Base.repo` fájlt a következő tárházakban.  A művelet továbbá felvesz a **[openlogic]** , az Azure Linux ügynök-csomagokat tartalmazó tárházban:

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

    > [!Note]
    > Ez az útmutató többi használandó használja legalább az `[openlogic]` adattár, az alábbi Azure Linux-ügynök telepítéséhez használandó.

7. Futtassa a következő parancsot az aktuális yum metaadatokat törléséhez, és telepítse a frissítéseket:

        # sudo yum clean all

    Hacsak nem hoz létre a CentOS egy régebbi verzióját egy kép, javasoljuk, hogy minden a csomagok frissítése a legújabb verzióra:

        # sudo yum -y update

    A parancs futtatása után esetleg szükséges újraindítás.

8. Módosítsa a rendszermag rendszerindítási sorához további kernel paramétereket tartalmazza az Azure-hoz a grub-hibát konfigurációjában. Ehhez nyissa meg a `/etc/default/grub` egy szövegszerkesztőben, és a Szerkesztés a `GRUB_CMDLINE_LINUX` paramétert, például:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

   Ez biztosítja az összes konzol üzenetet küld az első soros port, amely segítheti az Azure támogatási problémák hibakeresésében. Azt is kikapcsolja az új CentOS 7 elnevezési konvencióinak hálózati adapterek. A fentiek mellett javasoljuk, hogy *eltávolítása* a következő paraméterekkel:

        rhgb quiet crashkernel=auto

    Grafikus és a csendes rendszerindító, amelyek nem hasznos, ha egy felhőalapú környezetben, ahol szeretnénk a soros port kell küldeni a naplókat. A `crashkernel` beállítás lehet bal konfigurálva, ha szükséges, de vegye figyelembe, hogy ez a paraméter csökken a rendelkezésre álló memória a virtuális gépen legalább 128 MB, amely a kisebb Virtuálisgép-méretek problémás lehet.

9. Ha elkészült szerkesztési `/etc/default/grub` kiszolgálónként felett, a következő parancsot a grub konfigurációs újraépítése:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

10. Ha az a rendszerkép összeállítására **VMware, VirtualBox vagy KVM:** gondoskodjon arról, hogy a Hyper-V illesztőprogramok a initramfs szerepelnek:

   Szerkesztés `/etc/dracut.conf`, adja hozzá a tartalmat:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

   Építse újra a initramfs:

        # sudo dracut -f -v

11. Az Azure Linux-ügynök és a függőségek telepítése:

        # sudo yum install python-pyasn1 WALinuxAgent
        # sudo systemctl enable waagent

12. Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez.

   Az Azure Linux-ügynök automatikusan konfigurálhatják a lapozóterület használata a helyi erőforrás-lemez, amely az Azure-ban üzembe helyezés után a virtuális Géphez van csatlakoztatva. Vegye figyelembe, hogy a helyi erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy ki kell üríteni, ha a virtuális gép. Az Azure Linux-ügynök telepítése után (lásd az előző lépésben), módosítsa a következő paramétereket lévő `/etc/waagent.conf` megfelelően:

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

## <a name="next-steps"></a>További lépések

Most már készen áll a CentOS Linux rendszerű virtuális merevlemez használatával hozzon létre új virtuális gépek az Azure-ban. Ha ez az első alkalommal, hogy a .vhd fájlt videófájl az Azure-ba, tekintse meg a [egy Linux virtuális gép létrehozása egy egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd).
