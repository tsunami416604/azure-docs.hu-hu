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
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 17b4df83b141d5365a8d6244c4ab73b0eba5ed73
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972349"
---
# <a name="information-for-non-endorsed-distributions"></a>Nem támogatott disztribúciókkal kapcsolatos tudnivalók
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Az Azure platform a Linux operációs rendszer csak akkor, ha egy futó virtuális gépek garantált szolgáltatási SZINTJEI érvényesek, a [által támogatott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szolgál. A támogatott disztribúciókról a Linux-rendszerképeket az Azure piactéren, a szükséges konfigurációval szerepelnek.

* [A Linuxon futó Azure - által támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A Microsoft Azure Linux-lemezképeinek támogatását](https://support.microsoft.com/kb/2941892)

Az Azure-ban minden disztribúcióján használhatók meg kell felelniük az előfeltételnek kipróbálja a platform megfelelően fusson.  Ez a cikk nincs semmiképpen sem teljes körű, mivel minden terjesztési különböző; és lehetséges, hogy még akkor is, ha az összes alábbi feltétel megfelel, módosítania kell jelentősen a Linux rendszer annak érdekében, hogy megfelelően fut a platformon.

Ebből az okból, azt javasoljuk, hogy először a szolgáltatást egy [Linux az Azure által támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) amikor csak lehetséges. A következő cikk végigvezeti az Azure-ban támogatott a különböző támogatott Linux disztribúciókról előkészítése:

* **[CentOS-alapú Disztribúciókon](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Ez a cikk további részének útmutatásra van szüksége az Azure-ban futó Linux-disztribúció összpontosít.

## <a name="general-linux-installation-notes"></a>Általános Linux telepítési jegyzetek
* A VHDX formátum nem támogatott az Azure-ban, csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelőjével vagy a convert-vhd-parancsmag használatával. VirtualBox használatakor ez azt jelenti, hogy kiválasztja **rögzített méretű** dinamikusan kiosztott lemez létrehozásakor az alapértelmezett helyett.
* Az Azure csak az 1. generációs virtuális gépeket támogatja. 1. generációs virtuális gép és a rögzített méretű lemezekké dinamikusan növekvő VHDX-re a VHD-fájl formátumát származó alakíthatók. De nem módosíthatja a virtuális gép generációját. További információkért lásd: [kell hozhatok létre 1 vagy 2. generációs virtuális gépek a Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* A VHD számára engedélyezett maximális mérete 1,023 GB.
* A Linux rendszer telepítése esetén *ajánlott* LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciókat használni. LVM neve nem felel meg a klónozott virtuális gépeket, így elkerülhető, különösen akkor, ha minden eddiginél kell operációsrendszer-lemezt egy másik, azonos virtuális Géphez van csatlakoztatva a hibaelhárításhoz. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) használnak, az adatlemezeket.
* Kernel támogatása csatlakoztatni UDF fájlrendszerek megadása kötelező. Első rendszerindításkor az Azure-ban az üzembe helyezési konfiguráció átadódik a Linux rendszerű virtuális gép UDF-formátumú adathordozót, amely csatolva van a Vendég-n keresztül. Az Azure Linux-ügynök csatlakoztatása az UDF-fájlrendszer, olvassa el a konfigurációját, és a virtuális gép képesnek kell lennie.
* Linux-kernel verziók alatt 2.6.37 támogatott Hyper-v, nagyobb Virtuálisgép-mérettel. A probléma elsősorban hatással van a felsőbb rétegbeli használó régebbi disztribúciók Red Hat 2.6.32 kernel, és kijavítottuk RHEL 6.6 (kernel-2.6.32-504). Rendszert futtató egyéni 2.6.37 régebbi kernelekkel, vagy régebbi RHEL-alapú kernelekkel 2.6.32-504 a rendszerindító paramétert kell beállítani, mint `numa=off` a parancssori grub.conf a kernel. További információ: Red Hat [KB-os 436883](https://access.redhat.com/solutions/436883).
* Az operációsrendszer-lemez nem konfigurál egy lapozó partíciót. A Linux-ügynök beállítható úgy, hogy hozzon létre egy ideiglenes erőforrás lemezen a lapozófájl.  További információ található a következő lépéseket.
* Az Azure-ban minden virtuális merevlemezek rendelkeznie kell egy virtuális méret 1 MB igazítva. A virtuális merevlemez nyers lemezről történő konvertálása során biztosítania kell, hogy a nyers lemez mérete nagyobb-e az átalakítás előtt 1MB többszöröse. További információ található a következő lépéseket.

### <a name="installing-kernel-modules-without-hyper-v"></a>A Hyper-V nélkül kernel-modulok telepítése
Azure-t a Hyper-V hipervizort, így Linux megköveteli, hogy az egyes kernel modulok telepítve vannak-e ahhoz, hogy az Azure-ban. Ha egy virtuális Gépet, amely a Hyper-V-en kívül lett létrehozva, a Linux-telepítők előfordulhat, hogy nem tartalmaznak az illesztőprogramok a Hyper-V a kezdeti ramdisk (initrd vagy initramfs), kivéve, ha azt észleli, hogy fut a Hyper-V környezetben. A különböző virtualizálási rendszerek (vagyis a Virtualbox, KVM, stb.) a Linux-rendszerképek előkészítése használatakor szükség lehet építse újra az initrd annak érdekében, hogy legalább az `hv_vmbus` és `hv_storvsc` kernel-modulok elérhetők a kezdeti ramdisk a.  Ez az egy ismert probléma, legalább a felsőbb rétegbeli terjesztési Red Hat-alapú rendszereken.

A mechanizmus az initrd vagy initramfs rendszerkép újraépítése a terjesztési függvénye. Tekintse át a disztribúció dokumentációjában vagy a támogatási a megfelelő eljárást.  Íme egy példa, hogy hogyan építse újra az initrd használatával a `mkinitrd` segédprogrammal:

Először készítsen biztonsági másolatot a meglévő initrd-lemezképet:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Ezután építse újra az initrd-a `hv_vmbus` és `hv_storvsc` kernel modulok:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Virtuális merevlemezek átméretezése
Azure-beli virtuális Merevlemezek lemezképeihez rendelkeznie kell egy virtuális méret 1 MB igazítva.  Általában a Hyper-V használatával létrehozott virtuális merevlemezek már össze kell hangolni megfelelően.  Ha a virtuális merevlemez nincs megfelelően igazítva, a következőhöz hasonló hibaüzenet jelenhet meg, amikor megpróbál létrehozni egy *kép* a VHD-ből:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Orvoslása érdekében ez a viselkedés, méretezze át a virtuális gép vagy a Hyper-V Manager konzol használatával, vagy a [átméretezése-VHD](http://technet.microsoft.com/library/hh848535.aspx) Powershell-parancsmagot.  Ha nem fut a Windows-környezetben, javasoljuk, hogy qemu-img konvertálása (ha szükséges), és a virtuális merevlemez átméretezése.

> [!NOTE]
> Egy ismert hiba tapasztalható qemu-img verzió > = 2.2.1-es, amely egy helytelenül formázott virtuális merevlemez eredményez. A hiba elhárítása QEMU 2.6. Ajánlott qemu-img 2.2.0 vagy alacsonyabb, vagy az 2.6 vagy újabb verziójára frissíteni. Referencia: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. A VHD-t közvetlenül használni például az eszközök átméretezése `qemu-img` vagy `vbox-manage` eredményezhet egy indíthatatlanná válik VHD-t.  Ezért javasoljuk, hogy első convert a VHD-nyers lemezképét.  Ha a Virtuálisgép-lemezkép már létre lett hozva nyers lemezképe (például KVM-mel egyes Hipervizorok alapértelmezett), majd, kihagyhatja ezt a lépést:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. A lemezképet, győződjön meg arról, hogy teljesíti-e a virtuális méret 1 MB szükséges méretének kiszámításához.  Az alábbi bash-héjparancsfájlt ennek végrehajtásában.  A szkript "`qemu-img info`" a lemezkép virtuális méretének meghatározásához, majd kiszámítja a mérete, a következő 1 MB-ra:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Méretezze át a nyers lemez, ahogyan az a fenti szkript $rounded_size használatával:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Mostantól a nyers lemez konvertálása vissza egy rögzített méretű virtuális merevlemez:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Vagy qemu verziójával **2.6** közé tartozik a `force_size` lehetőséget:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Linux-Kernel-követelmények
A Linux Integration Services (LIS) illesztőprogramokat, a Hyper-V és az Azure közvetlenül a felsőbb szintű Linux-kernel van hozzájárult. Legutóbbi Linux-Kernelverzió (azaz 3.x) tartalmazó terjesztések már elérhető illesztőprogramok, vagy ellenkező esetben biztosítson backported verziói ezeket az illesztőprogramokat a kernelt.  Ezeket az illesztőprogramokat folyamatosan változik a felsőbb rétegbeli kernel, az új javításokat és szolgáltatásokat, így lehetséges esetén ajánlott futtatni egy [által támogatott terjesztési](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , amely tartalmazza ezeket a javításokat és frissítéseket.

Ha futtatja a Red Hat Enterprise Linux verziók variant **6.0-6.3**, akkor a Hyper-V LIS legújabb illesztőprogramjait telepítenie kell. Az illesztőprogramok találhatók [ezen a helyen](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). RHEL kezdődően **6.4 +** (és származékai) LIS illesztőprogramokat a kernel már megtalálhatók, és ezért nincs további telepítési csomag az Azure-ban ezek a rendszerek futtatásához szükséges.

Ha egy egyéni kernel szükség, javasoljuk, hogy egy újabb rendszermag használja (azaz **3.8 +**). Ezeket a disztribúciók vagy szállítók, akik a saját rendszermag fenntartása, az egyes erőfeszítés nem rendszeresen backport a LIS kell az egyéni kernel a felsőbb rétegbeli kernel származó illesztőprogramok.  Akkor is, ha már futtat egy viszonylag új kernel verziója, erősen ajánlott nyomon követéséhez a LIS illesztőprogramok és backport felsőbb rétegbeli megoldások megjelenésekor azokat, igény szerint. Az LIS illesztőprogram forrásfájljainak helyét érhető el a [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) fájlt a Linux kernel forrás fa:

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

Minimális hiányában a következő javításokat az ismert problémákat okozhat az Azure-ban, és így ezek szerepelnie kell a kernel. Ez a lista semmiképpen sem teljes körű, vagy minden disztribúcióján használhatók befejeződött:

* [ata_piix: alapértelmezés szerint a Hyper-V illesztőprogramok lemezek késleltetése](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: fiók számára az átvitel közbeni csomagokat a VISSZAÁLLÍTÁSI elérési úton](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: WRITE_SAME használatának elkerülése](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: RAID és a virtuális gazdagép adapter illesztőprogramjai azonos írási letiltása](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL mutató visszakeresési javítás](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: kör puffer hibákat eredményezhet i/o-rögzíteni](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: dupla végrehajtásának __scsi_remove_device elleni védelem](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Az Azure Linux-ügynök
A [Azure Linux-ügynök](../extensions/agent-linux.md) (waagent) megfelelően az Azure-beli Linuxos virtuális gép kiépítéséhez szükséges. A legújabb verzióra, a file hibáinak beolvasása, vagy a pull-kérelmek elküldéséhez a [Linux-ügynök GitHub-adattárat](https://github.com/Azure/WALinuxAgent).

* A Linux-ügynök jelent meg az Apache 2.0 licenc. Terjesztések már meg RPM- vagy deb csomagok az ügynök számára, és így egyes esetekben ez telepíthetők és minimális erőfeszítéssel frissítve.
* Az Azure Linux-ügynök szükséges Python v2.6 +.
* Az ügynök a python-pyasn1 modul is szükséges. A legtöbb disztribúciók adja meg a különálló csomagként telepíthető.
* Bizonyos esetekben az Azure Linux-ügynök nem lehet NetworkManager kompatibilis. Az RPM-vagy Deb-csomagokat, disztribúció által biztosított számos NetworkManager konfigurálja a waagent csomaghoz ütközést, és így eltávolítja NetworkManager a Linux-ügynök csomag telepítésekor.
* Az Azure Linux-ügynök kell lennie a minimális támogatott verziónál újabb lásd a jelen cikk [részletek](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Általános Linux rendszerre vonatkozó követelmények

* Módosítsa a rendszermag rendszerindítási sorához grub-HIBÁT vagy GRUB2 az alábbi paramétereket tartalmazza. Ez is biztosítja, hogy a konzol összes üzenetet kapnak-e az első soros porthoz, amely segítheti az Azure által támogatott hibaelhárítás során:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Ez is biztosítja, hogy a konzol összes üzenetet kapnak-e az első soros porthoz, amely segítheti az Azure támogatási problémák hibakeresésében.
  
    A fentiek mellett javasoljuk, hogy *eltávolítása* Ha léteznek az alábbi paraméterekkel:
  
        rhgb quiet crashkernel=auto
  
    Grafikus és a csendes rendszerindító nem hasznos egy felhőalapú környezetben, ahol szeretnénk a soros port kell küldeni a naplókat. A `crashkernel` beállítás lehet bal konfigurálva, ha szükséges, de vegye figyelembe, hogy ez a paraméter csökkenti az elérhető memória a virtuális gépen legalább 128 MB, amely a kisebb Virtuálisgép-méretek problémás lehet.

* Az Azure Linux-ügynök telepítése
  
    Az Azure Linux-ügynök üzembe helyezés az Azure-on Linux-rendszerképen megadása kötelező.  Terjesztések csomagként az RPM- vagy Deb (a csomag neve általában "WALinuxAgent" vagy "walinuxagent") adja meg az ügynök.  Az ügynök emellett manuálisan is telepíthető a lépéseket követve a [Linux-ügynök útmutató](../extensions/agent-linux.md).

* Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindítás elindításához.  Ez általában az alapértelmezett érték.

* Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez
  
    Az Azure Linux-ügynök automatikusan konfigurálhatják a lapozóterület használata a helyi erőforrás-lemez, amely az Azure-ban üzembe helyezés után a virtuális Géphez van csatlakoztatva. A helyi erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy ki kell üríteni, ha a virtuális gép. Az Azure Linux-ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a következő paramétereket lévő /etc/waagent.conf:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* Utolsó lépésként futtassa az alábbi parancsokat a virtuális gép megszüntetése:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > A Virtualbox a következő hiba jelenhet Futtatás után "waagent-force - megszüntetési": `[Errno 5] Input/output error`. Ez a hibaüzenet nem kritikus fontosságú, és figyelmen kívül hagyható.
  > 
  > 

* Állítsa le a virtuális gépet, és töltse fel a VHD-t az Azure-bA.

