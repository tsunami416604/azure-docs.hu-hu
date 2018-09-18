---
title: Töltse fel, vagy az Azure CLI 2.0-val egyéni Linux rendszerű virtuális gép másolása |} A Microsoft Docs
description: Töltse fel, vagy a Resource Manager-alapú üzemi modellel és az Azure CLI 2.0 egyéni virtuális gép másolása
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
ms.date: 07/06/2017
ms.author: cynthn
ms.openlocfilehash: 3fb6957cf6af5c09a355b61c7c2440a929d1b837
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736671"
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Linux rendszerű virtuális gép létrehozása egyéni lemezről az Azure CLI 2.0 használatával

<!-- rename to create-vm-specialized -->

Ez a cikk bemutatja, hogyan feltölthet egy egyéni virtuális merevlemez (VHD) vagy a példányt egy meglévő VHD-vel az Azure-ban, és hozzon létre új Linux rendszerű virtuális gépek (VM) az egyéni lemezről. Telepítése és konfigurálása a Linux-disztribúció az igényeinek megfelelően, valamint a virtuális merevlemez használatával gyorsan hozzon létre egy új Azure virtuális gépet.

Ha a szeretné több virtuális gép létrehozása a testre szabott lemezről, létre kell hoznia egy képet a virtuális gép vagy a virtuális merevlemez. További információkért lásd: [hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek, a parancssori felületről](tutorial-custom-images.md).

Erre két lehetősége van:
* [VHD feltöltése](#option-1-upload-a-specialized-vhd)
* [Egy meglévő Azure virtuális gép másolása](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Gyors parancsok

Létrehozásakor egy új virtuális gépet [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) testreszabott vagy specializált lemezből, **csatolása** a lemez (--csatolása operációsrendszer-lemez) egy egyéni vagy a Piactéri rendszerkép megadása helyett (--kép). A következő példában létrehozunk egy nevű virtuális Gépet *myVM* nevű felügyelt lemezzel *myManagedDisk* az egyéni virtuális merevlemezből létrehozni:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Követelmények
A következő lépések végrehajtásához szüksége:

* Linux rendszerű virtuális gép, amely az Azure-beli használatra készült. A [készítse elő a virtuális gép](#prepare-the-vm) szakaszban Ez a cikk bemutatja, hogyan telepítésével az Azure Linux-ügynök (waagent) szükséges, a virtuális gép a megfelelő működéshez az Azure-ban, és, hogy az SSH használatával kapcsolódni tud a disztribúció adott információk kereséséhez.
* Egy meglévő VHD-fájl [Azure által támogatott Linux-disztribúció](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy [kapcsolatos tudnivalók nem támogatott disztribúciókkal](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) a VHD formátumú virtuális lemezre. Több eszköz létezik, hozzon létre egy virtuális gép és a virtuális merevlemez:
  * Telepítse és konfigurálja [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy [KVM](http://www.linux-kvm.org/page/RunningKVM), ügyelve arra, hogy a VHD-t használja, mint a kép formátuma. Ha szükséges, [kép konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) használatával **qemu-img konvertálása**.
  * Is használhatja a Hyper-V [a Windows 10-es](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [a Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális Gépet, adja meg VHD formátumban. Szükség esetén átválthat VHDX-lemezek VHD-t használó [qemu-img konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-parancsmagot. További Azure nem támogatja dinamikus virtuális merevlemezek, feltöltésével, ezért az ilyen lemezek konvertálása statikus VHD feltöltése előtt szüksége. Eszközöket használhatja például [GÓHOZ készült Azure VHD segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) feltöltése az Azure-ba, a folyamat során a dinamikus lemezek konvertálása.
> 
> 


* Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index#az_login).

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa paraméterneveket foglalt *myResourceGroup*, *mystorageaccount*, és *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Az Azure támogatja különböző Linux-disztribúciók (lásd: [támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). A következő cikkek végigvezetik az Azure által támogatott különböző Linux-disztribúciók előkészítése:

* [CentOS-alapú Disztribúciókon](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Egyéb - nem által támogatott Disztribúciók](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Is megtekintheti a [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további általános tippek Linux-rendszerképek előkészítéséről az Azure-hoz.

> [!NOTE]
> A [SLA-t az Azure platform](https://azure.microsoft.com/support/legal/sla/virtual-machines/) csak akkor, ha a támogatott disztribúciókról egyik használja a támogatott verziók a konfigurációs adatokkal, a Linux rendszerű virtuális gépekre vonatkozik [Linux az Azure által támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>1. lehetőség: A VHD feltöltése

Egyéni virtuális merevlemez, amely a helyi gépen futtatja vagy egy másik felhőben exportált tölthet fel. A virtuális merevlemez használatával hozzon létre egy új Azure virtuális Gépet, meg kell a VHD feltöltése egy tárfiókba, és hozzon létre egy felügyelt lemezt a VHD-ből. 

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az egyéni lemez feltöltése és a virtuális gépek létrehozása előtt először hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az_group_create).

A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup* a a *eastus* helye: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy tárfiókot, az egyéni lemez és a virtuális gépek [az tárfiók létrehozása](/cli/azure/storage/account#az_storage_account_create). 

Az alábbi példa létrehoz egy tárfiókot, nevű *mystorageaccount* a korábban létrehozott erőforráscsoportot:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Tárfiókkulcsok listája
Azure létrehoz két 512 bites kulcsot minden olyan tárfiókhoz. A hozzáférési kulcsokat a tárfiókba, például az írási műveletek elvégzése hitelesítésekor szolgálnak. Tudjon meg többet [Itt a storage-hozzáférés kezelése](../../storage/common/storage-account-manage.md#access-keys). Megtekintheti a hozzáférési kulcsok [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#az_storage_account_keys_list).

A létrehozott tárfiók elérési kulcsainak megtekintése:

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
Ugyanúgy, mint hoz létre, hogy logikusan rendszerezhesse az a helyi fájlrendszerben eltérő címtárak tárolók, a lemezek rendszerezéséhez a tárfiókon belül hoz létre. Egy tárfiók tetszőleges számú tárolót tartalmazhat. Hozzon létre egy tárolót az [az a tároló létrehozása](/cli/azure/storage/container#az_storage_container_create).

Az alábbi példa létrehoz egy tárolót *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>A VHD feltöltése
Töltsön fel az egyéni lemez [az storage blob feltöltése](/cli/azure/storage/blob#az_storage_blob_upload). Töltse fel, és tárolja az egyéni lemezt lapblobként.

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


Hozzon létre egy felügyelt lemezt a virtuális merevlemez használatával [az lemez létrehozása](/cli/azure/disk#az_disk_create). Az alábbi példa létrehoz egy felügyelt lemezt nevű *myManagedDisk* a VHD-vel a névvel ellátott tárfiókot és tárolót a feltöltött:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>2. lehetőség: A meglévő virtuális gép másolása

Is a testre szabott virtuális gép létrehozása az Azure-ban, és ezután másolja ki az operációsrendszer-lemez és mellékelje egy új virtuális Gépet egy másik példány létrehozásához. Ez nem okoz gondot tesztelési, de ha szeretne egy meglévő Azure virtuális gép használja, a modell több új virtuális gép, valóban érdemes létrehoznia egy **kép** helyette. További információ a lemezkép létrehozása a meglévő Azure virtuális gépből: [hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek, a parancssori felületről](tutorial-custom-images.md)

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

A felügyelt lemez létrehozása. Ebben a példában létrehozunk egy felügyelt lemezt nevű *myManagedDisk* a pillanatképből, amely 128 GB méretű standard szintű storage-ban.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozza létre a virtuális Gépen való [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) csatolja (--csatolása operációsrendszer-lemez) a felügyelt lemezt az operációsrendszer-lemezként. A következő példában létrehozunk egy nevű virtuális Gépet *myNewVM* a feltöltött virtuális merevlemezből létrehozni a felügyelt lemez használata:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

SSH-t a virtuális gép a forrás virtuális gép hitelesítő adatok használatával képesnek kell lennie. 

## <a name="next-steps"></a>További lépések
Miután előkészített és az egyéni virtuális lemez feltöltött, tudjon meg többet arról [Resource Manager és a sablonok használatával](../../azure-resource-manager/resource-group-overview.md). Azt is érdemes [adatlemez hozzáadása](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) új virtuális gépekhez. Ha a elérését igénylő virtuális gépeken futó alkalmazások, ügyeljen arra, hogy [nyithat meg portokat és végpontokat](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

