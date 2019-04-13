---
title: PaaS-erőforrásokhoz – Azure CLI-vel való hálózati hozzáférés korlátozása |} A Microsoft Docs
description: Ebben a cikkben megismerheti, hogyan korlátozható az Azure-erőforrások, például az Azure Storage és Azure SQL Database-hálózati hozzáférés korlátozása a virtuális hálózati Szolgáltatásvégpontok az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 4d93cfe78159fdf4ef3c34e8f80732603b701538
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521682"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása a virtuális hálózati Szolgáltatásvégpontok az Azure CLI használatával

Virtuális hálózati szolgáltatásvégpontokkal egy adott virtuális hálózati alhálózatra korlátozható az egyes Azure-szolgáltatási erőforrásokhoz való hálózati hozzáférés. Emellett teljesen le is tiltható az internetes hozzáférés az erőforrásokhoz. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és a támogatott Azure-szolgáltatások között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-erőforrások felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Virtuális hálózat létrehozása egyetlen alhálózattal
* Alhálózat hozzáadása és szolgáltatásvégpont engedélyezése
* Azure-erőforrás létrehozása és hálózati hozzáférés engedélyezése az erőforráshoz egyetlen alhálózatról
* Virtuális gép (VM) üzembe helyezése az egyes alhálózatokon
* Erőforráshoz való alhálózati hozzáférés ellenőrzése
* Erőforráshoz való alhálózati és internetes hozzáférés letiltásának ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Előtt egy virtuális hálózatot hoz létre, akkor hozzon létre egy erőforráscsoportot a virtuális hálózatot és más ebben a cikkben létrehozott összes erőforrást. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Hozzon létre egy virtuális hálózatot egyetlen alhálózattal rendelkező [az network vnet létrehozása](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése 

Engedélyezheti a szolgáltatásvégpontokat csak a szolgáltatások, amelyek támogatják a Szolgáltatásvégpontok. Szolgáltatási végpont engedélyezve van elérhető szolgáltatások megtekintése az Azure-helyen [az network vnet--végpontszolgáltatások listázásához](/cli/azure/network/vnet). Az alábbi példa az elérhető szolgáltatások service-végpont engedélyezve van egy listáját adja vissza a *eastus* régióban. Visszaadott szolgáltatások listájában, idővel növekszik, ahogy további Azure-szolgáltatások válnak a szolgáltatásvégpont engedélyezve van.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Hozzon létre egy további alhálózatot a virtuális hálózaton [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet). Ebben a példában egy szolgáltatásvégpontot *Microsoft.Storage* jön létre az alhálózatot: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Alhálózat hálózati hozzáférésének korlátozása

Hozzon létre egy hálózati biztonsági csoport [az network nsg létrehozása](/cli/azure/network/nsg). A következő példában létrehozunk egy hálózati biztonsági csoport nevű *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

A hálózati biztonsági csoport társítása a *privát* alhálózat [az hálózati virtuális hálózat alhálózati frissítés](/cli/azure/network/vnet/subnet). Az alábbi példa hozzárendeli a *myNsgPrivate* hálózati biztonsági csoportot, amely a *privát* alhálózat:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

A biztonsági szabályok létrehozása [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule). A következő szabály lehetővé teszi, hogy a kimenő hozzáférést az Azure Storage szolgáltatáshoz rendelt nyilvános IP-címek: 

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

Minden egyes hálózati biztonsági csoport több tartalmaz [alapértelmezett biztonsági szabályokat](security-overview.md#default-security-rules). A következő szabály felülbírálja az összes nyilvános IP-címet a kimenő hozzáférést engedélyező alapértelmezett biztonsági szabály. A `destination-address-prefix "Internet"` beállítás megtagadja a kimenő hozzáférést az összes nyilvános IP-címek. Az előző szabályban, a magasabb prioritású, amely lehetővé teszi a hozzáférést a nyilvános IP-címek az Azure Storage miatt ez a szabály felülbírálja.

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

A következő szabály lehetővé teszi, hogy az SSH-forgalmat bárhonnan az alhálózatra bejövő. Ez a szabály felülbírálja azon alapértelmezett biztonsági szabályokat, amelyek elutasítanak minden bejövő forgalmat az internetről. SSH engedélyezett az alhálózathoz, hogy a kapcsolat egy későbbi lépésben tesztelhető legyen.

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

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. Ez a cikk további része az Azure Storage-fiókot, például a hálózati hozzáférés korlátozásához szükséges lépéseket tartalmazza.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy Azure storage-fiókkal [az tárfiók létrehozása](/cli/azure/storage/account). Cserélje le `<replace-with-your-unique-storage-account-name>` , egyedi el az összes Azure-helyen 3 – 24 karakter közötti hosszúságú, melynek neve használatával csak számokból és kisbetűkből állhat.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

A tárfiók létrehozása után a tárfiók kapcsolati karakterlánca olvasson be egy változót [az storage account show-connection-string](/cli/azure/storage/account). A kapcsolati karakterlánc segítségével hozzon létre fájlmegosztást egy későbbi lépésben.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Megtekintheti a változó tartalmát, és jegyezze fel az értékét a **AccountKey** adja vissza a kimenetben, mert használatban van egy későbbi lépésben.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

Hozzon létre fájlmegosztást a storage-fiókban [az tárolási fájlmegosztás létrehozása](/cli/azure/storage/share). Egy későbbi lépésben a fájlmegosztás csatlakoztatva van, győződjön meg arról, hogy a hálózati hozzáférés.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Storage-fiók összes hálózati hozzáférés megtagadása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. Kijelölt hálózatok való hozzáférés korlátozására, módosítsa az alapértelmezett művelet *Megtagadás* a [storage-fiók frissítése az](/cli/azure/storage/account). Hálózati hozzáférés megtagadva, miután a tárfiók nem érhető egyetlen hálózathoz sem.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Hálózati hozzáférés engedélyezése alhálózatról

Hálózati hozzáférés engedélyezése a storage-fiókjában az *privát* alhálózat [az tárolási fiók hálózati-szabály hozzáadása](/cli/azure/storage/account/network-rule).

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

A virtuális gép létrehozása a *nyilvános* alhálózat [az virtuális gép létrehozása](/cli/azure/vm). Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI információkat jelenít meg az alábbi példához hasonló: 

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

Jegyezze fel a **publicIpAddress** visszaadott kimenet. Ezzel a címmel eléri a virtuális Gépet egy későbbi lépésben az internetről.

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

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A létrehozás után jegyezze fel a **publicIpAddress** a kimenetben adja vissza. Ezzel a címmel eléri a virtuális Gépet egy későbbi lépésben az internetről.

## <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

SSH-t a *myVmPrivate* virtuális Gépet. Cserélje le  *\<publicIpAddress >* a nyilvános IP-címét a *myVmPrivate* virtuális Gépet.

```bash 
ssh <publicIpAddress>
```

Hozzon létre egy mappát a csatlakoztatási pont:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Csatlakoztassa az Azure-fájlmegosztás létrehozott könyvtárba. A következő parancs futtatása előtt cserélje le a `<storage-account-name>` a fiók nevére és `<storage-account-key>` lekért kulccsal [hozzon létre egy tárfiókot](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Kap a `user@myVmPrivate:~$` parancssort. Az Azure-fájlmegosztás sikeresen csatlakoztatva */mnt/MyAzureFileShare*.

Győződjön meg arról, hogy a virtuális gép nem rendelkezik kimenő kapcsolattal bármely nyilvános IP-címek:

```bash
ping bing.com -c 4
```

Nem kap választ, mert a *Magánjellegű* alhálózathoz rendelt hálózati biztonsági csoport nem engedélyezi a kimenő hozzáférést olyan nyilvános IP-címekhez, amelyek nem az Azure Storage szolgáltatáshoz rendelt címek.

Az SSH-munkamenetből történő kilépéshez a *myVmPrivate* virtuális Gépet.

## <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

A következő paranccsal hozhat létre az SSH-munkamenetből a *myVmPublic* virtuális Gépet. Cserélje le `<publicIpAddress>` a nyilvános IP-címét a *myVmPublic* virtuális Géphez: 

```bash 
ssh <publicIpAddress>
```

Hozzon létre egy könyvtárat a csatlakoztatási pont:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Próbálja meg csatlakoztatni az Azure-fájlmegosztás létrehozott könyvtárba. Ez a cikk feltételezi, hogy Ubuntu legújabb verzióját telepítette. Ha a korábbi Ubuntu használ, tekintse meg [csatlakoztatás linuxon](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) további útmutatás a fájlmegosztások csatlakoztatására. A következő parancs futtatása előtt cserélje le a `<storage-account-name>` a fiók nevére és `<storage-account-key>` lekért kulccsal [hozzon létre egy tárfiókot](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

A hozzáférés megtagadva, és a egy `mount error(13): Permission denied` hiba történt, mert a *myVmPublic* belül a virtuális gép üzemel a *nyilvános* alhálózat. A *Nyilvános* alhálózat nem rendelkezik az Azure Storage-hoz engedélyezett szolgáltatásvégponttal, és a tárfiók kizárólag a *Magánjellegű* alhálózatról engedélyezi a hozzáférést, a *Nyilvános* alhálózatról nem.

Az SSH-munkamenetből történő kilépéshez a *myVmPublic* virtuális Gépet.

A számítógépről kísérlet megtekintéséhez a megosztásokat a tárfiókban található [az tárolási megosztási listán](/cli/azure/storage/share?view=azure-cli-latest). Cserélje le `<account-name>` és `<account-key>` a tárfiók nevét és kulcsát, [hozzon létre egy tárfiókot](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

A hozzáférés megtagadva, és megjelenik egy *a kérelem nem jogosult e művelet elvégzéséhez* hiba, mert a számítógép nem szerepel a következőben a *privát* alhálózatának a *MyVirtualNetwork* virtuális hálózatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [az csoport törlése](/cli/azure) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben engedélyezett egy szolgáltatásvégpontot egy virtuális hálózat alhálózatához. Megismerte, hogy az Azure-szolgáltatásokkal üzembe helyezett erőforrásokhoz szolgáltatásvégpontok engedélyezhetők. Létrehozott egy Azure Storage-fiókot, és egy adott virtuális hálózati alhálózat erőforrásaira korlátozta a fiók felé irányuló hálózati hozzáférést. További információkat a szolgáltatásvégpontokról a [szolgáltatásvégpontok áttekintését](virtual-network-service-endpoints-overview.md) és az [alhálózatok kezelését](virtual-network-manage-subnet.md) ismertető cikkekben olvashat.

Ha több virtuális hálózat található a fiókjában, érdemes lehet összekapcsolni két virtuális hálózatot, hogy az egyes virtuális hálózatokban található erőforrások kommunikálhassanak egymással. További információ [virtuális hálózatok összekapcsolása](tutorial-connect-virtual-networks-cli.md).
