---
title: "Töltse fel az egyéni Linux képet az Azure CLI 1.0 |} Microsoft Docs"
description: "Hozzon létre, és egy virtuális merevlemez (VHD) feltöltése az Azure-bA a Resource Manager üzembe helyezési modellel és az Azure CLI 1.0 használatával egyéni Linux képének."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: ca4c6cb9296028275b2b032af0c94baabeec1223
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Töltse fel, és az egyéni lemezképet Linux virtuális gép létrehozása az Azure CLI 1.0
Ez a cikk bemutatja, hogyan egy virtuális merevlemez (VHD) feltöltése az Azure Resource Manager telepítési modellel, és a Linux virtuális gépek létrehozása az egyéni lemezképet. Ez a funkció lehetővé teszi telepítése és konfigurálása a Linux distro az igényeinek megfelelően, valamint, hogy a virtuális merevlemez használatával gyorsan hozzon létre az Azure virtuális gépek (VM).


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#quick-commands) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.


## <a name="quick-commands"></a>Gyors parancsok
Ha szeretné gyorsan a feladatnak a, a következő szakasz részleteit a következő parancsokat egy virtuális Gépet az Azure-bA feltölteni. Részletes információkat és a környezetben az egyes lépések a dokumentum többi részén található [itt indítása](#requirements).

Győződjön meg arról, hogy rendelkezik [az Azure CLI 1.0](../../cli-install-nodejs.md) jelentkezett be, és a Resource Manager módot használ:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre `myResourceGroup`, `mystorageaccount`, és `myimages`.

Először hozzon létre egy erőforráscsoportot. Az alábbi példa létrehoz egy erőforráscsoportot `myResourceGroup` a a `WestUs` helye:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

Hozzon létre egy tárfiókot, a virtuális lemezek tárolására. Az alábbi példa létrehoz egy nevű tárfiók `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

A tárfiók hozzáférési kulcsainak listázása. Jegyezze fel a `key1`:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

A storage-fiókra a kapott kulcsot belül tárolókat hozhat létre. Az alábbi példa létrehoz egy nevű tárolót `myimages` használ a tárolási értékének `key1`:

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Végezetül a VHD-fájlt feltölti a létrehozott tároló. Adja meg a helyi elérési útját a virtuális merevlemez alapján `/path/to/disk/mydisk.vhd`:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Mostantól létrehozhat egy virtuális Gépet a feltöltött virtuális lemez [Resource Manager-sablon használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Az URI-t a lemez megadásával is használhatja a parancssori felület (`--image-urn`). Az alábbi példakód létrehozza a virtuális gépek nevű `myVM` korábban feltöltött a virtuális lemez segítségével:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

A cél tárfiókkal nem lehet ugyanaz, mint ahol a virtuális lemez feltöltött. Is meg kell adni, vagy válasz kér, által igényelt minden további paramétereket a `azure vm create` parancs például a virtuális hálózat, a nyilvános IP-cím, a felhasználónevet és az SSH-kulcsok. További információ a [érhető el erőforrás-kezelő parancssori paraméterek](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Követelmények
A következő lépések elvégzéséhez szüksége:

* **Linux operációs rendszer van telepítve, a .vhd-fájllá** -telepíteni egy [Azure által támogatott Linux-disztribúció](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy lásd: [nem támogatott disztribúciókkal kapcsolatos információi](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) a virtuális merevlemez virtuális lemezre formátumban. Több különféle eszköz létezik a virtuális gép és a virtuális merevlemez létrehozásához:
  * Telepítse és konfigurálja [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy [KVM](http://www.linux-kvm.org/page/RunningKVM), ügyelve arra, hogy a virtuális merevlemez használata a képformátum. Ha szükséges, akkor [lemezkép konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) használatával `qemu-img convert`.
  * Is használhatja a Hyper-V [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [Windows Server 2012 vagy 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális Gépet, adja meg a VHD formátumban. Szükség esetén, VHDX-lemezek konvertálása virtuális merevlemez használatával [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-parancsmagot. További Azure nem támogatja dinamikus virtuális merevlemezek, feltöltése, ezért ilyen lemezek konvertálása statikus virtuális merevlemezek feltöltés előtt meg kell. Használhatja például a [NYISSA meg az Azure virtuális merevlemez segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) átalakítani a dinamikus lemezek Azure feltöltése során.
> 
> 

* Az egyéni lemezkép alapján létrehozott virtuális gépek ugyanazt a tárfiókot, a lemezképet kell lennie
  * Hozzon létre egy tárfiók és tároló, mind az egyéni lemezképet, mind a létrehozott virtuális gépek tárolására
  * Miután létrehozta a virtuális gépek, nyugodtan törölheti a kép

Győződjön meg arról, hogy rendelkezik [az Azure CLI 1.0](../../cli-install-nodejs.md) jelentkezett be, és a Resource Manager módot használ:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre `myResourceGroup`, `mystorageaccount`, és `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>A feltölteni kívánt lemezkép előkészítése
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


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Erőforráscsoportok logikailag összegyűjteni az Azure erőforrások, például a virtuális hálózati és tárolási a virtuális gép támogatása érdekében. Tudjon meg többet az [Azure erőforráscsoportokat Itt](../../azure-resource-manager/resource-group-overview.md). Feltöltése az egyéni lemezképet, és a virtuális gépek létrehozását, mielőtt először hozzon létre egy erőforráscsoportot. 

Az alábbi példa létrehoz egy erőforráscsoportot `myResourceGroup` a a `WestUS` helye:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Create a storage account
Virtuális gépek lapblobokat egy tárfiókon belül tárolódnak. Tudjon meg többet az [itt az Azure blob storage](../../storage/common/storage-introduction.md#blob-storage). Az egyéni lemezképet, és a virtuális gépek storage-fiók létrehozása. A virtuális gépeket hoz létre az egyéni lemezképet kell, hogy képként ugyanazt a tárfiókot.

Az alábbi példa létrehoz egy nevű tárfiók `mystorageaccount` a korábban létrehozott erőforráscsoport:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Tárfiókkulcsok listázása
Az Azure létrehoz két 512 bites kulcsot minden tárfiók. A tárelérési kulcsok írási műveletek végrehajtására többek között a tárolási fiók hitelesítéséhez használt. Tudjon meg többet az [tárolási itt történő hozzáférés felügyeletéhez](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). A tárelérési kulcsok megtekintéséhez a `azure storage account keys list` parancsot.

A létrehozott tárfiók hozzáférési kulcsainak megtekintése:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
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
Az Ön által létrehozott különböző könyvtárak, hogy logikusan rendszerezhesse az a helyi fájlrendszer ugyanúgy hozzon létre egy tárfiókot, a virtuális lemezek és a képek rendszerezéséhez tárolókra. A storage-fiók korlátlan számú tárolót tárolhat tartalmazhat. 

Az alábbi példa létrehoz egy nevű tárolót `myimages`, az előző lépésben kapott hozzáférési kulcsot (`key1`):

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Virtuális merevlemez feltöltéséhez
Most ténylegesen feltöltheti az egyéni lemezképet. A virtuális gép által használt összes virtuális lemezek, töltse fel, és tárolja az egyéni lemezképet oldalblobként.

Adja meg a hozzáférési kulcsot a tároló létrehozott az előző lépést, és elérési útját az egyéni lemezképet a helyi számítógépen:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Egyéni lemezképet a virtuális gép létrehozása
Amikor virtuális gépeket hoz létre az egyéni lemezképet, adja meg az URI-t a lemezképet. Győződjön meg arról, hogy a rendeltetési tárolási fiók megfelel az egyéni lemezképet tároló. A virtuális Gépet az Azure CLI vagy erőforrás-kezelő JSON-sablon használatával hozhat létre.

### <a name="create-a-vm-using-the-azure-cli"></a>Hozzon létre egy virtuális Gépet az Azure parancssori felület használatával
Adja meg, hogy a `--image-urn` paraméterrel a `azure vm create` parancsot úgy, hogy az egyéni lemezképet mutasson. Győződjön meg arról, hogy `--storage-account-name` megegyezik a tárfiókot, az egyéni lemezképet tároló. Nem kell ugyanabban a tárolóban használják az egyéni lemezképet a virtuális gépek tárolására. Ügyeljen arra, hogy az egyéni lemezképek feltöltés előtt hozzon létre semmilyen további tárolókat ugyanúgy, mint a korábbi lépéseket.

Az alábbi példakód létrehozza a virtuális gépek nevű `myVM` származó az egyéni lemezképet:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Meg kell adni, vagy válasz kér, által igényelt minden további paramétereket a `azure vm create` parancs például a virtuális hálózat, a nyilvános IP-cím, a felhasználónevet és az SSH-kulcsok. További információ a [érhető el erőforrás-kezelő parancssori paraméterek](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>A virtuális gép egy JSON-sablon létrehozása
Az Azure Resource Manager-sablonok olyan JavaScript Object Notation (JSON), amelyeket a környezet létrehozása build kíván. A sablonok megszakadnak a például számítási és hálózati különböző erőforrás-szolgáltatók. Meglévő sablonok használhatja, vagy a saját írni. Tudjon meg többet az [erőforrás-kezelő és a sablonok használatával](../../azure-resource-manager/resource-group-overview.md).

Belül a `Microsoft.Compute/virtualMachines` a sablon-szolgáltatót, hogy egy `storageProfile` csomópont, amely tartalmazza a konfigurációs részleteket a virtuális gép számára. A két fő paraméterek szerkesztése a `image` és `vhd` URI-azonosítók, amelyek az új virtuális gép virtuális lemez és az egyéni lemezképet. A következő egyéni lemezképet a JSON példáját mutatja be:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Használhat [a meglévő sablont, és hozzon létre egy virtuális Gépet egy egyéni lemezképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) vagy foglalkozó témakört [a saját Azure Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md). 

Ha már konfigurált egy sablon, a virtuális gépek létrehozása a `azure group deployment create` parancsot. Adja meg a JSON sablonok az URI a `--template-uri` paraméter:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Ha egy számítógépen helyben tárolt JSON-fájl, használhatja a `--template-file` paraméter helyette:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Következő lépések
Miután előkészített és feltöltése az egyéni virtuális lemez, akkor további információ [erőforrás-kezelő és a sablonok használatával](../../azure-resource-manager/resource-group-overview.md). Is érdemes lehet [hozzá adatlemezt](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) az új virtuális gépek. Ha fut a virtuális gépek elérését igénylő alkalmazások, ügyeljen arra, hogy [nyisson meg portokat és a végpontok](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

