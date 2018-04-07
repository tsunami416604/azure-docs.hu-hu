---
title: Zoned Linux virtuális gép létrehozása az Azure parancssori felülettel |} Microsoft Docs
description: Hozzon létre egy Linux virtuális Gépet egy rendelkezésre állási zónát az Azure parancssori felület
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 512b6cde1a1de70f020a9af1254d2bc8e78f1b5f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Hozzon létre egy Linux virtuális gép egy rendelkezésre állási zónát az Azure parancssori felület

Ez a cikk lépéseit az Azure parancssori felület használatával Linux virtuális gép létrehozása az Azure rendelkezésre állási zónában. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

Egy rendelkezésre állási zóna használatához hozzon létre a virtuális gép egy [támogatott Azure-régiót](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

Győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) és bejelentkezett az Azure-fiókkal rendelkező [az bejelentkezési](/cli/azure/reference-index#az_login).


## <a name="check-vm-sku-availability"></a>A VM-termékváltozatok rendelkezésre állásának ellenőrzése
A virtuális gépek méretének vagy termékváltozatainak rendelkezésre állása régiónként és zónánként eltérhet. Ha fel szeretne készülni a rendelkezésre állási zónák használatára, megtekintheti a virtuális gépek termékváltozatainak listáját Azure-régió és zóna szerint. Ezáltal megfelelő virtuálisgép-méretet választhat, valamint biztosíthatja a zónák közötti rugalmasság kívánt szintjét. További információ a virtuális gépek különböző típusairól és méreteiről: [Virtuálisgép-méretek – áttekintés](sizes.md).

Megtekintheti a rendelkezésre álló virtuális gép termékváltozatok rendelkező a [az vm lista-SKU](/cli/azure/vm#az_vm_list_skus) parancsot. Az alábbi példa az *eastus2* régióban található virtuális gépek elérhető termékváltozatait listázza:

```azurecli
az vm list-skus --location eastus2 --output table
```

A kimenet a következő sűrített példához hasonló, amelyben azok a rendelkezésre állási zónák láthatók, amelyekben minden virtuálisgép-méret elérhető:

```azurecli
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

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal.  

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni. Ebben a példában az erőforráscsoport neve *myResourceGroupVM* jön létre a *eastus2* régióban. USA keleti régiója 2 az Azure-régiókban, amely támogatja a rendelkezésre állási zónák egyik.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Az erőforráscsoport létrehozása vagy módosítása egy virtuális Gépet, amely alatt ez a cikk látható során van megadva.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. 

Egy virtuális gép létrehozásakor több lehetőség is rendelkezésre áll, például az operációsrendszer-lemezkép, a lemezméretezés vagy a rendszergazdai hitelesítő adatok. Ebben a példában egy *myVM* nevű virtuális gépet hozunk létre, amelyen az Ubuntu Server fut. A virtuális gép létrehozása a rendelkezésre állási zóna *1*. Alapértelmezés szerint a virtuális gép létrehozása a *Standard_DS1_v2* méretét.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

A virtuális gép létrehozása eltarthat néhány percig. A virtuális gép létrehozása után az Azure CLI a virtuális géppel kapcsolatos adatokat jelenít meg. Vegye figyelembe a `zones` érték, amely megadja, hogy a rendelkezésre állási zóna, amelyben a virtuális gép fusson. 

```azurecli 
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

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Erősítse meg a zóna felügyelt lemezes és IP-címhez

Telepítésekor a virtuális gép egy rendelkezésre állási zónában, a kezelt lemez a virtuális gép létrehozása ugyanazon a rendelkezésre állási területen. Alapértelmezés szerint egy nyilvános IP-címet is létrejön, hogy a zónában. Az alábbi példák ezeket az erőforrásokat adatainak beolvasása.

Győződjön meg arról, hogy a virtuális Gépet felügyelt lemezes a rendelkezésre állási zónában, használja a [az vm megjelenítése](/cli/azure/vm#az_vm_show) parancs sikeresen lefut a lemezazonosítót. Ebben a példában a lemezazonosítót egy későbbi lépésben használt változó tárolja. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Most kaphat a felügyelt lemezes információt:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

A kimenetben az látható, hogy a felügyelt lemez és a virtuális gép azonos rendelkezésre állási zónában található:

```azurecli
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

Használja a [az vm-ip-címeinek listáját](/cli/azure/vm#az_vm_list_ip_addresses) parancs sikeresen lefut a nyilvános IP-cím erőforrás neve *myVM*. Ebben a példában a neve egy változó egy későbbi lépésben használt tárolódik.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Most kaphat információt az IP-cím:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

A kimeneti azt mutatja, hogy az IP-címet a virtuális géppel ugyanazon rendelkezésre állási területen:

```azurecli
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

Ebben a cikkben megtanulta, a virtuális gép létrehozása egy rendelkezésre állási zónában. Itt további információkat talál az Azure-beli virtuális gépek [régióiról és rendelkezésre állásáról](regions-and-availability.md).




