---
title: Oktatóanyag - Azure-lemezek kezelése az Azure CLI használatával | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és felügyelhet Azure-lemezeket virtuális gépekhez az Azure CLI 2.0 használatával.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 889facbf9612f2462a10c886a428ac052becefd8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704487"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli-20"></a>Oktatóanyag – Azure-lemezek kezelése az Azure CLI 2.0 használatával

Az Azure-beli virtuális gépek (VM-ek) lemezeket használnak az operációs rendszerek, alkalmazások és adatok tárolására. Virtuális gép létrehozásakor fontos szempont, hogy a számítási feladatok jelentette várható terhelésnek megfelelő lemezméretet és konfigurációt válasszon ki. Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe és kezelhet virtuálisgép-lemezeket. Az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezek teljesítménye
> * Adatlemezek csatolása és előkészítése
> * Lemezek átméretezése
> * Lemez-pillanatképek

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="default-azure-disks"></a>Alapértelmezett Azure-lemezek

Egy Azure-beli virtuális gép létrehozásakor a rendszer két lemezt automatikusan a virtuális géphez csatol.

**Operációsrendszer-lemez** – Az operációsrendszer-lemezek akár 2 TB méretűek is lehetnek, és a virtuális gépek operációs rendszerei találhatók rajtuk. Az operációsrendszer-lemez alapértelmezés szerint a */dev/sda* címkét kapja. Az operációsrendszer-lemez gyorsítótárazási konfigurációja az operációs rendszer teljesítményéhez van optimizálva. A konfiguráció miatt az operációsrendszer-lemez **nem** használható alkalmazásokhoz és adatokhoz. Alkalmazásokhoz és adatokhoz használjon adatlemezeket, amelyekről később lesz szó az oktatóanyagban.

**Ideiglenes lemez** – Az ideiglenes lemezek olyan tartós állapotú meghajtót (SSD-t) használnak, amely ugyanazon az Azure-gazdagépen található, mint a virtuális gép. Az ideiglenes lemezek nagy teljesítményűek és olyan műveletekhez használhatók, mint például az ideiglenes adatfeldolgozás. Ha azonban a virtuális gépet egy új gazdagépre költöztetik, az ideiglenes lemezen tárolt adatokat a rendszer eltávolítja. Az ideiglenes lemez méretét a virtuális gép mérete határozza meg. Az ideiglenes lemezek a */dev/sdb* címkét kapják, a csatlakoztatási pontjuk pedig */mnt*.

### <a name="temporary-disk-sizes"></a>Ideiglenes lemezek méretei

| Típus | Gyakori méretek | Ideiglenes lemez max. mérete (GiB) |
|----|----|----|
| [Általános célú](sizes-general.md) | A, B és D sorozat | 1600 |
| [Számításra optimalizált](sizes-compute.md) | F sorozat | 576 |
| [Memóriaoptimalizált](sizes-memory.md) | D, E, G és M sorozat | 6144 |
| [Tárolásra optimalizált](sizes-storage.md) | L sorozat | 5630 |
| [GPU](sizes-gpu.md) | N sorozat | 1440 |
| [Nagy teljesítmény](sizes-hpc.md) | A és H sorozat | 2000 |

## <a name="azure-data-disks"></a>Azure-adatlemezek

Alkalmazások telepítéséhez és adatok tárolásához további adatlemezek adhatók hozzá. Az adatlemezeket akkor érdemes használni, ha tartós és rugalmas adattárolásra van szükség. Az egyes adatlemezek kapacitása maximum 4 TB lehet. A virtuális gép mérete határozza meg, hány adatlemez csatolható a virtuális géphez. A virtuális gépek minden vCPU-jához két adatlemez csatolható.

### <a name="max-data-disks-per-vm"></a>Adatlemezek max. száma virtuális gépenként

| Típus | Virtuális gép mérete | Adatlemezek max. száma virtuális gépenként |
|----|----|----|
| [Általános célú](sizes-general.md) | A, B és D sorozat | 64 |
| [Számításra optimalizált](sizes-compute.md) | F sorozat | 64 |
| [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md) | D, E és G sorozat | 64 |
| [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md) | L sorozat | 64 |
| [GPU](sizes-gpu.md) | N sorozat | 64 |
| [Nagy teljesítmény](sizes-hpc.md) | A és H sorozat | 64 |

## <a name="vm-disk-types"></a>Virtuálisgép-lemezek típusai

Az Azure két lemeztípust kínál.

### <a name="standard-disk"></a>Standard lemez

A merevlemez-meghajtókra épülő Standard Storage költséghatékony tárolási megoldás, amely emellett jó teljesítményt nyújt. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.

### <a name="premium-disk"></a>Prémium lemez

A prémium lemezek SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. A Premium Storage támogatja a DS, a DSv2, a GS és az FS sorozatú virtuális gépeket. Lemezméret kiválasztásakor az értéket felfelé kerekíti a rendszer a következő típusra. Ha például a lemez mérete kisebb 128 GB-nál, a lemez típusa P10. Ha a lemez mérete 129 GB és 512 GB közé esik, a típus P20. Az 512 GB feletti méretek esetében a típus P30.

### <a name="premium-disk-performance"></a>Prémium szintű lemezek teljesítménye

|Prémium szintű tárolólemezek típusai | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Lemezméret (felfelé kerekítés) | 32 GB | 64 GB | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) |
| Lemezenkénti maximális IOPS-érték | 120 | 240 | 500 | 2300 | 5000 | 7500 | 7500 |
Adattovábbítás lemezenként | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Míg a fenti táblázatban a lemezenkénti maximális IOPS-érték látható, nagyobb teljesítmény is elérhető több adatlemez összevonásával. Például egy Standard_GS5 virtuális gép esetében maximálisan 80 000 IOPS érhető el. A virtuális gépenkénti maximális IOPS-értékkel kapcsolatos részletes információkért lásd a [Linux rendszerű virtuális gépek méreteit](sizes.md) ismertető cikket.

## <a name="create-and-attach-disks"></a>Lemezek létrehozása és csatolása

Adatlemezek létrehozhatók és csatolhatók a virtuális gépek létrehozáskor vagy a meglévő virtuális gépekhez is.

### <a name="attach-disk-at-vm-creation"></a>Lemez csatolása virtuális gép létrehozásakor

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal.

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. A következő példa létrehoz egy *myVM* nevű virtuális gépet, hozzáadja az *azureuser* nevű felhasználói fiókot, valamint SSH-kulcsokat hoz létre, ha azok még nem léteznének. A `--datadisk-sizes-gb` argumentum használatával adhatja meg, hogy egy további lemezt kell létrehozni és a virtuális géphez csatolni. Több lemez létrehozásához és csatolásához a lemezméreteket egy szóközzel tagolt listában adja meg. A következő példában egy virtuális gépet hozunk létre két, egyenként 128 GB méretű adatlemezzel. Mivel a lemezek mérete 128 GB, mindkettő P10 típusú lemezként van konfigurálva, amely lemezenként maximálisan 500 IOPS-t biztosít.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Lemez csatolása meglévő virtuális géphez

Új lemez egy meglévő virtuális gépen való létrehozásához és csatolásához használja az [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) parancsot. A következő példában egy 128 gigabájt méretű, prémium szintű lemezt hozunk létre, majd csatoljuk a legutóbbi lépésben létrehozott virtuális géphez.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --disk myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Adatlemezek előkészítése

Miután hozzácsatolt egy lemezt a virtuális géphez, az operációs rendszert konfigurálni kell a lemez használatához. A következő példa bemutatja, hogyan konfigurálhatja manuálisan a lemezt. Ez a folyamat is automatizálható a cloud-init használatával, amelyet egy [későbbi oktatóanyag](./tutorial-automate-vm-deployment.md) mutat be.

### <a name="manual-configuration"></a>Manuális konfigurálás

Hozzon léte egy SSH-kapcsolatot a virtuális géppel. Cserélje le a példában szereplő IP-címet a virtuális gép nyilvános IP-címére.

```azurecli-interactive
ssh azureuser@52.174.34.95
```

Particionálja a lemezt az `fdisk` használatával.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Írjon egy fájlrendszert a partícióra az `mkfs` paranccsal.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Csatolja az új lemezt, hogy elérhető legyen az operációs rendszerben.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

A lemez a *datadrive* (adatmeghajtó) csatlakozási ponton keresztül érhető el, ami a `df -h` parancs futtatásával ellenőrizhető.

```bash
df -h
```

A kimenet a */datadrive* pontra csatlakoztatott új meghajtót mutatja.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Annak érdekében, hogy a meghajtó a rendszer újraindítása után ismét csatolva legyen, fel kell venni azt az */etc/fstab* fájlba. Ehhez kérje le a lemez UUID-jét a `blkid` segédprogrammal.

```bash
sudo -i blkid
```

A kimenet megjeleníti a meghajtó UUID-jét, amely esetünkben a `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Adjon hozzá egy, a következőhöz hasonló sort az */etc/fstab* fájlba.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Most, hogy a lemez konfigurálva lett, zárja be az SSH-munkamenetet.

```bash
exit
```

## <a name="resize-vm-disk"></a>Virtuálisgép-lemez átméretezése

A virtuális gépek üzembe helyezését követően az operációsrendszer-lemez vagy bármely más csatolt lemez mérete növelhető. A lemez méretét akkor érdemes növelni, ha több tárhelyre vagy nagyobb teljesítményre (például P10, P20 vagy P30) van szükség. A lemezek mérete nem csökkenthető.

A lemez méretének növelése előtt szükség van a lemez azonosítójára vagy nevére. Az [az disk list](/cli/azure/disk#az-disk-list) paranccsal listázhatja az összes lemezt az erőforráscsoportban. Jegyezze fel az átméretezni kívánt lemez nevét.

```azurecli-interactive
az disk list \
    --resource-group myResourceGroupDisk \
    --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
    --output table
```

A virtuális gépet fel kell szabadítani. Az [az vm deallocate](/cli/azure/vm#az-vm-deallocate) paranccsal állíthatja le és szabadíthatja fel a virtuális gépet.

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

A lemezt az [az disk update](/cli/azure/vm/disk#az-vm-disk-update) paranccsal méretezheti át. Ez a példa a *myDataDisk* nevű lemezt méretezi át 1 terabájtra.

```azurecli-interactive
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Az átméretezés befejeztével indítsa el a virtuális gépet.

```azurecli-interactive
az vm start --resource-group myResourceGroupDisk --name myVM
```

Ha az operációsrendszer-lemezt méretezi át, a partíció automatikusan kibővül. Ha adatlemezt méretez át, az aktuális partíciókat a virtuális gép operációs rendszerében kell bővíteni.

## <a name="snapshot-azure-disks"></a>Pillanatképek készítése Azure-lemezekről

A lemezpillanatképek létrehozása során az Azure egy csak olvasható, adott időponthoz kötött másolatot hoz létre a lemezről. Az Azure-beli virtuális gépekről készült pillanatképek akkor lehetnek hasznosak, ha a virtuális gépek állapotának gyors mentésére van szükség a konfiguráció módosítása előtt. Amennyiben a konfiguráció módosítása végül nem a várt eredményt hozza, a virtuális gép állapota visszaállítható a pillanatképből. Ha a virtuális gép egynél több lemezzel rendelkezik, az egyes lemezekről egymástól független pillanatképek készülnek. Alkalmazáskonzisztens biztonsági másolatok készítéséhez érdemes lehet leállítania a virtuális gépet a lemezpillanatképek készítése előtt. Másik megoldásként használhatja az [Azure Backup szolgáltatást](/azure/backup/) is, amelynek segítségével automatikus biztonsági mentést végezhet, miközben a virtuális gép fut.

### <a name="create-snapshot"></a>Pillanatkép készítése

A virtuálisgép-lemez pillanatképének elkészítése előtt szükség van a lemez azonosítójára vagy nevére. A lemez azonosítóját az [az vm show](/cli/azure/vm#az-vm-show) paranccsal kérheti le. A példában a lemezazonosítót egy változó tárolja, így az egy későbbi lépésben majd felhasználható.

```azurecli-interactive
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Most, hogy rendelkezik a virtuálisgép-lemez azonosítójával, a következő paranccsal készítheti el a lemez pillanatképét.

```azurcli
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Lemez létrehozása pillanatképből

A pillanatkép ezután lemezzé alakítható, amelynek segítségével újra létrehozhatja a virtuális gépet.

```azurecli-interactive
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Virtuális gép visszaállítása pillanatképből

A virtuálisgép-helyreállítás bemutatása érdekében törölje a meglévő virtuális gépet.

```azurecli-interactive
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Hozzon létre egy új virtuális gépet pillanatképlemezből.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Adatlemez újracsatolása

Az összes adatlemezt újra kell csatolni a virtuális gépre.

Először keresse ki a lemez nevét az [az disk list](/cli/azure/disk#az-disk-list) paranccsal. A példában a lemez nevét a *datadisk* nevű változóba helyezzük, amelyet a következő lépésben használunk majd.

```azurecli-interactive
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

A lemezt az [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) paranccsal csatolhatja.

```azurecli-interactive
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a virtuálisgép-lemezekkel kapcsolatos témakörökkel ismerkedett meg, például:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezek teljesítménye
> * Adatlemezek csatolása és előkészítése
> * Lemezek átméretezése
> * Lemez-pillanatképek

Folytassa a következő oktatóanyaggal, amely a virtuális gép konfigurálásának automatizálását ismerteti.

> [!div class="nextstepaction"]
> [Virtuálisgép-konfiguráció létrehozása](./tutorial-automate-vm-deployment.md)
