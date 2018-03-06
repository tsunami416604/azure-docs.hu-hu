---
title: "Optimalizálhatja a Linux virtuális Gépet az Azure-on |} Microsoft Docs"
description: "Ismerje meg, győződjön meg arról, hogy meg van adva a Linux virtuális Gépet az optimális teljesítmény érdekében az Azure Optimalizálási tippek"
keywords: "Linux virtuális gépek, virtuális gép linux ubuntu virtuális gép"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.openlocfilehash: e143ed1e6bcece7efac9126c8e46408e7a88a5c0
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="optimize-your-linux-vm-on-azure"></a>Linuxos virtuális gép optimalizálása az Azure-ban
A Linux virtuális gép (VM) létrehozása áll a parancssorból vagy a portálról. Ez az oktatóanyag bemutatja, hogyan annak beállítása, a Microsoft Azure platformon a teljesítmény optimalizálása érdekében. Ez a témakör az Ubuntu Server virtuális gép használja, de is létrehozhat Linux virtuális gép használt [a saját lemezképek sablonként](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Előfeltételek
Ez a témakör azt feltételezi, hogy már rendelkezik Azure-előfizetés ([ingyenes próbaidőszakra](https://azure.microsoft.com/pricing/free-trial/)) és a virtuális gép már létrehozta az Azure-előfizetése. Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és az Azure-előfizetése bejelentkezett [az bejelentkezési](/cli/azure/#az_login) előtt [hozzon létre egy virtuális Gépet](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure OS Disk
Miután létrehozott egy Linux virtuális Gépet az Azure-ban, két lemez társítva van. **/ dev/sda** az operációsrendszer-lemez van **/dev/sdb** az ideiglenes lemez.  Ne használja a fő operációsrendszer-lemez (**/dev/sda**) számára, mert az operációs rendszer kivételével bármely más virtuális gép gyors rendszerindítás van optimalizálva, és nem biztosítja a megfelelő teljesítmény a munkaterhelések. Egy vagy több lemezt csatolni a beolvasandó állandó VM és az adatok tárolási optimalizált. 

## <a name="adding-disks-for-size-and-performance-targets"></a>A lemezek hozzáadása a méret és Teljesítménycélok
A virtuális gép mérete alapján, csatolhat egy A-sorozatú, a D sorozat 32 lemezein legfeljebb 16 további lemezek és a 64 lemez egy G-sorozat a számítógép - minden egyes akár 1 TB-nál. Szükség esetén a hely és IOps követelmények hozzáadhat további lemezeket. Minden lemezhez tartozik 500 IOps teljesítményt célja a standard szintű tárolót, és lemezenként 5000 iops teljesítményt legfeljebb a prémium szintű Storage.  Prémium szintű Storage lemezek kapcsolatos további információkért lásd: [prémium szintű Storage: nagy teljesítményű tárolást az Azure virtuális gépeken](../windows/premium-storage.md)

Prémium szintű Storage lemezeken, ahol a gyorsítótár-beállítások vannak állítva a vagy a maximális IOps eléréséhez **ReadOnly** vagy **nincs**, le kell tiltania **korlátok** Linux fájlrendszert csatlakoztatása során. Mivel a prémium szintű Storage biztonsági lemezek írási műveletek tartós, a beállítások nem kell korlátok.

* Ha **reiserFS**, a tiltsa le a csatlakoztatási beállítással `barrier=none` (korlátok engedélyezése esetén használja a `barrier=flush`)
* Ha **ext3/ext4**, a tiltsa le a csatlakoztatási beállítással `barrier=0` (korlátok engedélyezése esetén használja a `barrier=1`)
* Ha **XFS**, a tiltsa le a csatlakoztatási beállítással `nobarrier` (korlátok engedélyezése esetén használja a kapcsolót `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Nem felügyelt tárolási fiókokkal kapcsolatos megfontolások
Az alapértelmezett művelet, ha egy virtuális Gépet az Azure CLI 2.0 létrehozásához, hogy Azure felügyelt lemezt használja.  Ezeknek a lemezeknek az Azure platform kezeli, és nem igényelnek azokról az előkészítő vagy helyen tárolja azokat.  Nem felügyelt lemezek tárfiók szükséges, és néhány további teljesítmény szempontot kell figyelembe venni.  További információ a felügyelt lemezekről: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md).  Az alábbi szakasz ismerteti a teljesítménnyel kapcsolatos szempontok, csak a nem felügyelt lemezek használatakor.  Ebben az esetben az alapértelmezett és ajánlott tárolási megoldás felügyelt lemezeket használni.

Ha egy virtuális Gépet hoz létre nem felügyelt lemezek, ellenőrizze, hogy a storage-fiókok közel biztosítása és a hálózati késés csökkentése érdekében érdemes a virtuális gép ugyanabban a régióban elhelyezkedő csatlakoztassa a lemezeket.  Minden standard szintű tárfiók van maximum 20 IOps és 500 TB kapacitású mérete.  Ezt a határt körülbelül 40 terhelésnek kitett lemezekre, beleértve az operációs rendszer lemezének és bármely hoz létre az adatlemezek megfelelően működik. Prémium szintű Storage-fiókok maximális IOps korlátozva van, de nincs a egy 32 TB-os méretkorlátját. 

Ha a magas IOps-munkaterhelések és kezelésével standard szintű tárolót választotta a lemezen, szükség lehet több storage-fiókok győződjön meg arról, hogy a nem szabványos Storage-fiókok a 20 000 IOps-korláttal nem érte-e osztani a lemezeket. A virtuális gép lemezei vegyesen tartalmazhatnak eltérő tárfiókokból és az optimális konfigurációját eléréséhez tárfióktípusokat.
 

## <a name="your-vm-temporary-drive"></a>A virtuális gép ideiglenes meghajtó
Alapértelmezés szerint a virtuális gépek létrehozásakor Azure biztosít egy operációsrendszer-lemez (**/dev/sda**) és egy ideiglenes lemez (**/dev/sdb**).  Minden további lemezek megjelenítése mentése másként hozzáadása **/dev/sdc**, **/dev/sdd**, **/dev/sde** és így tovább. Ideiglenes tárolt összes adat (**/dev/sdb**) nincs tartós, és elvesznek, ha a meghatározott események, például a átméretezése virtuális gép újbóli üzembe helyezése, vagy egy karbantartási újraindítja a virtuális Gépet.  A Virtuálisgép-méretet, a központi telepítéskor választott mérete és a ideiglenes lemez típusa nem kapcsolódik. A prémium szintű mindegyikét méretezés virtuális gépek (DS, a G és DS_V2 sorozat) az ideiglenes meghajtón a helyi SSD további teljesítmény érdekében legfeljebb 48 k által támogatott iops-érték. 

## <a name="linux-swap-file"></a>Linux lapozófájl
Ha az Azure virtuális gép Ubuntu vagy CoreOS lemezképből CustomData egy felhő-config küldendő felhő inicializálás használhatja. Ha Ön [Linux egyéni lemezkép feltöltése](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) használó felhő inicializálás, is konfigurálhatja a felhő inicializálás használó swap partíciókkal.

Ubuntu felhő lemezképek, a felhő inicializálás konfigurálása a lapozófájl-kapacitás partíciót kell használnia. További információkért lásd: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

A lemezképek felhő inicializálás támogatás nélkül az Azure piactérről telepített Virtuálisgép-rendszerképek van integrálva van az operációs rendszer virtuális gép Linux ügynök. Ez az ügynök lehetővé teszi, hogy a virtuális gép kommunikál a különböző Azure-szolgáltatásokhoz. Ha telepítette az Azure piactérről szabványos lemezkép, kellene megfelelően konfigurálja a Linux lapozófájl beállításait érdekében tegye a következőket:

Keresse meg és módosíthatja a két bejegyzés a **/etc/waagent.conf** fájlt. Egy dedikált swap fájl és a lapozófájl méretének azok ellenőrzése. A paraméterek módosítása szeretne `ResourceDisk.EnableSwap=N` és `ResourceDisk.SwapSizeMB=0` 

Módosítsa a paramétereket a következő beállításokat:

* ResourceDisk.EnableSwap=Y
* Az igényeknek MB-ban ResourceDisk.SwapSizeMB={size} 

A módosítás létrejötte után kell indítsa újra a waagent vagy a Linux virtuális Gépet, a változások követése érdekében.  A módosításokat végrehajtották, és használatakor létrehozását a lapozófájl tudja a `free` parancsot megtekintéséhez a szabad terület. Az alábbi példa rendelkezik egy 512MB lapozófájl módosítása eredményeként jött létre a **waagent.conf** fájlt:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Prémium szintű Storage i/o ütemezési algoritmus
A 2.6.18 rendelkező Linux kernel, az alapértelmezett ütemezés algoritmus i/o módosult a határidő, a CFQ (teljesen valós Üzenetsor-kezelés algoritmus). Közvetlen elérésű i/o-mintára van elhanyagolható teljesítmény különbségek CFQ és a határidő közötti különbség.  Ahol a lemez i/o-mintát túlnyomórészt szekvenciális az SSD-alapú lemezek átállítaná a NOOP vagy határidő algoritmus érhető el jobb i/o-teljesítmény.

### <a name="view-the-current-io-scheduler"></a>Az aktuális i/o-ütemező megtekintése
Használja az alábbi parancsot:  

```bash
cat /sys/block/sda/queue/scheduler
```

Kimeneti, amely megadja, hogy a jelenlegi Feladatütemező követően láthatja.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Az aktuális eszköz (/ dev/sda) i/o algoritmus ütemezés módosítása
Az alábbi parancsokat használja:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Ez a beállítás alkalmazása **/dev/sda** különálló nincs hasznos. Állítsa be összes adatok lemezen, ahol szekvenciális i/o egység dominálja az i/o-mintát.  

Kimenetnek kell megjelennie a következő, amely jelzi, hogy **grub.cfg** sikeresen újraépítve, és hogy az alapértelmezett ütemező NOOP környezetébe.  

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

A Redhat terjesztési termékcsalád csak van szüksége a következő parancsot:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Szoftver RAID használatával eléréséhez magasabb I / Ops
Ha a munkaterhelések magasabb iops-értéket, mint egyetlen lemez biztosíthat igényelnek, több lemezt a szoftver RAID-konfigurációt használni szeretné. Azure már lemez rugalmasság a helyi háló rétegben hajt végre, mert a legmagasabb szintű RAID-0 csíkozást konfigurációról teljesítmény elérése érdekében.  Kiépítése és lemezek létrehozása az Azure környezetben, és csatolja őket a Linux virtuális gép particionálás, formázásával, és a meghajtó csatlakoztatása előtt.  További részleteket a RAID Szoftvertelepítés konfigurálása a Linux virtuális Gépet az azure-ban található a  **[szoftver RAID konfigurálása Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**  dokumentum.

## <a name="next-steps"></a>További lépések
Ne feledje, hogy minden optimalizálási vitafórum tesztek végrehajtása előtt, és a módosítás a hatás méréséhez minden módosítás után szükség szerint.  Optimalizálás rendkívül részletes folyamat, amely rendelkezik a különböző eredmények a környezetben az egyes számítógépek közötti.  Mi működik egy konfiguráció mások esetében nem használható.

Néhány hasznos további forrásanyagokra mutató hivatkozásokat: 

* [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-beli virtuális gépek számítási feladataihoz](premium-storage.md)
* [Azure Linux ügynök felhasználói útmutatója](agent-user-guide.md)
* [Az Azure Linux virtuális gépeken futó MySQL teljesítményének optimalizálása](classic/optimize-mysql.md)
* [Szoftveres RAID Linux konfigurálása](configure-raid.md)
