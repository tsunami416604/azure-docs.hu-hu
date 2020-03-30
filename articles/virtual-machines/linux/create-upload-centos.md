---
title: CentOS-alapú Linux-virtuális merevlemez létrehozása és feltöltése
description: Ismerje meg, hogyan hozhat létre és tölthet fel egy CentOS-alapú Linux operációs rendszert tartalmazó Virtuális Azure-merevlemezt (VHD).
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/25/2019
ms.author: guybo
ms.openlocfilehash: 8899249fd284f69fa26bab8cd70aaf6a67fbb83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066782"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>CentOS-alapú virtuális gép előkészítése az Azure-beli használatra

Ismerje meg, hogyan hozhat létre és tölthet fel egy CentOS-alapú Linux operációs rendszert tartalmazó Virtuális Azure-merevlemezt (VHD).

* [CentOS 6.x virtuális gép előkészítése az Azure-hoz](#centos-6x)
* [CentOS 7.0+ virtuális gép előkészítése az Azure-hoz](#centos-70)


## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már telepített egy CentOS (vagy hasonló származékos) Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik .vhd fájlok létrehozásához, például egy virtualizációs megoldás, például a Hyper-V. További információt [a Hyper-V szerepkör telepítése és a Virtuális gép konfigurálása című témakörben talál.](https://technet.microsoft.com/library/hh846766.aspx)

**CentOS telepítési megjegyzések**

* A Linux Azure-ra való előkészítésével kapcsolatos további tippekért tekintse meg [az általános Linux-telepítési megjegyzéseket](create-upload-generic.md#general-linux-installation-notes) is.
* A VHDX formátum nem támogatott az Azure-ban, csak **a rögzített virtuális merevlemez**.  A lemezt A Hyper-V Manager vagy a convert-vhd parancsmag segítségével virtuális merevlemez-formátumra konvertálhatja. Ha a VirtualBox ez azt jelenti, hogy a **Rögzített méret** lehetőséget választja, szemben a lemez létrehozásakor dinamikusan lefoglalt alapértelmezett beállítással.
* A Linux rendszer telepítésekor *ajánlott* az LVM helyett szabványos partíciókat használni (ez gyakran sok telepítés alapértelmezett). Ezzel elkerülhető, hogy az LVM-név ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik azonos virtuális géphez kell csatlakoztatni a hibaelhárításhoz. [Az LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [a RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezeken is használható.
* Kernel támogatás szerelési UDF fájlrendszerek szükséges. Az Azure-ban a kiépítési konfiguráció első indításkor a linuxos virtuális gép a vendéghez csatlakoztatott UDF-formátumú adathordozón keresztül kerül átadásra. Az Azure Linux-ügynöknek képesnek kell lennie az UDF fájlrendszer csatlakoztatására a konfiguráció olvasásához és a virtuális gép kiépítéséhez.
* A 2.6.37 alatti Linux kernel verziók nem támogatják a NUMA-t a nagyobb virtuális gépméretekkel rendelkező Hyper-V-ben. Ez a probléma elsősorban az upstream Red Hat 2.6.32 kernelt használó régebbi disztribúciákat érinti, és az RHEL 6.6 (kernel-2.6.32-504) értékben javították. A 2.6.37-nél régebbi egyéni kerneleket vagy 2.6.32-504-nél régebbi RHEL-alapú kerneleket futtató rendszereknek be kell állítaniuk a boot paramétert `numa=off` a grub.conf kernel parancssorában. További információ: Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Ne konfiguráljon lapozópartíciót az operációs rendszer lemezén. A Linux-ügynök beállítható úgy, hogy hozzon létre egy cserefájlt az ideiglenes erőforráslemezen.  Erről további információt az alábbi lépésekben talál.
* Az Azure-beli virtuális hálózat minden virtuális d-jének 1 MB-hoz igazított virtuális mérettel kell rendelkeznie. Nyers lemezről virtuális merevlemezre történő konvertáláskor győződjön meg arról, hogy a nyers lemez mérete a konvertálás előtt az 1 MB többszöröse. További információt a [Linux telepítési megjegyzések című témakörben](create-upload-generic.md#general-linux-installation-notes) talál.

## <a name="centos-6x"></a>CentOS 6.x

1. A Hyper-V Manager ben válassza ki a virtuális gépet.

2. Kattintson a **Csatlakozás gombra** a virtuális gép konzolablakának megnyitásához.

3. A CentOS 6 rendszerben a NetworkManager zavarhatja az Azure Linux-ügynököt. Távolítsa el a csomagot a következő parancs futtatásával:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Hozza létre vagy `/etc/sysconfig/network` szerkesztse a fájlt, és adja hozzá a következő szöveget:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Hozza létre vagy `/etc/sysconfig/network-scripts/ifcfg-eth0` szerkesztse a fájlt, és adja hozzá a következő szöveget:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Módosítsa az udev-szabályokat, hogy ne hozzon létre statikus szabályokat az Ethernet-csatoló(k)hoz. Ezek a szabályok problémákat okozhatnak a virtuális gépek Microsoft Azure vagy Hyper-V szolgáltatásban történő klónozása során:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul a következő parancs futtatásával:

    ```bash
    sudo chkconfig network on
    ```

8. Ha az Azure-adatközpontokban üzemeltetett OpenLogic-tükröket szeretné használni, `/etc/yum.repos.d/CentOS-Base.repo` cserélje le a fájlt a következő tárházakra.  Ez is hozzáadja a **[openlogic]** tárházat, amely további csomagokat, például az Azure Linux-ügynök:

   ```console
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
   ```

    > [!Note]
    > Az útmutató többi része feltételezi, hogy `[openlogic]` legalább a tárrestó thasználja, amely az alábbi Azure Linux-ügynök telepítéséhez lesz használva.

9. Adja hozzá a következő sort az /etc/yum.conf fájlhoz:

    ```console
    http_caching=packages
    ```

10. A következő parancs futtatásával törölje az aktuális yum metaadatokat, és frissítse a rendszert a legújabb csomagokkal:

    ```bash
    yum clean all
    ```

    Hacsak nem hoz létre képet a CentOS egy régebbi verziójához, javasoljuk, hogy frissítse az összes csomagot a legújabbra:

    ```bash
    sudo yum -y update
    ```

    A parancs futtatása után újraindításra lehet szükség.

11. (Nem kötelező) Telepítse a Linux integrációs szolgáltatások (LIS) illesztőprogramjait.

    > [!IMPORTANT]
    > A lépés a CentOS 6.3-as és korábbi verzióihoz **szükséges,** a későbbi kiadásokhoz pedig nem kötelező.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Azt is megteheti, kövesse a [lis letöltési lapon](https://www.microsoft.com/download/details.aspx?id=51612) található manuális telepítési utasításokat, hogy telepítse az RPM-et a virtuális gépre.

12. Telepítse az Azure Linux-ügynök és a függőségek. A waagent szolgáltatás indítása és engedélyezése:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    A WALinuxAgent csomag eltávolítja a NetworkManager és a NetworkManager-gnome csomagokat, ha azokat még nem távolította el a 3.

13. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. Ehhez nyissa meg `/boot/grub/menu.lst` a szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Ez azt is biztosítja, hogy az összes konzolüzenet az első soros portra kerüljön, amely segítséget nyújthat az Azure-támogatásnak a hibakeresési problémák megoldásában.

    A fentieken kívül ajánlott a következő paraméterek *eltávolítása:*

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port.  A `crashkernel` lehetőség lehet hagyni konfigurálva, ha szükséges, de vegye figyelembe, hogy ez a paraméter csökkenti a rendelkezésre álló memória a virtuális gép 128 MB vagy több, ami problémás lehet a kisebb virtuális gép mérete.

    > [!Important]
    > A CentOS 6.5-ös és korábbi `numa=off`paramétereinek szintén be kell állítaniuk a kernel paramétert. Lásd Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el.  Ez általában az alapértelmezett.

15. Ne hozzon létre csereterületet az operációs rendszer lemezén.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterület a helyi erőforráslemez, amely csatlakozik a virtuális géphez kiépítése után az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy *ideiglenes* lemez, és lehet, hogy kiüríti, ha a virtuális gép megszüntetése. Az Azure Linux-ügynök telepítése után (lásd az előző `/etc/waagent.conf` lépést) megfelelően módosítsa a következő paramétereket:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Kattintson **a Művelet -> Leállítás a** Hyper-V Kezelőben parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.



## <a name="centos-70"></a>CentOS 7.0+

**Változások a CentOS 7-ben (és hasonló származékokban)**

A CentOS 7 virtuális gép előkészítése az Azure-hoz nagyon hasonlít a CentOS 6-hoz, de számos fontos különbség van, amelyeket érdemes megjegyezni:

* A NetworkManager-csomag már nem ütközik az Azure Linux-ügynökkel. Ez a csomag alapértelmezés szerint telepítve van, és azt javasoljuk, hogy ne távolítsuk el.
* A GRUB2 mostantól alapértelmezett rendszertöltőként használatos, így a kernel paramétereinek szerkesztési eljárása megváltozott (lásd alább).
* Az XFS mostantól az alapértelmezett fájlrendszer. Az ext4 fájlrendszer szükség esetén továbbra is használható.

**Konfigurációs lépések**

1. A Hyper-V Manager ben válassza ki a virtuális gépet.

2. Kattintson a **Csatlakozás gombra** a virtuális gép konzolablakának megnyitásához.

3. Hozza létre vagy `/etc/sysconfig/network` szerkesztse a fájlt, és adja hozzá a következő szöveget:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Hozza létre vagy `/etc/sysconfig/network-scripts/ifcfg-eth0` szerkesztse a fájlt, és adja hozzá a következő szöveget:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Módosítsa az udev-szabályokat, hogy ne hozzon létre statikus szabályokat az Ethernet-csatoló(k)hoz. Ezek a szabályok problémákat okozhatnak a virtuális gépek Microsoft Azure vagy Hyper-V szolgáltatásban történő klónozása során:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Ha az Azure-adatközpontokban üzemeltetett OpenLogic-tükröket szeretné használni, `/etc/yum.repos.d/CentOS-Base.repo` cserélje le a fájlt a következő tárházakra.  Ez is hozzáadja az **[openlogic]** tárházat, amely tartalmazza az Azure Linux-ügynök csomagjait:

   ```console
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
   ```
    
   > [!Note]
   > Az útmutató többi része feltételezi, hogy `[openlogic]` legalább a tárrestó thasználja, amely az alábbi Azure Linux-ügynök telepítéséhez lesz használva.

7. Az aktuális yum metaadatok törléséhez és a frissítések telepítéséhez futtassa a következő parancsot:

    ```bash
    sudo yum clean all
    ```

    Hacsak nem hoz létre képet a CentOS egy régebbi verziójához, javasoljuk, hogy frissítse az összes csomagot a legújabbra:

    ```bash
    sudo yum -y update
    ```

    A parancs futtatása után újrakellett indítani az újraindítást.

8. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. Ehhez nyissa meg `/etc/default/grub` a szövegszerkesztőt, `GRUB_CMDLINE_LINUX` és szerkesztse a paramétert, például:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez azt is biztosítja, hogy az összes konzolüzenet az első soros portra kerüljön, amely segítséget nyújthat az Azure-támogatásnak a hibakeresési problémák megoldásában. Emellett kikapcsolja az új CentOS 7 elnevezési konvenciókat a hálózati adapterek számára. A fentieken kívül ajánlott a következő paraméterek *eltávolítása:*

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port. A `crashkernel` lehetőség lehet hagyni konfigurálva, ha szükséges, de vegye figyelembe, hogy ez a paraméter csökkenti a rendelkezésre álló memória a virtuális gép 128 MB vagy több, ami problémás lehet a kisebb virtuális gép mérete.

9. Miután végzett `/etc/default/grub` szerkesztési egy fenti, futtassa a következő parancsot, hogy újraarakja a grub konfiguráció:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Ha a lemezképet **VMware, VirtualBox vagy KVM** rendszerből építi: Győződjön meg arról, hogy a Hyper-V illesztőprogramok az initramfok részét képezik:

    Szerkesztés `/etc/dracut.conf`, tartalom hozzáadása:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Építse újra az initramfokat:

    ```bash
    sudo dracut -f -v
    ```

11. Telepítse az Azure Linux-ügynök és a függőségek:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Ne hozzon létre csereterületet az operációs rendszer lemezén.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterület a helyi erőforráslemez, amely csatlakozik a virtuális géphez kiépítése után az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy *ideiglenes* lemez, és lehet, hogy kiüríti, ha a virtuális gép megszüntetése. Az Azure Linux-ügynök telepítése után (lásd az előző `/etc/waagent.conf` lépést) megfelelően módosítsa a következő paramétereket:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Kattintson **a Művelet -> Leállítás a** Hyper-V Kezelőben parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>További lépések

Most már készen áll arra, hogy a CentOS Linux virtuális merevlemezével új virtuális gépeket hozzon létre az Azure-ban. Ha ez az első alkalom, hogy feltölti a .vhd fájlt az Azure-ba, olvassa el a Linux virtuális gép létrehozása egyéni lemezről című [témakört.](upload-vhd.md#option-1-upload-a-vhd)
