---
title: Rövid útmutató – Linux virtuális gép létrehozása az Azure CLI 2.0 használatával | Microsoft Docs
description: Ebből a rövid útmutatóból elsajátíthatja, hogyan használható az Azure CLI 2.0 Linux virtuális gépek létrehozásra
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c45f8f010d69337d21fce327933990a573988a4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187929"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Rövid útmutató: Linux virtuális gép létrehozása az Azure CLI 2.0-val

Az Azure CLI 2.0 az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használja az Azure CLI 2.0-t Ubuntut futtató Linux virtuális gép (VM) üzembe helyezésére az Azure-ban. Ha látni szeretné a virtuális gépet munka közben, hozzon létre SSH-kapcsolatot a virtuális géphez, és telepítse az NGINX webkiszolgálót.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal.

A következő példa létrehoz egy *myVM* nevű virtuális gépet, hozzáadja az *azureuser* nevű felhasználói fiókot, valamint SSH-kulcsokat hoz létre, ha azok még nem léteznek a kulcsok alapértelmezett helyén (*~/.ssh*). Ha konkrét kulcsokat szeretne használni, használja az `--ssh-key-value` beállítást:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő kimeneti példa azt mutatja be, hogy a virtuális gép létrehozási művelete sikeres volt.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Vegye észere a saját `publicIpAddress` címét a virtuális gép kimenetében. A következő lépésekben ez a cím használható a virtuális gép eléréséhez.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Alapértelmezés szerint csak SSH-kapcsolatok vannak megnyitva, amikor Linux virtuális gépet hoz létre az Azure-ban. Használja az [az vm open-port](/cli/azure/vm#az_vm_open_port) parancsot a 80-as TCP-port NGINX webkiszolgálóhoz történő megnyitásához:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

A virtuális géphez a szokásos módon kapcsolódhat SSH-val. Cserélje le **publicIpAddress** kifejezést a virtuális gép nyilvános IP-címére, amint az a virtuális gép előző kimenetében szerepel:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>A webkiszolgáló telepítése

A virtuális gép működés közbeni megtekintéséhez telepítse az NGINX webkiszolgálót. A csomagforrások frissítéséhez és a legújabb NGINX-csomag telepítéséhez futtassa a következő parancsokat az SSH-munkamenetből:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Amikor elkészült, zárja be (`exit`) az SSH-munkamenetet.

## <a name="view-the-web-server-in-action"></a>A webkiszolgáló megtekintése működés közben

Most, hogy az NGINX telepítve van, és a 80-as port meg van nyitva a virtuális gépén az internetről, tetszőleges böngészőt használhat az alapértelmezett NGINX-kezdőlap megtekintéséhez. Használja a virtuális gép korábbi lépésben beszerzett nyilvános IP-címét. A következő példában az alapértelmezett NGINX-webhely látható:

![Alapértelmezett NGINX-webhely](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás. Lépjen ki a virtuális géphez indított SSH-munkamenetből, majd törölje az erőforrásokat a következő módon:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a követésével üzembe helyezett egy egyszerű virtuális gépet, megnyitott egy hálózati portot a webes forgalomnak, és telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.


> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)
