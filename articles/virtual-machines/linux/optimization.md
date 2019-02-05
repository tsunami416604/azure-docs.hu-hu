---
title: Az Azure-ban Linux rendszerű virtuális gép optimalizálása |} A Microsoft Docs
description: Ismerje meg, ellenőrizze, hogy meg van adva az optimális teljesítmény érdekében az Azure-on Linuxos virtuális gép Optimalizálási tippek
keywords: linuxos virtuális gépek, linux virtuális gép ubuntus virtuális gép
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
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
ms.subservice: disks
ms.openlocfilehash: e983ae549664abf46de256e987e09191a2a44c0f
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728520"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Linuxos virtuális gép optimalizálása az Azure-ban
Linux rendszerű virtuális gépek (VM) a könnyű a parancssorból vagy a portálon. Ez az oktatóanyag bemutatja, hogyan annak beállítása, a Microsoft Azure platformon a teljesítmény optimalizálása érdekében. Ez a témakör egy Ubuntu Server rendszerű virtuális gép használja, de a Linux rendszerű virtuális gép használatával is létrehozhat [saját lemezképek sablonként](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Előfeltételek
Ez a témakör azt feltételezi, hogy már rendelkezik egy működő Azure-előfizetés ([ingyenes próbaidőszakra](https://azure.microsoft.com/pricing/free-trial/)) és a már üzembe helyezett virtuális gépek az Azure-előfizetésben helyezi. Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-előfizetésbe [az bejelentkezési](/cli/azure/reference-index) előtt [hozzon létre egy virtuális Gépet](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure-OS lemez
Miután létrehozott egy Linux rendszerű virtuális gép az Azure-ban, két lemezt társítva van. **/ dev/sda** az operációsrendszer-lemezt, **/dev/sdb** az ideiglenes lemez.  Ne használja a fő operációsrendszer-lemez (**/dev/sda**) kivételével az operációs rendszer, mert a gyors Virtuálisgép-rendszerindítás ideje van optimalizálva, és nem biztosít a számítási feladatokhoz megfelelő teljesítmény. Egy vagy több lemez csatolása a virtuális gép első állandó szeretné az adatok nem optimalizált. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Lemezek hozzáadása a méret és teljesítménycéljai
A virtuális gép mérete alapján, a-sorozatú, a D sorozatú 32 lemezek akár 16 további lemez is csatlakoztatható, és a gépi 64 lemez a G sorozat – egyenként legfeljebb 1 TB-nál. A hely és IOps-követelmények igény szerint további lemezeket ad hozzá. Minden lemezhez tartozik egy 500 IOps teljesítményt célja Standard Storage és lemezenként 5000 IOps legfeljebb a Premium Storage számára.  A Premium Storage-lemezekkel kapcsolatos további információkért lásd: [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek](../windows/premium-storage.md)

A Premium Storage-lemez, ahol a gyorsítótár-beállítások lettek állítva, vagy a legmagasabb IOps eléréséhez **ReadOnly** vagy **nincs**, le kell tiltania **korlátok** csatlakoztatása közben a Linux rendszerben. Mivel a biztonsági Premium Storage-lemez írási műveletek tartós, a beállítások nem kell korlátok.

* Ha **reiserFS**, a tiltsa le a csatlakoztatási lehetőséggel `barrier=none` (engedélyezése akadályozó tényezők, használja a `barrier=flush`)
* Ha **ext3/ext4**, a tiltsa le a csatlakoztatási lehetőséggel `barrier=0` (engedélyezése akadályozó tényezők, használja a `barrier=1`)
* Ha **XFS**, a tiltsa le a csatlakoztatási lehetőséggel `nobarrier` (engedélyezése akadályozó tényezők, használja a kapcsolót `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Nem felügyelt tárfiók fiókokkal kapcsolatos megfontolások
Az alapértelmezett műveletet, amikor egy virtuális Gépet hoz létre az Azure CLI-vel az Azure Managed Disks használata.  Ezeket a lemezeket az Azure platform kezeli, és nincs szükség előkészítésre és helyen tárolja őket.  Nem felügyelt lemezek tárfiók szükséges, és néhány további teljesítmény szempontot kell figyelembe venni.  További információ a felügyelt lemezekről: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md).  Az alábbi szakasz ismerteti a teljesítménnyel kapcsolatos megfontolások csak a nem felügyelt lemezek használata esetén.  Újra, az alapértelmezett és ajánlott tárolási megoldás, hogy a felügyelt lemezek használata.

Ha nem felügyelt lemezekkel rendelkező virtuális Gépet hoz létre, ellenőrizze, hogy csatlakoztassa a lemezeket a storage-fiókok hálózatbővítési hálózati késés minimalizálása érdekében és a virtuális géppel azonos régióban található.  Minden standard szintű tárfiók rendelkezik egy legfeljebb 20 k IOps és mérete 500 TB kapacitást.  Ez a korlát minden megfelelően működik körülbelül 40 kitett lemezekre, beleértve az operációsrendszer-lemez és bármely adatlemez hoz létre. A Premium Storage-fiókok esetében nem maximális IOps korlátozott, de 32 TB-os mérete korlátozva van. 

Ha többé vesződnie a sérült magas IOps számítási feladatokat, és a lemezek számára választott Standard Storage, szüksége lehet a lemezek elosztja a több tárfiókot, hogy nem elérte a standard szintű Tárfiókok esetén 20 000 IOps-korlátját. A virtuális gép lemezeket vegyesen tartalmazhatnak különböző storage-fiókok és a storage-fióktípusok az optimális konfigurációt eléréséhez.
 

## <a name="your-vm-temporary-drive"></a>A virtuális gép ideiglenes meghajtó
Alapértelmezés szerint ha egy virtuális Gépet hoz létre az Azure biztosít operációsrendszer-lemezt (**/dev/sda**) és a egy ideiglenes lemezt (**/dev/sdb**).  Minden további lemezek megjelenítése mentése másként hozzáadása **/dev/sdc**, **/dev/sdd**, **/dev/sde** és így tovább. Az ideiglenes lemezen lévő összes adatot (**/dev/sdb**) nem tartós, és elvesznek, ha az adott események például a virtuális gép átméretezése, újbóli üzembe helyezés, vagy egy karbantartási újraindítja a virtuális gép.  A telepítéskor kiválasztott Virtuálisgép-méretet a méretét és típusát az ideiglenes lemez kapcsolódik. Minden, a prémium szintű méretezés a virtuális gépek (DS, G és DS_V2 sorozat) az ideiglenes meghajtó élvezik legfeljebb 48 k további teljesítmény egy helyi SSD iops-t. 

## <a name="linux-swap-file"></a>Linux lapozófájl
Ha az Azure virtuális gép egy Ubuntu vagy a CoreOS rendszerképből, majd használhatja a CustomData a felhőkonfiguráció küldeni a cloud-init. Ha Ön [egyéni Linux-rendszerképek feltöltése](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , amely a cloud-Init eszközt használ, a cloud-init használata lapozó partíciót is konfigurálnia.

A felhő-rendszerképek Ubuntu a lapozó partíció konfigurálása a cloud-init kell használnia. További információkért lásd: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

A cloud-init-támogatás nélkül lemezképek esetén az Azure Marketplace-ről üzembe helyezett Virtuálisgép-rendszerképek van integrálva van az operációs rendszer virtuális gépek Linux-ügynök. Ez az ügynök kommunikál a különböző Azure-szolgáltatások a virtuális gép lehetővé teszi. Ha telepítette az Azure Marketplace-ről szabványos lemezkép, kell megfelelő konfigurálása a Linux lapozófájl beállításait a következőket:

Keresse meg és módosíthatja a két bejegyzést a **/etc/waagent.conf** fájlt. Azok az ellenőrzés egy dedikált lapozófájl létezik-e, és a lapozófájl méretét. A paraméterek módosítása szeretne `ResourceDisk.EnableSwap=N` és `ResourceDisk.SwapSizeMB=0` 

Módosítsa a paramétereket a következő beállításokat:

* ResourceDisk.EnableSwap=Y
* Igény szerint MB-ban ResourceDisk.SwapSizeMB={size} 

Miután elvégezte a módosítást, meg kell indítsa újra a waagent vagy a Linux rendszerű virtuális gép változásokkal.  A módosításokat végrehajtották, és a egy lapozófájl létrejött használatakor a `free` parancsot megtekintéséhez a szabad terület. Az alábbi példa egy 512MB lapozófájl módosítása eredményeként létrehozott rendelkezik a **waagent.conf** fájlt:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/o-ütemezési algoritmus a Premium Storage
A 2.6.18 a Linux kernel, az alapértelmezett ütemezés algoritmus i/o módosult a határidő, a CFQ (teljesen valós üzenetsor-kezelési algoritmus). Közvetlen elérésű IO-minták nincs elhanyagolható teljesítménybeli különbséget CFQ és a határidő közötti különbség.  SSD-alapú lemezek ahol túlnyomórészt szekvenciális-e a lemez i/o-minta váltson vissza az NOOP vagy határidő algoritmus érheti el jobb i/o-teljesítményt.

### <a name="view-the-current-io-scheduler"></a>Az aktuális i/o-ütemezőt megtekintése
Használja az alábbi parancsot:  

```bash
cat /sys/block/sda/queue/scheduler
```

Az alábbi kimenet, amely azt jelzi, hogy a jelenlegi Feladatütemező láthatja.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Az aktuális eszköz (/ dev/sda) algoritmus ütemezés i/o-módosítása
A következő parancsokat használja:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Ezt a beállítást a alkalmazása **/dev/sda** önállóan nincs hasznos. Állítsa be az összes adatlemezt, ahol a szekvenciális i/o legnagyobb az i/o-minta.  

Kimenetnek kell megjelennie a következő, amely jelzi, hogy **grub.cfg** sikeresen újraépítve, és hogy az alapértelmezett ütemező NOOP frissült.  

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

A Red Hat terjesztési termékcsalád csak lesz szüksége a következő parancsot:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Szoftver RAID használatával elérése magasabb I / művelet
A számítási feladatokat, mint egyetlen megadhat további IOps van szükség, ha egy szoftver több lemez, RAID-konfigurációt használni szeretne. Már végez az Azure-lemez rugalmasság a helyi fabric rétegben, mert a legmagasabb szintű RAID-0 csíkozást konfigurációról teljesítményt érhet el.  Kiépítése, és a lemezek létrehozása Azure környezetben, és csatolja őket a Linux rendszerű virtuális particionálás, formázásával, és a meghajtó csatlakoztatása előtt.  További részleteket a Szoftvertelepítés RAID konfigurálása a Linux rendszerű virtuális gépen az Azure-ban megtalálható a **[szoftver RAID konfigurálása linuxon](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** dokumentumot.

## <a name="next-steps"></a>További lépések
Ne feledje, hogy az összes optimalizálási vitafórum, a tesztek végrehajtása előtt, és a módosítás a hatás méréséhez minden módosítás után szükség szerint.  Optimalizálás az lépésről lépésre, amely a különböző eredményeket rendelkezik másik gépen a környezetben.  Mi működik egy konfiguráció mások számára nem működnek.

Néhány hasznos további forrásokra mutató hivatkozásokat: 

* [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-beli virtuális gépek számítási feladataihoz](premium-storage.md)
* [Azure Linux-ügynök használati útmutatója](../extensions/agent-linux.md)
* [Azure-beli Linuxos virtuális gépeken a MySQL teljesítményének optimalizálása](classic/optimize-mysql.md)
* [Szoftveres RAID linuxon konfigurálása](configure-raid.md)
