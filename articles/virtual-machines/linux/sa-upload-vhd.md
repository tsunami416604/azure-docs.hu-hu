---
title: Egyéni Linux-lemez feltöltése az Azure CLI-vel
description: Virtuális merevlemez (VHD) létrehozása és feltöltése az Azure-ba a Resource Manager telepítési modelljével és az Azure CLI-vel
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: 7ec9b670f8b2eb1731511deb1d01cfc7db55054f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758572"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Linuxos virtuális gép feltöltése és létrehozása egyéni lemezről az Azure CLI-vel

Ez a cikk bemutatja, hogyan tölthet fel egy virtuális merevlemezt (VHD) egy Azure storage-fiókaz Azure CLI-vel, és linuxos virtuális gépeket hozhat létre erről az egyéni lemezről. Ez a funkció lehetővé teszi, hogy telepítse nés konfigurálja és konfigurálja a Linux disztribúció az Ön igényeinek, majd használja ezt a virtuális merevlemezt az Azure virtuális gépek (VM-ek) gyors létrehozásához.

Ez a témakör tárfiókokat használ a végső virtuális merevlemezekhez, de ezeket a lépéseket felügyelt lemezek használatával is [megteheti.](upload-vhd.md) 

## <a name="quick-commands"></a>Gyors parancsok
Ha gyorsan el kell végeznie a feladatot, a következő szakasz részletezi az alapparancsokat egy virtuális merevlemez Azure-ba való feltöltéséhez. Részletesebb információk és összefüggésben az egyes lépésekkel megtalálható a többi a dokumentum, [kezdve itt](#requirements).

Győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van, és az az bejelentkezéssel be van jelentkezve egy Azure-fiókba. [az login](/cli/azure/reference-index)

A következő példákban cserélje le a példaparaméterneveket a saját értékeire. Példa `myResourceGroup`paraméternevekre `mystorageaccount`, `mydisks`és a.

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy `WestUs` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

Hozzon létre egy tárfiókot, hogy tartsa a virtuális lemezek az [az storage fiók létrehozása.](/cli/azure/storage/account) A következő példa létrehoz `mystorageaccount`egy tárfiókot nevű:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Sorolja fel a tárfiók hozzáférési kulcsait az [az storage-fiók kulcslistájával.](/cli/azure/storage/account/keys) Jegyezze fel `key1`a következőket:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Hozzon létre egy tárolót a tárfiókon belül az [az storage container create](/cli/azure/storage/container)használatával kapott tárolási kulcs használatával. A következő példa létrehoz `mydisks` egy tárolót, `key1`amelynek neve a tárolókulcs-érték használatával:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Végül töltse fel a virtuális merevlemezt az [az storage blob feltöltésével létrehozott tárolóba.](/cli/azure/storage/blob) Adja meg a virtuális merevlemez `/path/to/disk/mydisk.vhd`helyi elérési útját a következő csoportban:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Adja meg a lemez`--image`URI-ját ( ) az [az vm create](/cli/azure/vm). A következő példa létrehoz `myVM` egy virtuális gépet, amelyet a korábban feltöltött virtuális lemez használatával neveztek el:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A céltárfióknak meg kell egyeznie azzal, ahova a virtuális lemezt töltötte fel. Meg kell adnia, vagy meg kell adnia az összes további paramétert, amelyet az **az vm create** parancs igényel, például a virtuális hálózat, a nyilvános IP-cím, a felhasználónév és az SSH-kulcsok. A [rendelkezésre álló klasszikus CLI Resource Manager paraméterekről olvashat bővebben.](../azure-cli-arm-commands.md#virtual-machines)

## <a name="requirements"></a>Követelmények
A következő lépések végrehajtásához a következőkre van szükség:

* **Linux operációs rendszer telepítve egy .vhd fájl -** Telepítse [na egy Azure által jóváhagyott Linux disztribúció](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy [lásd a nem támogatott disztribúciók)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)egy virtuális lemez a VHD formátumban. Több eszköz létezik virtuális gép és virtuális merevlemez létrehozásához:
  * Telepítse és konfigurálja [a QEMU-t](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy a [KVM-et,](https://www.linux-kvm.org/page/RunningKVM)ügyelve arra, hogy a Virtuális merevlemezt használja képformátumként. Szükség esetén a segítségével `qemu-img convert` [konvertálhat egy képet.](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)
  * A Hyper-V [windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)rendszeren is használható.

> [!NOTE]
> Az újabb VHDX formátum ot az Azure nem támogatja. Amikor virtuális gép létrehozása, adja meg a virtuális merevlemez formátumban. Szükség esetén a VHDX-lemezek et virtuális [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) merevlemezre konvertálhatja a [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-parancsmag használatával. Továbbá az Azure nem támogatja a dinamikus Virtuális merevlemezek feltöltését, ezért az ilyen lemezeket a feltöltés előtt statikus VIRTUÁLIS MEREVLEMEZekké kell konvertálnia. Az [azure-beli virtuális merevlemez-segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) a GO-hoz olyan eszközök használatával konvertálhatja a dinamikus lemezeket az Azure-ba való feltöltés folyamata során.
> 
> 

* Az egyéni lemezről létrehozott virtuális gépeknek ugyanabban a tárfiókban kell elhelyezkedniük, mint magának a lemeznek.
  * Tárfiók és tároló létrehozása az egyéni lemez és a létrehozott virtuális gépek tárolásához
  * Miután létrehozta az összes virtuális gépet, biztonságosan törölheti a lemezt

Győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van, és az az bejelentkezéssel be van jelentkezve egy Azure-fiókba. [az login](/cli/azure/reference-index)

A következő példákban cserélje le a példaparaméterneveket a saját értékeire. Példa `myResourceGroup`paraméternevekre `mystorageaccount`, `mydisks`és a.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>A feltöltendő lemez előkészítése
Az Azure különböző Linux-disztribúciszokat támogat [(lásd: Jóváhagyott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Az alábbi cikkek bemutatják, hogyan készítheti el az Azure-ban támogatott különböző Linux-disztribúciákat:

* **[CentOS-alapú disztribúciók](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Egyéb - Nem jóváhagyott disztribúciók](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Tekintse meg a **[Linux telepítési megjegyzések](create-upload-generic.md#general-linux-installation-notes)** részletesebb tippeket a Linux-lemezképek azure-beli előkészítésével kapcsolatos általánostippekért.

> [!NOTE]
> Az [Azure platform SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) csak akkor vonatkozik a Linuxot futtató virtuális gépekre, ha a jóváhagyott disztribúciók egyikét a "Támogatott verziók" a Linux ban az [Azure által jóváhagyott disztribúciókon](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)megadott konfigurációs adatokkal használják.
> 
> 

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoportok logikailag egyesítik az összes Azure-erőforrást a virtuális gépek, például a virtuális hálózat és a tárolás támogatásához. További információerőforrás-csoportok az [erőforráscsoportok – áttekintés című témakörben találhatók.](../../azure-resource-manager/management/overview.md) Az egyéni lemez feltöltése és a virtuális gépek létrehozása előtt először létre kell hoznia egy erőforráscsoportot [az csoport létrehozásával.](/cli/azure/group)

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy tárfiókot az egyéni lemez hez és a virtuális gépekhez az [az storage fiók létrehozása.](/cli/azure/storage/account) Az egyéni lemezről létrehozott nem felügyelt lemezekkel rendelkező virtuális gépeknek ugyanabban a tárfiókban kell lenniük, mint az adott lemeznek. 

A következő példa létrehoz `mystorageaccount` egy korábban létrehozott erőforráscsoportban megnevezett tárfiókot:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Tárfiók kulcsainak listázása
Az Azure két 512 bites hozzáférési kulcsot hoz létre minden tárfiókhoz. Ezek a hozzáférési kulcsok a tárfiókhitelesítő műveletek, például írási műveletek végrehajtásához használatosak. A tárfiók hozzáférési kulcsairól a [Tárfiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md)című témakörben talál további információt. A hozzáférési kulcsokat az [az storage-fiók kulcslistájával](/cli/azure/storage/account/keys)tekintheti meg.

A létrehozott tárfiók hozzáférési kulcsainak megtekintése:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Az eredmény az alábbihoz hasonlóan fog kinézni:

```output
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Jegyezze fel, `key1` ahogy fogja használni, hogy a tárfiók a következő lépésekben.

## <a name="create-a-storage-container"></a>Tárolótároló létrehozása
Ugyanúgy, ahogy különböző könyvtárakat hoz létre a helyi fájlrendszer logikus rendszerezéséhez, tárolókat hozhat létre egy tárfiókon belül a lemezek rendszerezéséhez. A tárfiók tetszőleges számú tárolót tartalmazhat. Hozzon létre egy tárolót [az az storage tároló létrehozása.](/cli/azure/storage/container)

A következő példa létrehoz `mydisks`egy tároló nevű:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>VHD feltöltése
Most töltse fel az egyéni lemezt [az az storage blob feltöltéssel.](/cli/azure/storage/blob) Az egyéni lemezt oldalblobként tölti fel és tárolja.

Adja meg a hozzáférési kulcsot, az előző lépésben létrehozott tárolót, majd a helyi számítógép egyéni lemezének elérési útját:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása
Nem felügyelt lemezekkel rendelkező virtuális gép létrehozásához adja`--image`meg a lemezURI-ját ( ) az [az vm create](/cli/azure/vm)segítségével. A következő példa létrehoz `myVM` egy virtuális gépet, amelyet a korábban feltöltött virtuális lemez használatával neveztek el:

Adja meg `--image` a [paramétert az vm létrehozása](/cli/azure/vm) az egyéni lemezre mutat. Győződjön `--storage-account` meg arról, hogy megegyezik a tárfiók, ahol az egyéni lemez tárolja. Nem kell használni a tárolót, mint az egyéni lemez a virtuális gépek tárolására. Győződjön meg arról, hogy hozzon létre további tárolókat ugyanúgy, mint a korábbi lépéseket az egyéni lemez feltöltése előtt.

A következő példa létrehoz `myVM` egy virtuális gép neve az egyéni lemezről:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Továbbra is meg kell adnia, vagy a válasz utasításokat, az összes további paraméterek által megkövetelt **az az vm create** parancs, például a felhasználónév és az SSH-kulcsok.


## <a name="resource-manager-template"></a>Resource Manager-sablon
Az Azure Resource Manager-sablonok JavaScript-objektumnotúcés (JSON) fájlok, amelyek meghatározzák a létrekívánt környezetet. A sablonok különböző erőforrás-szolgáltatókra, például számítási vagy hálózati bontásra vannak bontva. Használhatja a meglévő sablonokat, vagy írjon a saját. További információ az [Erőforrás-kezelő és a sablonok használatáról.](../../azure-resource-manager/management/overview.md)

A `Microsoft.Compute/virtualMachines` sablon szolgáltatóján belül van `storageProfile` egy csomópont, amely tartalmazza a virtuális gép konfigurációs adatait. A két fő szerkesztandó paraméter `image` `vhd` az egyéni lemezre és az új virtuális gép virtuális lemezére mutató URI-k. Az alábbi példa a JSON-t mutatja be egyéni lemez használatára:

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

Ezzel a meglévő sablonnal virtuális gép létrehozása [egyéni lemezképből,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) vagy olvassa el [a saját Azure Resource Manager-sablonok létrehozását.](../../azure-resource-manager/templates/template-syntax.md) 

Miután konfiguráltegy sablont, az [az csoport központi telepítési létrehozása](/cli/azure/group/deployment) segítségével hozza létre a virtuális gépeket. Adja meg a JSON-sablon `--template-uri` URI-ját a következő paraméterrel:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Ha a számítógépen helyileg tárolt JSON-fájl található, `--template-file` a következő paramétert használhatja:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>További lépések
Miután előkészítette és feltöltötte az egyéni virtuális lemezt, többet is elolvashat [az Erőforrás-kezelő és a sablonok használatáról.](../../azure-resource-manager/management/overview.md) Előfordulhat, hogy [adatlemezt](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) is szeretne hozzáadni az új virtuális gépekhez. Ha a virtuális gépeken futó alkalmazásokat el kell érnie, győződjön meg [aportok és végpontok megnyitásáról.](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

