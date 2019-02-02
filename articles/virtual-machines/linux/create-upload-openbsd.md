---
title: Hozzon létre, és a egy OpenBSD Virtuálisgép-Rendszerkép feltöltése az Azure-bA |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és töltse fel a virtuális merevlemez (VHD), amely tartalmazza az OpenBSD operációs rendszert az Azure CLI-n keresztül az Azure virtuális gép létrehozása
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: b31425849eacc0b1f88e8dbd623804cefff9112f
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662754"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Hozzon létre és OpenBSD lemez Rendszerkép feltöltése az Azure-bA
Ez a cikk bemutatja, hogyan hozhat létre és töltse fel a virtuális merevlemez (VHD), amely tartalmazza az OpenBSD operációs rendszert. Miután feltölti azt, segítségével azt a saját rendszerkép hozzon létre egy virtuális gépet (VM) az Azure-ban az Azure CLI-n keresztül.


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy rendelkezik-e a következő elemek:

* **Azure-előfizetés** – Ha nincs fiókja, létrehozhat egyet, néhány perc alatt. Ha rendelkezik MSDN-előfizetéssel, lásd: [havi Azure-kredit a Visual Studio-előfizetők](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ellenkező esetben megtudhatja, hogyan [hozzon létre egy ingyenes próbafiókot](https://azure.microsoft.com/pricing/free-trial/).  
* **Az Azure CLI** – ellenőrizze, hogy a legújabb [Azure CLI-vel](/cli/azure/install-azure-cli) telepítve, és az Azure-fiókjával bejelentkezett [az bejelentkezési](/cli/azure/reference-index#az_login).
* **OpenBSD operációs rendszer telepítve van egy .vhd fájl** – egy operációs rendszer támogatott OpenBSD ([6.1-es verzió AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.1/amd64/)) virtuális merevlemezt kell telepíteni. Több eszközt található .vhd fájlok létrehozásához. Például használhatja például a Hyper-V virtualizálási megoldás létrehozása a .vhd fájlt, és az operációs rendszer telepítése. Telepítse és használja a Hyper-V kapcsolatos útmutatásért lásd: [Hyper-V telepítése és a egy virtuális gép létrehozása](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Az Azure-ban OpenBSD lemezkép előkészítése
A virtuális gépre, amelyre telepítve van az OpenBSD operációs rendszer 6.1, amelyeket hozzá a Hyper-V támogatja, hajtsa végre az alábbi eljárásokat:

1. A DHCP telepítés során nincs engedélyezve, ha engedélyezze a szolgáltatást a következőképpen:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Az alábbiak szerint állíthatja a soros konzol:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Csomag telepítése a következőképpen konfigurálja:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Alapértelmezés szerint a `root` felhasználó le van tiltva, az Azure-beli virtuális gépeken. Felhasználók futtathat parancsokat emelt szintű jogosultságokkal használatával a `doas` OpenBSD VM parancsot. Doas alapértelmezés szerint engedélyezve van. További információkért lásd: [doas.conf](http://man.openbsd.org/doas.conf.5). 

5. Telepítse és konfigurálja az Azure-ügynök előfeltételei a következők szerint:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Az Azure-ügynök legújabb kiadása mindig találhatók [GitHub](https://github.com/Azure/WALinuxAgent/releases). Az ügynököt a következőképpen telepítheti:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Az Azure-ügynök telepítése után célszerű győződjön meg arról, hogy fut a következő:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. A rendszer megtisztítsa tőle, és lehetővé teszi a megfelelő reprovisioning megszüntetése. A következő parancsot a az utoljára kiépített felhasználói fiókot és a társított adatok is törli:

    ```sh
    waagent -deprovision+user -force
    ```

Mostantól leállíthatja a virtuális gép.


## <a name="prepare-the-vhd"></a>A virtuális merevlemez előkészítése
A VHDX formátum nem támogatott az Azure-ban, csak **rögzített VHD**. A lemez konvertálása Hyper-V kezelőjével vagy a Powershell használatával rögzített VHD formátumú [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) parancsmagot. Ilyen például, mint következő.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Tárolási erőforrások létrehozása és feltöltése
Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Töltse fel a VHD-t, hozzon létre egy tárfiókot [az tárfiók létrehozása](/cli/azure/storage/account#az_storage_account_create). Tárfiókok nevének egyedinek kell lenniük, így adja meg a saját nevét. Az alábbi példa létrehoz egy tárfiókot, nevű *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

A tárfiókhoz való hozzáférés szabályozásához, szerezze be a tárfiók hívóbetűjét, a [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#az_storage_account_keys_list) módon:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Logikai szétválasztására a feltöltött virtuális merevlemezeket, hozzon létre egy tárolót, a tárfiókban található [az a tároló létrehozása](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Végezetül töltse fel a VHD-t a [az storage blob feltöltése](/cli/azure/storage/blob#az_storage_blob_upload) módon:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Virtuális gép létrehozása a virtuális merevlemezről
Létrehozhat egy virtuális Gépet egy [mintaparancsfájl](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) vagy közvetlenül az [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). A feltöltött OpenBSD VHD megadásához használja a `--image` paramétert a következők szerint:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Az IP-cím beszerzése a virtuális gép OpenBSD [az vm list-ip-addresses](/cli/azure/vm#list-ip-addresses) módon:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Mostantól már Ön is SSH szokásos módon OpenBSD virtuális géphez:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>További lépések
Ha szeretne többet megtudni a OpenBSD6.1 Hyper-V támogatását, olvassa el [OpenBSD 6.1](https://www.openbsd.org/61.html) és [hyperv.4](http://man.openbsd.org/hyperv.4).

Ha szeretne egy virtuális gép létrehozása felügyelt lemezről, olvassa el [az lemez](/cli/azure/disk). 
