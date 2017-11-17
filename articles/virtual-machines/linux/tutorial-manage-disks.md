---
title: "Az Azure parancssori felülettel Azure lemezek kezelése |} Microsoft Docs"
description: "Útmutató – az Azure parancssori felülettel Azure lemezek kezelése"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5a7a58d4c402bcaf639bd255bb7c8b111694e548
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="manage-azure-disks-with-the-azure-cli"></a>Az Azure parancssori felülettel Azure lemezek kezelése

Az Azure virtuális gépek lemez használata a virtuális gépek operációs rendszer, az alkalmazások és az adatok tárolására. Virtuális gép létrehozásakor fontos válasszon ki egy lemez méretét és a konfiguráció megfelelő a várt munkaterhelés. Ez az oktatóanyag ismerteti, központi telepítésére és felügyeletére a virtuális gépek lemezei. További tudnivalók:

> [!div class="checklist"]
> * Az operációs rendszer és a ideiglenes lemezek
> * Az adatlemezek
> * Standard és Premium lemezek
> * Lemez teljesítménye
> * Csatlakoztatása és adatlemezek előkészítése
> * Lemezek átméretezése
> * Lemez pillanatképek


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="default-azure-disks"></a>Azure-lemezeket alapértelmezett

Egy Azure virtuális gép létrehozásakor a rendszer automatikusan csatolja két lemez a virtuális gép az. 

**Operációsrendszer-lemez** -operációs rendszer lemezek is kell méretezni, legfeljebb 1 terabájtnál, és a virtuális gépek operációs rendszert futtatja. Az operációs rendszer lemezének lett címkézve */dev/sda* alapértelmezés szerint. A lemez az operációs rendszer lemezének konfigurációját gyorsítótárazása az operációs rendszer teljesítményének van optimalizálva. Ezt a konfigurációt, az operációs rendszer lemezének miatt **nem kell** alkalmazások vagy az adatok tárolására szolgál. Az alkalmazások és adatok használja az adatlemezek, amely részletes leírást a cikk későbbi részében. 

**Ideiglenes lemez** -ideiglenes lemezek található SSD-meghajtó használatát a virtuális gépként Azure ugyanazon a gazdagépen. Ideiglenes lemezek magas performant és műveletek, például a ideiglenes adatfeldolgozási használható. Azonban a virtuális gép új gazdagépre helyezik át, ha eltávolítják egy ideiglenes lemezen tárolt adatokat. Az ideiglenes lemez mérete határozza meg a Virtuálisgép-méretet. Ideiglenes lemezek tartalma */dev/sdb* , és a csatlakoztatási pont a */mnt*.

### <a name="temporary-disk-sizes"></a>Ideiglenes mérete

| Típus | Virtuálisgép-mérettel | Maximális ideiglenes lemez (GB) |
|----|----|----|
| [Általános célú](sizes-general.md) | A-D sorozat része | 800 |
| [Számításra optimalizált](sizes-compute.md) | F sorozat | 800 |
| [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md) | D és G sorozat | 6144 |
| [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md) | L sorozat | 5630 |
| [GPU](sizes-gpu.md) | N sorozat | 1440 |
| [Magas teljesítmény](sizes-hpc.md) | A és H sorozat | 2000 |

## <a name="azure-data-disks"></a>Az Azure data lemezek

További adatlemezt felvehető alkalmazások telepítése és adatok tárolására. Az adatlemezek minden helyzetben, ahol van szükség az adatok tartósságát és rugalmas tárolási kell használni. Minden adat lemez 1 terabájtnál maximális kapacitása nem. A virtuális gép mérete határozza meg, hány adatlemezek csatolható a virtuális gépek. Minden virtuális gép vCPU, a két adatlemezek kapcsolható ki. 

### <a name="max-data-disks-per-vm"></a>Maximális adatlemezek virtuális gépenként

| Típus | Virtuálisgép-mérettel | Maximális adatlemezek virtuális gépenként |
|----|----|----|
| [Általános célú](sizes-general.md) | A-D sorozat része | 32 |
| [Számításra optimalizált](sizes-compute.md) | F sorozat | 32 |
| [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md) | D és G sorozat | 64 |
| [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md) | L sorozat | 64 |
| [GPU](sizes-gpu.md) | N sorozat | 48 |
| [Magas teljesítmény](sizes-hpc.md) | A és H sorozat | 32 |

## <a name="vm-disk-types"></a>Virtuális gép lemeztípusok

Azure két lemez biztosít.

### <a name="standard-disk"></a>Standard méretű lemez

A merevlemez-meghajtókra épülő Standard Storage költséghatékony tárolási megoldás, amely emellett jó teljesítményt nyújt. Standard lemezek ideális, ha a költséghatékony fejlesztői és munkaterhelés teszteléséhez.

### <a name="premium-disk"></a>Prémium szintű lemez

Premium lemezek SSD-alapú nagy teljesítményű, alacsony késésű lemez üzemelnek. Tökéletes éles munkaterhelést futtató virtuális gépekhez. Prémium szintű Storage támogatja a DS-méretek, DSv2-méretek, GS sorozatnak és FS sorozatú virtuális gépeket. Prémium szintű lemezekhez három típusa (P10, P20, P30) helyen, és a lemez mérete a lemez típusát határozza meg. Ha választja, a lemez méretét az érték felfelé kerekíti a következő típusra. Például ha a lemez mérete 128 GB-nál kevesebb, a lemez típusát P10. Ha a lemez mérete 129 GB és 512 GB közötti, egy P20 érték. Több mint 512 GB semmit a P30 mérete.

### <a name="premium-disk-performance"></a>Prémium szintű lemez teljesítménye

|Prémium szintű storage lemeztípus | P10 | P20 | P30 |
| --- | --- | --- | --- |
| A lemezméret (kerek mentése) | 128 GB | 512 GB | 1024 GB (1 TB) |
| Lemezenkénti maximális IOPS-érték | 500 | 2,300 | 5,000 |
Adattovábbítás lemezenként | 100 MB/s | 150 MB/s | 200 MB/s |

A fenti táblázat azonosítja a maximális iops-érték lemezenként, magasabb szintű teljesítmény szerint több adatlemezek szétosztott érhető el. Virtuális gép Standard_GS5 például 80000 IOPS maximális érhető el. A virtuális gép maximális iops-értéket részletes információkért lásd: [Linux Virtuálisgép-méretek](sizes.md).

## <a name="create-and-attach-disks"></a>Hozzon létre, és csatlakoztassa a lemezeket

Az adatlemezek hozható létre és csatolni a virtuális gép létrehozáskor vagy egy meglévő virtuális gépre.

### <a name="attach-disk-at-vm-creation"></a>A virtuális gép létrehozásakor lemez csatolása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) paranccsal. 

```azurecli-interactive 
az group create --name myResourceGroupDisk --location eastus
```

Hozzon létre egy virtuális gép használata a [az virtuális gép létrehozása]( /cli/azure/vm#create) parancsot. A `--datadisk-sizes-gb` argumentum használatával adja meg, hogy egy további szabad létre kell-e, és a virtuális géphez csatolt. Hozzon létre, és egynél több lemez csatolása, használja a lemez mérete értékének szóközökkel elválasztott kötetnevek listáját. A következő példában egy virtuális gép két adatlemezek, mindkét 128 GB hozza létre. Mivel a mérete 128 GB-os, ezek a lemezek egyaránt be van állítva, P10s, amely legfeljebb 500 iops-érték lemezenként.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --data-disk-sizes-gb 128 128 \
  --generate-ssh-keys
```

### <a name="attach-disk-to-existing-vm"></a>Meglévő virtuális lemez csatolása

Hozzon létre, és egy új lemezt csatolni a meglévő virtuális gépből, használja a [az méretű lemez csatolása](/cli/azure/vm/disk#attach) parancsot. Az alábbi példa létrehoz egy prémium szintű lemez 128 GB-nál, és csatolja azt a virtuális gép a legutóbbi lépésben létrehozott.

```azurecli-interactive 
az vm disk attach --vm-name myVM --resource-group myResourceGroupDisk --disk myDataDisk --size-gb 128 --sku Premium_LRS --new 
```

## <a name="prepare-data-disks"></a>Az adatlemezek előkészítése

Ha egy lemezt a virtuális géphez van csatolva, az operációs rendszer kell megadni a lemez használata. A következő példa bemutatja, hogyan manuálisan konfigurálnia a lemezt. Ez a folyamat is automatizálható a felhő-init használatával egy [az oktatóanyag későbbi](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Kézi konfigurálás

Hozzon léte egy SSH-kapcsolatot a virtuális géppel. A példa IP-cím cserélje le a virtuális gép nyilvános IP-cím.

```azurecli-interactive 
ssh 52.174.34.95
```

A lemez particionálása `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

A partíció használatával egy fájlrendszer írni a `mkfs` parancsot.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Csatlakoztassa az új lemezt úgy, hogy az operációs rendszerben érhető el.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

A lemez most már a következőkkel érhetők el a *datadrive* csatlakozási pont, amely futtatásával ellenőrizheti a `df -h` parancsot. 

```bash
df -h
```

Az alábbiakat mutatja be az új meghajtó csatlakoztatva */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Győződjön meg arról, hogy a meghajtó leválasztásra-e a rendszer újraindítása után, hogy azt kell felvenni a */etc/fstab* fájlt. Ehhez az szükséges, a lemez az UUID beolvasása a `blkid` segédprogram.

```bash
sudo -i blkid
```

A kimenet megjeleníti a meghajtó UUID `/dev/sdc1` ebben az esetben.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

A következőhöz hasonló sort a */etc/fstab* fájlt. Is vegye figyelembe, hogy az írási korlátok használatával tiltható *korlát = 0*, ez a konfiguráció lemez a jobb teljesítmény érdekében. 

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail,barrier=0   1  2
```

Most, hogy a lemez van konfigurálva, akkor zárja be az SSH-munkamenetet.

```bash
exit
```

## <a name="resize-vm-disk"></a>Virtuálisgép-lemez átméretezése

Ha egy virtuális Gépre van telepítve, a az operációsrendszer-lemez vagy a mellékelt adatok lemezzel mérete növelhető. Lemez méretének növelését akkor hasznos, ha több tárhelyet igényel, vagy a teljesítmény (P10, P20, P30) magasabb szintű. Vegye figyelembe, hogy lemezek csökkenteni mérete nem lehet.

Az azonosító vagy a lemez neve előtt növelni a lemezméretet, van szükség. Használja a [az Lemezlista](/cli/azure/disk#az_disk_list) parancs sikeresen lefut az összes lemez erőforráscsoportban. Jegyezze fel az a lemez neve, amelyhez átméretezése.

```azurecli-interactive 
az disk list -g myResourceGroupDisk --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table
```

A virtuális gép felszabadítása is kell. Használja a [az virtuális gép felszabadítása]( /cli/azure/vm#deallocate) parancs használatával állítsa le, és a virtuális gép felszabadítása.

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Használja a [az lemez frissítés](/cli/azure/vm/disk#update) parancs a lemez átméretezése. Ez a példa átméretezi nevű lemez *myDataDisk* való 1 terabájt.

```azurecli-interactive 
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Az átméretezés befejezése után indítsa el a virtuális Gépet.

```azurecli-interactive 
az vm start --resource-group myResourceGroupDisk --name myVM
```

Ha már átméretezték, ezért az operációsrendszer-lemez, a rendszer automatikusan kiegészíti a partíció. Ha adatlemezt átméretezte, aktuális partíciókat kell a virtuális gépek operációs rendszer bővíthető.

## <a name="snapshot-azure-disks"></a>Pillanatkép készítése az Azure-lemezeket

Lemez pillanatkép létrehozása van folyamatban a lemez olvasási csak, pont időponthoz kötött másolatot készít. Az Azure virtuális gép pillanatképei hasznos, ha a virtuális gépek állapotának gyors mentése konfigurációs módosítások végrehajtása előtt. Abban az esetben, ha a konfigurációs módosítások nem bizonyul, virtuális gép visszaállítható a pillanatkép. Ha egy virtuális gép több lemez, a rendszer pillanatképet készít minden lemez a többi függetlenül. Alkalmazás konzisztens biztonsági másolatok fogadására, fontolja meg a virtuális gép leállítása előtt lemez pillanatképek készítése. Másik megoldásként használhatja a [Azure Backup szolgáltatás](/azure/backup/), amely lehetővé teszi az automatikus biztonsági mentés végrehajtásához, a virtuális gép futása közben.

### <a name="create-snapshot"></a>Pillanatkép létrehozása

Mielőtt létrehozna egy virtuálisgép-lemez pillanatképét, az azonosító vagy a lemez neve van szükség. Használja a [az vm megjelenítése](https://docs.microsoft.com/en-us/cli/azure/vm#az_vm_show) parancs sikeresen lefut a lemezazonosítót. Ebben a példában a lemezazonosító van egy változó tárolja, hogy egy későbbi lépésben használható.

```azurecli-interactive 
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Most, hogy a virtuális gép lemezét azonosítóját, a következő parancs pillanatképet hoz létre a lemezen.

```azurcli
az snapshot create -g myResourceGroupDisk --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Pillanatképből lemez létrehozása

A pillanatkép majd lemezre, amely segítségével hozza létre újra a virtuális gép lehet konvertálni.

```azurecli-interactive 
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Virtuális gép pillanatkép visszaállítása

Virtuális gép helyreállítása bemutatásához, törölje a meglévő virtuális gép. 

```azurecli-interactive 
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Hozzon létre egy új virtuális gépet a pillanatkép lemezről.

```azurecli-interactive 
az vm create --resource-group myResourceGroupDisk --name myVM --attach-os-disk mySnapshotDisk --os-type linux
```

### <a name="reattach-data-disk"></a>Csatlakoztassa újra az adatok lemezre

Minden adatlemezek kell lennie a virtuális gép csatlakoztathatóak újra.

Először keresse a lemez neve használatával végzett a [az Lemezlista](https://docs.microsoft.com/cli/azure/disk#az_disk_list) parancsot. Ebben a példában a lemez neve helyezi nevű változó *datadisk*, amellyel a következő lépésben.

```azurecli-interactive 
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Használja a [az méretű lemez csatolása](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) parancs a lemez csatlakoztatásához.

```azurecli-interactive 
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóprogramban megismerte méretű lemezek témakörök, mint:

> [!div class="checklist"]
> * Az operációs rendszer és a ideiglenes lemezek
> * Az adatlemezek
> * Standard és Premium lemezek
> * Lemez teljesítménye
> * Csatlakoztatása és adatlemezek előkészítése
> * Lemezek átméretezése
> * Lemez pillanatképek

Továbblépés a következő oktatóanyag automatizálása a Virtuálisgép-konfiguráció megismeréséhez.

> [!div class="nextstepaction"]
> [Virtuálisgép-konfiguráció létrehozása](./tutorial-automate-vm-deployment.md)
