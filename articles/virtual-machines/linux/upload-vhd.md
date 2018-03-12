---
title: "Töltse fel, vagy az Azure CLI 2.0 egyéni Linux virtuális gép másolása |} Microsoft Docs"
description: "Töltse fel vagy másolja a Resource Manager üzembe helyezési modellel és az Azure CLI 2.0 testreszabott virtuális gép"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
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
ms.openlocfilehash: 49e6f4a1222d11d096577265e3e7c7a30169bf81
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Linux virtuális gép létrehozása az Azure CLI 2.0 egyéni lemezről

<!-- rename to create-vm-specialized -->

A cikkből megtudhatja, hogyan tölthet fel egy testreszabott virtuális merevlemez (VHD) vagy a példány egy meglévő VHD az Azure-ban, és hozzon létre új Linux virtuális gépek (VM) a egyéni lemezről. Telepítése és konfigurálása a Linux distro az igényeinek megfelelően, valamint, hogy a virtuális merevlemez használatával gyorsan hozzon létre egy új Azure virtuális gépet.

Ha azt szeretné, a testre szabott lemezről több virtuális gép létrehozásához, egy lemezképet kell létrehoznia a virtuális gép vagy a virtuális merevlemez. További információkért lásd: [hozzon létre egy Azure virtuális gép a parancssori felület használatával egyéni lemezkép](tutorial-custom-images.md).

Erre két lehetősége van:
* [VHD feltöltése](#option-1-upload-a-specialized-vhd)
* [Egy meglévő Azure virtuális gép másolása](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Gyors parancsok

Segítségével új virtuális gép létrehozásakor [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) testreszabott vagy speciális lemezről, **csatolása** a lemez (--csatolása operációsrendszer-lemez) egy egyéni vagy Piactéri lemezkép megadása helyett (--kép). Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* nevű felügyelt lemezzel *myManagedDisk* a testreszabott virtuális merevlemezből létrehozni:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Követelmények
A következő lépések elvégzéséhez szüksége:

* Linux virtuális gépek, amelyek az Azure-ban történő használatra készült. A [készítse elő a virtuális gép](#prepare-the-vm) szakasz Ez a cikk bemutatja, hogyan adhat distro konkrét információk a telepítése az Azure Linux ügynök (waagent), amely pedig szükséges, a virtuális gép működéséhez az Azure-ban, és meg kell kapcsolódnia kell az SSH használatával.
* A meglévő VHD-fájl [Azure által támogatott Linux-disztribúció](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy lásd: [nem támogatott disztribúciókkal kapcsolatos információi](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) a VHD formátumú virtuális lemezre. Több különféle eszköz létezik a virtuális gép és a virtuális merevlemez létrehozásához:
  * Telepítse és konfigurálja [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy [KVM](http://www.linux-kvm.org/page/RunningKVM), ügyelve arra, hogy a virtuális merevlemez használata a képformátum. Ha szükséges, akkor [lemezkép konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) használatával **qemu-img átalakítása**.
  * Is használhatja a Hyper-V [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [Windows Server 2012 vagy 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális Gépet, adja meg a VHD formátumban. Szükség esetén, VHDX-lemezek konvertálása virtuális merevlemez használatával [qemu-img átalakítása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-parancsmagot. További Azure nem támogatja dinamikus virtuális merevlemezek, feltöltése, ezért ilyen lemezek konvertálása statikus virtuális merevlemezek feltöltés előtt meg kell. Használhatja például a [NYISSA meg az Azure virtuális merevlemez segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) átalakítani a dinamikus lemezek Azure feltöltése során.
> 
> 


* Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre *myResourceGroup*, *mystorageaccount*, és *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Azure támogatja a különböző Linux terjesztésekről (lásd: [támogatott Disztribúciókkal](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). A következő cikkekben végigvezeti Önt a különböző Linux terjesztésekről Azure által támogatott előkészítése:

* [CentOS-alapú Disztribúciók](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Egyéb - nem támogatott Disztribúciókkal](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Lásd még: a [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további általános tippek Linux lemezképek előkészítése az Azure-bA.

> [!NOTE]
> A [Azure platformon SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) csak akkor, ha a konfigurációs részleteket a támogatott verziók használt egyik a hitelesített terjesztéseket Linuxot futtató virtuális gépek érvényes [az Azure által támogatott Linux Azokat a terjesztéseket](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>1. lehetőség: A virtuális merevlemez feltöltéséhez.

Testre szabott virtuális Merevlemezt, amely a helyi számítógépen futó vagy egy másik felhőből exportált feltölthet. A virtuális merevlemez használatával hozzon létre egy új Azure virtuális Gépet, akkor kell a VHD-fájlt feltölti egy tárfiókot, és hozzon létre egy felügyelt lemezes virtuális merevlemezről. 

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Feltöltése az egyéni lemez és virtuális gépek létrehozását, mielőtt először hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az_group_create).

Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup* a a *eastus* helye: [Azure felügyelt lemezekhez – áttekintés](../windows/managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Create a storage account

Hozzon létre egy tárfiókot, az egyéni lemez és virtuális gépek [az storage-fiók létrehozása](/cli/azure/storage/account#az_storage_account_create). 

Az alábbi példa létrehoz egy nevű tárfiók *mystorageaccount* a korábban létrehozott erőforráscsoport:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Tárfiókkulcsok listázása
Az Azure létrehoz két 512 bites kulcsot minden tárfiók. A hozzáférési kulcsot használnak az hitelesítésekor, a tárfiók, például az írási műveletek elvégzésére. Tudjon meg többet az [tárolási itt történő hozzáférés felügyeletéhez](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Megtekintheti a tárelérési kulcsokat a [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#az_storage_account_keys_list).

A létrehozott tárfiók hozzáférési kulcsainak megtekintése:

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
Jegyezze fel a **key1** , amellyel kommunikálni tud a storage-fiókot a következő lépéseket fogja használni.

### <a name="create-a-storage-container"></a>A tároló létrehozása
Az Ön által létrehozott különböző könyvtárak, hogy logikusan rendszerezhesse az a helyi fájlrendszer ugyanúgy hozzon létre egy tárfiókot, a lemezek rendszerezéséhez tárolókra. A storage-fiók korlátlan számú tárolót tárolhat tartalmazhat. Hozzon létre egy tárolót a [az tároló létrehozása](/cli/azure/storage/container#az_storage_container_create).

Az alábbi példa létrehoz egy nevű tárolót *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>A virtuális merevlemez feltöltéséhez
Töltsön fel az egyéni lemezzel [az tárolási blob feltöltése](/cli/azure/storage/blob#az_storage_blob_upload). Töltse fel, és tárolja az egyéni lemezt oldalblobként.

Adja meg a hozzáférési kulcsot a tároló létrehozott az előző lépést, és az egyéni lemez elérési útja a helyi számítógépen:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
A virtuális merevlemez feltöltése eltarthat egy ideig.

### <a name="create-a-managed-disk"></a>Kezelt lemez létrehozása


Hozzon létre egy felügyelt lemezes a VHD-t a [az lemez létrehozása](/cli/azure/disk#az_disk_create). Az alábbi példakód létrehozza nevű felügyelt lemezes *myManagedDisk* feltöltött a nevű tárfiók és tároló virtuális merevlemezről:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>2. lehetőség: Egy meglévő virtuális gép másolása

Is a testre szabott virtuális gép létrehozása az Azure-ban és majd másolja az operációsrendszer-lemezképet és mellékelje egy új virtuális Gépet egy másik példányt létrehozni. Ennek megfelelően működik tesztelési, de ha egy meglévő Azure virtuális gép használata a modell több új virtuális gépek, valóban készítsen egy **kép** helyette. További információ a lemezkép létrehozása egy meglévő Azure virtuális gépről: [hozzon létre egy egyéni rendszerképet az Azure virtuális gép a parancssori felület használatával](tutorial-custom-images.md)

### <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Ez a példa pillanatképet hoz létre a virtuális gépek nevű *myVM* erőforráscsoportban *myResourceGroup* nevű pillanatképet készít, és *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>A kezelt lemez létrehozása

Hozzon létre egy új felügyelt lemezes a pillanatkép.

A pillanatkép azonosítója beolvasása. Ebben a példában a pillanatkép neve *osDiskSnapshot* és az a *myResourceGroup* erőforráscsoportot.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

A kezelt lemez létrehozása. Ebben a példában létrehozunk egy felügyelt lemezes nevű *myManagedDisk* a pillanatképből, amely 128 GB-nál standard szintű tárolót.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozza létre a virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) , és csatlakoztassa (--csatolása operációsrendszer-lemez) a kezelt lemez az operációs rendszer lemezeként. Az alábbi példakód létrehozza a virtuális gépek nevű *myNewVM* a felügyelt lemezes, a feltöltött virtuális merevlemezből létrehozni:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

SSH-ból a virtuális Gépet, a hitelesítő adatok használatával a forrás virtuális gép képesnek kell lennie. 

## <a name="next-steps"></a>További lépések
Miután előkészített és feltöltése az egyéni virtuális lemez, akkor további információ [erőforrás-kezelő és a sablonok használatával](../../azure-resource-manager/resource-group-overview.md). Is érdemes lehet [hozzá adatlemezt](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) az új virtuális gépek. Ha fut a virtuális gépek elérését igénylő alkalmazások, ügyeljen arra, hogy [nyisson meg portokat és a végpontok](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

