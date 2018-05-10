---
title: Hozzon létre, és töltse fel a Red Hat Enterprise Linux virtuális merevlemez Azure verem használható |} Microsoft Docs
description: Ismerje meg, létrehozása és feltöltése az Azure virtuális merevlemez (VHD), amely tartalmazza a Red Hat Linux operációs rendszert.
services: azure-stack
documentationcenter: ''
author: JeffGoldner
manager: BradleyB
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: jeffgo
ms.openlocfilehash: d7b79962822dcfec96c7087100b1537d8046ab46
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>Red Hat-alapú virtuális gép előkészítése Azure verem
Ebben a cikkben, megtudhatja, hogyan készíti elő a Red Hat Enterprise Linux (RHEL) virtuális gépek Azure-verem használható. Ebben a cikkben ismertetett RHEL verziók az alábbiak 7.1 +. A hipervizorok előkészítése az ebben a cikkben ismertetett a Hyper-V, a kernel-alapú virtuális gép (KVM), és a VMware.

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>A Hyper-V kezelőjéből a Red Hat-alapú virtuális gép előkészítése

### <a name="prerequisites"></a>Előfeltételek
Jelen szakaszban feltételezzük, hogy már egy ISO-fájlt kapott a Red Hat webhelyéről és az RHEL kép telepítése virtuális merevlemezre (VHD). Az operációs rendszer lemezképének telepítése Hyper-V kezelőjének használatával kapcsolatos további részletekért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gépek](http://technet.microsoft.com/library/hh846766.aspx).

**RHEL telepítési megjegyzések**

* Az Azure verem nem támogatja a VHDX formátumú. Az Azure által támogatott csak rögzített VHD-t. Hyper-V kezelője segítségével konvertálja a lemezt VHD formátumú, vagy a convert-vhd parancsmagot használhatja. Ha VirtualBox használja, jelölje be **mérete rögzített** figyelésekor az alapértelmezett beállítás dinamikusan lefoglalt, a lemez létrehozásakor.
* Az Azure verem csak az 1. generációs virtuális gépek támogatja. 1. generációs virtuális gépek VHDX-re a virtuális merevlemez formátumának és a rögzített méretű lemezekké dinamikusan bővülő válthat. Nem módosíthatja a virtuális gép generációját. További információkért lásd: [érdemes létrehozni 1 vagy 2. generációs virtuális gépek a Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* A maximális engedélyezett a virtuális merevlemez mérete 1,023 GB.
* A Linux operációs rendszer telepítésekor azt javasoljuk, hogy standard partíciók helyett használ logikai kötet Manager (LVM), amelyek gyakran sok telepítés az alapértelmezett beállítás. Ezzel a gyakorlattal elkerülheti LVM neve ütközik a klónozott virtuális gépek, különösen akkor, ha a hibaelhárítási egy másik virtuális gép operációsrendszer-lemez csatolása átállítására lenne szükség.
* Univerzális lemez formátum (UDF) fájlrendszerek csatlakoztatására kernel támogatására szükség. Az Azure-on első rendszerindításkor az UDF-formátumú adathordozót, amely csatolva van a Vendég továbbítja a létesítési konfiguráció a Linux virtuális gép. Az Azure Linux ügynök csatlakoztatása az UDF fájlrendszer beolvasni a konfigurációt, és a virtuális gép kiépítéséhez képesnek kell lennie.
* A Linux kernel 2.6.37 verziónál korábbi verziói nem támogatják nem egységes memóriaelérés (NUMA) a Hyper-V a nagyobb virtuális gépek méretét. A probléma főként hatások régebbi azokat a terjesztéseket, amelyek használják a felsőbb rétegbeli Red Hat 2.6.32 kernel és javítását a RHEL 6.6 (kernel-2.6.32-504). Egyéni kernelek futtatják, amelyek régebbiek 2.6.37 vagy régebbi 2.6.32-504 RHEL-alapú kernelek kell állítani a `numa=off` paraméter rendszerindító a kernel parancssorában grub.conf. További információkért tekintse meg a Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Ne konfiguráljon egy swap partíció az operációs rendszer tárolására. A Linux-ügynök beállítható úgy, hogy az ideiglenes erőforrás lemezen a lapozófájl létrehozásához.  További információ található a következő lépések.
* Minden, az Azure virtuális merevlemez rendelkeznie kell egy virtuális mérete 1MB igazodik. Virtuális merevlemez egy nyers lemezen történő átalakítása meg kell győződnie arról, hogy a nyers lemez mérete 1MB átalakítás előtti többszöröse. További részletek találhatók az alábbi lépéseket.
* Az Azure verem nem támogatja a felhő inicializálás. Virtuális gép meg kell konfigurálni a Windows Azure Linux ügynök (WALA) támogatott verziója.

### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>A Hyper-V kezelőjéből a RHEL 7 virtuális gép előkészítése

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Kattintson a **Connect** nyissa meg a konzol ablakot a virtuális gép számára.

3. Hozzon létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Hozzon létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    NM_CONTROLLED = nem

5. Győződjön meg arról, hogy a hálózati szolgáltatás rendszerindításkor indul a következő parancs futtatásával:

        # sudo systemctl enable network

6. Regisztrálja a Red Hat előfizetés engedélyezése az RHEL tárházból csomagok telepítése a következő parancs futtatásával:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Módosítsa a kernel rendszerindító sor lárvajárat konfigurációs kiegészítő rendszermag paraméterek az Azure-bA felvenni. Ez a módosítás végrehajtásához nyissa meg a `/etc/default/grub` egy szövegszerkesztőben, és a Szerkesztés a `GRUB_CMDLINE_LINUX` paraméter. Példa:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez biztosítja, hogy minden konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására. Ez a konfiguráció is kikapcsolja új RHEL 7 elnevezési szabályai a hálózati adapterek. Emellett javasoljuk, hogy távolítsa el a következő paraméterekkel:
   
        rhgb quiet crashkernel=auto
   
    Grafikus és a csendes rendszerindító egy felhőalapú környezetben, ahol azt szeretnénk, hogy minden a naplókat a soros port küldendő nem hasznosak. Ha elhagyja a `crashkernel` beállítás konfigurálva, ha szükséges. Vegye figyelembe, hogy ez a paraméter csökkenti a virtuális gépen legalább 128 MB, rendelkezésre álló memória mennyisége kisebb virtuálisgép-méretek a problematikus amely lehet.

8. Miután befejezte az szerkesztési `/etc/default/grub`, lárvajárat konfigurációját, hogy a következő parancsot:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9. Győződjön meg arról, hogy az SSH-kiszolgálót telepítve és konfigurálva van, amely általában az alapértelmezett rendszerindítás elindításához. Módosítsa `/etc/ssh/sshd_config` tartalmazza a következő sort:

        ClientAliveInterval 180

10. A WALinuxAgent csomag `WALinuxAgent-<version>`, rendelkezik lett leküldött a Red Hat kiegészítő funkciók tárházba. A kiegészítő funkciók tárház engedélyezése a következő parancs futtatásával:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Az Azure Linux ügynök telepítése a következő parancs futtatásával:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

12. Ne hozzon létre az operációs rendszer lemezen lévő lapozóterület.

    Az Azure Linux ügynök a helyi erőforrás lemez a virtuális géphez csatolt Azure virtuális gép kiépítése után segítségével automatikusan konfigurálhatók a lapozóterület. Vegye figyelembe, hogy a helyi erőforrás egy ideiglenes lemez, és előfordulhat, hogy lehet üríteni, amikor a virtuális gép van platformelőfizetés. Miután telepítette az Azure Linux ügynök az előző lépésben, módosítsa a következő paramétereket a `/etc/waagent.conf` megfelelően:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Ha azt szeretné, az előfizetés regisztrációját, a következő parancsot:

        # sudo subscription-manager unregister

14. A virtuális gép kiosztásának megszüntetése, és előkészíti az Azure-on történő üzembe helyezéséhez a következő parancsok futtatásával:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Kattintson a **művelet** > **leállítása** a Hyper-V kezelőjében. A Linux virtuális merevlemez az Azure-bA feltölteni kívánt készen áll.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Készítse elő a Red Hat-alapú virtuális gépek KVM a

### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>A KVM RHEL 7 virtuális gép előkészítése

1. Töltse le az RHEL 7 KVM képe a Red Hat webhelyről. Ez az eljárás RHEL 7 használja a példaként.

2. Legfelső szintű jelszót állíthat be.

    Egy titkosított jelszót létrehozni, és másolja a parancs:

        # openssl passwd -1 changeme

    Állítsa be a guestfish egy gyökérszintű jelszót:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   A második mező a legfelső szintű felhasználó módosítása "!" a titkosított jelszót.

3. Hozzon létre egy virtuális gép KVM qcow2 lemezképről. A lemez típusa **qcow2**, és a virtuális hálózati illesztő eszközmodell **virtio**. Ezután indítsa el a virtuális gépet, és jelentkezzen be rendszergazdaként.

4. Hozzon létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Hozzon létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

6. Győződjön meg arról, hogy a hálózati szolgáltatás rendszerindításkor indul a következő parancs futtatásával:

        # sudo systemctl enable network

7. Regisztrálja a Red Hat előfizetés engedélyezhető az RHEL összetevőtárházat-csomagok telepítése a következő parancs futtatásával:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8. Módosítsa a kernel rendszerindító sor lárvajárat konfigurációs kiegészítő rendszermag paraméterek az Azure-bA felvenni. A konfiguráció végrehajtását, nyissa meg a `/etc/default/grub` egy szövegszerkesztőben, és a Szerkesztés a `GRUB_CMDLINE_LINUX` paraméter. Példa:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez a parancs is biztosítja, hogy minden konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására. A parancs is kikapcsolja új RHEL 7 elnevezési szabályai a hálózati adapterek. Emellett javasoljuk, hogy távolítsa el a következő paraméterekkel:
   
        rhgb quiet crashkernel=auto
   
    Grafikus és a csendes rendszerindító egy felhőalapú környezetben, ahol azt szeretnénk, hogy minden a naplókat a soros port küldendő nem hasznosak. Ha elhagyja a `crashkernel` beállítás konfigurálva, ha szükséges. Vegye figyelembe, hogy ez a paraméter csökkenti a virtuális gépen legalább 128 MB, rendelkezésre álló memória mennyisége kisebb virtuálisgép-méretek a problematikus amely lehet.

9. Miután befejezte az szerkesztési `/etc/default/grub`, lárvajárat konfigurációját, hogy a következő parancsot:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Vegyen fel Hyper-V-modulokkal initramfs.

    Szerkesztés `/etc/dracut.conf` , és adja hozzá a tartalomhoz:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Építse újra initramfs:

        # dracut -f -v

11. Távolítsa el a felhő inicializálás:

        # yum remove cloud-init

12. Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához:

        # systemctl enable sshd

    Módosítsa a /etc/ssh/sshd_config tartalmazza a következő sorokat:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. A WALinuxAgent csomag `WALinuxAgent-<version>`, rendelkezik lett leküldött a Red Hat kiegészítő funkciók tárházba. A kiegészítő funkciók tárház engedélyezése a következő parancs futtatásával:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Az Azure Linux ügynök telepítése a következő parancs futtatásával:

        # yum install WALinuxAgent

    A waagent szolgáltatás engedélyezése:

        # systemctl enable waagent.service

15. Ne hozzon létre az operációs rendszer lemezen lévő lapozóterület.

    Az Azure Linux ügynök a helyi erőforrás lemez a virtuális géphez csatolt Azure virtuális gép kiépítése után segítségével automatikusan konfigurálhatók a lapozóterület. Vegye figyelembe, hogy a helyi erőforrás egy ideiglenes lemez, és előfordulhat, hogy lehet üríteni, amikor a virtuális gép van platformelőfizetés. Miután telepítette az Azure Linux ügynök az előző lépésben, módosítsa a következő paramétereket a `/etc/waagent.conf` megfelelően:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Az előfizetés regisztrációját (ha szükséges) a következő parancs futtatásával:

        # subscription-manager unregister

17. A virtuális gép kiosztásának megszüntetése, és előkészíti az Azure-on történő üzembe helyezéséhez a következő parancsok futtatásával:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Állítsa le a virtuális gép KVM.

19. Alakítsa át a qcow2 képet a VHD formátummal.

> [!NOTE]
> Egy ismert hiba van qemu-img verzió > = 2.2.1-en, amely egy nem megfelelően formázott virtuális merevlemez eredményez. A hiba kijavítása QEMU 2.6. Javasoljuk, hogy használja a qemu-img 2.2.0 vagy alsó, vagy frissítse a 2.6 vagy újabb rendszerre. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>A VMware Red Hat-alapú virtuális gép előkészítése
### <a name="prerequisites"></a>Előfeltételek
Ez a szakasz azt feltételezi, hogy még telepítette RHEL virtuális gép VMware-ben. VMware-ben az operációs rendszer telepítésével kapcsolatos részletekért lásd: [VMware vendég operációs rendszer telepítési útmutató](http://partnerweb.vmware.com/GOSIG/home.html).

* A Linux operációs rendszer telepítésekor azt javasoljuk, hogy standard partíciók helyett használ LVM, amelyek gyakran sok telepítés az alapértelmezett beállítás. LVM neve ütközik a klónozott virtuális gépet, így elkerülhető, különösen akkor, ha egy operációsrendszer-lemez legalább egyszer kell hibaelhárítási egy másik virtuális géphez csatlakoztatható. LVM vagy RAID használható adatlemezek Ha előnyben részesített.
* Ne konfiguráljon egy swap partíció az operációs rendszer tárolására. Konfigurálhatja a Linux-ügynök ideiglenes erőforrás lemezen a lapozófájl létrehozásához. További információ található a következő lépések a.
* A virtuális merevlemez létrehozásakor válassza ki a **tároló virtuális lemez egyetlen fájlként**.


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>A VMware RHEL 7 virtuális gép előkészítése
1. Hozzon létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2. Hozzon létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

3. Győződjön meg arról, hogy a hálózati szolgáltatás rendszerindításkor indul a következő parancs futtatásával:

        # sudo chkconfig network on

4. Regisztrálja a Red Hat előfizetés engedélyezése az RHEL tárházból csomagok telepítése a következő parancs futtatásával:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5. Módosítsa a kernel rendszerindító sor lárvajárat konfigurációs kiegészítő rendszermag paraméterek az Azure-bA felvenni. Ez a módosítás végrehajtásához nyissa meg a `/etc/default/grub` egy szövegszerkesztőben, és a Szerkesztés a `GRUB_CMDLINE_LINUX` paraméter. Példa:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ez a konfiguráció is biztosítja, hogy minden konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására. Azt is kikapcsolja új RHEL 7 elnevezési szabályai a hálózati adapterek. Emellett javasoljuk, hogy távolítsa el a következő paraméterekkel:
   
        rhgb quiet crashkernel=auto
   
    Grafikus és a csendes rendszerindító egy felhőalapú környezetben, ahol azt szeretnénk, hogy minden a naplókat a soros port küldendő nem hasznosak. Ha elhagyja a `crashkernel` beállítás konfigurálva, ha szükséges. Vegye figyelembe, hogy ez a paraméter csökkenti a virtuális gépen legalább 128 MB, rendelkezésre álló memória mennyisége kisebb virtuálisgép-méretek a problematikus amely lehet.

6. Miután befejezte az szerkesztési `/etc/default/grub`, lárvajárat konfigurációját, hogy a következő parancsot:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7. Adja hozzá a Hyper-V-modulokkal initramfs.

    Szerkesztés `/etc/dracut.conf`, adja hozzá a tartalomhoz:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Építse újra initramfs:

        # dracut -f -v

8. Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához. Ez a beállítás általában az alapértelmezett beállítás. Módosítsa `/etc/ssh/sshd_config` tartalmazza a következő sort:

        ClientAliveInterval 180

9. A WALinuxAgent csomag `WALinuxAgent-<version>`, rendelkezik lett leküldött a Red Hat kiegészítő funkciók tárházba. A kiegészítő funkciók tárház engedélyezése a következő parancs futtatásával:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Az Azure Linux ügynök telepítése a következő parancs futtatásával:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

11. Ne hozzon létre az operációs rendszer lemezen lévő lapozóterület.

    Az Azure Linux ügynök a helyi erőforrás lemez a virtuális géphez csatolt Azure virtuális gép kiépítése után segítségével automatikusan konfigurálhatók a lapozóterület. Vegye figyelembe, hogy a helyi erőforrás egy ideiglenes lemez, és előfordulhat, hogy lehet üríteni, amikor a virtuális gép van platformelőfizetés. Miután telepítette az Azure Linux ügynök az előző lépésben, módosítsa a következő paramétereket a `/etc/waagent.conf` megfelelően:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Ha azt szeretné, az előfizetés regisztrációját, a következő parancsot:

        # sudo subscription-manager unregister

13. A virtuális gép kiosztásának megszüntetése, és előkészíti az Azure-on történő üzembe helyezéséhez a következő parancsok futtatásával:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

14. Állítsa le a virtuális gépet, és a VMDK-fájl átalakítása a VHD formátummal.

> [!NOTE]
> Egy ismert hiba van qemu-img verzió > = 2.2.1-en, amely egy nem megfelelően formázott virtuális merevlemez eredményez. A hiba kijavítása QEMU 2.6. Javasoljuk, hogy használja a qemu-img 2.2.0 vagy alsó, vagy frissítse a 2.6 vagy újabb rendszerre. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Készítse elő a Red Hat-alapú virtuális gép ISO-Lemezképet a automatikusan kickstart fájl használatával
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Kickstart fájlból RHEL 7 virtuális gép előkészítése

1.  Hozzon létre egy kickstart fájlt, amely tartalmazza a következő tartalmat, és mentse a fájlt. Kickstart telepítésével kapcsolatos részletekért lásd: a [Kickstart a telepítési útmutató](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2. Helyezze a kickstart fájlt, ahol a telepítés rendszer tudja-e érni.

3. A Hyper-V kezelőjében hozzon létre egy új virtuális gépet. Az a **virtuális merevlemez csatlakoztatása** lapon jelölje be **virtuális merevlemez csatlakoztatása később**, és fejezze be az új virtuális gép varázsló.

4. Nyissa meg a virtuális gép beállításait:

    a.  Egy új virtuális merevlemez virtuális géphez csatolása. Ügyeljen arra, hogy válasszon **VHD formátumú** és **rögzített méretű**.

    b.  A telepítés ISO csatolja a DVD-meghajtóhoz.

    c.  Állítsa be a BIOS rendszerindítási CD-ről.

5. Virtuális gép elindítása. Amikor megjelenik a telepítési útmutatóban, nyomja meg **lapon** a rendszerindítási beállításokat.

6. Adjon meg `inst.ks=<the location of the kickstart file>` az indítási beállítások, és nyomja le az végén **Enter**.

7. Várjon, amíg a telepítés befejezéséhez. Amikor elkészült, a program automatikusan leállítani a virtuális gép. A Linux virtuális merevlemez az Azure-bA feltölteni kívánt készen áll.

## <a name="known-issues"></a>Ismert problémák
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>A Hyper-V illesztőprogram sikerült sem szerepelhet a kezdeti RAM-lemez nem Hyper-V hipervizort használatakor

Bizonyos esetekben Linux telepítők előfordulhat, hogy nem szerepel az illesztőprogramok a Hyper-V a kezdeti RAM-lemez (initrd vagy initramfs) kivéve, ha a Linux észleli, hogy fut-e a Hyper-V környezetben.

Ha egy eltérő virtualizációs rendszer (Ez azt jelenti, hogy Virtualbox, Xen, stb.) a Linux-lemezkép előkészítése használata esetén szükség lehet annak érdekében, hogy legalább initrd, hogy a hv_vmbus és hv_storvsc kernel modulok érhetők el a kezdeti RAM lemezen. Ez az egy ismert probléma, legalább a felsőbb rétegbeli Red Hat terjesztési alapuló rendszereken.

A probléma megoldásához, Hyper-V-modulokkal hozzáadása initramfs, és azt újjáépítenie:

Szerkesztés `/etc/dracut.conf`, és adja hozzá a következőket:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

Építse újra initramfs:

        # dracut -f -v

További részletekért olvassa el [initramfs újraépítése](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>További lépések
Most már készen áll a Red Hat Enterprise Linux virtuális merevlemez használatával hozzon létre új virtuális gépek Azure-készletben. Ha ez történik, hogy a .vhd fájl feltöltendő Azure verem először, [a piactér eszközkészlet segítségével létrehozása és közzététele a Piactéri elemek](azure-stack-marketplace-publisher.md).

A Red Hat Enterprise Linux futtatásához hitelesített hipervizorok kapcsolatos további tudnivalókért lásd: [a Red Hat webhely](https://access.redhat.com/certified-hypervisors).
