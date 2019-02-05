---
title: Töltse fel, vagy az Azure CLI-vel egyéni Linuxos virtuális gép másolása |} A Microsoft Docs
description: Töltse fel, vagy egy egyéni virtuális gép másolása a Resource Manager üzemi modell és az Azure CLI használatával
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 47227b1f9ceb4ba9e35180aa0cb171d1edd5bb9a
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696846"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Linux rendszerű virtuális gép létrehozása egy egyéni lemezről az Azure CLI-vel

<!-- rename to create-vm-specialized -->

Ez a cikk bemutatja, hogyan tölthet fel egy egyéni virtuális merevlemez (VHD), és a egy meglévő virtuális merevlemez másolása az Azure-ban. Az újonnan létrehozott virtuális Merevlemezt szolgál majd hozzon létre új Linux rendszerű virtuális gépek (VM). Telepítése és konfigurálása a Linux-disztribúció az igényeinek megfelelően, valamint a virtuális merevlemez használatával hozzon létre egy új Azure virtuális gépet.

Több virtuális gép létrehozása a testre szabott lemezről, először hozzon létre egy rendszerképet a virtuális gép vagy a virtuális Merevlemezt. További információkért lásd: [egy Azure virtuális gép egyéni rendszerképének létrehozása a parancssori felület használatával](tutorial-custom-images.md).

Hozzon létre egy egyéni lemezt, két lehetősége van:
* [VHD feltöltése](#option-1-upload-a-specialized-vhd)
* [Egy meglévő Azure virtuális gép másolása](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Gyors parancsok

Az új virtuális gép létrehozásakor [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) testreszabott vagy speciális lemezről, **csatolása** a lemez (--csatolása operációsrendszer-lemez) ahelyett, hogy adjon meg egy egyéni vagy a Piactéri lemezképet (– kép). A következő példában létrehozunk egy nevű virtuális Gépet *myVM* nevű felügyelt lemezzel *myManagedDisk* az egyéni virtuális merevlemezből létrehozni:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Követelmények
A következő lépések elvégzéséhez szüksége lesz:

* Linux rendszerű virtuális gép, amely az Azure-beli használatra készült. A [készítse elő a virtuális gép](#prepare-the-vm) szakaszban Ez a cikk bemutatja, hogyan disztribúció-specifikus információkat az Azure Linux-ügynök (waagent), szükség van egy virtuális gép SSH-n keresztül kapcsolódhat az telepítésével.
* Egy meglévő VHD-fájl [Azure által támogatott Linux-disztribúció](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy [kapcsolatos tudnivalók nem támogatott disztribúciókkal](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) a VHD formátumú virtuális lemezre. Több eszköz létezik, hozzon létre egy virtuális gép és a virtuális merevlemez:
  * Telepítse és konfigurálja [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy [KVM](http://www.linux-kvm.org/page/RunningKVM), ügyelve arra, hogy a VHD-t használja, mint a kép formátuma. Ha szükséges, [kép konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) a `qemu-img convert`.
  * Is használhatja a Hyper-V [a Windows 10-es](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [a Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális Gépet, adja meg VHD formátumban. Szükség esetén átválthat a virtuális merevlemez VHDX-lemezek [qemu-img konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-parancsmagot. Az Azure nem támogatja a dinamikus VHD feltöltése, ezért az ilyen lemezek konvertálása statikus VHD feltöltése előtt szüksége. Eszközöket használhatja például [GÓHOZ készült Azure VHD segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) feltölti őket az Azure-ba, a folyamat során a dinamikus lemezek konvertálása.
> 
> 


* Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókjába [az bejelentkezési](/cli/azure/reference-index#az-login).

A következő példákban cserélje le példa a paraméter nevét a saját értékeit, például *myResourceGroup*, *mystorageaccount*, és *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Az Azure támogatja különböző Linux-disztribúciók (lásd: [támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Az alábbi cikkek ismertetik az Azure által támogatott különböző Linux-disztribúciók előkészítése:

* [CentOS-alapú Disztribúciókon](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Egyebek: Nem támogatott disztribúciók](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Is megtekintheti a [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további általános tippek Linux-rendszerképek előkészítéséről az Azure-hoz.

> [!NOTE]
> A [SLA-t az Azure platform](https://azure.microsoft.com/support/legal/sla/virtual-machines/) csak akkor, ha a megadott "Támogatott verziói" alatt a konfigurációs adatokkal használt egyik a támogatott disztribúciókról Linux rendszerű virtuális gépekre vonatkozik [Linux az Azure által támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Option 1: VHD feltöltése

Egyéni virtuális merevlemez, amely a helyi gépen futtatja vagy egy másik felhőben exportált tölthet fel. Virtuális merevlemez használatával hozzon létre egy új Azure virtuális Gépet, szüksége lesz a virtuális lemezek feltöltése egy tárfiókba, és hozzon létre egy felügyelt lemezt a VHD-ből. További információ: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md).

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az egyéni lemez feltöltése és a virtuális gépek létrehozása előtt hozzon létre egy erőforráscsoportot a szüksége [az csoport létrehozása](/cli/azure/group#az-group-create).

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy tárfiókot, az egyéni lemez és a virtuális gépek [az tárfiók létrehozása](/cli/azure/storage/account). Az alábbi példa létrehoz egy tárfiókot, nevű *mystorageaccount* a korábban létrehozott erőforráscsoportot:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Tárfiókkulcsok listája
Azure létrehoz két 512 bites kulcsot minden olyan tárfiókhoz. A hozzáférési kulcsokat a tárfiókba, például ha írási műveletek elvégzése hitelesítésekor szolgálnak. További információkért lásd: [storage-hozzáférés kezelése](../../storage/common/storage-account-manage.md#access-keys). 

Megtekintheti a hozzáférési kulcsok [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#az-storage-account-keys-list). Ha például megtekintéséhez a hozzáférési kulcsok tárolására létrehozott fiók:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

Az eredmény az alábbihoz hasonlóan fog kinézni:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Jegyezze fel a **key1** ugyanis kommunikál a storage-fiókjába a következő lépésben fogja használni.

### <a name="create-a-storage-container"></a>Storage-tároló létrehozása
Ugyanúgy, mint hoz létre, hogy logikusan rendszerezhesse az a helyi fájlrendszerben eltérő címtárak tárolók, a lemezek rendszerezéséhez a tárfiókon belül fog létrehozni. Storage-fiók több tároló is tartalmazhat. Hozzon létre egy tárolót az [az a tároló létrehozása](/cli/azure/storage/container#az-storage-container-create).

Az alábbi példa létrehoz egy tárolót *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>A VHD feltöltése
Töltse fel az egyéni lemez [az storage blob feltöltése](/cli/azure/storage/blob#az-storage-blob-upload). Töltse fel fog, és tárolja az egyéni lemezt lapblobként.

Adja meg a hozzáférési kulccsal, a létrehozott tárolót az előző lépést, és az egyéni lemez elérési útját a helyi számítógépen:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
A virtuális merevlemez feltöltése eltarthat egy ideig.

### <a name="create-a-managed-disk"></a>Felügyelt lemez létrehozása


Hozzon létre egy felügyelt lemezt a VHD-vel rendelkező [az lemez létrehozása](/cli/azure/disk#az-disk-create). Az alábbi példa létrehoz egy felügyelt lemezt nevű *myManagedDisk* a VHD-vel a névvel ellátott tárfiókot és tárolót a feltöltött:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Option 2: Egy meglévő virtuális gép másolása

Is egy egyéni virtuális gép létrehozása az Azure-ban, és ezután másolja ki az operációsrendszer-lemez és mellékelje egy új virtuális Gépet egy másik példány létrehozásához. Ez nem okoz gondot a tesztelésre, de ha szeretne-e egy meglévő Azure virtuális gép használja, a modell több új virtuális gép, hozzon létre egy *kép* helyette. További információ a lemezkép létrehozása a meglévő Azure virtuális gépből: [egy Azure virtuális gép egyéni rendszerképének létrehozása a parancssori felület használatával](tutorial-custom-images.md).

### <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Ez a példa létrehoz egy nevű virtuális gép pillanatképét *myVM* erőforráscsoportban *myResourceGroup* , és létrehoz egy pillanatképet nevű *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>A felügyelt lemez létrehozása

Új felügyelt lemez létrehozása pillanatképből a.

A pillanatkép Azonosítójának lekéréséhez. Ebben a példában a pillanatkép neve *osDiskSnapshot* van, és a *myResourceGroup* erőforráscsoportot.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

A felügyelt lemez létrehozása. Ebben a példában létrehozunk egy felügyelt lemezt nevű *myManagedDisk* a pillanatképből, amelyben a lemez a standard storage és 128 GB méretű.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása

A virtuális gép létrehozása [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) csatolja (--csatolása operációsrendszer-lemez) a felügyelt lemezt az operációsrendszer-lemezként. A következő példában létrehozunk egy nevű virtuális Gépet *myNewVM* létrehozta a feltöltött virtuális merevlemezről a felügyelt lemez használata:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Kell látnia az SSH-t a virtuális Gépet a hitelesítő adatokat a forrás virtuális Gépről. 

## <a name="next-steps"></a>További lépések
Miután előkészített és az egyéni virtuális lemez feltöltött, tudjon meg többet arról [Resource Manager és a sablonok használatával](../../azure-resource-manager/resource-group-overview.md). Azt is érdemes [adatlemez hozzáadása](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) új virtuális gépekhez. Ha a elérését igénylő virtuális gépeken futó alkalmazások, ügyeljen arra, hogy [nyithat meg portokat és végpontokat](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
