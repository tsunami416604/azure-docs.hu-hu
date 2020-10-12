---
title: Linux rendszerű virtuális gép optimalizálása az Azure-ban
description: Ismerkedjen meg az optimalizálási tippekkel, hogy megbizonyosodjon arról, hogy beállította-e a linuxos virtuális gépet az Azure-beli optimális teljesítmény érdekében
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: eff512c9d050eb293391233848fcece83e845680
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654191"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Linux rendszerű virtuális gép optimalizálása az Azure-ban
A linuxos virtuális gép (VM) létrehozása a parancssorból vagy a portálról egyszerű. Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a teljesítményét a Microsoft Azure platform teljesítményének optimalizálása érdekében. Ez a témakör egy Ubuntu Server-alapú virtuális gépet használ, de [a saját rendszerképeit sablonként](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használva is létrehozhatja.  

## <a name="prerequisites"></a>Előfeltételek
Ez a témakör feltételezi, hogy már rendelkezik egy működő Azure-előfizetéssel ([ingyenes próbaverziós regisztrációval](https://azure.microsoft.com/pricing/free-trial/)), és már üzembe helyezett egy virtuális gépet az Azure-előfizetésében. A [virtuális gép létrehozása](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)előtt győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van és be van jelentkezve az Azure-előfizetésbe az az [login](/cli/azure/reference-index) használatával.

## <a name="azure-os-disk"></a>Azure operációsrendszer-lemez
Miután létrehozta a Linux rendszerű virtuális gépet az Azure-ban, két lemezzel van társítva. a **/dev/sda** az operációs rendszer lemeze, a **/dev/sdb** az ideiglenes lemez.  Ne használja a fő operációsrendszer-lemezt (**/dev/sda**), kivéve az operációs rendszert, mivel a virtuális gép rendszerindítási idejére van optimalizálva, és nem biztosít megfelelő teljesítményt a munkaterhelések számára. Egy vagy több lemezt szeretne csatlakoztatni a virtuális géphez, hogy állandó és optimalizált tárterületet kapjon az adatai számára. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Lemezek hozzáadása a méret és a teljesítmény céljaihoz
A virtuális gép mérete alapján akár 16 további lemezt is csatlakoztathat egy sorozatú, D sorozatú, 32-es és 64-es lemezeken egy G sorozatú gépen, amelyek mindegyike akár 32 TB méretű is lehet. A tárhely-és IOps-követelményekhez szükség szerint további lemezeket is hozzáadhat. Az egyes lemezek 500 IOps, a standard szintű tároláshoz és a Premium Storage IOps legfeljebb 20 000 rendelkeznek.

Annak érdekében, hogy Premium Storage lemezek legmagasabb IOps legyenek elérhetők, ahol a gyorsítótár beállításai **readonly** vagy **none**értékre lettek állítva, le kell tiltania a **korlátokat** , miközben a fájlrendszert a Linux rendszerhez csatlakoztatja. Nincs szükség korlátokra, mert a lemezre Premium Storage lemezek írása a gyorsítótár-beállítások számára tartós.

* Ha a **reiserFS**-t használja, tiltsa le a korlátozásokat a csatlakoztatási lehetőség használatával `barrier=none` (az akadályok engedélyezéséhez `barrier=flush` )
* Ha **ext3/ext4**-t használ, tiltsa le a korlátozásokat a csatlakoztatási lehetőség használatával `barrier=0` (az akadályok engedélyezéséhez `barrier=1` )
* Ha **XFS**használ, tiltsa le a korlátozásokat a csatlakoztatási lehetőséggel `nobarrier` (a korlátok engedélyezéséhez használja a kapcsolót `barrier` ).

## <a name="unmanaged-storage-account-considerations"></a>Nem felügyelt Storage-fiókok szempontjai
Ha az Azure CLI-vel hoz létre virtuális gépet, az alapértelmezett művelet az Azure Managed Disks használata.  Ezeket a lemezeket az Azure platform kezeli, és nem igényel előkészítést vagy helyet a tároláshoz.  A nem felügyelt lemezekhez szükség van egy Storage-fiókra, és további teljesítménnyel kapcsolatos szempontokat kell figyelembe vennie.  További információ a felügyelt lemezekről: [Azure Managed Disks – áttekintés](../managed-disks-overview.md).  A következő szakasz csak akkor ismerteti a teljesítménnyel kapcsolatos szempontokat, ha nem felügyelt lemezeket használ.  Az alapértelmezett és az ajánlott tárolási megoldás a felügyelt lemezek használata.

Ha nem felügyelt lemezekkel rendelkező virtuális gépet hoz létre, akkor győződjön meg arról, hogy a virtuális géppel azonos régióban található Storage-fiókok lemezeit csatlakoztatja a közeli közelség és a hálózati késés csökkentése érdekében.  A standard szintű Storage-fiókok maximális 20000 IOps és 500 TB-os kapacitással rendelkeznek.  Ez a korlát körülbelül 40 erősen használt lemezre működik, beleértve az operációsrendszer-lemezt és a létrehozott adatlemezeket is. Premium Storage-fiókok esetében nincs maximális IOps korlát, de 32 TB-os mérethatárt. 

Ha magas IOps számítási feladatokra van szükség, és a lemezek esetében a standard szintű tárterületet választotta, előfordulhat, hogy a lemezeket több Storage-fiókban kell megosztania, hogy biztosan ne elérje a 20 000 IOps korlátot a standard Storage-fiókokhoz. A virtuális gép különféle lemezeket tartalmazhat a különböző tárolási fiókok és a Storage-fiókok típusai között az optimális konfiguráció eléréséhez.
 

## <a name="your-vm-temporary-drive"></a>A virtuális gép ideiglenes meghajtója
Alapértelmezés szerint a virtuális gépek létrehozásakor az Azure egy operációsrendszer-lemezt (**/dev/sda**) és egy ideiglenes lemezt (**/dev/sdb**) biztosít Önnek.  A hozzáadott további lemezek a **/dev/SDC**, a **/dev/SDD**, a **/dev/Sde** és így tovább jelennek meg. Az ideiglenes lemezen (**/dev/sdb**) lévő összes érték nem tartós, és elvész, ha adott események, például a virtuális gépek átméretezése, újratelepítése vagy karbantartása kényszeríti a virtuális gép újraindítását.  Az ideiglenes lemez mérete és típusa az üzembe helyezéskor kiválasztott virtuálisgép-mérethez kapcsolódik. A prémium méretű virtuális gépek (DS, G és DS_V2 sorozatok) mindegyike az ideiglenes meghajtót egy helyi SSD-vel támogatja, amely akár 48k-IOps is biztosít további teljesítményt. 

## <a name="linux-swap-partition"></a>Linux-swap partíció
Ha az Azure-beli virtuális gép Ubuntu-vagy CoreOS-rendszerképből származik, a CustomData használatával felhő-konfigurációt küldhet a Cloud-init számára. Ha [olyan egyéni Linux-rendszerképet töltött](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) fel, amely a Cloud-init szolgáltatást használja, a Cloud-init használatával is konfigurálhatja a swap-partíciókat.

Ubuntu Cloud images esetén a Cloud-init használatával kell konfigurálnia a swap partíciót. További információ: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

A Cloud-init támogatás nélküli rendszerképeknél az Azure piactéren üzembe helyezett virtuálisgép-lemezképek rendelkeznek egy, az operációs rendszerhez integrált virtuálisgép-Linux-ügynökkel. Ez az ügynök lehetővé teszi a virtuális gép számára a különböző Azure-szolgáltatásokkal való interakciót. Feltételezve, hogy az Azure Marketplace-ről standard rendszerképet helyezett üzembe, a következő lépéseket kell elvégeznie a Linux-swap fájl beállításainak megfelelő konfigurálásához:

Két bejegyzés megkeresése és módosítása a **/etc/waagent.conf** fájlban. Egy dedikált swap-fájl létezését és a lapozófájl méretét vezérlik. Az ellenőrzéshez `ResourceDisk.EnableSwap` szükséges paraméterek a következők. `ResourceDisk.SwapSizeMB` 

A megfelelően engedélyezett lemez és a csatlakoztatott lapozófájl engedélyezéséhez győződjön meg arról, hogy a paraméterek a következő beállításokkal rendelkeznek:

* ResourceDisk. EnableSwap = Y
* ResourceDisk. SwapSizeMB = {az igényeinek megfelelő méret MB-ban} 

A módosítás elvégzése után újra kell indítania a waagent, vagy újra kell indítania a linuxos virtuális gépet, hogy azok tükrözzék ezeket a módosításokat.  Ismeri a módosításokat, és egy lapozófájl jött létre, amikor a `free` parancs használatával megtekinti a szabad területet. Az alábbi példában a **waagent. conf** fájl módosításának eredményeképpen létrehozott 512-es swap-fájl látható:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/O-ütemezési algoritmus a Premium Storagehoz
A 2.6.18 Linux kernelben az alapértelmezett I/O-ütemezési algoritmus a határidőtől a CFQ (teljesen tisztességes üzenetsor-kezelő algoritmus) módosult. A véletlen hozzáférésű I/O-minták esetében elhanyagolható a különbség a CFQ és a határidő közötti teljesítménybeli különbségekben.  Az SSD-alapú lemezek esetében, amelyeknél a lemez I/O-mintázata túlnyomórészt szekvenciális, a NOOP vagy a határidős algoritmusra való váltás jobb I/O-teljesítményt érhet el.

### <a name="view-the-current-io-scheduler"></a>Az aktuális I/O-ütemező megtekintése
Használja az alábbi parancsot:  

```bash
cat /sys/block/sda/queue/scheduler
```

A következő kimenet jelenik meg, amely az aktuális ütemező állapotát jelzi.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>I/O-ütemezési algoritmus aktuális eszközének (/dev/sda) módosítása
Az alábbi parancsokat használja:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Ha ezt a beállítást csak a **/dev/sda** alkalmazza, nem hasznos. Állítsa be az összes adatlemezt, ahol a szekvenciális I/O az I/O-mintát uralja.  

A következő kimenetnek kell megjelennie, amely azt jelzi, hogy a **grub. cfg** újraépítése sikeres volt, és az alapértelmezett ütemező frissült a NOOP.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

A Red Hat Distribution Family esetében csak a következő parancsra van szükség:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

Az Ubuntu 18,04 és az Azure által hangolt kernel több várólistás I/O-ütemező használatával működik. Ebben az esetben `none` a megfelelő kijelölés a helyett `noop` . További információ: [Ubuntu I/O-ütemező](https://wiki.ubuntu.com/Kernel/Reference/IOSchedulers).

## <a name="using-software-raid-to-achieve-higher-iops"></a>A szoftveres RAID használata nagyobb I/Ops eléréséhez
Ha a számítási feladatok több IOps igényelnek, mint amennyit csak egyetlen lemez tud biztosítani, több lemez szoftveres RAID-konfigurációját kell használnia. Mivel az Azure már elvégezte a lemezes rugalmasságot a helyi háló rétegben, a legmagasabb szintű teljesítményt érheti el egy RAID-0 csíkozási konfigurációból.  Lemezeket építhet ki és hozhat létre az Azure-környezetben, és csatlakoztathatja őket a linuxos virtuális géphez a meghajtók particionálása, formázása és csatlakoztatása előtt.  A szoftveres RAID-beállítás Azure-beli Linux rendszerű virtuális gépen való konfigurálásáról további részleteket a **[szoftveres RAID konfigurálása Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** -dokumentumban talál.

A hagyományos RAID-konfiguráció alternatívájaként azt is megteheti, hogy a logikai kötet-kezelőt (LVM) is telepíti úgy, hogy több fizikai lemezt is konfiguráljon egyetlen csíkozott logikai tárolási kötetre. Ebben a konfigurációban az olvasások és írások elosztása a mennyiségi csoportban található több lemezre történik (a RAID0-hez hasonlóan). A teljesítmény szempontjából valószínű, hogy a logikai kötetek csíkozását szeretné használni, így az olvasás és az írás az összes csatlakoztatott adatlemezt felhasználja.  A csíkozott logikai kötetek Azure-beli Linux rendszerű virtuális gépen való konfigurálásával kapcsolatos további részleteket az **[LVM konfigurálása az Azure-ban linuxos virtuális gépen](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** című témakörben találhat.

## <a name="next-steps"></a>Következő lépések
Ne feledje, ahogy az összes optimalizálási vitafórumhoz hasonlóan az egyes módosítások előtt és után is végre kell hajtania a teszteket, hogy mérjék a változás hatását.  Az optimalizálás egy lépésről lépésre haladó folyamat, amely különböző eredményekkel rendelkezik a környezet különböző gépei között.  Előfordulhat, hogy az egyik konfiguráció működése nem működik mások számára.

Néhány hasznos hivatkozás további erőforrásokra:

* [Az Azure Linux-ügynök használati útmutatója](../extensions/agent-linux.md)
* [Szoftveres RAID konfigurálása Linuxon](configure-raid.md)
