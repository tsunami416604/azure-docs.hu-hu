---
title: Linuxos virtuális merevlemez létrehozása és feltöltése
description: Ismerje meg, hogy hozzon létre és töltsön fel egy Azure virtuális merevlemezt (VHD), amely egy Linux operációs rendszert tartalmaz.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: f700dec6486bad9e7024d7c908a70dd0ff2b342c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066763"
---
# <a name="information-for-non-endorsed-distributions"></a>A nem jóváhagyott felosztásokra vonatkozó információk

Az Azure platform SLA csak akkor vonatkozik a Linux operációs rendszert futtató virtuális gépekre, ha a [jóváhagyott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) egyikét használják. Ezek a jóváhagyott disztribúciók előre konfigurált Linux-lemezképek az Azure Marketplace-en.

* [Linux az Azure-ban - Jóváhagyott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linuxos lemezképek támogatása a Microsoft Azure-ban](https://support.microsoft.com/kb/2941892)

Az Azure-on futó összes disztribúciónak számos előfeltétele van. Ez a cikk nem lehet átfogó, mivel minden disztribúció más. Még akkor is, ha megfelel az alábbi kritériumoknak, előfordulhat, hogy jelentősen módosítania kell a Linux rendszert, hogy megfelelően működjön.

Azt javasoljuk, hogy kezdje az Egyik Linux az [Azure által támogatott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az alábbi cikkek bemutatják, hogyan készítheti el az Azure-ban támogatott különböző jóváhagyott Linux-disztribúciákat:

* **[CentOS-alapú disztribúciók](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Ez a cikk az Azure-beli Linux-disztribúció futtatásához adott általános útmutatásra összpontosít.

## <a name="general-linux-installation-notes"></a>Általános Linux telepítési megjegyzések
* A Hyper-V virtuális merevlemez (VHDX) formátumnem támogatott az Azure-ban, csak *a rögzített virtuális merevlemez.*  A lemezt A Hyper-V Manager vagy a [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) parancsmag segítségével virtuális merevlemez-formátumra konvertálhatja. A VirtualBox használata esetén a lemez létrehozásakor válassza a **Rögzített méret** lehetőséget az alapértelmezett (dinamikusan lefoglalva) helyett.
* Az Azure támogatja a Gen1 (BIOS-rendszerindítás) & Gen2 (UEFI rendszerindítás) virtuális gépek.
* A virtuális merevlemez maximális mérete 1023 GB.
* A Linux rendszer telepítésekor azt javasoljuk, hogy a logikai kötetkezelő (LVM) helyett szabványos partíciókat használjon, ami sok telepítés alapértelmezett beállítása. A szabványos partíciók használatával elkerülhető lvm-név ütközik a klónozott virtuális gépek, különösen akkor, ha egy operációs rendszer lemez valaha is csatlakozik egy másik azonos virtuális gép hibaelhárításhoz. [Az LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [a RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezeken is használható.
* Kernel támogatása szerelési UDF fájlrendszerek szükséges. Az Azure-ban a kiépítési konfiguráció első indításkor a linuxos virtuális gép a vendéghez csatlakoztatott UDF-formátumú adathordozóhasználatával kerül átadásra. Az Azure Linux-ügynök csatlakoztatnia kell az UDF fájlrendszert a konfiguráció olvasásához és a virtuális gép kiépítéséhez.
* Linux kernel verziók korábbi, mint 2.6.37 nem támogatja a NUMA a Hyper-V nagyobb virtuális gép mérete. Ez a probléma elsősorban az upstream Red Hat 2.6.32 kernelt használó régebbi disztribúciákat érinti, és a Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504) verzióban lett rögzítve. A 2.6.37-nél régebbi egyéni kerneleket vagy 2.6.32-504-nél régebbi RHEL-alapú kerneleket futtató rendszereknek be kell állítaniuk a boot paramétert `numa=off` a grub.conf kernel parancssorában. További információ: [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Ne konfiguráljon lapozópartíciót az operációs rendszer lemezén. A Linux-ügynök beállítható úgy, hogy az ideiglenes erőforráslemezen hozzon létre egy lapozófájlt, a következő lépéseknek megfelelően.
* Az Azure-beli virtuális hálózat minden virtuális d-jének 1 MB-hoz igazított virtuális mérettel kell rendelkeznie. Nyers lemezről virtuális merevlemezre történő konvertáláskor győződjön meg arról, hogy a nyers lemez mérete a konvertálás előtt 1 MB többszöröse, a következő lépésekben leírtak szerint.

### <a name="installing-kernel-modules-without-hyper-v"></a>Kernel modulok telepítése Hyper-V nélkül
Az Azure a Hyper-V hipervizoron fut, ezért a Linux bizonyos kernelmodulok futtatásához az Azure-ban. Ha a Virtuális gép a Hyper-V-n kívül lett létrehozva, előfordulhat, hogy a Linux-telepítők nem tartalmazzák a Hyper-V illesztőprogramjait a kezdeti ramdiskben (initrd vagy initramfs), kivéve, ha a virtuális gép észleli, hogy Hyper-V környezetben fut. Ha egy másik virtualizációs rendszer (például VirtualBox, KVM, és így tovább), hogy előkészítse a Linux-lemezkép, előfordulhat, hogy újra kell építeni az initrd, hogy legalább a hv_vmbus és hv_storvsc kernel modulok állnak rendelkezésre a kezdeti ramdisk.  Ez az ismert probléma az upstream Red Hat disztribúción alapuló rendszerekre, és esetleg másokra is.

Az initrd vagy initramfs kép újjáépítésének mechanizmusa a disztribúciótól függően változhat. A megfelelő eljárásról a disztribúció dokumentációjában vagy támogatásban olvashat.  Íme egy példa az initrd újjáépítésére `mkinitrd` a segédprogram használatával:

1. A meglévő initrd kép biztonsági másolatot kell kapnia:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Építse újra az initrdet a hv_vmbus és hv_storvsc kernelmodulokkal:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Virtuális gépek átméretezése
Az Azure-beli virtuális merevlemez-lemezképeknek 1 MB-hoz igazított virtuális mérettel kell rendelkezniük.  A Hyper-V használatával létrehozott Virtuális központi és gépi azonosítók általában megfelelően vannak igazítva.  Ha a virtuális merevlemez nincs megfelelően igazítva, a következőhöz hasonló hibaüzenet jelenhet meg, amikor megpróbál létrehozni egy képet a virtuális merevlemezről.

* A Virtuális merevlemez\//\<http: mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd nem támogatott virtuális mérete 21475270656 bájt. A méretnek egész számnak kell lennie (MU-kban).

Ebben az esetben méretezze át a virtuális gép segítségével vagy a Hyper-V Manager konzol vagy a [Resize-VHD](https://technet.microsoft.com/library/hh848535.aspx) PowerShell-parancsmag használatával.  Ha nem Windows-környezetben fut, azt `qemu-img` javasoljuk, hogy (ha szükséges) konvertáljon és méretezze át a virtuális merevlemezt.

> [!NOTE]
> Van egy [ismert hiba qemu-img](https://bugs.launchpad.net/qemu/+bug/1490611) verziók >=2.2.1, hogy az eredmények egy helytelenül formázott Virtuális merevlemez. A probléma ki lett javítva a QEMU 2.6-ban. Javasoljuk a `qemu-img` 2.2.0 vagy az alacsonyabb, illetve a 2.6 vagy újabb használatát.
> 

1. A virtuális merevlemez átméretezése közvetlenül `vbox-manage` olyan eszközökkel, például, vagy `qemu-img` vezethet egy nem indítható virtuális merevlemez.  Javasoljuk, hogy először konvertálja a virtuális merevlemezt RAW lemezképpé.  Ha a virtuálisgép-lemezképet RAW lemezképként hozták létre (ez az alapértelmezett érték néhány hipervizor, például a KVM esetében), akkor kihagyhatja ezt a lépést.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Számítsa ki a lemezkép szükséges méretét úgy, hogy a virtuális méret 1 MB-ra legyen igazítva.  A következő bash `qemu-img info` shell parancsfájl segítségével határozza meg a virtuális mérete a lemezkép, majd kiszámítja a méretet a következő 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Méretezze át `$rounded_size` a nyers lemezt a fent meghatározott módon.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Most konvertálja vissza a RAW lemezt rögzített méretű virtuális merevlemezre.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Vagy, a qemu változat 2.6 `force_size` +, tartalmazza a lehetőséget.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux kernel követelmények

A Hyper-V és az Azure Linux integrációs szolgáltatások (LIS) illesztőprogramjai közvetlenül hozzájárulnak a upstream Linux kernelhez. Számos disztribúció, amely tartalmazza a legújabb Linux kernel verzió (például 3.x) rendelkezik ezekkel az illesztőprogramokkal már elérhető, vagy más módon biztosítbackported változatai ezen illesztőprogramok a kernelek.  Ezek az illesztőprogramok folyamatosan frissülnek az upstream kernelben új javításokkal és funkciókkal, ezért ha lehetséges, javasoljuk, hogy futson egy [jóváhagyott disztribúciót,](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) amely tartalmazza ezeket a javításokat és frissítéseket.

Ha a Red Hat Enterprise Linux 6.0-6.3-as verzióját futtatja, akkor telepítenie kell a [Hyper-V legújabb LIS-illesztőprogramjait.](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409) Az RHEL 6.4+ (és származékai) kezdetével a LIS illesztőprogramok már szerepelnek a kernelben, így nincs szükség további telepítőcsomagokra.

Ha egyéni rendszermagra van szükség, javasoljuk a legújabb kernelverziót (például 3.8+). A saját rendszermagot fenntartó disztribúciók vagy szállítók esetében rendszeresen vissza kell portolnia a LIS-illesztőprogramokat az upstream kernelről az egyéni kernelre.  Még akkor is, ha már egy viszonylag új kernel verziót futtat, javasoljuk, hogy kövesse nyomon a LIS-illesztőprogramok upstream javításait, és szükség szerint visszaportálja őket. A LIS illesztőprogram forrásfájljainak helyei a Linux kernel forrásfájában található [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) fájlban vannak megadva:
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
A következő javításokat kell tartalmaznia a kernelben. Ez a lista nem lehet teljes minden disztribúcióesetében.

* [ata_piix: alapértelmezés szerint a lemezeket a Hyper-V-illesztőprogramok között elhalasztja](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: A visszanem küldött csomagok fiókja a RESET elérési úton](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: ne használjon WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Disable WRITE SAME raid és virtuális gazdaadapter illesztőprogramok](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL mutató dereference fix](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: a gyűrűpuffer meghibásodása I/O-lefagyást eredményezhet](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: véd a kettős végrehajtás __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Az Azure Linux ügynök
Az [Azure Linux-ügynök](../extensions/agent-linux.md) `waagent` egy Linux virtuális gépet az Azure-ban. Beszerezheti a legújabb verziót, a fájlproblémákat, vagy lekéréses kérelmeket küldhet a [Linux Agent GitHub tártárán.](https://github.com/Azure/WALinuxAgent)

* A Linux ügynök az Apache 2.0 licenc alatt jelenik meg. Számos disztribúció már biztosít RPM vagy .deb csomagokat az ügynök számára, és ezek a csomagok könnyen telepíthetők és frissíthetők.
* Az Azure Linux-ügynök python-v2.6+-t igényel.
* Az ügynök is megköveteli a python-pyasn1 modul. A legtöbb disztribúció külön csomagként biztosítja ezt a modult.
* Bizonyos esetekben előfordulhat, hogy az Azure Linux-ügynök nem kompatibilis a NetworkManager. A disztribúciók által biztosított RPM/deb csomagok közül sok a NetworkManagert a waagent csomag ütközéseként konfigurálja. Ezekben az esetekben eltávolítja a NetworkManager-t a Linux-ügynökcsomag telepítésekor.
* Az Azure Linux-ügynöknek a [minimálisan támogatott verzióval](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)vagy annál magasabbnak kell lennie.

## <a name="general-linux-system-requirements"></a>Általános Linux rendszerkövetelmények

1. Módosítsa a GRUB vagy A GRUB2 kernel rendszerindító sorát úgy, hogy az tartalmazza a következő paramétereket, hogy az összes konzolüzenet az első soros portra kerülhessen. Ezek az üzenetek segítséget nyújthatnak az Azure-támogatásnak a problémák hibakeresésében.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Azt is *javasoljuk, hogy távolítsa el* a következő paramétereket, ha léteznek.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafikus és csendes rendszerindítás nem hasznos egy felhőkörnyezetben, ahol azt akarjuk, hogy minden naplót küldeni a soros portra. A `crashkernel` beállítás szükség esetén konfigurálható maradhat, de vegye figyelembe, hogy ez a paraméter legalább 128 MB-tal csökkenti a virtuális gép ben rendelkezésre álló memória mennyiségét, ami a kisebb virtuálisgép-méretek számára problémát okozhat.

1. Telepítse az Azure Linux-ügynök.
  
    Az Azure Linux-ügynök az Azure-on egy Linux-lemezkép kiépítéséhez szükséges.  Számos disztribúció rpm vagy .deb csomagként biztosítja az ügynököt (a csomagot általában WALinuxAgent vagy walinuxagent-nek nevezik).  Az ügynök manuálisan is telepíthető a [Linux Agent Guide](../extensions/agent-linux.md)lépéseit követve.

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el.  Ez a konfiguráció általában az alapértelmezett.

1. Ne hozzon létre csereterületet az operációs rendszer lemezén.
  
    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterület a helyi erőforráslemez, amely csatlakozik a virtuális géphez kiépítése után az Azure-ban. A helyi erőforráslemez egy *ideiglenes* lemez, és lehet, hogy kiüríti, ha a virtuális gép kiürül. Az Azure Linux-ügynök telepítése után (a fenti 2. lépés) szükség szerint módosítsa a következő paramétereket az /etc/waagent.conf fájlban.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Futtassa a következő parancsokat a virtuális gép kiirtásához.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > A Virtualbox lehet látni a `waagent -force -deprovision` következő `[Errno 5] Input/output error`hiba futtatása után, hogy azt mondja . Ez a hibaüzenet nem kritikus, ezért figyelmen kívül hagyható.

* Állítsa le a virtuális gépet, és töltse fel a virtuális merevlemezt az Azure-ba.

