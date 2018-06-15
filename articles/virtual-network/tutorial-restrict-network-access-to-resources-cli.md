---
title: PaaS erőforrások – az Azure parancssori felület való hálózati hozzáférés korlátozása |} Microsoft Docs
description: Ebből a cikkből megismerheti, hogyan korlátozására és a hálózati hozzáférés korlátozása az Azure-erőforrások, például az Azure Storage és az Azure SQL Database, a virtuális hálózati szolgáltatás végpontokat az Azure parancssori felület használatával.
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
ms.openlocfilehash: f357861a7a44b249e06f091a8693b7f2d8dd5178
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841979"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Hálózati hozzáférés korlátozása PaaS erőforrások virtuális hálózati szolgáltatás végpontokon az Azure parancssori felület használatával

Virtuális hálózati szolgáltatási végpont lehetővé teszik a virtuális hálózati alhálózat az Azure szolgáltatás erőforrásokhoz való hálózati hozzáférés korlátozásához. Eltávolíthatja az internet-hozzáférés az erőforrásokhoz is. Végpontok adja meg a közvetlen kapcsolat a virtuális hálózat és a támogatott Azure-szolgáltatásokat, hogy lehetővé teszi a virtuális hálózat titkos címterület használata az Azure-szolgáltatások eléréséhez. Azure-erőforrások Szolgáltatásvégpontok keresztül mindig irányuló forgalom a Microsoft Azure hálózat marad. Ebből a cikkből megismerheti, hogyan:

* Hozzon létre egy virtuális hálózatot egyetlen alhálózattal
* Adjon hozzá egy alhálózatot és egy végpontot engedélyezése
* Hozzon létre egy Azure-erőforrás és a hálózati hozzáférés engedélyezése, hogy csak egy alhálózatból
* Virtuális gép (VM) telepítése minden egyes alhálózathoz
* Erősítse meg a hozzáférést egy erőforráshoz alhálózatból származó
* Győződjön meg arról, a hozzáférés megtagadva erőforrás alhálózat és az internetről

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Rendszererőforrásokra telepíti, és a parancssori felület helyileg, a gyors üzembe helyezés megköveteli, hogy futnak-e az Azure parancssori felület 2.0.28 verzió vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt hozzon létre egy erőforráscsoportot a virtuális hálózat, és ez a cikk létrehozott összes többi erőforrása van. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Hozzon létre egy virtuális hálózatot egyetlen alhálózattal rendelkező [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>A szolgáltatásvégpont engedélyezése 

Csak a végpontok támogató szolgáltatások Szolgáltatásvégpontok engedélyezheti. Tekintse meg az Azure-beli hely, a szolgáltatási végpont engedélyezve van az elérhető szolgáltatások [az hálózati lista-végpont-szolgáltatások vnet](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services). Az alábbi példa elérhető szolgáltatás-végpont-kompatibilis szolgáltatások listáját adja vissza a *eastus* régióban. A szolgáltatások visszaadott tájékoztatás idővel bővülni fog, több Azure-szolgáltatások egyre szolgáltatásvégpont engedélyezve van.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Hozzon létre egy további alhálózatot a virtuális hálózatban az [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Ebben a példában a szolgáltatásvégpont *Microsoft.Storage* az alhálózat jön létre: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Egy alhálózatot a hálózati hozzáférés korlátozása

Hozzon létre egy hálózati biztonsági csoport [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

A hálózati biztonsági csoportra, és társítsa a *titkos* alhálózat [az hálózati vnet alhálózati frissítés](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Az alábbi példa társítja a *myNsgPrivate* hálózati biztonsági csoportra, és a *titkos* alhálózati:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

A biztonsági szabályok létrehozása [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). A következő szabály lehetővé teszi, hogy a nyilvános IP-címek az Azure Storage szolgáltatás kimenő hozzáférést: 

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

Each network security group contains several [default security rules](security-overview.md#default-security-rules). The rule that follows overrides a default security rule that allows outbound access to all public IP addresses. The `destination-address-prefix "Internet"` option denies outbound access to all public IP addresses. The previous rule overrides this rule, due to its higher priority, which allows access to the public IP addresses of Azure Storage.

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

The following rule allows SSH traffic inbound to the subnet from anywhere. The rule overrides a default security rule that denies all inbound traffic from the internet. SSH is allowed to the subnet so that connectivity can be tested in a later step.

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

## <a name="restrict-network-access-to-a-resource"></a>Egy erőforrás való hálózati hozzáférés korlátozása

Azure-végpontok engedélyezett szolgáltatások segítségével létrehozott erőforrások való hálózati hozzáférés korlátozása szükséges lépések szolgáltatásban függően változik. Az egyes szolgáltatások lépéseit minden egyes szolgáltatás dokumentációjában találhat. Ez a cikk hátralévő része az Azure Storage-fiók, mint például a hálózati hozzáférés korlátozása.

### <a name="create-a-storage-account"></a>Create a storage account

Az Azure storage-fiók létrehozása [az storage-fiók létrehozása](/cli/azure/storage/account#az_storage_account_create). Cserélje le `<replace-with-your-unique-storage-account-name>` , minden Azure helyszínen, 3 – 24 karakter hosszúságúnak, egyedi névvel használatával csak számokat és kisbetűket tartalmazhatnak.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

A tárfiók létrehozása után kéri le a kapcsolati karakterláncot a tárfiók egy változóba a [az storage-fiók megjelenítése-kapcsolat-karakterlánc](/cli/azure/storage/account#az_storage_account_show_connection_string). A kapcsolati karakterlánc használatával létre fájlmegosztást egy későbbi lépésben.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Tekintheti meg a változó tartalmát, és jegyezze fel a következő **AccountKey** adja vissza a kimenetben, mert használatban van egy későbbi lépésben.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban lévő

Fájlmegosztás létrehozása a tárfiók [az storage-megosztás létrehozása](/cli/azure/storage/share#az_storage_share_create). Ezt a fájlmegosztást egy későbbi lépésben csatlakoztatva van a hálózati hozzáférést megerősítéséhez.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>A storage-fiók összes hálózati hozzáférés megtagadása

Alapértelmezés szerint a storage-fiókok bármely hálózatban lévő ügyfelek hálózati kapcsolatokat fogadjon. A kijelölt hálózatokhoz való hozzáférés korlátozásához, módosítsa az alapértelmezett művelet *Megtagadás* rendelkező [az tárolási fiók frissítés](/cli/azure/storage/account#az_storage_account_update). Ha a hálózati hozzáférés megtagadva a tárfiók nem érhető el egyetlen hálózathoz sem.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Egy alhálózatot a hálózati hozzáférés engedélyezése

A tárolási fiók a hálózati hozzáférés engedélyezése a *titkos* alhálózat [az tárolási fiók hálózati-szabály hozzáadása](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Tesztelje a storage-fiók a hálózati hozzáférést, telepítenie kell a virtuális gépek minden egyes alhálózatot.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

A virtuális gép létrehozása a *nyilvános* alhálózat [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Ha SSH-kulcsok még nem léteznek a kulcs alapértelmezett helye, a parancs létrehozza azokat. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló: 

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

Vegye figyelembe a **publicIpAddress** visszaadott kimenet. Ez a cím a virtuális gép egy későbbi lépésben az internetről való eléréséhez használt.

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

A virtuális gép létrehozásához néhány percet vesz igénybe. A létrehozás után vegye figyelembe a **publicIpAddress** visszaadott kimenet. Ez a cím a virtuális gép egy későbbi lépésben az internetről való eléréséhez használt.

## <a name="confirm-access-to-storage-account"></a>Erősítse meg a tárfiók eléréséhez

SSH-ból a *myVmPrivate* virtuális gép. Cserélje le *<publicIpAddress>* a nyilvános IP-címével a *myVmPrivate* virtuális gép.

```bash 
ssh <publicIpAddress>
```

Hozzon létre egy mappát egy csatlakoztatási pontot:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

A létrehozott könyvtárba Azure fájlmegosztás csatlakoztatása. A következő parancs futtatása előtt cserélje le a `<storage-account-name>` a fióknévvel és `<storage-account-key>` olvassa be a kulccsal [hozzon létre egy tárfiókot](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Megjelenik a `user@myVmPrivate:~$` kérdés. Sikeresen csatlakoztatva az Azure fájlmegosztások */mnt/MyAzureFileShare*.

Győződjön meg arról, hogy a virtuális gép rendelkezik-e a kimenő kapcsolat bármely nyilvános IP-címek:

```bash
ping bing.com -c 4
```

Nem érkezett válasz, mert a társított hálózati biztonsági csoport kapni a *titkos* alhálózat nem engedélyezi a nyilvános IP-címek kívül a címek az Azure Storage szolgáltatás kimenő hozzáférést.

Az SSH-munkamenetet, hogy kilép a *myVmPrivate* virtuális gép.

## <a name="confirm-access-is-denied-to-storage-account"></a>Erősítse meg a tárfiók a hozzáférés megtagadva

Az SSH-munkamenetet létrehozni, az alábbi parancs segítségével a *myVmPublic* virtuális gép. Cserélje le `<publicIpAddress>` a nyilvános IP-címével a *myVmPublic* VM: 

```bash 
ssh <publicIpAddress>
```

Hozzon létre egy könyvtárat egy csatlakoztatási pontot:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Kísérlet történt a címtárban létrehozott Azure fájlmegosztás csatlakoztatásához. Ez a cikk feltételezi, hogy telepítette az Ubuntu legújabb verzióját. Ha a korábbi Ubuntu használ, tekintse meg [Linux csatlakoztatási](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a fájlmegosztások csatlakoztatása kapcsolatos további utasításokat. A következő parancs futtatása előtt cserélje le a `<storage-account-name>` a fióknévvel és `<storage-account-key>` olvassa be a kulccsal [hozzon létre egy tárfiókot](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Nincs hozzáférése, és érkezik egy `mount error(13): Permission denied` hiba, mert a *myVmPublic* belül a virtuális gép telepítve van a *nyilvános* alhálózati. A *nyilvános* alhálózati nincs engedélyezve az Azure Storage szolgáltatásvégpont, és a tárfiók csak a hálózati hozzáférés lehetővé teszi a *titkos* alhálózati, nem a *nyilvános*alhálózat.

Az SSH-munkamenetet, hogy kilép a *myVmPublic* virtuális gép.

A számítógépről, próbálja meg a storage-fiókkal, és tekintse meg a megosztások [az tároló megosztás lista](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Cserélje le `<account-name>` és `<account-key>` a tárfiók nevét és a kulcsot az [hozzon létre egy tárfiókot](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Nincs hozzáférése, és megjelenik egy *ehhez a kérelemhez nem jogosult a művelet elvégzéséhez* hiba, mert a számítógép nincs a a *titkos* alhálózata a *MyVirtualNetwork* virtuális hálózat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [az csoport törlése](/cli/azure#az_group_delete) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy végpontot a virtuális hálózati alhálózat engedélyezve van. Megtudta, hogy engedélyezhető-e a végpontok a több Azure-szolgáltatásokkal üzembe helyezett erőforrás. Létrehozott egy Azure Storage-fiók és a tárolási fiók csak egy virtuális hálózati alhálózat erőforrásainak korlátozott hálózati hozzáférést. Szolgáltatásvégpontok kapcsolatos további információkért lásd: [szolgáltatás végpontok áttekintése](virtual-network-service-endpoints-overview.md) és [alhálózatok kezelése](virtual-network-manage-subnet.md).

Ha több virtuális hálózat már rendelkezik fiókjába, érdemes lehet összekapcsolni két virtuális hálózatok, az egyes virtuális hálózati erőforrások is kommunikálhatnak egymással. Megtudhatja, hogyan: [virtuális hálózatok csatlakoztatása](tutorial-connect-virtual-networks-cli.md).