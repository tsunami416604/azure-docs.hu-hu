---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: b922b5ea225c61948240e40903ac43f56fde3fb5
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227018"
---
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. 

Az alábbi példa egy *myVM* nevű virtuális gépet és SSH-kulcsokat hoz létre, ha azok még nem léteznek a kulcsok alapméretezett helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A parancs emellett az *azureuser* nevet állítja be rendszergazdai felhasználónévként. Később ezt a nevet fogja használni a virtuális géphez való csatlakozáshoz. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím a később lépésekben használható a virtuális gép eléréséhez.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára 

Alapértelmezés szerint kizárólag SSH-kapcsolatok engedélyezettek az Azure-ban üzembe helyezett, Linux rendszerű virtuális gépeken. Mivel ez a virtuális gép webkiszolgáló lesz, meg kell nyitnia a 80-as portot az internet irányából. A kívánt port megnyitásához használja az [az vm open-port](/cli/azure/vm#az_vm_open_port) parancsot.  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val


Ha még nem ismeri a virtuális gépéhez tartozó nyilvános IP-címet, futtassa az [az network public-ip list](/cli/azure/network/public-ip#list) parancsot. Erre az IP-címre több későbbi lépésben is szüksége lesz.


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Helyettesítse be a virtuális gépe tényleges nyilvános IP-címét. Ebben a példában az IP-cím a következő: *40.68.254.142*. Az *azureuser* a virtuális gép létrehozásakor beállított rendszergazdai felhasználónév.

```bash
ssh azureuser@40.68.254.142
```

