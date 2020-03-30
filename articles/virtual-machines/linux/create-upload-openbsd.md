---
title: OpenBSD-lemezkép létrehozása és feltöltése
description: Ismerje meg, hogyan hozhat létre és tölthet fel olyan virtuális merevlemezt (VHD), amely az OpenBSD operációs rendszert tartalmazza, és azure-beli virtuális gépet hozhat létre az Azure CLI-n keresztül
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 1ad1a66d67be7aefe4d9a7acae993e8788cbb193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066740"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>OpenBSD lemezkép létrehozása és feltöltése az Azure-ba
Ez a cikk bemutatja, hogyan lehet létrehozni és feltölteni egy virtuális merevlemezt (VHD), amely tartalmazza az OpenBSD operációs rendszert. Miután feltöltötte, saját lemezképként használhatja egy virtuális gép (VM) létrehozásához az Azure CLI-n keresztül az Azure CLI-n keresztül.


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy a következő elemekkel rendelkezik:

* **Azure-előfizetés** – Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egyet. Ha MSDN-előfizetéssel rendelkezik, olvassa el [a Visual Studio-előfizetőkhavi Azure-kreditjét.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Ellenkező esetben megtudhatja, hogyan [hozhat létre ingyenes próbafiókot.](https://azure.microsoft.com/pricing/free-trial/)  
* **Azure CLI** – Győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-azure-cli) telepítve van, és az az bejelentkezéssel be van jelentkezve az Azure-fiókjába. [az login](/cli/azure/reference-index)
* **A .vhd fájlban telepített OpenBSD operációs rendszert** - Egy támogatott OpenBSD operációs rendszert ([6.6 verzió AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) virtuális merevlemezre kell telepíteni. Több eszköz létezik a .vhd fájlok létrehozásához. Például egy virtualizációs megoldás, például a Hyper-V segítségével létrehozhatja a .vhd fájlt, és telepítheti az operációs rendszert. A Hyper-V telepítéséről és használatáról a [Hyper-V telepítése és virtuális gép létrehozása című témakörben](https://technet.microsoft.com/library/hh846766.aspx)talál.


## <a name="prepare-openbsd-image-for-azure"></a>OpenBSD-lemezkép előkészítése az Azure-hoz
Azon a virtuális gépen, ahol telepítette az OpenBSD 6.1 operációs rendszert, amely a Hyper-V támogatást adta hozzá, hajtsa végre az alábbi eljárásokat:

1. Ha a DHCP nincs engedélyezve a telepítés során, engedélyezze a szolgáltatást az alábbiak szerint:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Soros konzol beállítása az alábbiak szerint:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. A csomag telepítésének konfigurálása az alábbiak szerint:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Alapértelmezés szerint `root` a felhasználó le van tiltva az Azure-beli virtuális gépeken. A felhasználók emelt szintű jogosultságokkal `doas` futtathatnak parancsokat az OpenBSD virtuális gép parancsával. A Doas alapértelmezés szerint engedélyezve van. További információ: [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Az Azure-ügynök előfeltételeinek telepítése és konfigurálása az alábbiak szerint:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Az Azure-ügynök legújabb kiadása mindig megtalálható a [GitHubon.](https://github.com/Azure/WALinuxAgent/releases) Az ügynököt a következőképpen telepítheti:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Az Azure Agent telepítése után érdemes ellenőrizni, hogy az a következőképpen fut-e:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. A rendszer kitakarítása és újbóli kiépítésre való alkalmassá a kitakarítása. A következő parancs törli az utolsó kiépített felhasználói fiókot és a kapcsolódó adatokat is:

    ```sh
    waagent -deprovision+user -force
    ```

Most már leállíthatja a virtuális gép.


## <a name="prepare-the-vhd"></a>A virtuális merevlemez előkészítése
A VHDX formátum nem támogatott az Azure-ban, csak **a rögzített virtuális merevlemez**. A lemezt a Hyper-V Manager vagy a Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) parancsmag használatával rögzített VHD formátumra konvertálhatja. Egy példa a következő.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Tárolási erőforrások létrehozása és feltöltés
Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

A virtuális merevlemez feltöltéséhez hozzon létre egy tárfiókot az [az storage-fiók létrehozása.](/cli/azure/storage/account) A tárfiókneveknek egyedinek kell lenniük, ezért adja meg a saját nevét. A következő példa létrehoz egy *mystorageaccount nevű tárfiókot:*

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

A tárfiókhoz való hozzáférés szabályozásához szerezze be a tárkulcs az [az storage-fiókkulcsainak listájával](/cli/azure/storage/account/keys) az alábbiak szerint:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

A feltöltött Virtuális gépek logikai elkülönítéséhez hozzon létre egy tárolót a tárfiókon belül az az storage container create:To logically separate the VHDs you upload, create a container in the storage account with [az storage container create:](/cli/azure/storage/container)

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Végül töltse fel a virtuális merevlemezt [az az storage blob feltöltésével](/cli/azure/storage/blob) az alábbiak szerint:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Virtuális gép létrehozása a virtuális merevlemezről
Létrehozhat egy virtuális gép egy [minta parancsfájlt,](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) vagy közvetlenül [az az vm create](/cli/azure/vm). A feltöltött OpenBSD vHD megadásához `--image` használja a következő paramétert:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Szerezze be az OpenBSD virtuális gép IP-címét [az vm list-ip-címekkel](/cli/azure/vm) az alábbiak szerint:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Most már ssh-t is használhatsz az OpenBSD VM-edhez a szokásos módon:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>További lépések
Ha többet szeretne tudni az OpenBSD6.1 Hyper-V támogatásáról, olvassa el az [OpenBSD 6.1](https://www.openbsd.org/61.html) és [hyperv.4 című témakört.](https://man.openbsd.org/hyperv.4)

Ha felügyelt lemezről szeretne virtuális géphez létrehozni, olvassa el [az az lemezt.](/cli/azure/disk) 
