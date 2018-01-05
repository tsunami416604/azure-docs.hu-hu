---
title: "Zoned Linux virtuális gép létrehozása az Azure parancssori felülettel |} Microsoft Docs"
description: "Hozzon létre egy Linux virtuális Gépet egy rendelkezésre állási zónát az Azure parancssori felület"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 5e742187295d0bd6dbc0767ee164335fc0cf9f02
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Hozzon létre egy Linux virtuális gép egy rendelkezésre állási zónát az Azure parancssori felület

Ez a cikk lépéseit az Azure parancssori felület használatával Linux virtuális gép létrehozása az Azure rendelkezésre állási zónában. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

Győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) és bejelentkezett az Azure-fiókkal rendelkező [az bejelentkezési](/cli/azure/#login).


## <a name="check-vm-sku-availability"></a>Virtuális gép SKU elérhetőségének ellenőrzése
Lévő Virtuálisgép-méretek vagy SKU, régió, és a zóna változhat. Segítségével megtervezheti a rendelkezésre állási zónákban használhatják, listázhatja a rendelkezésre álló virtuális gép termékváltozatok Azure-régió, és a zóna. Ez a lehetőség lehetővé teszi, hogy válassza ki a megfelelő Virtuálisgép-méretet, és szerezhetik be a kívánt rugalmassági zónák. A különböző Virtuálisgép-típusokon és méretek további információkért lásd: [Virtuálisgép-méretek – áttekintés](sizes.md).

Megtekintheti a rendelkezésre álló virtuális gép termékváltozatok rendelkező a [az vm lista-SKU](/cli/azure/vm#az_vm_list_skus) parancsot. Az alábbi példa felsorolja a rendelkezésre álló virtuális gép termékváltozatok a *eastus2* régió:

```azurecli
az vm list-skus --location eastus2 --output table
```

A kimenete a következőhöz hasonló tömörített, amely mutatja a rendelkezésre állási zónákat, amelyben minden egyes Virtuálisgép-méretet érhető el:

```azurecli
ResourceType      Locations  Name               Tier       Size     Zones
----------------  ---------  -----------------  ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2    Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2    Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s       Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s       Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3    Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3    Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3     Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3     Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal.  

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Egy erőforráscsoportot a virtuális gépek előtt létre kell hozni. Ebben a példában az erőforráscsoport neve *myResourceGroupVM* jön létre a *eastus2* régióban. Az USA 2. keleti régiója az egyik olyan Azure-régió, amely előzetes verzióban is támogatja a rendelkezésre állási zónákat.

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus2
```

Az erőforráscsoport létrehozása vagy módosítása egy virtuális Gépet, amely alatt ez a cikk látható során van megadva.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

A virtuális gép létrehozása a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. 

A virtuális gép létrehozásakor több lehetőség is elérhető, például az operációs rendszer lemezképét, a lemez méretezés és a felügyeleti hitelesítő adatokat. Ebben a példában egy virtuális gép létrehozása nevű *myVM* Ubuntu Server operációs rendszert futtató. A virtuális gép létrehozása a rendelkezésre állási zóna *1*. Alapértelmezés szerint a virtuális gép létrehozása a *Standard_DS1_v2* méretét. A rendelkezésre állási zónák Preview használható ez a méret.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys --zone 1
```

A virtuális gép létrehozásához néhány percig is eltarthat. A virtuális gép létrehozása után az Azure parancssori felület exportálja a virtuális gép kapcsolatos információkat. Vegye figyelembe a `zones` érték, amely megadja, hogy a rendelkezésre állási zóna, amelyben a virtuális gép fusson. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="zone-for-ip-address-and-managed-disk"></a>IP-cím- és felügyeltlemez-zóna

Telepítésekor a virtuális gép egy rendelkezésre állási zónában, az IP-cím és a felügyelt-erőforrást telepít ugyanazon a rendelkezésre állási területen. Az alábbi példák ezeket az erőforrásokat adatainak beolvasása.

Először használja a [az vm-ip-címeinek listáját](/cli/azure/vm#az_vm_list_ip_addresses) parancs sikeresen lefut a nyilvános IP-cím erőforrás neve *myVM*. Ebben a példában a neve egy változó egy későbbi lépésben használt tárolódik.

```azurecli-interactive
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Most kaphat információt az IP-cím:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

A kimeneti azt mutatja, hogy az IP-címet a virtuális géppel ugyanazon rendelkezésre állási területen:

```azurecli-interactive
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
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

Ehhez hasonlóan ellenőrizze, hogy a virtuális Gépet felügyelt lemezes a rendelkezésre állási zónában. Használja a [az vm megjelenítése](/cli/azure/vm#az_vm_show) parancs sikeresen lefut a lemezazonosítót. Ebben a példában a lemezazonosítót egy későbbi lépésben használt változó tárolja. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Most kaphat a felügyelt lemezes információt:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

A kimenetben az látható, hogy a felügyelt lemez és a virtuális gép azonos rendelkezésre állási zónában található:

```azurecli-interactive
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/Subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2017-09-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```
 


## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan hozható létre virtuális gép egy rendelkezésre állási zónában. Itt további információkat talál az Azure-beli virtuális gépek [régióiról és rendelkezésre állásáról](regions-and-availability.md).




