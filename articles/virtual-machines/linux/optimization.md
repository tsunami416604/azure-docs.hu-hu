---
title: Linux rendszerű virtuális gép optimalizálása az Azure-ban
description: Ismerje meg az optimalizálási tippeket, amelyek biztosítják, hogy a Linux virtuális gép az Azure optimális teljesítményéhez beállítsa
keywords: linux virtuális gép, virtuális gép linux, ubuntu virtuális gép
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 0ff901e7d53e0814ab064867be6185709e9b6a20
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452349"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Linux rendszerű virtuális gép optimalizálása az Azure-ban
Linuxos virtuális gép (VM) létrehozása könnyen ellátható a parancssorból vagy a portálról. Ez az oktatóanyag bemutatja, hogyan biztosíthatja, hogy a Microsoft Azure platformteljesítményének optimalizálása érdekében állítsa be. Ez a témakör egy Ubuntu Server VM-et használ, de létrehozhat Linux virtuális gépet is [a saját lemezképeivel sablonként.](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="prerequisites"></a>Előfeltételek
Ez a témakör feltételezi, hogy már rendelkezik egy működő Azure-előfizetéssel ([ingyenes próbaverzió ),](https://azure.microsoft.com/pricing/free-trial/)és már kiépített egy virtuális számítógépet az Azure-előfizetésébe. A virtuális gép [létrehozása](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)előtt győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van, és be van jelentkezve az Azure-előfizetésébe az [az bejelentkezéssel.](/cli/azure/reference-index)

## <a name="azure-os-disk"></a>Azure operációs rendszer lemeze
Miután létrehozott egy Linux virtuális gép az Azure-ban, két lemez t. **A /dev/sda** az operációs rendszer lemeze, a **/dev/sdb** az ideiglenes lemez.  Ne használja a fő operációs rendszer lemez (**/dev/sda**) semmit, kivéve az operációs rendszer, mivel gyors virtuális gép indítási idő, és nem nyújt jó teljesítményt a számítási feladatok. Egy vagy több lemezt szeretne csatolni a virtuális géphez, hogy állandó és optimalizált tárhelyet kapjon az adatokhoz. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Lemezek hozzáadása méret- és teljesítménycélokhoz
A virtuális gép mérete alapján legfeljebb 16 további lemezt csatolhat egy A-sorozatú, 32 lemezt egy D sorozatú és 64 lemezt egy G sorozatú gépen - mindegyik legfeljebb 32 TB méretű. Szükség szerint további lemezeket adhat hozzá a tárhely és az IOps-követelmények szerint. Minden lemez teljesítménycél 500 IOps standard storage és akár 20 000 IOps lemezenként a prémium szintű storage-hoz.

A legmagasabb IOps eléréséhez a prémium szintű storage lemezeken, ahol a gyorsítótár beállításait **readonly** vagy **nincs**értékre állították be, le kell tiltania az **akadályokat** a fájlrendszer Linuxon történő csatlakoztatása közben. Nincs szükség akadályokra, mert a prémium szintű storage-alapú lemezekre történő írások tartósak ezekhez a gyorsítótár-beállításokhoz.

* Ha **reiserFS-t**használ, tiltsa `barrier=none` le a korlátokat a `barrier=flush`rögzítési lehetőség gel (A korlátok engedélyezéséhez használja)
* **Ext3/ext4**használata esetén tiltsa le a `barrier=0` korlátokat a rögzítési `barrier=1`lehetőséggel (A korlátok engedélyezéséhez használja)
* Ha **XFS-t**használ, tiltsa `nobarrier` le a korlátokat a csatlakoztatási lehetőséggel (Az akadályok engedélyezéséhez használja a lehetőséget) `barrier`

## <a name="unmanaged-storage-account-considerations"></a>Nem felügyelt tárfiókkal kapcsolatos szempontok
Az azure CLI-vel létrehozott virtuális gép alapértelmezett művelete az Azure Managed Disks használata.  Ezeket a lemezeket az Azure platform kezeli, és nem igényel semmilyen előkészítést vagy helyet azok tárolásához.  A nem felügyelt lemezekhez tárfiókot kell tárolni, és további teljesítménybeli szempontokat is figyelembe kell venniük.  További információ a felügyelt lemezekről: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md).  A következő szakasz csak a nem felügyelt lemezek használata esetén ismerteti a teljesítménnyel kapcsolatos szempontokat.  Az alapértelmezett és ajánlott tárolási megoldás a felügyelt lemezek használata.

Ha nem felügyelt lemezekkel hoz létre virtuális gép, győződjön meg arról, hogy a virtuális géptel azonos régióban található tárfiókokból csatol lemezeket a közvetlen közelség és a hálózati késés minimalizálása érdekében.  Minden standard szintű tárfiók legfeljebb 20k IOps és egy 500 TB-os méretkapacitással rendelkezik.  Ez a korlát körülbelül 40 erősen használt lemezre korlátozódik, beleértve az operációs rendszer lemezét és a létrehozott adatlemezeket is. Prémium szintű storage-fiókok esetén nincs maximális IOps-korlát, de van egy 32 TB-os méretkorlát. 

Magas IOps-számítási feladatok kezelése során, és a lemezek standard tára, előfordulhat, hogy több tárfiókok között kell osztania a lemezeket, hogy megbizonyosodjon arról, hogy nem érte el a 20 000 IOps-korlát ot a standard tárfiókok. A virtuális gép az optimális konfiguráció elérése érdekében különböző tárfiókokból és tárfiók-típusokból származó lemezek kombinációját tartalmazhatja.
 

## <a name="your-vm-temporary-drive"></a>A virtuális gép ideiglenes meghajtója
Alapértelmezés szerint virtuális gép létrehozásakor az Azure operációs rendszerlemezt (**/dev/sda**) és ideiglenes lemezt (**/dev/sdb)** biztosít.  Minden hozzáadott további lemez **/dev/sdc**, **/dev/sdd**, **/dev/sde** és így tovább jelenik meg. Az ideiglenes lemezen (**/dev/sdb**) lévő összes adat nem tartós, és elveszhet, ha bizonyos események, például a virtuális gép átméretezése, átcsoportosítása vagy karbantartása kényszeríti a virtuális gép újraindítását.  Az ideiglenes lemez mérete és típusa a telepítéskor kiválasztott virtuális gép méretéhez kapcsolódik. Az ideiglenes meghajtót az ideiglenes meghajtó takarásában lévő összes prémium méretű virtuális gép (DS, G és DS_V2 sorozat) egy helyi SSD támogatja a 48k IOps további teljesítményérdekében. 

## <a name="linux-swap-partition"></a>Linux swap partíció
Ha az Azure virtuális gép egy Ubuntu vagy CoreOS-lemezképből származik, majd a CustomData segítségével felhőalapú konfigurációt küldhet a felhőalapú initnek. Ha [feltöltött egy egyéni Linux-lemezképet,](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) amely felhő-init, a swap partíciókat is konfigurálja a cloud-init használatával.

Az Ubuntu Cloud Images, a rendszernek a felhő-init kell használnia a swap partíciókonfigurálás. További információ: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Felhőalapú init támogatás nélküli lemezképek esetén az Azure Piactérről telepített virtuálisgép-lemezképek rendelkeznek az operációs rendszerrel integrált Virtuálisgép-Linux-ügynökkel. Ez az ügynök lehetővé teszi, hogy a virtuális gép különböző Azure-szolgáltatásokkal kommunikáljon. Feltéve, hogy egy szabványos lemezképet telepített az Azure Piactérről, a Linux-cserefájl beállításainak helyes konfigurálásához a következőket kell tennie:

Keresse meg és módosítsa az **/etc/waagent.conf** fájl két bejegyzését. Ezek szabályozzák a dedikált swap fájl létezését és a swap fájl méretét. Az ellenőrizéshez szükséges paraméterek `ResourceDisk.EnableSwap` a következők:`ResourceDisk.SwapSizeMB` 

A megfelelően engedélyezett lemez és csatlakoztatott lapozófájl engedélyezéséhez győződjön meg arról, hogy a paraméterek beállításai a következők:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size in MB to meet your needs} 

Miután elvégezte a módosítást, újra kell indítania a waagentet, vagy újra kell indítania a Linux virtuális gépét, hogy tükrözze ezeket a változásokat.  Tudja, hogy a módosítások végrehajtásra kerültek, és `free` egy swap fájl jött létre, amikor a parancs segítségével megtekinti a szabad területet. A következő példában a **waagent.conf** fájl módosítása eredményeként létrehozott 512 MB-os cserefájl jön létre:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/O ütemezési algoritmus a prémium szintű tároláshoz
A 2.6.18 Linux kernelsegítségével az alapértelmezett I/O ütemezési algoritmus határidőről CFQ-ra változott (teljesen tisztességes sorban állási algoritmus). A véletlenszerű hozzáférésű I/O minták esetében elhanyagolható különbség van a CFQ és a Deadline közötti teljesítménykülönbségek között.  Olyan SSD-alapú lemezek esetében, ahol a lemez I/O mintája túlnyomórészt szekvenciális, a NOOP vagy a Deadline algoritmusra való visszakapcsolás jobb I/O-teljesítményt érhet el.

### <a name="view-the-current-io-scheduler"></a>Az aktuális I/O-ütemező megtekintése
Használja az alábbi parancsot:  

```bash
cat /sys/block/sda/queue/scheduler
```

A következő kimenet látható, amely az aktuális ütemezőt jelzi.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Az I/O-ütemezési algoritmus aktuális eszközének (/dev/sda) módosítása
Az alábbi parancsokat használja:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> A **/dev/sda** beállítás alkalmazása önmagában nem hasznos. Minden olyan adatlemezen be állítható, ahol a szekvenciális I/O uralja az I/O-mintát.  

A következő kimenetnek kell láthatónak lennie, jelezve, hogy a **grub.cfg** sikeresen átlett építve, és hogy az alapértelmezett ütemező frissítve lett a NOOP-ra.  

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

A Red Hat disztribúciós család esetében csak a következő parancsra van szükség:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Szoftveres RAID használata magasabb I/Ops-műveletek eléréséhez
Ha a számítási feladatok több IOps-t igényelnek, mint amennyit egyetlen lemez biztosít, több lemez szoftveres RAID-konfigurációját kell használnia. Mivel az Azure már elvégzi a lemez rugalmasságát a helyi hálórétegen, a RAID-0 csíkozási konfigurációból a legmagasabb szintű teljesítményt érheti el.  Az Azure-környezetben kiépítheti és hozza létre a lemezeket, és csatolja őket a Linux virtuális géphez particionálás, formázás és a meghajtók csatlakoztatása előtt.  További részletek a szoftveres RAID-beállítások konfigurálása a Linux virtuális gép az azure-ban megtalálható a **[konfigurálása Szoftver RAID Linux on Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** dokumentum.

A hagyományos RAID-konfiguráció alternatívájaként a Logikai kötetkezelő (LVM) telepítését is választhatja annak érdekében, hogy több fizikai lemezt konfiguráljon egyetlen csíkozott logikai tárolókötetbe. Ebben a konfigurációban az olvasások és írások a kötetcsoportban található több lemezre kerülnek (hasonlóan a RAID0-hoz). A teljesítmény miatt valószínűleg a logikai köteteket úgy szeretné csíkozni, hogy az olvasások és írások az összes csatolt adatlemezt használják.  A csíkozott logikai kötet ek azure-beli konfigurálása az Azure-beli Linux virtuális gép **[konfigurálása az Azure-dokumentumban található az LVM konfigurálása linuxos virtuális gépen.](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

## <a name="next-steps"></a>Következő lépések
Ne feledje, hogy mint minden optimalizálási vitafórumnál, minden módosítás előtt és után teszteket kell végeznie a módosítás hatásának méréséhez.  Optimalizálás egy lépésről lépésre folyamat, amely különböző eredményeket a különböző gépek a környezetben.  Mi működik egy konfiguráció nem működik mások számára.

Néhány hasznos link a további forrásokhoz:

* [Az Azure Linux-ügynök használati útmutatója](../extensions/agent-linux.md)
* [Szoftveres RAID konfigurálása Linuxon](configure-raid.md)
