---
title: "Töltse fel az Azure CLI 2.0 egyéni Linux lemezzel |} Microsoft Docs"
description: "Hozzon létre, és töltse fel a virtuális merevlemez (VHD) a Resource Manager üzembe helyezési modellel és az Azure CLI 2.0 használatával"
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
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 9159960af396e89f373da711e0cc46fdd996ab83
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Töltse fel, és a Linux virtuális gép létrehozása az Azure CLI 2.0 egyéni lemezről
Ez a cikk bemutatja, hogyan egy virtuális merevlemez (VHD) feltöltése az Azure CLI 2.0 Azure storage-fiók és a Linux virtuális gépek létrehozása a egyéni lemezt. Az [Azure CLI 1.0-s](upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verziójával is elvégezheti ezeket a lépéseket. Ez a funkció lehetővé teszi telepítése és konfigurálása a Linux distro az igényeinek megfelelően, valamint, hogy a virtuális merevlemez használatával gyorsan hozzon létre az Azure virtuális gépek (VM).

Ez a témakör a végső virtuális merevlemezeket használ a storage-fiókok, de ezeket a lépéseket használatával is végrehajthatja [által kezelt lemezeken](upload-vhd.md). 

## <a name="quick-commands"></a>Gyors parancsok
Ha szeretné gyorsan a feladatnak a, a következő szakasz részleteit a következő parancsokat a virtuális merevlemez feltöltéséhez az Azure-bA. Részletes információkat és a környezetben az egyes lépések a dokumentum többi részén található [itt indítása](#requirements).

Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/#login).

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre `myResourceGroup`, `mystorageaccount`, és `mydisks`.

Először hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoportot `myResourceGroup` a a `WestUs` helye:

```azurecli
az group create --name myResourceGroup --location westus
```

Hozzon létre egy tárfiókot, ahhoz, hogy a virtuális lemezek, amelyek [az storage-fiók létrehozása](/cli/azure/storage/account#create). Az alábbi példa létrehoz egy nevű tárfiók `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

A tárfiók hozzáférési kulcsainak listázása [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#list). Jegyezze fel a `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Hozzon létre egy tárolót a tárfiókon belül a biztonságitár-kulcs használatával kapott [az tároló létrehozása](/cli/azure/storage/container#create). Az alábbi példa létrehoz egy nevű tárolót `mydisks` használ a tárolási értékének `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Végezetül a VHD-fájlt feltölti a létrehozott tároló [az tárolási blob feltöltése](/cli/azure/storage/blob#upload). Adja meg a helyi elérési útját a virtuális merevlemez alapján `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Adja meg az URI-t a lemez (`--image`) rendelkező [az virtuális gép létrehozása](/cli/azure/vm#create). Az alábbi példakód létrehozza a virtuális gépek nevű `myVM` korábban feltöltött a virtuális lemez segítségével:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A cél tárfiókkal nem lehet ugyanaz, mint ahol a virtuális lemez feltöltött. Is meg kell adni, vagy válasz kér, által igényelt minden további paramétereket a **az virtuális gép létrehozása** parancs például a virtuális hálózat, a nyilvános IP-cím, a felhasználónevet és az SSH-kulcsok. További információ a [érhető el erőforrás-kezelő parancssori paraméterek](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Követelmények
A következő lépések elvégzéséhez szüksége:

* **Linux operációs rendszer van telepítve, a .vhd-fájllá** -telepíteni egy [Azure által támogatott Linux-disztribúció](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy lásd: [nem támogatott disztribúciókkal kapcsolatos információi](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) a virtuális merevlemez virtuális lemezre formátumban. Több különféle eszköz létezik a virtuális gép és a virtuális merevlemez létrehozásához:
  * Telepítse és konfigurálja [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy [KVM](http://www.linux-kvm.org/page/RunningKVM), ügyelve arra, hogy a virtuális merevlemez használata a képformátum. Ha szükséges, akkor [lemezkép konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) használatával `qemu-img convert`.
  * Is használhatja a Hyper-V [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [Windows Server 2012 vagy 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális Gépet, adja meg a VHD formátumban. Szükség esetén, VHDX-lemezek konvertálása virtuális merevlemez használatával [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-parancsmagot. További Azure nem támogatja dinamikus virtuális merevlemezek, feltöltése, ezért ilyen lemezek konvertálása statikus virtuális merevlemezek feltöltés előtt meg kell. Használhatja például a [NYISSA meg az Azure virtuális merevlemez segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) átalakítani a dinamikus lemezek Azure feltöltése során.
> 
> 

* Az egyéni lemez alapján létrehozott virtuális gépek ugyanazt a tárfiókot, magán a lemezen kell lennie
  * Hozzon létre egy tárfiók és tároló az egyéni lemez és a létrehozott virtuális gépek tárolására
  * Miután létrehozta a virtuális gépek, nyugodtan törölheti a lemezen

Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/#login).

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre `myResourceGroup`, `mystorageaccount`, és `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Készítse elő a feltölteni kívánt lemez
Azure támogatja a különböző Linux terjesztésekről (lásd: [támogatott Disztribúciókkal](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). A következő cikkekben végigvezeti Önt a különböző Linux terjesztésekről Azure által támogatott előkészítése:

* **[CentOS-alapú Disztribúciók](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Egyéb - nem támogatott Disztribúciókkal](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Lásd még: a  **[Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes)**  kapcsolatos további általános tippek Linux lemezképek előkészítése az Azure-bA.

> [!NOTE]
> A [Azure platformon SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) csak akkor, ha a konfigurációs részleteket a támogatott verziók használt egyik a hitelesített terjesztéseket Linuxot futtató virtuális gépek érvényes [az Azure által támogatott Linux Azokat a terjesztéseket](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Erőforráscsoportok logikailag összegyűjteni az Azure erőforrások, például a virtuális hálózati és tárolási a virtuális gép támogatása érdekében. További információ erőforráscsoportok, lásd: [erőforráscsoportokat áttekintése](../../azure-resource-manager/resource-group-overview.md). Feltöltése az egyéni lemez és virtuális gépek létrehozását, mielőtt először hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#create).

Az alábbi példa létrehoz egy erőforráscsoportot `myResourceGroup` a a `westus` helye:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Create a storage account

Hozzon létre egy tárfiókot, az egyéni lemez és virtuális gépek [az storage-fiók létrehozása](/cli/azure/storage/account#create). A virtuális gépeket hoz létre az egyéni lemez kell lenniük, hogy a lemez, ugyanazt a tárfiókot a nem felügyelt lemezzel. 

Az alábbi példa létrehoz egy nevű tárfiók `mystorageaccount` a korábban létrehozott erőforráscsoport:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Tárfiókkulcsok listázása
Az Azure létrehoz két 512 bites kulcsot minden tárfiók. A tárelérési kulcsok írási műveletek végrehajtására többek között a tárolási fiók hitelesítéséhez használt. Tudjon meg többet az [tárolási itt történő hozzáférés felügyeletéhez](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Megtekintheti a tárelérési kulcsokat a [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#list).

A létrehozott tárfiók hozzáférési kulcsainak megtekintése:

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
Jegyezze fel a `key1` , amellyel kommunikálni tud a storage-fiókot a következő lépéseket fogja használni.

## <a name="create-a-storage-container"></a>A tároló létrehozása
Az Ön által létrehozott különböző könyvtárak, hogy logikusan rendszerezhesse az a helyi fájlrendszer ugyanúgy hozzon létre egy tárfiókot, a lemezek rendszerezéséhez tárolókra. A storage-fiók korlátlan számú tárolót tárolhat tartalmazhat. Hozzon létre egy tárolót a [az tároló létrehozása](/cli/azure/storage/container#create).

Az alábbi példa létrehoz egy nevű tárolót `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Virtuális merevlemez feltöltéséhez
Töltsön fel az egyéni lemezzel [az tárolási blob feltöltése](/cli/azure/storage/blob#upload). Töltse fel, és tárolja az egyéni lemezt oldalblobként.

Adja meg a hozzáférési kulcsot a tároló létrehozott az előző lépést, és az egyéni lemez elérési útja a helyi számítógépen:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása
Nem felügyelt lemezzel rendelkező virtuális gép létrehozása, adja meg az URI-t a lemez (`--image`) rendelkező [az virtuális gép létrehozása](/cli/azure/vm#create). Az alábbi példakód létrehozza a virtuális gépek nevű `myVM` korábban feltöltött a virtuális lemez segítségével:

Adja meg, hogy a `--image` paraméterrel [az virtuális gép létrehozása](/cli/azure/vm#create) úgy, hogy az egyéni lemez mutasson. Győződjön meg arról, hogy `--storage-account` megegyezik a tárfiókot, az egyéni lemez tárolására. Nincs a virtuális gépek tárolására használni, az egyéni lemezként ugyanabban a tárolóban. Ügyeljen arra, hogy az egyéni lemez feltöltés előtt hozzon létre semmilyen további tárolókat ugyanúgy, mint a korábbi lépéseket.

Az alábbi példakód létrehozza a virtuális gépek nevű `myVM` a egyéni lemezről:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Meg kell adni, vagy válasz kér, által igényelt minden további paramétereket a **az virtuális gép létrehozása** parancsot például felhasználónév és SSH-kulcsok.


## <a name="resource-manager-template"></a>Resource Manager-sablon
Az Azure Resource Manager-sablonok olyan JavaScript Object Notation (JSON), amelyeket a környezet létrehozása build kíván. A sablonok megszakadnak a például számítási és hálózati különböző erőforrás-szolgáltatók. Meglévő sablonok használhatja, vagy a saját írni. Tudjon meg többet az [erőforrás-kezelő és a sablonok használatával](../../azure-resource-manager/resource-group-overview.md).

Belül a `Microsoft.Compute/virtualMachines` a sablon-szolgáltatót, hogy egy `storageProfile` csomópont, amely tartalmazza a konfigurációs részleteket a virtuális gép számára. A két fő paraméterek szerkesztése a `image` és `vhd` URI-azonosítók, amelyek az egyéni és az új virtuális gép virtuális lemezzel. A következő egyéni lemezt használ a JSON példáját mutatja be:

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

Használhat [a meglévő sablont, és hozzon létre egy virtuális Gépet egy egyéni lemezképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) vagy foglalkozó témakört [a saját Azure Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md). 

Ha egy sablon konfigurálva van, a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#create) a virtuális gépek létrehozásához. Adja meg a JSON sablonok az URI a `--template-uri` paraméter:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Ha egy számítógépen helyben tárolt JSON-fájl, használhatja a `--template-file` paraméter helyette:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Következő lépések
Miután előkészített és feltöltése az egyéni virtuális lemez, akkor további információ [erőforrás-kezelő és a sablonok használatával](../../azure-resource-manager/resource-group-overview.md). Is érdemes lehet [hozzá adatlemezt](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) az új virtuális gépek. Ha fut a virtuális gépek elérését igénylő alkalmazások, ügyeljen arra, hogy [nyisson meg portokat és a végpontok](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

