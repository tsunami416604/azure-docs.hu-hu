---
title: Töltse fel az Azure CLI-vel egyéni Linux lemez |} A Microsoft Docs
description: Hozzon létre, és a egy virtuális merevlemezt (VHD) feltöltése az Azure-bA a Resource Manager-alapú üzemi modellel és az Azure CLI
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
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: a04c4d41d9682389347009446c590fc4e27400b1
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659541"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Töltse fel, és a egy Linux virtuális gép létrehozása egyéni lemezről az Azure CLI-vel

Ez a cikk bemutatja, hogyan töltse fel a virtuális merevlemez (VHD) az Azure storage-fiókba az Azure CLI-vel és a Linux rendszerű virtuális gépek létrehozása az egyéni lemezről. Ez a funkció lehetővé teszi telepítése és konfigurálása a Linux-disztribúció, az igényeinek megfelelően, valamint a virtuális merevlemez használatával gyorsan létrehozhat az Azure-beli virtuális gépek (VM).

Ez a témakör a storage-fiókok használja a végleges virtuális merevlemezek, de is elvégezheti ezeket a lépéseket használatával [felügyelt lemezek](upload-vhd.md). 

## <a name="quick-commands"></a>Gyors parancsok
Ha szeretne gyorsan elvégezni a feladatot, a következő szakaszban részletek alap parancsok VHD feltöltése az Azure-bA. Részletes információkat és a környezet minden lépés találja a dokumentum többi részén [itt indítása](#requirements).

Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index#az_login).

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa paraméterneveket foglalt `myResourceGroup`, `mystorageaccount`, és `mydisks`.

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy `WestUs` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

Hozzon létre egy tárfiókot, amely tárolja a virtuális lemezek [az tárfiók létrehozása](/cli/azure/storage/account). Az alábbi példa létrehoz egy tárfiókot, nevű `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Az a tárfiók hozzáférési kulcsainak listázása [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#az_storage_account_keys_list). Jegyezze fel a `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Hozzon létre egy tárolót a tárfiókban a tárfiók hívóbetűjét használja a beszerzett [az a tároló létrehozása](/cli/azure/storage/container). Az alábbi példa létrehoz egy tárolót `mydisks` használata a storage kulcs értékét `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Végül töltse fel a VHD-t a létrehozott tárolóba [az storage blob feltöltése](/cli/azure/storage/blob#az_storage_blob_upload). Adja meg a VHD-t a helyi elérési útja `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Adja meg az URI-t a lemez (`--image`) rendelkező [az virtuális gép létrehozása](/cli/azure/vm). A következő példában létrehozunk egy nevű virtuális Gépet `myVM` korábban feltöltött a virtuális lemez segítségével:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A cél tárfiók nem lehet ugyanaz, mint ahol a virtuális lemez feltöltött. Emellett meg kell adnia, vagy választ kér, által igényelt összes további paramétert a **az virtuális gép létrehozása** parancsot, mint például a virtuális hálózat, nyilvános IP-címe, felhasználónév és SSH-kulcsokat. További információ a [elérhető CLI Resource Manager-paraméterek](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Követelmények
A következő lépések végrehajtásához szüksége:

* **Linux operációs rendszer telepítve van egy .vhd fájl** -telepítése egy [Azure által támogatott Linux-disztribúció](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy [kapcsolatos tudnivalók nem támogatott disztribúciókkal](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) a VHD formátumú virtuális lemezre . Több eszköz létezik, hozzon létre egy virtuális gép és a virtuális merevlemez:
  * Telepítse és konfigurálja [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy [KVM](http://www.linux-kvm.org/page/RunningKVM), ügyelve arra, hogy a VHD-t használja, mint a kép formátuma. Ha szükséges, [kép konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) használatával `qemu-img convert`.
  * Is használhatja a Hyper-V [a Windows 10-es](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [a Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális Gépet, adja meg VHD formátumban. Szükség esetén átválthat VHDX-lemezek VHD-t használó [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-parancsmagot. További Azure nem támogatja dinamikus virtuális merevlemezek, feltöltésével, ezért az ilyen lemezek konvertálása statikus VHD feltöltése előtt szüksége. Eszközöket használhatja például [GÓHOZ készült Azure VHD segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) feltöltése az Azure-ba, a folyamat során a dinamikus lemezek konvertálása.
> 
> 

* Az egyéni lemezről létrehozott virtuális gépek kell lennie, mint maga a lemez ugyanabban a tárfiókban
  * Hozzon létre egy storage-fiók és az egyéni lemez és a létrehozott virtuális gépek tárolására szolgáló tároló
  * Miután létrehozta a virtuális gépek, nyugodtan törölheti a lemez

Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index#az_login).

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa paraméterneveket foglalt `myResourceGroup`, `mystorageaccount`, és `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>A feltölteni kívánt lemez előkészítése
Az Azure támogatja különböző Linux-disztribúciók (lásd: [támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). A következő cikkek végigvezetik az Azure által támogatott különböző Linux-disztribúciók előkészítése:

* **[CentOS-alapú Disztribúciókon](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Egyéb - nem által támogatott Disztribúciók](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Is megtekintheti a **[Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes)** kapcsolatos további általános tippek Linux-rendszerképek előkészítéséről az Azure-hoz.

> [!NOTE]
> A [SLA-t az Azure platform](https://azure.microsoft.com/support/legal/sla/virtual-machines/) csak akkor, ha a támogatott disztribúciókról egyik használja a támogatott verziók a konfigurációs adatokkal, a Linux rendszerű virtuális gépekre vonatkozik [Linux az Azure által támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Erőforráscsoportok logikailag egyesítheti az összes Azure-erőforrások a virtuális gépekhez, például a virtuális hálózati és tárolási támogatásához. További információk erőforráscsoportok, lásd: [erőforráscsoportokat áttekintése](../../azure-resource-manager/resource-group-overview.md). Az egyéni lemez feltöltése és a virtuális gépek létrehozása előtt először hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az_group_create).

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy tárfiókot, az egyéni lemez és a virtuális gépek [az tárfiók létrehozása](/cli/azure/storage/account). Olyan virtuális gépek nem felügyelt lemezek, amelyek az egyéni lemezt kell lennie a lemezen, ugyanazt a tárfiókot hoz létre. 

Az alábbi példa létrehoz egy tárfiókot, nevű `mystorageaccount` a korábban létrehozott erőforráscsoportot:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Tárfiókkulcsok listája
Azure létrehoz két 512 bites kulcsot minden olyan tárfiókhoz. Ezek hozzáférési kulcsokkal hitelesítésekor a storage-fiókban, mint például az írási műveletek elvégzésére. Tudjon meg többet [Itt a storage-hozzáférés kezelése](../../storage/common/storage-account-manage.md#access-keys). Megtekintheti a hozzáférési kulcsok [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#az_storage_account_keys_list).

A létrehozott tárfiók elérési kulcsainak megtekintése:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
Jegyezze fel a `key1` ugyanis kommunikál a storage-fiókjába a következő lépésben fogja használni.

## <a name="create-a-storage-container"></a>Storage-tároló létrehozása
Ugyanúgy, mint hoz létre, hogy logikusan rendszerezhesse az a helyi fájlrendszerben eltérő címtárak tárolók, a lemezek rendszerezéséhez a tárfiókon belül hoz létre. Egy tárfiók tetszőleges számú tárolót tartalmazhat. Hozzon létre egy tárolót az [az a tároló létrehozása](/cli/azure/storage/container).

Az alábbi példa létrehoz egy tárolót `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>VHD feltöltése
Töltsön fel az egyéni lemez [az storage blob feltöltése](/cli/azure/storage/blob#az_storage_blob_upload). Töltse fel, és tárolja az egyéni lemezt lapblobként.

Adja meg a hozzáférési kulccsal, a létrehozott tárolót az előző lépést, és az egyéni lemez elérési útját a helyi számítógépen:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása
Nem felügyelt lemezekkel rendelkező virtuális Gépet létrehozni, adja meg az URI-t a lemez (`--image`) rendelkező [az virtuális gép létrehozása](/cli/azure/vm). A következő példában létrehozunk egy nevű virtuális Gépet `myVM` korábban feltöltött a virtuális lemez segítségével:

Adja meg, hogy a `--image` paraméterrel [az virtuális gép létrehozása](/cli/azure/vm) , hogy az egyéni lemez mutasson. Ügyeljen arra, hogy `--storage-account` megegyezik a tárfiókban, az egyéni lemez tárolására. Nem kell ugyanazt a tárolót használja az egyéni lemez a virtuális gépek tárolására. Ellenőrizze, hogy további tárolókat ugyanúgy, mint a korábbi lépések létrehozása az egyéni lemez feltöltése előtt.

A következő példában létrehozunk egy nevű virtuális Gépet `myVM` a egyéni lemezről:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Meg kell adnia, vagy választ kér, által igényelt összes további paramétert a **az virtuális gép létrehozása** parancs például a felhasználónév és SSH-kulcsokat.


## <a name="resource-manager-template"></a>Resource Manager-sablon
Az Azure Resource Manager-sablonok a JavaScript Object Notation (JSON) fájlok határozza meg a kívánt hozhat létre környezetet. A sablonok sorolhatók például a számítás- vagy különböző erőforrás-szolgáltatónak. Használhatja a meglévő sablonok vagy saját. Tudjon meg többet [Resource Manager és a sablonok használatával](../../azure-resource-manager/resource-group-overview.md).

Belül a `Microsoft.Compute/virtualMachines` szolgáltató a sablon, hogy egy `storageProfile` csomópont, amely a virtuális gép konfigurációs adatait tartalmazza. A két fő paraméterek szerkesztése a `image` és `vhd` URI-k, amelyek az egyéni és az új virtuális gép virtuális lemezzel. Az alábbiakban látható egy példa a JSON-t egyéni lemezt használ:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Használhat [a meglévő sablont, és a egy virtuális gép létrehozása egyéni rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) vagy olvasási kapcsolatos [a saját Azure Resource Manager-sablonok létrehozására](../../azure-resource-manager/resource-group-authoring-templates.md). 

Ha egy sablon konfigurálva van, az [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create) hozhat létre a virtuális gépeket. Adja meg az URI-ját a JSON-sablon az a `--template-uri` paramétert:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Ha helyileg a számítógépen tárolt JSON-fájlt, használhatja a `--template-file` paraméter helyett:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>További lépések
Miután előkészített és az egyéni virtuális lemez feltöltött, tudjon meg többet arról [Resource Manager és a sablonok használatával](../../azure-resource-manager/resource-group-overview.md). Azt is érdemes [adatlemez hozzáadása](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) új virtuális gépekhez. Ha a elérését igénylő virtuális gépeken futó alkalmazások, ügyeljen arra, hogy [nyithat meg portokat és végpontokat](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

