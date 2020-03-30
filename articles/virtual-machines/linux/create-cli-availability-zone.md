---
title: Zónazóna Linuxos virtuális gép létrehozása az Azure CLI-vel
description: Linuxos virtuális gép létrehozása rendelkezésre állási zónában az Azure CLI-vel
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/05/2018
ms.author: cynthn
ms.openlocfilehash: e229bb7af02255c0714c559b841afac9a66a7c7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535612"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Linuxos virtuális gép létrehozása rendelkezésre állási zónában az Azure CLI-vel

Ez a cikk az Azure CLI használatával egy Linux virtuális gép létrehozása egy Azure rendelkezésre állási zónában. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

Rendelkezésre állási zóna használatához egy [támogatott Azure-régióban](../../availability-zones/az-overview.md#services-support-by-region) hozza létre a virtuális gépet.

Győződjön meg arról, hogy telepítette a legújabb [Azure CLI-t,](/cli/azure/install-az-cli2) és bejelentkezett egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index)


## <a name="check-vm-sku-availability"></a>A VM-termékváltozatok rendelkezésre állásának ellenőrzése
A virtuális gépek méretének vagy termékváltozatainak rendelkezésre állása régiónként és zónánként eltérhet. Ha fel szeretne készülni a rendelkezésre állási zónák használatára, megtekintheti a virtuális gépek termékváltozatainak listáját Azure-régió és zóna szerint. Ezáltal megfelelő virtuálisgép-méretet választhat, valamint biztosíthatja a zónák közötti rugalmasság kívánt szintjét. További információ a virtuális gépek különböző típusairól és méreteiről: [Virtuálisgép-méretek – áttekintés](sizes.md).

Megtekintheti a rendelkezésre álló virtuálisgép-skus az [az vm list-skus](/cli/azure/vm) paranccsal. Az alábbi példa az *eastus2* régióban található virtuális gépek elérhető termékváltozatait listázza:

```azurecli
az vm list-skus --location eastus2 --output table
```

A kimenet a következő sűrített példához hasonló, amelyben azok a rendelkezésre állási zónák láthatók, amelyekben minden virtuálisgép-méret elérhető:

```output
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal.  

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni. Ebben a példában egy *myResourceGroupVM* nevű erőforráscsoport jön létre az *eastus2* régióban. Az USA keleti régiója 2 az Azure-régiók egyike, amely támogatja a rendelkezésre állási zónákat.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Az erőforráscsoport a virtuális gép létrehozásakor vagy módosításakor van megadva, amely ebben a cikkben látható.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. 

Egy virtuális gép létrehozásakor több lehetőség is rendelkezésre áll, például az operációsrendszer-lemezkép, a lemezméretezés vagy a rendszergazdai hitelesítő adatok. Ebben a példában egy *myVM* nevű virtuális gépet hozunk létre, amelyen az Ubuntu Server fut. A virtuális gép az *1.* Alapértelmezés szerint a virtuális gép Standard_DS1_v2 *méretben* jön létre.

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

A virtuális gép létrehozása eltarthat néhány percig. A virtuális gép létrehozása után az Azure CLI a virtuális géppel kapcsolatos adatokat jelenít meg. Vegye figyelembe `zones` az értéket, amely azt jelzi, hogy a rendelkezésre állási zóna, amelyben a virtuális gép fut. 

```output
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>A felügyelt lemez és IP-cím zónájának megerősítése

Amikor a virtuális gép egy rendelkezésre állási zónában van telepítve, a virtuális gép felügyelt lemeze ugyanabban a rendelkezésre állási zónában jön létre. Alapértelmezés szerint ebben a zónában nyilvános IP-cím is létrejön. Az alábbi példák ezekről az erőforrásokról kapnak információt.

Annak ellenőrzéséhez, hogy a virtuális gép felügyelt lemeze a rendelkezésre állási zónában van-e, az [az vm show](/cli/azure/vm) paranccsal adja vissza a lemezazonosítót. Ebben a példában a lemezazonosító egy későbbi lépésben használt változóban van tárolva. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Most már információkat kaphat a felügyelt lemezről:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

A kimenetben az látható, hogy a felügyelt lemez és a virtuális gép azonos rendelkezésre állási zónában található:

```output
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Használja az [az vm list-ip-addresses](/cli/azure/vm) parancsot a nyilvános IP-cím erőforrás nevének visszaadására a *myVM-ben.* Ebben a példában a név egy későbbi lépésben használt változóban tárolódik.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Most már kaphat információt az IP-cím:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

A kimenet azt mutatja, hogy az IP-cím ugyanabban a rendelkezésre állási zónában van, mint a virtuális gép:

```output
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan hozható létre virtuális gép egy rendelkezésre állási zónában. További információ az Azure-beli virtuális gépek [elérhetőségéről.](availability.md)




