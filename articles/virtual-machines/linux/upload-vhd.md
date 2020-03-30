---
title: Egyéni Linuxos virtuális gép feltöltése vagy másolása az Azure CLI-vel
description: Egyéni virtuális gép feltöltése vagy másolása az Erőforrás-kezelő telepítési modelljével és az Azure CLI-vel
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: b5063c8037a763c1919d2172a81c8abbbd406ace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060152"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Linuxos virtuális gép létrehozása egyéni lemezről az Azure CLI-vel

<!-- rename to create-vm-specialized -->

Ez a cikk bemutatja, hogyan tölthet fel egy testreszabott virtuális merevlemezt (VHD), és hogyan másolhat egy meglévő virtuális merevlemezt az Azure-ban. Az újonnan létrehozott virtuális merevlemez ezután új Linux virtuális gépek (VM-ek) létrehozásához használatos. Telepítheti és konfigurálhatja a Linux disztribúció a követelményeknek, majd használja, hogy a virtuális merevlemez egy új Azure virtuális gép létrehozásához.

Ha több virtuális gépet szeretne létrehozni a testreszabott lemezről, először hozzon létre egy lemezképet a virtuális gépből vagy a virtuális merevlemezről. További információ: [Egyéni lemezkép létrehozása egy Azure-beli virtuális gép a CLI használatával.](tutorial-custom-images.md)

Két lehetősége van egyéni lemez létrehozására:
* VHD feltöltése
* Meglévő Azure-virtuális gép másolása


## <a name="requirements"></a>Követelmények
A következő lépések végrehajtásához a következőkre van szükség:

- Egy Linux virtuális gép, amely az Azure-ban való használatra készült. A [vm előkészítése](#prepare-the-vm) szakaszban ez a cikk ismerteti, hogyan talál disztribúció-specifikus információkat az Azure Linux-ügynök (waagent), amely szükséges, hogy csatlakozzon egy virtuális gép SSH-val.
- A VHD-fájl egy meglévő [Azure által jóváhagyott Linux-disztribúcióból](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy [a nem jóváhagyott disztribúciókra vonatkozó információk](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)megtekintése) egy virtuális lemezre a Virtuális merevlemezre A Virtuális merevlemez formátumban. Több eszköz létezik virtuális gép és virtuális merevlemez létrehozásához:
  - Telepítse és konfigurálja [a QEMU-t](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy a [KVM-et,](https://www.linux-kvm.org/page/RunningKVM)ügyelve arra, hogy a Virtuális merevlemezt használja képformátumként. Szükség esetén [a kép konvertálható](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) a segítségével. `qemu-img convert`
  - A Hyper-V [windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)rendszeren is használható.

> [!NOTE]
> Az újabb VHDX formátum ot az Azure nem támogatja. Amikor virtuális gép létrehozása, adja meg a virtuális merevlemez formátumban. Szükség esetén a VHDX-lemezeket virtuális merevlemezre konvertálhatja [a qemu-img konvertálásával](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell parancsmaggal. Az Azure nem támogatja a dinamikus virtuális merevlemezek feltöltését, ezért az ilyen lemezeket a feltöltés előtt statikus virtuális merevlemezekké kell konvertálnia. Az olyan eszközök használatával, mint például [az Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) dinamikus lemezek konvertálásához az Azure-ba való feltöltés ük folyamata során.
> 
> 


- Győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van, és be van jelentkezve egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index#az-login)

A következő példákban cserélje le a példaparaméterneveket a saját értékeire, például `myResourceGroup`a , `mystorageaccount`és `mydisks`a.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Az Azure különböző Linux-disztribúciszokat támogat [(lásd: Jóváhagyott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Az alábbi cikkek ismertetik, hogyan kell elkészíteni az Azure-ban támogatott különböző Linux-disztribúciákat:

* [CentOS-alapú disztribúciók](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES és openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Egyéb: Nem támogatott disztribúciók](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Tekintse meg a [Linux telepítési megjegyzések](create-upload-generic.md#general-linux-installation-notes) részletesebb tippeket a Linux-lemezképek azure-beli előkészítésével kapcsolatos általánostippekért.

> [!NOTE]
> Az [Azure platform SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) csak akkor vonatkozik a Linuxot futtató virtuális gépekre, ha a jóváhagyott disztribúciók egyikét a "Támogatott verziók" a Linux ban az [Azure által jóváhagyott disztribúciókon](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)megadott konfigurációs adatokkal együtt használják.
> 
> 

## <a name="option-1-upload-a-vhd"></a>1. lehetőség: Virtuális merevlemez feltöltése

Most már feltöltheti a VHD-t közvetlenül egy felügyelt lemezre. További információt a [VHD feltöltése az Azure-ba az Azure CLI használatával (VHD feltöltése) témakörben](disks-upload-vhd-to-managed-disk-cli.md)talál.

## <a name="option-2-copy-an-existing-vm"></a>2. lehetőség: Meglévő virtuális gép másolása

Egyéni virtuális gép az Azure-ban is létrehozhat, majd másolja az operációs rendszer lemezét, és csatolja egy új virtuális géphez egy másik példány létrehozásához. Ez a tesztelés megfelelő, de ha egy meglévő Azure virtuális gépet szeretne használni modellként több új virtuális géphez, hozzon létre egy *lemezképet.* Ha további információra van haa, hogy hozzon létre egy lemezképet egy meglévő Azure-beli virtuális gépről, olvassa el az [Azure-virtuális gép egyéni lemezképének létrehozása a CLI használatával című témakört.](tutorial-custom-images.md)

Ha egy meglévő virtuális gép másolása egy másik régióba, érdemes lehet használni azcopy [egy példányát egy lemez egy másik régióban.](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 

Ellenkező esetben készítsen pillanatképet a virtuális gépről, majd hozzon létre egy új operációsrendszer-virtuális merevlemezt a pillanatképből.

### <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Ez a példa létrehoz egy pillanatképet egy *myVM* nevű virtuális gépről a *myResourceGroup* erőforráscsoportban, és létrehoz egy *osDiskSnapshot*nevű pillanatképet.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>A felügyelt lemez létrehozása

Hozzon létre egy új felügyelt lemezt a pillanatképből.

A pillanatkép azonosítójának lekért. Ebben a példában a pillanatkép neve *osDiskSnapshot,* és a *myResourceGroup* erőforráscsoportban található.

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Hozza létre a felügyelt lemezt. Ebben a példában létrehozunk egy *myManagedDisk* nevű felügyelt lemezt a pillanatképünkből, ahol a lemez szabványos tárolóhelyen van, és 128 GB-os méretben van.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozza létre a virtuális gép [az vm létrehozása](/cli/azure/vm#az-vm-create) és csatolása (--attach-os-disk) a felügyelt lemez, mint az operációs rendszer lemeze. A következő példa létrehoz egy *myNewVM* nevű virtuális gépet a feltöltött virtuális merevlemezről létrehozott felügyelt lemez használatával:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

A virtuális gép hitelesítő adataival ssh-t kell tudnia betekinteni a virtuális gépbe. 

## <a name="next-steps"></a>További lépések
Miután előkészítette és feltöltötte az egyéni virtuális lemezt, többet is elolvashat [az Erőforrás-kezelő és a sablonok használatáról.](../../azure-resource-manager/management/overview.md) Előfordulhat, hogy [adatlemezt](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) is szeretne hozzáadni az új virtuális gépekhez. Ha a virtuális gépeken futó alkalmazásokat el kell érnie, győződjön meg [aportok és végpontok megnyitásáról.](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
