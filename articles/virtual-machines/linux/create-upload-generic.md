---
title: "Létrehozása és feltöltése az Azure Linux virtuális merevlemez"
description: "Ismerje meg, hogy létrehozása és feltöltése az Azure virtuális merevlemez (VHD) a Linux operációs rendszert tartalmazó."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: ccadf55c492c097ef96f25e469dbf36fc87b6102
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="information-for-non-endorsed-distributions"></a>Nem támogatott disztribúciókkal kapcsolatos tudnivalók
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Az Azure platformon SLA vonatkozik a Linux operációs rendszert futtató, csak ha egy futó virtuális gépek számára, a [által támogatott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szolgál. Az összes Linux terjesztéseket, a kép: Azure-katalógus által biztosított a szükséges konfigurációval hitelesített terjesztéseket.

* [Az Azure - Linux által támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A Microsoft Azure Linux lemezképek támogatása](https://support.microsoft.com/kb/2941892)

Azure-on futó összes terjesztéseket kell éri el a szükséges előfeltételeket kell arra, hogy megfelelően működjön azon a platformon.  Ez a cikk célja semmiképpen sem átfogó, amelyben minden terjesztési különbözik; és üzembe helyezését lehetséges, hogy akkor is, ha az megfelel minden, az alábbi feltételek továbbra is szüksége lesz jelentősen végeznünk a Linux rendszer győződjön meg arról, hogy megfelelően fut a platformon.

Az az oka, hogy azt javasoljuk, hogy az egyik megkezdése a [Azure támogatott Disztribúciókkal Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) amikor lehetséges. A következő cikkekben végigvezeti Önt a különböző hitelesített Linux terjesztéseket, az Azure által támogatott előkészítése:

* **[CentOS-alapú Disztribúciók](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Ez a cikk többi általános útmutatást a Linux-disztribúció Azure-on futó összpontosítanak.

## <a name="general-linux-installation-notes"></a>Jelzi, hogy általános Linux rendszerhez – telepítés
* A VHDX formátum nem támogatott az Azure csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelője vagy a convert-vhd-parancsmag segítségével. VirtualBox rendszer használata esetén ez azt jelenti, hogy kiválasztásával **mérete rögzített** az alapértelmezett, a lemez létrehozásakor dinamikusan kiosztott szemben.
* Azure csak az 1. generációs virtuális gépek támogatja. 1. generációs virtuális gépek VHDX-re a virtuális merevlemez formátumának és a rögzített méretű lemezekké dinamikusan bővülő válthat. De nem módosíthatja a virtuális gép generációját. További információkért lásd: [érdemes létrehozni 1 vagy 2. generációs virtuális gépek a Hyper-V?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* A VHD számára engedélyezett maximális mérete 1,023 GB.
* Ha telepíti a Linux rendszer *ajánlott* LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használja. LVM neve ütközik a klónozott virtuális gépek, így elkerülhető, különösen akkor, ha egy operációsrendszer-lemez legalább egyszer kell hibaelhárítási egy másik, azonos virtuális géphez csatlakoztatható. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezek használható.
* Kernel támogatása UDF fájlrendszerek csatlakoztatására szükség. Azure első rendszerindításkor az üzembe helyezési konfiguráció lett átadva a Linux virtuális gép keresztül UDF formátumú adathordozót, amely csatolva van a Vendég. Az Azure Linux ügynök a UDF fájlrendszerben beolvasni a konfigurációt, és helyezze üzembe a virtuális gép csatlakoztatása képesnek kell lennie.
* Linux kernel verziójánál régebbi 2.6.37 nem támogatott a Hyper-V nagyobb Virtuálisgép-méretek a. A probléma főként hatások régebbi azokat a terjesztéseket használatával a felsőbb rétegbeli Red Hat 2.6.32 kernel és javítását a RHEL 6.6 (kernel-2.6.32-504). Rendszerekre egyéni kernelek régebbi, mint 2.6.37 vagy régebbi RHEL alapú kernelek 2.6.32-504 a rendszerindító paramétert kell beállítani, mint `numa=off` a parancssori grub.conf a kernel. További információ: a Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Ne konfiguráljon egy swap partíciót az operációsrendszer-lemezképet. A Linux-ügynök beállítható úgy, hogy az ideiglenes erőforrás lemezen a lapozófájl létrehozásához.  További információk a megtalálhatók az alábbi lépéseket.
* Összes, a virtuális merevlemezeket kell rendelkeznie, amely többszörösei 1 MB méretű.

### <a name="installing-kernel-modules-without-hyper-v"></a>Hyper-V nélkül kernel-modulok telepítése
Azure fut a Hyper-V hipervizort, Linux megköveteli, hogy az egyes kernel modulok telepítve vannak-e az Azure-ban futtatásához. Ha egy virtuális Gépet, amely a Hyper-V kívül hozták létre, a Linux-telepítők nem tartalmazhatnak az illesztőprogramokat a Hyper-V a a kezdeti ramdisk (initrd vagy initramfs) kivéve, ha azt észleli, hogy fut-e egy Hyper-V környezetben. Ha egy eltérő virtualizációs rendszer (azaz Virtualbox, KVM, stb.) a Linux-lemezkép előkészítése, szükség lehet, hogy a initrd annak érdekében, hogy legalább az `hv_vmbus` és `hv_storvsc` kernel modulok érhetők el a kezdeti ramdisk.  Ez az egy ismert probléma, legalább a felsőbb rétegbeli Red Hat terjesztési alapú rendszereken.

A mechanizmus az initrd vagy initramfs kép újraépítése eltérhetnek attól függően, hogy a terjesztési. A terjesztési dokumentációt, vagy a megfelelő eljárás támogatása.  Íme egy példa, hogy a initrd használatával hogyan a `mkinitrd` segédprogram:

Először készítsen biztonsági másolatot a meglévő initrd lemezkép:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Ezután építse újra a initrd a `hv_vmbus` és `hv_storvsc` kernel modulok:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Virtuális merevlemezek átméretezése
Az Azure virtuális merevlemez képek rendelkeznie kell egy virtuális mérete 1MB igazodik.  Általában a Hyper-v-vel létrehozott virtuális merevlemezeket már igazítását megfelelően.  Ha a virtuális merevlemez nincs megfelelően igazítva, akkor az alábbihoz hasonló hibaüzenetet kaphat létrehozására tett kísérlet során egy *kép* a virtuális merevlemezről:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Ennek orvoslása érdekében méretezze át a virtuális gép vagy a Hyper-V Manager konzol használatával, vagy a [átméretezési-VHD](http://technet.microsoft.com/library/hh848535.aspx) Powershell-parancsmagot.  Ha egy Windows-környezetben nem fut majd ajánlott segítségével qemu-img (ha szükséges), és a virtuális merevlemez átméretezése.

> [!NOTE]
> Egy ismert hiba van qemu-img verzió > = 2.2.1-en, amely egy nem megfelelően formázott virtuális merevlemez eredményez. A hiba kijavítása QEMU 2.6. Javasoljuk, hogy használja a qemu-img 2.2.0 vagy alsó, vagy frissítse a 2.6 vagy újabb rendszerre. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Közvetlenül eszközökkel például a virtuális merevlemez átméretezése `qemu-img` vagy `vbox-manage` azt eredményezheti, egy indíthatatlanná VHD-t.  Ezért javasoljuk, hogy első convert a VHD-fájlt nyers lemezképet.  Ha a Virtuálisgép-lemezkép már létre lett hozva nyers lemezképet (az egyes Hipervizorok, például a KVM alapértelmezett), majd előfordulhat, hogy kihagyja ezt a lépést:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. A lemezképet, győződjön meg arról, hogy a virtuális mérete 1 MB-nál igazítva van-e a szükséges méretének kiszámításához.  A következő bash héjparancsfájlt ezzel segít.  A parancsfájl a "`qemu-img info`" határozza meg a virtuális méretet a lemezképet, majd kiszámítja a következő 1 MB méretű:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. A nyers $rounded_size használja, mint a fenti szkript beállított lemez átméretezése:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Most, a nyers lemez konvertálása vissza egy rögzített méretű virtuális merevlemez:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Vagy a verziójával qemu **2.6 +** közé tartozik a `force_size` lehetőséget:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Linux Kernel követelmények
A Linux integrációs szolgáltatások (LIS) illesztőprogramokat, a Hyper-V és az Azure közvetlenül a felsőbb rétegbeli Linux kernel vannak hozzájárult. A Linux kernel közelmúltban (azaz 3.x) tartalmazó terjesztések ezeket az illesztőprogramokat elérhető már rendelkezik, vagy ellenkező esetben adja meg ezeket az illesztőprogramokat backported verziói a kernelek a.  Ezeket az illesztőprogramokat folyamatosan változik a felsőbb rétegbeli kernel az új javításokat és szolgáltatásokat, amikor lehetséges futtatásához célszerű egy [által támogatott terjesztési](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , amely tartalmazza ezeket a javításokat és frissítéseit.

Ha futtatja a Red Hat Enterprise Linux-verziók variant **6.0-6.3**, akkor kell a Hyper-V legújabb LIS illesztőprogramok telepítését. Az illesztőprogramok található [ezen a helyen](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). RHEL frissítésétől **6.4 +** (és származékai) a LIS már már tartalmazza a kernel, és ezért nincs további telepítési csomag Azure ezekhez a rendszerekhez futtatásához szükséges.

Ha egy egyéni kernel szükség, javasoljuk, hogy újabb verziójára kernel használja (azaz **3.8 +**). Ezeket a felosztás vagy szállítók, akik a saját kernel karbantartása, néhány elérhető lesz szükséges rendszeresen backport a LIS-illesztőprogramok a felsőbb rétegbeli kernel az egyéni kernel a.  Akkor is, ha már viszonylag új kernel-verziót futtat, lehetőleg nyomon követéséhez a LIS illesztőprogramok és backport bármely felsőbb rétegbeli javítások azokat szükség szerint. A LIS illesztőprogram forrásfájljainak helyét érhető el a [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) fájlt a Linux kernel forrás fában:

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

Egy nagyon minimális a következő javítások hiányában az előzőekben problémákat okozhat az Azure-on és így ezek szerepelnie kell a kernel. Ebben a listában, de semmiképpen sem teljes körű teljes biztosít az összes disztribúciók számára:

* [ata_piix: késlelteti a lemezeket a Hyper-V illesztők alapértelmezés szerint](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: fiókot használja az átvitel közbeni csomagok a VISSZAÁLLÍTÁSI elérési úton](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: WRITE_SAME használatának elkerülése érdekében](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: RAID és virtuális állomás adapter illesztőprogramjai azonos írási letiltása](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL mutatót javítás kereshet vissza.](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: ring pufferbe hibákat eredményezhet i/o-rögzítés](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: __scsi_remove_device dupla végrehajtásának elleni védelem érdekében](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Az Azure Linux-ügynök
A [Azure Linux ügynök](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (waagent) szükséges megfelelően kiépítéséhez Linux virtuális gépek Azure-ban. Első a legújabb verzióra, a fájl problémákat, vagy a lekérési kérelmek elküldése a [Linux-ügynök GitHub-tárház](https://github.com/Azure/WALinuxAgent).

* A Linux-ügynök az Apache 2.0 licence szabadul fel. Terjesztések már meg RPM vagy deb csomagok az ügynök, és így egyes esetekben ez telepíthetők és minimális erőfeszítéssel frissítése.
* Az Azure Linux ügynök szükséges Python v2.6 +.
* Az ügynök a python-pyasn1 modul is szükséges. A legtöbb terjesztéseket adja meg, ez különálló csomagként telepíthető.
* Bizonyos esetekben az Azure Linux ügynök nem lehet NetworkManager kompatibilis. A felosztás által biztosított RPM/Deb-csomagok számos NetworkManager beállítása ütközés waagent-csomagot, és így is el fogja távolítani NetworkManager telepít a Linux-ügynök csomagja.

## <a name="general-linux-system-requirements"></a>Általános Linux rendszerre vonatkozó követelmények

* A kernel rendszerindító sor LÁRVAJÁRAT vagy GRUB2 tartalmazza a következő paraméterek módosítása. Ez biztosítja az összes konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Ez biztosítja az összes konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására.
  
    A fentiek mellett javasoljuk, hogy *eltávolítása* ha vannak ilyenek. a következő paraméterekkel:
  
        rhgb quiet crashkernel=auto
  
    Grafikus és a csendes rendszerindító egy felhőalapú környezetben, ahol azt szeretnénk, hogy minden a naplókat a soros port küldendő nem hasznosak. A `crashkernel` beállítás lehet, hogy szükség esetén konfigurálva balra, de vegye figyelembe, hogy ez a paraméter csökkenti a virtuális gépen legalább 128 MB, rendelkezésre álló memória mennyisége kisebb Virtuálisgép-méretek a problematikus esetleg.

* Az Azure Linux ügynök telepítése
  
    Az Azure Linux ügynök a Linux-lemezkép az Azure-on történő üzembe helyezéséhez szükséges.  Terjesztések egy RPM vagy Deb-csomagot (a csomag neve általában "WALinuxAgent" vagy "walinuxagent"), adja meg az ügynök.  Az ügynök is telepítheti manuálisan lépéseit a [Linux-ügynök útmutató](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához.  Ez általában az alapértelmezett beállítás.

* Ne hozzon létre lapozóterület meg az operációsrendszer-lemez
  
    Az Azure Linux ügynök automatikusan konfigurálhatók a lapozóterület használata a helyi erőforrás lemezt a virtuális Géphez csatolt Azure kiépítése után. Vegye figyelembe, hogy a helyi erőforrás lemez egy *ideiglenes* lemezre, és előfordulhat, hogy szerepelnek, ha a virtuális gép van platformelőfizetés. Az Azure Linux ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a /etc/waagent.conf a következő paraméterekkel:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* Utolsó lépésként, a virtuális gép kiosztásának megszüntetése a következő parancsok futtatásával:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > A Virtualbox jelenhetnek meg a következő hiba futtatása után "waagent-force - deprovision": `[Errno 5] Input/output error`. Ez a hibaüzenet nem fontos, és figyelmen kívül lesz hagyva.
  > 
  > 

* Szüksége lesz majd, állítsa le a virtuális gépet, és a virtuális merevlemez feltöltése az Azure-bA.

