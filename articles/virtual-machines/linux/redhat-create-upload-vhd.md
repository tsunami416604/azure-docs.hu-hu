---
title: Red Hat Enterprise Linux virtuális merevlemez létrehozása és feltöltése az Azure-ban való használatra
description: Ismerje meg, hogyan hozhat létre és tölthet fel egy Red Hat Linux operációs rendszert tartalmazó Virtuális Azure-merevlemezt (VHD).
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: guybo
ms.openlocfilehash: cd0a71c60930e3eb659255a23cdb03360730f2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060733"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Red Hat-alapú virtuális gép előkészítése az Azure-beli használatra
Ebben a cikkben megtudhatja, hogyan készíthet elő egy Red Hat Enterprise Linux (RHEL) virtuális gépet az Azure-ban való használatra. Az RHEL e cikkben szereplő verziói a 6.7+ és a 7.1+. A cikkben tárgyalt hipervizorok a Hyper-V, a kernel alapú virtuális gép (KVM) és a VMware. A Red Hat Cloud Access programjában való részvétel jogosultsági követelményeiről a [Red Hat Cloud Access webhelyén](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) és az [RHEL futtatása az Azure-ban](https://access.redhat.com/ecosystem/ccsp/microsoft-azure)című témakörben talál további információt. Az RHEL-lemezképek létrehozásának automatizálása az [Azure Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview)webhelyen található.

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Red Hat-alapú virtuális gép előkészítése a Hyper-V Manager ből

### <a name="prerequisites"></a>Előfeltételek
Ez a szakasz feltételezi, hogy már beszerzett egy ISO fájlt a Red Hat webhelyéről, és telepítette az RHEL-lemezképet egy virtuális merevlemezre (VHD). Az operációs rendszerlemezképek Hyper-V Manager használatával történő telepítéséről [a Hyper-V szerepkör telepítése és A virtuális gép konfigurálása](https://technet.microsoft.com/library/hh846766.aspx)című témakörben talál további részleteket.

**RHEL telepítési megjegyzések**

* Az Azure nem támogatja a VHDX formátumot. Az Azure csak a rögzített virtuális merevlemezt támogatja. A Hyper-V Manager segítségével konvertálhatja a lemezt VHD formátumba, vagy használhatja a convert-vhd parancsmaszt. A VirtualBox használata esetén válassza a **Rögzített méret** lehetőséget, szemben az alapértelmezett dinamikusan lefoglalt beállítással a lemez létrehozásakor.
* Az Azure támogatja a Gen1 (BIOS-rendszerindítás) & Gen2 (UEFI rendszerindítás) virtuális gépek.
* A virtuális merevlemez maximális mérete 1023 GB.
* Logikai kötetkezelő (LVM) támogatott, és az operációs rendszer lemezén vagy adatlemezek en Azure virtuális gépeken használható. Azonban általában ajánlott az operációs rendszer lemezén szabványos partíciókat használni az LVM helyett. Ez a gyakorlat elkerüli az LVM-név ütközését a klónozott virtuális gépekkel, különösen akkor, ha hibaelhárítási célból egy operációs rendszer lemezét egy másik azonos virtuális géphez kell csatlakoztatnia. Lásd még: [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dokumentáció.
* Kernel támogatás szerelési Universal Disk Format (UDF) fájlrendszerek szükséges. Az Azure-ban az első indításkor a vendéghez csatlakoztatott UDF-formátumú adathordozó átadja a kiépítési konfigurációt a Linux virtuális gépnek. Az Azure Linux-ügynöknek képesnek kell lennie az UDF fájlrendszer csatlakoztatására a konfiguráció olvasásához és a virtuális gép kiépítéséhez.
* Ne állítson be lapozópartíciót az operációs rendszer lemezén. A Linux-ügynök beállítható úgy, hogy hozzon létre egy cserefájlt az ideiglenes erőforráslemezen.  Erről további információt az alábbi lépésekben talál.
* Az Azure-beli virtuális hálózat minden virtuális d-jének 1 MB-hoz igazított virtuális mérettel kell rendelkeznie. Nyers lemezről virtuális merevlemezre történő konvertáláskor győződjön meg arról, hogy a nyers lemez mérete a konvertálás előtt az 1 MB többszöröse. További részletek az alábbi lépésekben találhatók. További információt a [Linux telepítési megjegyzések című témakörben](create-upload-generic.md#general-linux-installation-notes) talál.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Rhel 6 virtuális gép előkészítése a Hyper-V Manager ből

1. A Hyper-V Manager ben válassza ki a virtuális gépet.

1. Kattintson a **Csatlakozás gombra** a virtuális gép konzolablakának megnyitásához.

1. Az RHEL 6-ban a NetworkManager zavarhatja az Azure Linux-ügynököt. Távolítsa el a csomagot a következő parancs futtatásával:
   
        # sudo rpm -e --nodeps NetworkManager

1. A `/etc/sysconfig/network` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. A `/etc/sysconfig/network-scripts/ifcfg-eth0` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Helyezze át (vagy távolítsa el) az udev-szabályokat, hogy elkerülje az Ethernet-csatoló statikus szabályainak generálását. Ezek a szabályok problémákat okoznak, ha egy virtuális gépet klónoz a Microsoft Azure-ban vagy a Hyper-V-ben:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul a következő parancs futtatásával:

        # sudo chkconfig network on

1. Regisztrálja Red Hat-előfizetését, hogy engedélyezze a csomagok telepítését az RHEL tárházból a következő parancs futtatásával:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. A WALinuxAgent `WALinuxAgent-<version>`csomag , a Red Hat extra tárházba lett leküldéses. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. A módosítás elvégzéséhez `/boot/grub/menu.lst` nyisson meg egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett rendszermag a következő paramétereket tartalmazza:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Ez azt is biztosítja, hogy az összes konzolüzenetek küldése az első soros portra, amely segíthet az Azure-támogatás hibakeresési problémák.
    
    Ezenkívül azt javasoljuk, hogy távolítsa el a következő paramétereket:
    
        rhgb quiet crashkernel=auto
    
    Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port.  Szükség esetén `crashkernel` hagyhatja a beállítást konfigurálva. Vegye figyelembe, hogy ez a paraméter 128 MB-tal vagy többel csökkenti a virtuális gép ben rendelkezésre álló memória mennyiségét. Ez a konfiguráció kisebb virtuálisgép-méretek esetén is problémás lehet.


1. Győződjön meg arról, hogy a biztonságos rendszerhéj (SSH) kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el, ami általában az alapértelmezett. Módosítsa az /etc/ssh/sshd_config kapcsolót a következő szóval:

        ClientAliveInterval 180

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    A WALinuxAgent csomag telepítése eltávolítja a NetworkManager és a NetworkManager-gnome csomagokat, ha azokat még nem távolították el a 3.

1. Ne hozzon létre csereterületet az operációs rendszer lemezén.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterületet a virtuális géphez csatlakoztatott helyi erőforráslemez használatával, miután a virtuális gép ki van építve az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy ideiglenes lemez, és hogy a virtuális gép kiürítése esetén kiüríthető. Miután az előző lépésben telepítette az Azure Linux-ügynököt, módosítsa a következő paramétereket az /etc/waagent.conf fájlban:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Törölje az előfizetés regisztrációját (ha szükséges) a következő parancs futtatásával:

        # sudo subscription-manager unregister

1. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Kattintson a **Művelet** > **leállítása a** Hyper-V Manager programban parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Rhel 7 virtuális gép előkészítése a Hyper-V Manager ből

1. A Hyper-V Manager ben válassza ki a virtuális gépet.

1. Kattintson a **Csatlakozás gombra** a virtuális gép konzolablakának megnyitásához.

1. A `/etc/sysconfig/network` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. A `/etc/sysconfig/network-scripts/ifcfg-eth0` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=igen NM_CONTROLLED=igen

1. Győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul a következő parancs futtatásával:

        # sudo systemctl enable network

1. Regisztrálja Red Hat-előfizetését, hogy engedélyezze a csomagok telepítését az RHEL tárházból a következő parancs futtatásával:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. A módosítás elvégzéséhez `/etc/default/grub` nyissa meg a szövegszerkesztőt, és szerkesztse a `GRUB_CMDLINE_LINUX` paramétert. Példa:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez azt is biztosítja, hogy az összes konzolüzenetek küldése az első soros portra, amely segíthet az Azure-támogatás hibakeresési problémák. Ez a konfiguráció kikapcsolja az új RHEL 7 elnevezési konvenciók hálózati adapterek. Ezenkívül azt javasoljuk, hogy távolítsa el a következő paramétereket:
   
        rhgb quiet crashkernel=auto
   
    Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port. Szükség esetén `crashkernel` hagyhatja a beállítást konfigurálva. Vegye figyelembe, hogy ez a paraméter 128 MB-tal vagy annál nagyobb mértékben csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét, ami kisebb virtuálisgép-méretek esetén is problémás lehet.

1. Miután befejezte `/etc/default/grub`a szerkesztést, futtassa a következő parancsot a grub konfiguráció újraépítéséhez:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el, ami általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sort:

        ClientAliveInterval 180

1. A WALinuxAgent `WALinuxAgent-<version>`csomag , a Red Hat extra tárházba lett leküldéses. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Ne hozzon létre csereterületet az operációs rendszer lemezén.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterületet a virtuális géphez csatlakoztatott helyi erőforráslemez használatával, miután a virtuális gép ki van építve az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy ideiglenes lemez, és a virtuális gép kiürítése esetén kiüríthető. Miután telepítette az Azure Linux-ügynök az előző `/etc/waagent.conf` lépésben, módosítsa a következő paramétereket megfelelően:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Ha törölni szeretné az előfizetés regisztrációját, futtassa a következő parancsot:

        # sudo subscription-manager unregister

1. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Kattintson a **Művelet** > **leállítása a** Hyper-V Manager programban parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Red Hat-alapú virtuális gép előkészítése a KVM-től
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Rhel 6 virtuális gép előkészítése a KVM-től

1. Töltse le az RHEL 6 KVM-képét a Red Hat weboldaláról.

1. Állítsa be a root jelszót.

    Hozzon létre egy titkosított jelszót, és másolja a kimenetet a parancs:

        # openssl passwd -1 changeme

    Root jelszó beállítása vendéghallal:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Módosítsa a második mező a gyökér felhasználó "!!" a titkosított jelszóhoz.

1. Hozzon létre egy virtuális gépet a KVM-ben a qcow2 lemezképből. Állítsa a lemez típusát **qcow2**, és állítsa a virtuális hálózati adapter eszköz **modelljét virtio.** Ezután indítsa el a virtuális gépet, és jelentkezzen be gyökérként.

1. A `/etc/sysconfig/network` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. A `/etc/sysconfig/network-scripts/ifcfg-eth0` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Helyezze át (vagy távolítsa el) az udev-szabályokat, hogy elkerülje az Ethernet-csatoló statikus szabályainak generálását. Ezek a szabályok problémákat okoznak, ha egy virtuális gépet klónoz az Azure-ban vagy a Hyper-V-ben:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul a következő parancs futtatásával:

        # chkconfig network on

1. Regisztrálja Red Hat-előfizetését, hogy engedélyezze a csomagok telepítését az RHEL tárházból a következő parancs futtatásával:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. Ehhez a konfigurációhoz `/boot/grub/menu.lst` nyissa meg a szövegszerkesztőt, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Ez azt is biztosítja, hogy az összes konzolüzenetek küldése az első soros portra, amely segíthet az Azure-támogatás hibakeresési problémák.
    
    Ezenkívül azt javasoljuk, hogy távolítsa el a következő paramétereket:
    
        rhgb quiet crashkernel=auto
    
    Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port. Szükség esetén `crashkernel` hagyhatja a beállítást konfigurálva. Vegye figyelembe, hogy ez a paraméter 128 MB-tal vagy annál nagyobb mértékben csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét, ami kisebb virtuálisgép-méretek esetén is problémás lehet.


1. Hyper-V modulok hozzáadása az initramfokhoz:  

    A `/etc/dracut.conf`szerkesztést és a következő tartalmat adja hozzá:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs újraépítése:

        # dracut -f -v

1. Távolítsa el a cloud-init alkalmazást:

        # yum remove cloud-init

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el:

        # chkconfig sshd on

    Módosítsa az /etc/ssh/sshd_config kapcsolót úgy, hogy az a következő sorokat tartalmazza:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. A WALinuxAgent `WALinuxAgent-<version>`csomag , a Red Hat extra tárházba lett leküldéses. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterületet a virtuális géphez csatlakoztatott helyi erőforráslemez használatával, miután a virtuális gép ki van építve az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy ideiglenes lemez, és a virtuális gép kiürítése esetén kiüríthető. Miután az előző lépésben telepítette az Azure Linux-ügynököt, módosítsa a következő paramétereket az **/etc/waagent.conf** fájlban:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Törölje az előfizetés regisztrációját (ha szükséges) a következő parancs futtatásával:

        # subscription-manager unregister

1. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Állítsa le a virtuális gépet a KVM-ben.

1. Konvertálja a qcow2 képet VHD formátumra.

> [!NOTE]
> Van egy ismert hiba qemu-img verziók >=2.2.1, hogy az eredmények egy helytelenül formázott Virtuális merevlemez. A probléma ki lett javítva a QEMU 2.6-ban. Javasoljuk, hogy használja vagy qemu-img 2.2.0 vagy alacsonyabb, vagy frissíteni 2.6 vagy magasabb. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Rhel 7 virtuális gép előkészítése a KVM-től

1. Töltse le az RHEL 7 KVM-képét a Red Hat webhelyéről. Ez az eljárás az RHEL 7-et használja példaként.

1. Állítsa be a root jelszót.

    Hozzon létre egy titkosított jelszót, és másolja a kimenetet a parancs:

        # openssl passwd -1 changeme

    Root jelszó beállítása vendéghallal:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Módosítsa a második mező root felhasználó "!!" a titkosított jelszóhoz.

1. Hozzon létre egy virtuális gépet a KVM-ben a qcow2 lemezképből. Állítsa a lemez típusát **qcow2**, és állítsa a virtuális hálózati adapter eszköz **modelljét virtio.** Ezután indítsa el a virtuális gépet, és jelentkezzen be gyökérként.

1. A `/etc/sysconfig/network` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. A `/etc/sysconfig/network-scripts/ifcfg-eth0` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=igen NM_CONTROLLED=igen

1. Győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul a következő parancs futtatásával:

        # sudo systemctl enable network

1. Regisztrálja Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését az RHEL tárházból a következő parancs futtatásával:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. Ehhez a konfigurációhoz `/etc/default/grub` nyissa meg a szövegszerkesztőben, és szerkesztse a `GRUB_CMDLINE_LINUX` paramétert. Példa:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez a parancs azt is biztosítja, hogy az összes konzolüzenet az első soros portra kerüljön, amely segítséget nyújthat az Azure-támogatásnak a hibakeresési problémák megoldásában. A parancs kikapcsolja az új RHEL 7 elnevezési konvenciókat is a hálózati adapterek számára. Ezenkívül azt javasoljuk, hogy távolítsa el a következő paramétereket:
   
        rhgb quiet crashkernel=auto
   
    Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port. Szükség esetén `crashkernel` hagyhatja a beállítást konfigurálva. Vegye figyelembe, hogy ez a paraméter 128 MB-tal vagy annál nagyobb mértékben csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét, ami kisebb virtuálisgép-méretek esetén is problémás lehet.

1. Miután befejezte `/etc/default/grub`a szerkesztést, futtassa a következő parancsot a grub konfiguráció újraépítéséhez:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Hyper-V modulok hozzáadása initramfs-hoz.

    Tartalom `/etc/dracut.conf` szerkesztése és hozzáadása:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs újraépítése:

        # dracut -f -v

1. Távolítsa el a cloud-init alkalmazást:

        # yum remove cloud-init

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el:

        # systemctl enable sshd

    Módosítsa az /etc/ssh/sshd_config kapcsolót úgy, hogy az a következő sorokat tartalmazza:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. A WALinuxAgent `WALinuxAgent-<version>`csomag , a Red Hat extra tárházba lett leküldéses. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

        # yum install WALinuxAgent

    Engedélyezze a waagent szolgáltatást:

        # systemctl enable waagent.service

1. Ne hozzon létre csereterületet az operációs rendszer lemezén.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterületet a virtuális géphez csatlakoztatott helyi erőforráslemez használatával, miután a virtuális gép ki van építve az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy ideiglenes lemez, és a virtuális gép kiürítése esetén kiüríthető. Miután telepítette az Azure Linux-ügynök az előző `/etc/waagent.conf` lépésben, módosítsa a következő paramétereket megfelelően:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Törölje az előfizetés regisztrációját (ha szükséges) a következő parancs futtatásával:

        # subscription-manager unregister

1. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Állítsa le a virtuális gépet a KVM-ben.

1. Konvertálja a qcow2 képet VHD formátumra.

> [!NOTE]
> Van egy ismert hiba qemu-img verziók >=2.2.1, hogy az eredmények egy helytelenül formázott Virtuális merevlemez. A probléma ki lett javítva a QEMU 2.6-ban. Javasoljuk, hogy használja vagy qemu-img 2.2.0 vagy alacsonyabb, vagy frissíteni 2.6 vagy magasabb. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Red Hat-alapú virtuális gép előkészítése a VMware-ből
### <a name="prerequisites"></a>Előfeltételek
Ez a szakasz feltételezi, hogy már telepített egy RHEL virtuális gépet a VMware-ben. Az operációs rendszer VMware-ben történő telepítéséről a [VMware vendég operációs rendszer telepítési útmutatójában talál részleteket.](https://partnerweb.vmware.com/GOSIG/home.html)

* A Linux operációs rendszer telepítésekor azt javasoljuk, hogy az LVM helyett szabványos partíciókat használjon, ami gyakran sok telepítés alapértelmezett. Ezzel elkerülhető, hogy az LVM-név ütközik a klónozott virtuális géppel, különösen akkor, ha hibaelhárítási célból egy operációs rendszer lemezét egy másik virtuális géphez kell csatlakoztatni. Az LVM vagy a RAID szükség esetén adatlemezeken is használható.
* Ne állítson be lapozópartíciót az operációs rendszer lemezén. Beállíthatja, hogy a Linux-ügynök hozzon létre egy cserefájlt az ideiglenes erőforráslemezen. Erről a következő lépésekben talál további információt.
* A virtuális merevlemez létrehozásakor válassza a **Virtuális lemez tárolása egyetlen fájlként**lehetőséget.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Rhel 6 virtuális gép előkészítése a VMware-ből
1. Az RHEL 6-ban a NetworkManager zavarhatja az Azure Linux-ügynököt. Távolítsa el a csomagot a következő parancs futtatásával:
   
        # sudo rpm -e --nodeps NetworkManager

1. Hozzon létre egy **nevű** fájlt az /etc/sysconfig/ könyvtárban, amely a következő szöveget tartalmazza:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. A `/etc/sysconfig/network-scripts/ifcfg-eth0` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Helyezze át (vagy távolítsa el) az udev-szabályokat, hogy elkerülje az Ethernet-csatoló statikus szabályainak generálását. Ezek a szabályok problémákat okoznak, ha egy virtuális gépet klónoz az Azure-ban vagy a Hyper-V-ben:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul a következő parancs futtatásával:

        # sudo chkconfig network on

1. Regisztrálja Red Hat-előfizetését, hogy engedélyezze a csomagok telepítését az RHEL tárházból a következő parancs futtatásával:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. A WALinuxAgent `WALinuxAgent-<version>`csomag , a Red Hat extra tárházba lett leküldéses. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. Ehhez nyissa meg `/etc/default/grub` a szövegszerkesztőben, és `GRUB_CMDLINE_LINUX` szerkesztse a paramétert. Példa:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Ez azt is biztosítja, hogy az összes konzolüzenetek küldése az első soros portra, amely segíthet az Azure-támogatás hibakeresési problémák. Ezenkívül azt javasoljuk, hogy távolítsa el a következő paramétereket:
   
        rhgb quiet crashkernel=auto
   
    Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port. Szükség esetén `crashkernel` hagyhatja a beállítást konfigurálva. Vegye figyelembe, hogy ez a paraméter 128 MB-tal vagy annál nagyobb mértékben csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét, ami kisebb virtuálisgép-méretek esetén is problémás lehet.

1. Hyper-V modulok hozzáadása az initramfokhoz:

    A `/etc/dracut.conf`szerkesztést és a következő tartalmat adja hozzá:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs újraépítése:

        # dracut -f -v

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el, ami általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sort:

    ClientAliveInterval 180

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Ne hozzon létre csereterületet az operációs rendszer lemezén.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterületet a virtuális géphez csatlakoztatott helyi erőforráslemez használatával, miután a virtuális gép ki van építve az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy ideiglenes lemez, és a virtuális gép kiürítése esetén kiüríthető. Miután telepítette az Azure Linux-ügynök az előző `/etc/waagent.conf` lépésben, módosítsa a következő paramétereket megfelelően:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Törölje az előfizetés regisztrációját (ha szükséges) a következő parancs futtatásával:

        # sudo subscription-manager unregister

1. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Állítsa le a virtuális gépet, és alakítsa át a VMDK-fájlt .vhd fájllá.

> [!NOTE]
> Van egy ismert hiba qemu-img verziók >=2.2.1, hogy az eredmények egy helytelenül formázott Virtuális merevlemez. A probléma ki lett javítva a QEMU 2.6-ban. Javasoljuk, hogy használja vagy qemu-img 2.2.0 vagy alacsonyabb, vagy frissíteni 2.6 vagy magasabb. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Rhel 7 virtuális gép előkészítése a VMware-ből
1. A `/etc/sysconfig/network` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. A `/etc/sysconfig/network-scripts/ifcfg-eth0` fájl létrehozása vagy szerkesztése, valamint a következő szöveg hozzáadása:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=igen NM_CONTROLLED=igen

1. Győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul a következő parancs futtatásával:

        # sudo systemctl enable network

1. Regisztrálja Red Hat-előfizetését, hogy engedélyezze a csomagok telepítését az RHEL tárházból a következő parancs futtatásával:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. A módosítás elvégzéséhez `/etc/default/grub` nyissa meg a szövegszerkesztőt, és szerkesztse a `GRUB_CMDLINE_LINUX` paramétert. Példa:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez a konfiguráció azt is biztosítja, hogy az összes konzolüzenet az első soros portra kerüljön, amely segítséget nyújthat az Azure-támogatásnak a hibakeresési problémák megoldásában. Emellett kikapcsolja az új RHEL 7 elnevezési konvenciókat a hálózati adapterek számára. Ezenkívül azt javasoljuk, hogy távolítsa el a következő paramétereket:
   
        rhgb quiet crashkernel=auto
   
    Grafikus és csendes boot nem hasznos a felhő környezetben, ahol azt akarjuk, hogy az összes naplót kell küldeni a soros port. Szükség esetén `crashkernel` hagyhatja a beállítást konfigurálva. Vegye figyelembe, hogy ez a paraméter 128 MB-tal vagy annál nagyobb mértékben csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét, ami kisebb virtuálisgép-méretek esetén is problémás lehet.

1. Miután befejezte `/etc/default/grub`a szerkesztést, futtassa a következő parancsot a grub konfiguráció újraépítéséhez:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Hyper-V modulok hozzáadása az initramfs-hoz.

    Szerkesztés `/etc/dracut.conf`, tartalom hozzáadása:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs újraépítése:

        # dracut -f -v

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el. Ez a beállítás általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sort:

        ClientAliveInterval 180

1. A WALinuxAgent `WALinuxAgent-<version>`csomag , a Red Hat extra tárházba lett leküldéses. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Ne hozzon létre csereterületet az operációs rendszer lemezén.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterületet a virtuális géphez csatlakoztatott helyi erőforráslemez használatával, miután a virtuális gép ki van építve az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy ideiglenes lemez, és a virtuális gép kiürítése esetén kiüríthető. Miután telepítette az Azure Linux-ügynök az előző `/etc/waagent.conf` lépésben, módosítsa a következő paramétereket megfelelően:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Ha törölni szeretné az előfizetés regisztrációját, futtassa a következő parancsot:

        # sudo subscription-manager unregister

1. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Állítsa le a virtuális gépet, és konvertálja a VMDK fájlt VHD formátumra.

> [!NOTE]
> Van egy ismert hiba qemu-img verziók >=2.2.1, hogy az eredmények egy helytelenül formázott Virtuális merevlemez. A probléma ki lett javítva a QEMU 2.6-ban. Javasoljuk, hogy használja vagy qemu-img 2.2.0 vagy alacsonyabb, vagy frissíteni 2.6 vagy magasabb. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Red Hat-alapú virtuális gép előkészítése ISO-ból automatikus kickstart fájl használatával
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Rhel 7 virtuális gép előkészítése kickstart fájlból

1.  Hozzon létre egy kickstart fájlt, amely tartalmazza a következő tartalmat, és mentse a fájlt. A kickstart telepítéssel kapcsolatos részletekért tekintse meg a [Kickstart telepítési útmutatót.](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=igen NM_CONTROLLED=igen EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Helyezze a kickstart fájlt, ahol a telepítő rendszer hozzáférhet.

1. A Hyper-V Manager ben hozzon létre egy új virtuális gépet. A **Virtuális merevlemez csatlakoztatása** lapon jelölje be a **Virtuális merevlemez csatolása később**jelölőnégyzetet, és fejezze be az Új virtuális gép varázslót.

1. Nyissa meg a virtuális gép beállításait:

    a.  Új virtuális merevlemez csatolása a virtuális géphez. Győződjön meg arról, hogy a **VHD formátum** és a **Rögzített méret lehetőséget választja.**

    b.  Csatlakoztassa a telepítési ISO-t a DVD-meghajtóhoz.

    c.  Állítsa be a BIOS-t cd-ről történő rendszerindításra.

1. Indítsa el a virtuális gépet. Amikor megjelenik a telepítési útmutató, a **Tab billentyűvel** konfigurálja a rendszerindítási beállításokat.

1. Írja `inst.ks=<the location of the kickstart file>` be a rendszerindítási beállítások végén, és nyomja le az **Enter billentyűt.**

1. Várja meg, amíg a telepítés befejeződik. Amikor elkészült, a virtuális gép automatikusan leáll. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="known-issues"></a>Ismert problémák
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Nem Hyper-V hipervizor használata esetén a Hyper-V illesztőprogram nem vehető fel a kezdeti RAM-lemezbe.

Bizonyos esetekben előfordulhat, hogy a Linux telepítők nem tartalmazzák a Hyper-V illesztőprogramjait a kezdeti RAM-lemezben (initrd vagy initramfs), kivéve, ha a Linux észleli, hogy Hyper-V környezetben fut.

Ha egy másik virtualizációs rendszert (azaz VirtualBox, Xen, stb.) használ a Linux-lemezkép előkészítéséhez, előfordulhat, hogy újra kell építenie az initrd-et annak érdekében, hogy legalább a hv_vmbus és hv_storvsc kernelmodulok elérhetők legyenek a kezdeti RAM lemezen. Ez egy ismert probléma, legalábbis a rendszerek, amelyek alapján a upstream Red Hat forgalmazás.

A probléma megoldásához adjon hyper-V modulokat az initramfokhoz, és építse újra:

A `/etc/dracut.conf`szerkesztést és a következő tartalmat adja hozzá:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Initramfs újraépítése:

        # dracut -f -v

További információt az [initramfok újjáépítéséről](https://access.redhat.com/solutions/1958)talál.

## <a name="next-steps"></a>További lépések
* Most már készen áll a Red Hat Enterprise Linux virtuális merevlemezének használatára, hogy új virtuális gépeket hozzon létre az Azure-ban. Ha ez az első alkalom, hogy feltölti a .vhd fájlt az Azure-ba, olvassa el a Linux virtuális gép létrehozása egyéni lemezről című [témakört.](upload-vhd.md#option-1-upload-a-vhd)
* A Red Hat Enterprise Linux futtatására hitelesített hipervizorokról a Red Hat weboldalán talál további [részleteket.](https://access.redhat.com/certified-hypervisors)
* Ha többet szeretne tudni az éles használatra kész RHEL BYOS-képek használatáról, látogasson el a [BYOS](../workloads/redhat/byos.md)dokumentációs oldalára.
