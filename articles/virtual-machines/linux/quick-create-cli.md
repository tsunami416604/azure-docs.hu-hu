---
title: "Azure gyors üzembe helyezés – Windows virtuális gép létrehozása a parancssori felületen | Microsoft Docs"
description: "Gyorsan megismerheti a virtuális gépek Azure CLI-vel való létrehozásának módját."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/13/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: dab783e574ef4f5507737d5004fd8d2a98629458
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Linux virtuális gép létrehozása az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató részletesen bemutatja, hogyan lehet egy Ubuntu Servert futtató virtuális gépet üzembe helyezni az Azure CLI-vel. A kiszolgáló üzembe helyezése után a rendszer létrehoz egy SSH-kapcsolatot, és telepít egy NGINX-webkiszolgálót.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. 

Az alábbi példa egy *myVM* nevű virtuális gépet és SSH-kulcsokat hoz létre, ha azok még nem léteznek a kulcsok alapméretezett helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli-interactive 
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára 

Alapértelmezés szerint kizárólag SSH-kapcsolatok engedélyezettek az Azure-ban üzembe helyezett, Linux rendszerű virtuális gépeken. Ha ez a virtuális gép webkiszolgáló lesz, meg kell nyitnia a 80-as portot az internet irányából. A kívánt port megnyitásához használja az [az vm open-port](/cli/azure/vm#open-port) parancsot.  
 
 ```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje le a *<publicIpAddress>* helyőrzőt a virtuális gépe tényleges nyilvános IP-címére.  A fenti példában az IP-cím a következő volt: *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Az NGINX telepítése

Az alábbi parancsokkal csomag adatforrások frissítése, és telepítse a legújabb NGINX-csomagot. 

```bash 
# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Most, hogy az NGINX telepítve van, és a 80-as port meg van nyitva a virtuális gépén az internet irányából, tetszőleges böngészőt használhat az alapértelmezett NGINX-kezdőlap megtekintéséhez. Ügyeljen arra, hogy az alapértelmezett oldalt a fentebb dokumentált *publicIPAddress* használatával keresse fel. 

![Alapértelmezett NGINX-webhely](./media/quick-create-cli/nginx.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [az group delete](/cli/azure/group#delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás. Lépjen ki az SSH-munkamenetet a virtuális géphez, majd törli az erőforrást az alábbiak szerint:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, egy hálózati biztonsági csoport szabályát, valamint telepített egy webkiszolgálót. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.


> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)
