---
title: A PaaS-erőforrások hálózati hozzáférésének korlátozása – Azure CLI
description: Ebben a cikkben megtudhatja, hogyan korlátozhatja és korlátozhatja a hálózati hozzáférést az Azure-erőforrásokhoz, például az Azure Storage-hoz és az Azure SQL Database-hez, az Azure CLI használatával a virtuális hálózati szolgáltatás végpontjaival.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: f2dcc714bc9052dd51f114e24f0b9bd74b87480c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186405"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>A PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása virtuális hálózati szolgáltatás-végpontokkal az Azure CLI használatával

Virtuális hálózati szolgáltatásvégpontokkal egy adott virtuális hálózati alhálózatra korlátozható az egyes Azure-szolgáltatási erőforrásokhoz való hálózati hozzáférés. Emellett teljesen le is tiltható az internetes hozzáférés az erőforrásokhoz. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és a támogatott Azure-szolgáltatások között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-erőforrások felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Virtuális hálózat létrehozása egyetlen alhálózattal
* Alhálózat hozzáadása és szolgáltatásvégpont engedélyezése
* Azure-erőforrás létrehozása és hálózati hozzáférés engedélyezése az erőforráshoz egyetlen alhálózatról
* Virtuális gép (VM) üzembe helyezése az egyes alhálózatokon
* Erőforráshoz való alhálózati hozzáférés ellenőrzése
* Erőforráshoz való alhálózati és internetes hozzáférés letiltásának ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz és a cikkben létrehozott összes többi erőforráshoz. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Hozzon létre egy virtuális hálózatot egy alhálózattal [az az hálózati virtuális hálózat létrehozása .](/cli/azure/network/vnet)

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése 

A szolgáltatásvégpontok csak a szolgáltatás végpontokat támogató szolgáltatások engedélyezheti. Az azure-beli helyen elérhető szolgáltatásvégpont-alapú szolgáltatások megtekintése [az az hálózati vnet-list-endpoint-szolgáltatásokkal.](/cli/azure/network/vnet) A következő példa az *eastus* régióban elérhető szolgáltatás-végpont-kompatibilis szolgáltatások listáját adja vissza. A visszaadott szolgáltatások listája idővel növekedni fog, ahogy egyre több Azure-szolgáltatás válik szolgáltatásvégpont-engedélyezve.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Hozzon létre egy további alhálózatot a virtuális hálózatban az [hálózati virtuális hálózat alhálózatának létrehozása segítségével.](/cli/azure/network/vnet/subnet) Ebben a példában létrejön egy szolgáltatásvégpont a *Microsoft.Storage* számára az alhálózathoz: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Alhálózat hálózati hozzáférésének korlátozása

Hozzon létre egy hálózati biztonsági csoportot [az az network nsg create](/cli/azure/network/nsg)segítségével. A következő példa létrehoz egy *myNsgPrivate*nevű hálózati biztonsági csoportot.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Társítsa a hálózati biztonsági csoportot a *privát* alhálózathoz az [az hálózati virtuális hálózat alhálózati frissítésével.](/cli/azure/network/vnet/subnet) A következő példa a *myNsgPrivate* hálózati biztonsági csoportot társítja a *privát* alhálózathoz:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Hozzon létre biztonsági szabályokat az [az network nsg szabály létrehozásával.](/cli/azure/network/nsg/rule) A következő szabály lehetővé teszi a kimenő hozzáférést az Azure Storage-szolgáltatáshoz rendelt nyilvános IP-címekhez: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Minden hálózati biztonsági csoport több [alapértelmezett biztonsági szabályt](security-overview.md#default-security-rules)tartalmaz. Az alábbi szabály felülbírálja az alapértelmezett biztonsági szabályt, amely lehetővé teszi a kimenő hozzáférést az összes nyilvános IP-címhez. A `destination-address-prefix "Internet"` beállítás megtagadja a kimenő hozzáférést az összes nyilvános IP-címhez. Az előző szabály felülbírálja ezt a szabályt, a magasabb prioritás miatt, amely lehetővé teszi a hozzáférést az Azure Storage nyilvános IP-címeihez.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

A következő szabály lehetővé teszi, hogy az SSH-forgalom bárhonnan befelé határoljon. Ez a szabály felülbírálja azon alapértelmezett biztonsági szabályokat, amelyek elutasítanak minden bejövő forgalmat az internetről. Az SSH az alhálózathoz engedélyezett, így a kapcsolat egy későbbi lépésben tesztelhető.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Erőforráshoz való hálózati hozzáférés korlátozása

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. Ez a cikk további lépések az Azure Storage-fiók hálózati hozzáférésének korlátozására, példaként tartalmazza.

### <a name="create-a-storage-account"></a>Create a storage account

Hozzon létre egy Azure-tárfiókot [az az storage-fiók létrehozása.](/cli/azure/storage/account) Cserélje `<replace-with-your-unique-storage-account-name>` le egy olyan névre, amely az összes Azure-helyen egyedi, 3–24 karakter hosszú, csak számokat és kisbetűket használva.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

A tárfiók létrehozása után olvassa be a tárfiók kapcsolati karakterláncát egy változóba, amelynek [az az storage-fiók show-connection-string.](/cli/azure/storage/account) A kapcsolati karakterlánc egy későbbi lépésben fájlmegosztás létrehozására szolgál.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Tekintse meg a változó tartalmát, és vegye figyelembe a kimenetben visszaadott **AccountKey** értékét, mert azt egy későbbi lépésben használják.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

Hozzon létre egy fájlmegosztást a tárfiókban az [az storage share create](/cli/azure/storage/share)segítségével. Egy későbbi lépésben ez a fájlmegosztás csatlakoztatva van a hálózati hozzáférés megerősítéséhez.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Tárfiók hoz való összes hálózati hozzáférés megtagadása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. A kijelölt hálózatokhoz való hozzáférés korlátozásához módosítsa az alapértelmezett műveletet *Megtagadás* az [az tárfiók frissítésével .](/cli/azure/storage/account) Ha a hálózati hozzáférés le van tiltva, a tárfiók egyetlen hálózatról sem érhető el.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Hálózati hozzáférés engedélyezése alhálózatról

Hálózati hozzáférés engedélyezése a tárfiókhoz a *magánes* alhálózatból az [az storage account hálózati szabály hozzáadásával.](/cli/azure/storage/account/network-rule)

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Tárfiókhoz való hálózati hozzáférés teszteléséhez helyezzen üzembe egy virtuális gépet minden alhálózaton.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gép a *nyilvános* alhálózatban [az vm létrehozása.](/cli/azure/vm) Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI a következő példához hasonló információkat jelenít meg: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Vegye figyelembe a **publicIpAddress** a visszaadott kimeneten. Ez a cím egy későbbi lépésben az internetről való eléréséhez használható.

### <a name="create-the-second-virtual-machine"></a>A második virtuális gép létrehozása

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A létrehozás után vegye figyelembe a **nyilvánosIpAddress** a visszaadott kimenetben. Ez a cím egy későbbi lépésben az internetről való eléréséhez használható.

## <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

SSH a *myVmPrivate* virtuális gépbe. Cserélje le * \<a publicIpAddress>* a *myVmPrivate* virtuális gép nyilvános IP-címére.

```bash 
ssh <publicIpAddress>
```

Hozzon létre egy mappát egy csatlakoztatási ponthoz:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Csatlakoztassa az Azure-fájlmegosztást a létrehozott könyvtárhoz. A következő parancs futtatása előtt cserélje le `<storage-account-name>` a fiók nevét és `<storage-account-key>` a [Tárfiók létrehozása című](#create-a-storage-account)részben beolvasott kulcsot.

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Megjelenik `user@myVmPrivate:~$` a kérdés. Az Azure-fájlmegosztás sikeresen csatlakoztatva a */mnt/MyAzureFileShare*fájlhoz.

Győződjön meg arról, hogy a virtuális gép nem rendelkezik kimenő kapcsolattal más nyilvános IP-címekhez:

```bash
ping bing.com -c 4
```

Nem kap választ, mert a *Magánjellegű* alhálózathoz rendelt hálózati biztonsági csoport nem engedélyezi a kimenő hozzáférést olyan nyilvános IP-címekhez, amelyek nem az Azure Storage szolgáltatáshoz rendelt címek.

Lépjen ki az SSH-munkamenetből a *myVmPrivate* virtuális gépre.

## <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

A következő paranccsal ssh-munkamenetet hozhat létre a *myVmPublic* virtuális géppel. Cserélje `<publicIpAddress>` le a *myVmPublic* VM nyilvános IP-címét: 

```bash 
ssh <publicIpAddress>
```

Hozzon létre könyvtárat egy csatlakoztatási ponthoz:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Próbálja meg csatlakoztatni az Azure-fájlmegosztást a létrehozott könyvtárhoz. Ez a cikk feltételezi, hogy az Ubuntu legújabb verzióját telepítette. Ha az Ubuntu korábbi verzióit használja, a fájlmegosztások csatlakoztatásáról további útmutatást a [Mount on Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oldalon talál. A következő parancs futtatása előtt cserélje le `<storage-account-name>` a fiók névre és `<storage-account-key>` a Tárfiók létrehozása című csoportban beolvasott [kulcsra:](#create-a-storage-account)

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

A hozzáférés megtagadva, `mount error(13): Permission denied` és hibaüzenetet kap, mert a *myVmPublic* virtuális gép a *nyilvános* alhálózaton belül van telepítve. A *Nyilvános* alhálózat nem rendelkezik az Azure Storage-hoz engedélyezett szolgáltatásvégponttal, és a tárfiók kizárólag a *Magánjellegű* alhálózatról engedélyezi a hozzáférést, a *Nyilvános* alhálózatról nem.

Lépjen ki az SSH-munkamenetből a *myVmPublic* virtuális gépre.

A számítógépről próbálja meg megtekinteni a tárfiók megosztási adatait az [az storage share list](/cli/azure/storage/share?view=azure-cli-latest)ával. A `<account-name>` `<account-key>` tárfiók és a kulcs cseréje és a [tárfiók létrehozása](#create-a-storage-account)szolgáltatásból származó kulcs cseréje:

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

A hozzáférés megtagadva, és ez *a kérés nem jogosult a művelet végrehajtására,* mert a számítógép nem a *MyVirtualNetwork* virtuális hálózat *privát* alhálózatában van.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az csoport törlésével](/cli/azure) távolítsa el az erőforráscsoportot és az összes benne lévő erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben engedélyezte a szolgáltatásvégpontot egy virtuális hálózati alhálózathoz. Megismerte, hogy az Azure-szolgáltatásokkal üzembe helyezett erőforrásokhoz szolgáltatásvégpontok engedélyezhetők. Létrehozott egy Azure Storage-fiókot, és egy adott virtuális hálózati alhálózat erőforrásaira korlátozta a fiók felé irányuló hálózati hozzáférést. További információkat a szolgáltatásvégpontokról a [szolgáltatásvégpontok áttekintését](virtual-network-service-endpoints-overview.md) és az [alhálózatok kezelését](virtual-network-manage-subnet.md) ismertető cikkekben olvashat.

Ha több virtuális hálózat található a fiókjában, érdemes lehet összekapcsolni két virtuális hálózatot, hogy az egyes virtuális hálózatokban található erőforrások kommunikálhassanak egymással. Ebből, hogy miként, olvassa el [a Virtuális hálózatok csatlakoztatása ..](tutorial-connect-virtual-networks-cli.md)
