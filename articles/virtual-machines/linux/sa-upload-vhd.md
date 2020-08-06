---
title: Egyéni linuxos lemez feltöltése az Azure CLI-vel
description: Virtuális merevlemez (VHD) létrehozása és feltöltése az Azure-ba a Resource Manager-alapú üzemi modell és az Azure CLI használatával
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: d667ff3949fcf58affc72b5d75a40abd8ca0dcb6
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830308"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Linuxos virtuális gép feltöltése és létrehozása egyéni lemezről az Azure CLI-vel

Ebből a cikkből megtudhatja, hogyan tölthet fel egy virtuális merevlemezt (VHD) egy Azure Storage-fiókba az Azure CLI-vel, és hogyan hozhat létre linuxos virtuális gépeket ebből az egyéni lemezből. Ez a funkció lehetővé teszi a Linux-disztribúciók telepítését és konfigurálását az Ön igényei szerint, majd a virtuális merevlemez használatával gyorsan létrehozhat Azure-beli virtuális gépeket (VM).

Ez a témakör a végső VHD-k Storage-fiókjait használja, de ezeket a lépéseket [felügyelt lemezek](upload-vhd.md)használatával is végrehajthatja. 

## <a name="quick-commands"></a>Gyors parancsok
Ha gyorsan el kell végeznie a feladatot, a következő szakasz részletesen ismerteti a virtuális merevlemezek Azure-ba való feltöltésének alapparancsait. Az egyes lépésekhez tartozó részletesebb információkat és kontextust a dokumentum többi része is megtalálhatja [.](#requirements)

Győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van és be van jelentkezve egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. Példa paraméterek neveire `myResourceGroup` , `mystorageaccount` és `mydisks` .

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy `WestUs` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

Hozzon létre egy Storage-fiókot, amely a virtuális lemezeket az [az Storage Account Create](/cli/azure/storage/account)paranccsal fogja tárolni. A következő példában létrehozunk egy nevű Storage-fiókot `mystorageaccount` :

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Sorolja fel a Storage-fiókhoz tartozó hozzáférési kulcsokat az [az Storage Account Keys List](/cli/azure/storage/account/keys)paranccsal. Jegyezze fel a `key1` következőket:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Hozzon létre egy tárolót a Storage-fiókon belül az az [Storage Container Create](/cli/azure/storage/container)paranccsal kapott tárolási kulccsal. A következő példa létrehoz egy nevű tárolót `mydisks` a tárolási kulcs értékének használatával `key1` :

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Végül töltse fel a virtuális merevlemezt az az [Storage blob upload](/cli/azure/storage/blob)paranccsal létrehozott tárolóba. Adja meg a virtuális merevlemez helyi elérési útját a következőben `/path/to/disk/mydisk.vhd` :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Adja meg a lemez () URI-JÁT az `--image` [az VM Create](/cli/azure/vm)paranccsal. Az alábbi példa egy nevű virtuális GÉPET hoz létre `myVM` a korábban feltöltött virtuális lemez használatával:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A célként megadott Storage-fióknak meg kell egyeznie, mint ahol a virtuális lemezt feltöltötte. Emellett az az **VM Create** parancs által megkövetelt összes további paramétert is meg kell adnia vagy válaszolnia kell, mint például a virtuális hálózat, a nyilvános IP-cím, a Felhasználónév és az ssh-kulcsok. További információt a [klasszikus CLI Resource Manager-paraméterekről](/previous-versions/azure/virtual-machines/azure-cli-arm-commands#virtual-machines)itt olvashat.

## <a name="requirements"></a>Követelmények
A következő lépések elvégzéséhez a következőkre lesz szüksége:

* **Linux operációs rendszer. vhd-fájlba telepítve** – [Azure által támogatott Linux-disztribúció](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) telepítése (vagy a [nem támogatott disztribúciók információinak](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)megjelenítése) virtuális lemezre VHD formátumban. Több eszköz létezik a virtuális gép és a virtuális merevlemez létrehozásához:
  * Telepítse és konfigurálja a [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) -t vagy a [KVM](https://www.linux-kvm.org/page/RunningKVM)-t, ügyelve arra, hogy a VHD-t használja képformátumként. Ha szükséges, [konvertálhat egy képet](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) a használatával `qemu-img convert` .
  * A Hyper-V-t [Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) vagy [Windows Server 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))rendszeren is használhatja.

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális gépet, a VHD formátumot kell megadnia formátumként. Szükség esetén a VHDX-lemezeket [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) a vagy a PowerShell-parancsmag használatával is konvertálhatja VHD-re [`Convert-VHD`](/powershell/module/hyper-v/convert-vhd?view=win10-ps) . Emellett az Azure nem támogatja a dinamikus virtuális merevlemezek feltöltését, ezért a feltöltés előtt át kell alakítania a lemezeket a statikus VHD-k között. Az Azure-ba való feltöltés folyamata során olyan eszközöket használhat, mint például az [Azure VHD-segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) , amelyek a dinamikus lemezek átalakítására szolgálnak.
> 
> 

* Az egyéni lemezről létrehozott virtuális gépeknek ugyanabban a Storage-fiókban kell lenniük, mint maga a lemez.
  * Hozzon létre egy Storage-fiókot és-tárolót az egyéni lemez és a létrehozott virtuális gépek tárolásához
  * Miután létrehozta az összes virtuális gépet, nyugodtan törölheti a lemezt

Győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van és be van jelentkezve egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. Példa paraméterek neveire `myResourceGroup` , `mystorageaccount` és `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>A lemez feltöltésének előkészítése
Az Azure különböző Linux-disztribúciókat támogat (lásd a [támogatott disztribúciókat](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Az alábbi cikkek végigvezetik az Azure-ban támogatott különböző Linux-disztribúciók előkészítésének lépésein:

* **[CentOS-alapú disztribúciók](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES és openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Egyéb – nem támogatott disztribúciók](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Tekintse meg a **[Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes)** is, amelyek az Azure-hoz készült Linux-rendszerképek előkészítésével kapcsolatos általános tippeket mutatnak.

> [!NOTE]
> Az [Azure platform SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) -ja csak akkor vonatkozik a Linux rendszerű virtuális gépekre, ha a támogatott disztribúciók egyikét használja az [Azure által támogatott disztribúciók Linux-](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)verziójában megadott konfigurációs adatokkal.
> 
> 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az erőforráscsoportok logikailag egyesítik az összes Azure-erőforrást a virtuális gépek támogatásához, például a virtuális hálózatkezeléshez és a tárhelyhez. További információforrások az erőforráscsoportok [áttekintése](../../azure-resource-manager/management/overview.md)című témakörben találhatók. Az egyéni lemez feltöltése és a virtuális gépek létrehozása előtt először létre kell hoznia egy erőforráscsoportot az [az Group Create](/cli/azure/group)paranccsal.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy Storage-fiókot az egyéni lemez és a virtuális gépek számára az [az Storage Account Create](/cli/azure/storage/account)paranccsal. Az egyéni lemezről létrehozott nem felügyelt lemezekkel rendelkező virtuális gépeknek ugyanabban a Storage-fiókban kell lenniük, mint a lemezzel. 

A következő példa létrehoz egy nevű Storage `mystorageaccount` -fiókot a korábban létrehozott erőforráscsoporthoz:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Storage-fiók kulcsainak listázása
Az Azure minden egyes Storage-fiókhoz 2 512 bites hozzáférési kulcsokat hoz létre. Ezeket a hozzáférési kulcsokat a rendszer a Storage-fiókhoz való hitelesítéskor használja, például az írási műveletek végrehajtásához. A Storage-fiók hozzáférési kulcsaival kapcsolatos további információkért lásd: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md). A hozzáférési kulcsokat az [az Storage Account Keys List](/cli/azure/storage/account/keys)paranccsal tekintheti meg.

Tekintse meg a létrehozott Storage-fiók elérési kulcsait:

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

Jegyezze fel, hogy a `key1` következő lépésekben azt fogja használni, hogy együttműködjön a Storage-fiókkal.

## <a name="create-a-storage-container"></a>Tároló létrehozása
Ugyanúgy, ahogy különböző címtárakat hoz létre a helyi fájlrendszer logikus rendszerezéséhez, a lemezeket a Storage-fiókon belül hozhatja létre. A Storage-fiók tetszőleges számú tárolót tartalmazhat. Hozzon létre egy tárolót az [az Storage Container Create](/cli/azure/storage/container)paranccsal.

A következő példában létrehozunk egy nevű tárolót `mydisks` :

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>VHD feltöltése
Most töltse fel az egyéni lemezt az [az Storage blob upload](/cli/azure/storage/blob)paranccsal. Az egyéni lemez feltöltése és tárolása oldal blobként.

Adja meg a hozzáférési kulcsát, az előző lépésben létrehozott tárolót, majd az egyéni lemez elérési útját a helyi számítógépen:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>A virtuális gép létrehozása
A nem felügyelt lemezekkel rendelkező virtuális gépek létrehozásához adja meg a lemez () URI-JÁT az `--image` [az VM Create](/cli/azure/vm)paranccsal. Az alábbi példa egy nevű virtuális GÉPET hoz létre `myVM` a korábban feltöltött virtuális lemez használatával:

A `--image` paramétert az [az VM Create](/cli/azure/vm) paranccsal adhatja meg, hogy az egyéni lemezre mutasson. Győződjön meg arról, hogy `--storage-account` az megfelel az egyéni lemez tárolására használt Storage-fióknak. Nem kell ugyanazt a tárolót használnia, mint a virtuális gépek tárolására szolgáló egyéni lemezt. Ügyeljen arra, hogy a további tárolókat ugyanúgy hozza létre, mint a korábbi lépéseket az egyéni lemez feltöltése előtt.

Az alábbi példa egy nevű virtuális gépet hoz létre az `myVM` Egyéni lemezről:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Továbbra is meg kell adnia vagy válaszolnia kell az alkalmazáshoz, az az **VM Create** parancs által megkövetelt további paramétereket, például a felhasználónevet és az SSH-kulcsokat.


## <a name="resource-manager-template"></a>Resource Manager-sablon
Azure Resource Manager sablonok JavaScript Object Notation (JSON) fájlok, amelyek meghatározzák a létrehozni kívánt környezetet. A sablonok különböző erőforrás-szolgáltatókkal (például számítás vagy hálózat) vannak lebontva. Használhat meglévő sablonokat, vagy írhat sajátot is. További információ a [Resource Manager és a sablonok használatáról](../../azure-resource-manager/management/overview.md).

A `Microsoft.Compute/virtualMachines` sablon szolgáltatóján belül egy olyan `storageProfile` csomóponttal rendelkezik, amely a virtuális gép konfigurációs adatait tartalmazza. A szerkeszteni kívánt két fő paraméter az `image` `vhd` Egyéni lemezre és az új virtuális gép virtuális lemezére mutató URI. Az alábbi példa egy egyéni lemez használatára szolgáló JSON-példát mutat be:

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

[Ezzel a meglévő sablonnal létrehozhat egy virtuális gépet egyéni rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) , vagy megtekintheti [saját Azure Resource Manager sablonjainak létrehozását](../../azure-resource-manager/templates/template-syntax.md). 

Miután konfigurálta a sablont, az [az Group Deployment Create](/cli/azure/group/deployment) paranccsal hozhatja létre a virtuális gépeket. Adja meg a JSON-sablon URI-JÁT a `--template-uri` paraméterrel:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Ha a számítógépen helyileg tárolt JSON-fájl található, a `--template-file` paramétert használhatja helyette:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>További lépések
Miután felkészítette és feltöltötte az egyéni virtuális lemezt, további információt olvashat a [Resource Manager és a sablonok használatáról](../../azure-resource-manager/management/overview.md). Előfordulhat, hogy [adatlemezt](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) is szeretne hozzáadni az új virtuális gépekhez. Ha olyan alkalmazások futnak a virtuális gépeken, amelyekhez hozzá kell férnie, ne felejtse el [megnyitni a portokat és a végpontokat](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).