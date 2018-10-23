---
title: Az Azure-ban egy Linux VHD létrehozása és feltöltése
description: Ismerje meg, hozhat létre és töltse fel az Azure virtuális merevlemez (VHD), amely egy Linux operációs rendszert tartalmazza.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: szark
ms.openlocfilehash: 67796cc3cbb925bb18a917d17b8abb7c085de370
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638201"
---
# <a name="information-for-non-endorsed-distributions"></a>Információk a által támogatott Disztribúciók
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Az Azure platform a Linux operációs rendszer csak akkor, ha egy futó virtuális gépek garantált szolgáltatási SZINTJEI érvényesek, a [által támogatott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szolgál. A támogatott disztribúciókról az előre konfigurált Linux-rendszerképeket az Azure Marketplace-en szerepelnek.

* [A Linuxon futó Azure - által támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A Microsoft Azure Linux-lemezképeinek támogatását](https://support.microsoft.com/kb/2941892)

Az Azure-ban minden disztribúcióján használhatók előfeltételnek kell. Ez a cikk átfogó, nem lehet, mert minden terjesztési eltér. Akkor is, ha az összes alábbi feltétel megfelel, akkor előfordulhat, hogy módosítania kell jelentősen megfelelően fusson, a Linux rendszer.

Azt javasoljuk, hogy az egyik megkezdése a [Linux az Azure által támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A következő cikkek bemutatják, hogyan készíti elő az Azure-on támogatott a különböző támogatott Linux disztribúciókról:

* **[CentOS-alapú Disztribúciókon](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Ez a cikk általános útmutatást az Azure-ban futó Linux-disztribúció összpontosít.

## <a name="general-linux-installation-notes"></a>Általános Linux telepítési jegyzetek
* A Hyper-V virtuális merevlemez (VHDX) formátum nem támogatott az Azure-ban, csak *rögzített VHD*.  A lemez konvertálása Hyper-V kezelője segítségével VHD formátumú, vagy a [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) parancsmagot. Ha VirtualBox használ, válassza ki a **rögzített méretű** ahelyett, hogy az alapértelmezett (dinamikusan kiosztott) a lemez létrehozásakor.
* Az Azure csak az 1. generációs virtuális gépeket támogatja. 1. generációs virtuális gép a VHDX-re a VHD-fájl formátumát és a rögzített méretű lemezekké dinamikusan bővülő alakíthatja. Egy virtuálisgép-generáció nem módosítható. További információkért lásd: [kell hozhatok létre 1 vagy 2. generációs virtuális gépek a Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* A VHD számára engedélyezett maximális mérete 1,023 GB.
* Az javasoljuk, hogy használjon standard partíciók ahelyett, hogy logikai kötet-kezelő (LVM) sok telepítés az alapértelmezett Linux rendszer telepítésekor. Standard partíciók használatával a rendszer működésében LVM neve nem felel meg a klónozott virtuális gépeket, különösen akkor, ha egy operációsrendszer-lemez minden eddiginél egy másik azonos virtuális Géphez van csatolva a hibaelhárításhoz. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) használnak, az adatlemezeket.
* Kernel támogatása csatlakoztatni UDF fájlrendszerek szükség. Első rendszerindításkor az Azure-ban az üzembe helyezési konfiguráció átadódik a Linux rendszerű virtuális gép, amely csatolva van a Vendég UDF-formátumú adathordozó használatával. Az Azure Linux-ügynök csatlakoztatnia kell az UDF-fájlrendszer, olvassa el a konfigurációját, és a virtuális gép.
* Linux-kernel-verzióknál korábbi, mint a 2.6.37 nem támogatják a NUMA Hyper-v, nagyobb Virtuálisgép-mérettel. A probléma elsősorban hatással van a felsőbb rétegbeli használó régebbi disztribúciók Red Hat 2.6.32 kernel, és a Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504) lett kijavítva. Rendszert futtató egyéni 2.6.37 régebbi kernelekkel, vagy régebbi RHEL-alapú kernelekkel 2.6.32-504 a rendszerindító paramétert kell beállítani, mint `numa=off` grub.conf a kernel parancssorában. További információkért lásd: [Red Hat KB-os 436883](https://access.redhat.com/solutions/436883).
* Ne konfigurálja a lapozó partíció az operációsrendszer-lemez. A Linux-ügynök konfigurálható az ideiglenes erőforrás lemezen a lapozófájl létrehozásához a következő lépésben ismertetett módon.
* Az Azure-ban minden virtuális merevlemezek rendelkeznie kell egy virtuális méret 1 MB igazítva. A virtuális merevlemez nyers lemezről történő konvertálása során biztosítania kell, hogy a nyers lemezméret az átalakítás előtt 1 MB többszöröse-e a következő lépésben ismertetett módon.

### <a name="installing-kernel-modules-without-hyper-v"></a>A Hyper-V nélkül kernel-modulok telepítése
Azure-t a Hyper-V hipervizort, így Linux szükséges bizonyos kernel-modulok futtatását az Azure-ban. Ha egy virtuális Gépet, amely a Hyper-V-en kívül lett létrehozva, a Linux-telepítők előfordulhat, hogy nem tartalmaznak az illesztőprogramokat a Hyper-V (initrd vagy initramfs), a kezdeti ramdisk, ha nem észlel a virtuális gép fut-e a Hyper-V környezetben. Egy másik virtualizálási rendszerek (például Virtualbox KVM és így tovább) a Linux-rendszerképek előkészítése használatakor szükség lehet úgy építse újra az initrd címen, amely legalább a hv_vmbus, hv_storvsc és kernel-modulok elérhetők a kezdeti ramdisk meg.  Ez ismert probléma van a felsőbb rétegbeli Red Hat-terjesztés, és esetleg mások alapján rendszerekhez.

A mechanizmus az initrd vagy initramfs rendszerkép újraépítése a terjesztési függvénye. Tekintse át a disztribúció dokumentációjában vagy a támogatási a megfelelő eljárást.  Íme egy példa a használatával az initrd újjáépítését a `mkinitrd` segédprogrammal:

1. Készítsen biztonsági másolatot a meglévő initrd-lemezképet:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Építse újra az initrd hv_vmbus, hv_storvsc és a kernel-modulokkal:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Virtuális merevlemezek átméretezése
Azure-beli virtuális Merevlemezek lemezképeihez rendelkeznie kell egy virtuális méret 1 MB igazítva.  Általában megfelelően igazodnak a Hyper-V használatával létrehozott virtuális merevlemezeket.  A VHD-t a nincs megfelelően igazítva, ha egy a következőhöz hasonló hibaüzenet jelenhet meg, amikor megpróbál létrehozni egy képet a VHD-ből.

* A virtuális merevlemez http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd rendelkezik egy nem támogatott virtuális méret 21475270656 bájt. A mérete (a MB-ban) egész számnak kell lennie.

Ebben az esetben méretezze át a virtuális gép vagy a Hyper-V Manager konzol használatával, vagy a [átméretezése-VHD](http://technet.microsoft.com/library/hh848535.aspx) PowerShell-parancsmagot.  Ha nem Windows-környezetben, azt javasoljuk, `qemu-img` átalakítása (ha szükséges), és méretezze át a VHD-t.

> [!NOTE]
> Van egy [ismert hiba az qemu-img](https://bugs.launchpad.net/qemu/+bug/1490611) verzió > = 2.2.1-es, amely egy helytelenül formázott virtuális merevlemez eredményez. A hiba elhárítása QEMU 2.6. Javasoljuk `qemu-img` 2.2.0 vagy alacsonyabb, vagy 2.6-os vagy újabb.
> 

1. A VHD-t közvetlenül használni például az eszközök átméretezése `qemu-img` vagy `vbox-manage` eredményezhet egy indíthatatlanná válik VHD-t.  Azt javasoljuk, hogy először a virtuális merevlemez átalakítása nyers lemezképét.  Ha a Virtuálisgép-lemezkép (például KVM-mel egyes hipervizorok alapértelmezett) nyers lemez rendszerkép lett létrehozva, majd, kihagyhatja ezt a lépést.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. A lemezkép szükséges méretének kiszámítása úgy, hogy a virtuális méret 1 MB-ra van igazítva.  Az alábbi bash-shell script használ `qemu-img info` határozza meg a virtuális méretet, a lemezképet, majd kiszámítja a mérete, a következő 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$((($size/$MB + 1)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Méretezze át a nyers lemezek használatával `$rounded_size` fenti készletként.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Mostantól a nyers lemez konvertálása vissza egy rögzített méretű VHD-t.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Vagy qemu verziójú 2.6, például a `force_size` lehetőséget.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux-Kernel-követelmények

A Linux Integration Services (LIS) illesztőprogramokat, a Hyper-V és az Azure közvetlenül a felsőbb szintű Linux-kernel van hozzájárult. Terjesztések, amelyek tartalmazzák a legutóbbi Linux-Kernelverzió (például 3.x) már elérhető ezeket az illesztőprogramokat, vagy ellenkező esetben biztosítson backported verziói ezeket az illesztőprogramokat a kernelt.  Ezeket az illesztőprogramokat folyamatosan változik a felsőbb rétegbeli kernel, az új javításokat és szolgáltatásokat, ezért javasoljuk, ha lehetséges fut egy [által támogatott terjesztési](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , amely tartalmazza ezeket a javításokat és frissítéseket.

Ha egy Red Hat Enterprise Linux verziók 6.0-6.3-változatot futtatja, akkor telepítenie kell a [Hyper-V LIS legújabb illesztőprogramjait](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). 6.4 + RHEL (és származékai) kezdve a LIS illesztőprogramokat a kernel már megtalálhatók, és ezért semmilyen további telepítési csomagok szükségesek.

Ha egy egyéni kernel szükség, azt javasoljuk egy újabb rendszermag-verzió (például a 3.8-as +). Disztribúciók vagy szállítók, akik a saját rendszermag fenntartása meg kell rendszeresen backport LIS illesztőprogramokat a felsőbb rétegbeli kernel, az egyéni kernel.  Akkor is, ha egy viszonylag új kernel verziója már használ, erősen ajánlott szerinti nyomon követést bármely felső javítja a LIS illesztőprogramok és backport őket igény szerint. A helyek LIS illesztőprogram forrásfájljainak vannak megadva a [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) fájlt a Linux kernel forrás fa:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
A következő javításokat a kernel kell szerepelnie. Ez a lista nem lehet minden disztribúcióján használhatók befejeződött.

* [ata_piix: alapértelmezés szerint a Hyper-V illesztőprogramok lemezek késleltetése](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: fiók számára az átvitel közbeni csomagokat a VISSZAÁLLÍTÁSI elérési úton](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: WRITE_SAME használatának elkerülése](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: RAID és a virtuális gazdagép adapter illesztőprogramjai azonos írási letiltása](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL mutató visszakeresési javítás](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: kör puffer hibákat eredményezhet i/o-rögzíteni](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: dupla végrehajtásának __scsi_remove_device elleni védelem](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Az Azure Linux-ügynök
A [Azure Linux-ügynök](../extensions/agent-linux.md) `waagent` látja el az Azure-beli Linuxos virtuális gép. A legújabb verzióra, a file hibáinak beolvasása, vagy a pull-kérelmek elküldéséhez a [Linux-ügynök GitHub-adattárat](https://github.com/Azure/WALinuxAgent).

* A Linux-ügynök jelent meg az Apache 2.0 licenc. Terjesztések már meg RPM- vagy deb csomagok az ügynök számára, és ezeket a csomagokat könnyen telepíthető és frissíteni.
* Az Azure Linux-ügynök szükséges Python v2.6 +.
* Az ügynök a python-pyasn1 modul is szükséges. A legtöbb disztribúciók telepíteni különálló csomagként adja meg a modult.
* Bizonyos esetekben az Azure Linux-ügynök nem lehet NetworkManager kompatibilis. A waagent csomaghoz ütközés NetworkManager számos disztribúció által biztosított az RPM-vagy Deb-csomagok konfigurálása. Ezekben az esetekben NetworkManager azt fogja eltávolítani a Linux-ügynök csomag telepítésekor.
* Az Azure Linux-ügynök kell lennie, vagy annál magasabb a [minimális támogatott verziója](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Általános Linux rendszerre vonatkozó követelmények

1. Módosítsa a rendszermag rendszerindítási sorához grub-HIBÁT vagy GRUB2 az alábbi paramétereket tartalmazza, úgy, hogy az első soros port küldött összes konzol üzeneteket. Ezeket az üzeneteket is segítséget nyújt az Azure által támogatott problémákat hibakeresés.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Javasoljuk továbbá *eltávolítása* az alábbi paramétereket, ha vannak ilyenek.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafikus és a csendes rendszerindító nem hasznos a felhőalapú környezetekben, ahol szeretnénk a soros port küldött az összes napló. A `crashkernel` beállítás lehet, hogy a bal oldalon szükség esetén konfigurálva, de vegye figyelembe, hogy ez a paraméter csökkenti a virtuális gépen legalább 128 MB, esetleg problémát észlel, kisebb Virtuálisgép-méretek szerint rendelkezésre álló memória mennyisége.

2. Az Azure Linux-ügynök telepítése.
  
    Az Azure Linux-ügynök üzembe helyezés az Azure-on Linux-rendszerképen megadása kötelező.  Terjesztések csomagként az RPM- vagy Deb (a csomag neve általában WALinuxAgent vagy walinuxagent) adja meg az ügynök.  Az ügynök emellett manuálisan is telepíthető a lépéseket követve a [Linux-ügynök útmutató](../extensions/agent-linux.md).

3. Győződjön meg arról, hogy az SSH-kiszolgáló van telepítve, és rendszerindítás közben beállítva.  Ez a konfiguráció általában az alapértelmezett érték.

4. Lapozófájl-kapacitás az operációsrendszer-lemez ne hozzon létre.
  
    Az Azure Linux-ügynök automatikusan konfigurálhatják a lapozóterület használata a helyi erőforrás-lemez, amely az Azure-ban üzembe helyezés után a virtuális Géphez van csatlakoztatva. A helyi erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy ki kell üríteni, ha a virtuális gép. Miután telepítette az Azure Linux-ügynök (2. lépés fent), szükség szerint módosítsa a következő paramétereket lévő /etc/waagent.conf.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
* Futtassa az alábbi parancsokat a virtuális gép megszüntetéséhez.
  
    ```
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```  
  > [!NOTE]
  > A Virtualbox a következő hiba jelenhet Futtatás után `waagent -force -deprovision` szerint `[Errno 5] Input/output error`. Ez a hibaüzenet nem kritikus fontosságú, és figyelmen kívül hagyható.

* Állítsa le a virtuális gépet, és töltse fel a VHD-t az Azure-bA.

