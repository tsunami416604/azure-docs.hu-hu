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
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 67b4af9f3ef7fb078d6d125e0d3257ea85e288b0
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Linux virtuális gép létrehozása az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató részletesen bemutatja, hogyan lehet egy Ubuntu 16.04 LTS-t futtató virtuális gépet üzembe helyezni az Azure CLI-vel. A kiszolgáló üzembe helyezését követően SSH-n keresztül csatlakozunk a virtuális géphez az NGINX telepítéséhez. 

A kezdés előtt győződjön meg arról, hogy az Azure CLI telepítve van. További információért lásd az [Azure CLI telepítési útmutatóját](https://docs.microsoft.com/cli/azure/install-azure-cli). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#create) paranccsal. 

Az alábbi példa egy `myVM` nevű virtuális gépet és SSH-kulcsokat hoz létre, ha azok még nem léteznek a kulcsok alapméretezett helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára 

Alapértelmezés szerint kizárólag SSH-kapcsolatok engedélyezettek az Azure-ban üzembe helyezett, Linux rendszerű virtuális gépeken. Ha ez a virtuális gép webkiszolgáló lesz, meg kell nyitnia a 80-as portot az internet irányából.  A kívánt port megnyitásához egyetlen parancs szükséges.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje le a `<publicIpAddress>` helyőrzőt a virtuális gépe tényleges nyilvános IP-címére.  A fenti példában az IP-cím a következő volt: `40.68.254.142`.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Az NGINX telepítése

Az alábbi bash-parancsfájl segítségével frissítse a csomagforrásokat, és telepítse a legújabb NGINX-csomagot. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Most, hogy az NGINX telepítve van, és a 80-as port meg van nyitva a virtuális gépén az internet irányából, tetszőleges böngészőt használhat az alapértelmezett NGINX-kezdőlap megtekintéséhez. Ügyeljen arra, hogy az alapértelmezett oldalt a fentebb dokumentált `publicIpAddress` használatával keresse fel. 

![Alapértelmezett NGINX-webhely](./media/quick-create-cli/nginx.png) 


## <a name="delete-virtual-machine"></a>Virtuális gép törlése

Ha már nincs rá szükség, az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Magas rendelkezésre állású virtuális gép létrehozása oktatóanyag](create-cli-complete.md)

[A virtuális gépek parancssori felületen való üzembe helyezését ismertető minták megismerése](cli-samples.md)

