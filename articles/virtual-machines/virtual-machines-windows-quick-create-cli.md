---
title: "Azure gyors üzembe helyezés – Windows VM CLI létrehozása | Microsoft Docs"
description: "Gyorsan megismerheti a Windows rendszerű virtuális gép Azure parancssori felülettel való létrehozásának módját."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/20/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 0f9ff487e289eadb857508134b7e08b00360fdd3
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>Windowsos virtuális gép létrehozása az Azure parancssori felülettel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató részletesen bemutatja, hogyan lehet egy Windows Server 2016-ot futtató virtuális gépet az Azure CLI-vel üzembe helyezni.

A kezdés előtt győződjön meg arról, hogy az Azure CLI telepítve van. További információért lásd az [Azure CLI telepítési útmutatóját](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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

Az alábbi példa egy `myVM` nevű virtuális gépet hoz létre. Ez a példa az `azureuser` rendszergazdanevet és a ` myPassword12` jelszót használja. Az értékeket módosítsa a környezetének megfelelően. Ezekre az értékekre akkor van szükség, amikor kapcsolódik a virtuális géphez.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a nyilvános IP-címet. Ez a cím használható a virtuális gép eléréséhez.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Használja az alábbi parancsot egy távoli asztali kapcsolat létrehozásához a virtuális géppel. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="delete-virtual-machine"></a>Virtuális gép törlése

Ha már nincs rá szükség, az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Szerepkörök telepítését és a tűzfal konfigurálását ismertető oktatóanyag](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[A virtuális gépek parancssori felületen való üzembe helyezését ismertető minták megismerése](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
