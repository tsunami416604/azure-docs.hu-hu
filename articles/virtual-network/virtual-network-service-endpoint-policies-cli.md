---
title: Adatok kiszivárgásának korlátozása az Azure Storage-ra – Azure CLI
description: Ebben a cikkben megtudhatja, hogyan korlátozhatja és korlátozhatja a virtuális hálózati adatok kiszivárgását az Azure Storage-erőforrásokra az Azure CLI használatával a virtuális hálózati szolgáltatás végpontszabályzataival.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271185"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Az Azure Storage-fiókokba történő adatkiszivárgás kezelése virtuális hálózati szolgáltatásvégpont-szabályzatokkal az Azure CLI használatával

A virtuális hálózati szolgáltatás végpontszabályzatai lehetővé teszik, hogy az Azure Storage-fiókok hozzáférés-vezérlését a szolgáltatásvégpontokon belül egy virtuális hálózaton keresztül alkalmazza. Ez a számítási feladatok védelmének kulcsa, a tárfiókok engedélyezettek kezelésének és az adatok kiszivárgásának helye.
Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Hozzon létre egy virtuális hálózatot, és adjon hozzá egy alhálózatot.
* Engedélyezze a szolgáltatásvégpontot az Azure Storage számára.
* Hozzon létre két Azure Storage-fiókot, és engedélyezze a hálózati hozzáférést a fent létrehozott alhálózatból.
* Hozzon létre egy szolgáltatásvégpont-szabályzatot, amely csak az egyik tárfiókhoz engedélyezi a hozzáférést.
* Virtuális gép (VM) üzembe helyezése az alhálózatra.
* Hozzáférés megerősítése az engedélyezett tárfiókhoz az alhálózatból.
* Ellenőrizze, hogy a hozzáférés megtagadva a nem engedélyezett tárfiókhoz az alhálózatból.

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése 

Ebben a példában létrejön egy szolgáltatásvégpont a *Microsoft.Storage* számára a *Private*alhálózathoz: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Az Azure Storage-fiókok hálózati hozzáférése

Ez a szakasz azokat a lépéseket sorolja fel, amelyek korlátozzák az Azure Storage-fiók hálózati hozzáférésének korlátozását egy virtuális hálózat adott alhálózatáról a szolgáltatásvégponton keresztül.

### <a name="create-a-storage-account"></a>Create a storage account

Hozzon létre két Azure storage-fiókot az [az storage-fiók létrehozása.](/cli/azure/storage/account)

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

A tárfiókok létrehozása után olvassa be a storage-fiókok kapcsolati karakterláncát egy változóba [az az storage-fiók show-connection-string.After](/cli/azure/storage/account)the storage accounts are created, retrieve the connection string for the storage accounts into a variable with az storage account show-connection-string . A kapcsolati karakterlánc egy későbbi lépésben fájlmegosztás létrehozására szolgál.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Tekintse meg a változó tartalmát, és vegye figyelembe a kimenetben visszaadott **AccountKey** értékét, mert azt egy későbbi lépésben használják.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

Hozzon létre egy fájlmegosztást a tárfiókban az [az storage share create](/cli/azure/storage/share)segítségével. Egy későbbi lépésben ez a fájlmegosztás csatlakoztatva van a hálózati hozzáférés megerősítéséhez.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>A tárfiók hoz való összes hálózati hozzáférés megtagadása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. A kijelölt hálózatokhoz való hozzáférés korlátozásához módosítsa az alapértelmezett műveletet *Megtagadás* az [az tárfiók frissítésével .](/cli/azure/storage/account) Ha a hálózati hozzáférés le van tiltva, a tárfiók egyetlen hálózatról sem érhető el.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Hálózati hozzáférés engedélyezése a virtuális hálózat alhálózatáról

Hálózati hozzáférés engedélyezése a tárfiókhoz a *magánes* alhálózatból az [az storage account hálózati szabály hozzáadásával.](/cli/azure/storage/account/network-rule)

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Házirend alkalmazása az érvényes tárfiókhoz való hozzáférés engedélyezéséhez

Az Azure Service Endpoint szabályzatok csak az Azure Storage-hoz érhetők el. Ezért ebben az alhálózaton engedélyezzük a Service Endpoint for *Microsoft.Storage* szolgáltatást.

A szolgáltatásvégpont-házirendek a szolgáltatásvégpontokra vonatkoznak. Először hozzon létre egy szolgáltatás végpontszabályzatot. Ezután létrehozunk a szabályzatdefiníciókat a jelen szabályzat szerint az Azure Storage-fiókok hozlétre, hogy az alhálózat hozlétre engedélyezési

Szolgáltatásvégpont-házirend létrehozása

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Mentse az erőforrás URI-t az engedélyezett tárfiókhoz egy változóban. Az alábbi parancs végrehajtása előtt cserélje le * \<az előfizetés-id>az előfizetés-azonosító* tényleges értékére.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Hozzon létre & adjon hozzá egy szabályzatdefiníciót, amely engedélyezi a fenti Azure Storage-fiókot a szolgáltatásvégpont-szabályzathoz

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

És frissítse a virtuális hálózati alhálózatot, hogy társítsa az előző lépésben létrehozott szolgáltatásvégpont-házirendet

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Hozzáférés-korlátozás ellenőrzése az Azure Storage-fiókokhoz

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

A tárfiók hálózati hozzáférésének teszteléséhez telepítsen egy virtuális gép az alhálózatra.

Hozzon létre egy virtuális gép a *privát* alhálózat az [vm létrehozása.](/cli/azure/vm) Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

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

### <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

SSH a *myVmPrivate* virtuális gépbe. Cserélje le * \<a publicIpAddress>* a *myVmPrivate* virtuális gép nyilvános IP-címére.

```bash 
ssh <publicIpAddress>
```

Hozzon létre egy mappát egy csatlakoztatási ponthoz:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Csatlakoztassa az Azure-fájlmegosztást a létrehozott könyvtárhoz. Az alábbi parancs végrehajtása előtt cserélje le * \<a storage-account-key>* értéket a *$saConnectionString1.* **$saConnectionString1**

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Megjelenik `user@myVmPrivate:~$` a kérdés. Az Azure-fájlmegosztás sikeresen csatlakoztatva a */mnt/MyAzureFileShare*fájlhoz.

### <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

Ugyanattól a VM *myVmPrivate-ből*hozzon létre egy könyvtárat egy csatlakoztatási ponthoz:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Próbálja meg csatlakoztatni az Azure-fájlmegosztást a *storageacc storageacc* tárfiókból a létrehozott könyvtárhoz. Ez a cikk feltételezi, hogy az Ubuntu legújabb verzióját telepítette. Ha az Ubuntu korábbi verzióit használja, a fájlmegosztások csatlakoztatásáról további útmutatást a [Mount on Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oldalon talál. 

Az alábbi parancs végrehajtása előtt cserélje le * \<a tárfiók-kulcs>* a *$saConnectionString2.* **$saConnectionString2**

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

A hozzáférés megtagadva, `mount error(13): Permission denied` és hibaüzenetet kap, mert ez a tárfiók nem szerepel az alhálózatra alkalmazott szolgáltatásvégpont-házirend engedélyezési listájában. 

Lépjen ki az SSH-munkamenetből a *myVmPublic* virtuális gépre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az csoport törlésével](/cli/azure) távolítsa el az erőforráscsoportot és az összes benne lévő erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy szolgáltatásvégpont-szabályzatot alkalmazott egy Azure virtuális hálózati szolgáltatás végponton keresztül az Azure Storage-ba. Ön hozta létre az Azure Storage-fiókok at, és korlátozott hálózati hozzáférést csak bizonyos tárfiókok (és így megtagadta mások) egy virtuális hálózati alhálózat. Ha többet szeretne tudni a szolgáltatásvégpont-szabályzatokról, olvassa el [a Szolgáltatásvégpontok házirendjei – áttekintés című témakört.](virtual-network-service-endpoint-policies-overview.md)
