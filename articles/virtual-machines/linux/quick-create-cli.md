---
title: 'Rövid útmutató: Linuxos virtuális gép létrehozásához használja az Azure CLI-t'
description: Ebből a rövid útmutatóból elsajátíthatja, hogyan hozhat létre Linux rendszerű virtuális gépet az Azure CLI használatával.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
ms.openlocfilehash: cbe5276f1b302d62e84c8ba448ad594694a8290c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458726"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Rövid útmutató: Linux-alapú virtuális gép létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan használhatja az Azure parancssori felület (CLI) linuxos virtuális gép (VM) üzembe helyezéséhez az Azure-ban. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható.

Ebben az oktatóanyagban az Ubuntu 16.04 LTS-t fogjuk telepíteni. A virtuális gép működés közbeni bemutatásához SSH-val fog csatlakozni hozzá, és az NGINX-webkiszolgálót fogja telepíteni.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/bash](https://shell.azure.com/bash)is megnyithatja a segítségével. Válassza a **Másolás** lehetőséget a kódblokkok másolásához, beillesztését a Felhőrendszerhéjba, és válassza az **Enter** lehetőséget a futtatásához.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal.

A következő példa létrehoz egy *myVM* nevű virtuális gépet, és hozzáad egy *azureuser* nevű felhasználói fiókot. A `--generate-ssh-keys` paraméter segítségével automatikusan létrehoz egy SSH-kulcsot, és az alapértelmezett kulcshelyre (*~/.ssh )* helyezi el. Ha konkrét kulcsokat szeretne használni, használja az `--ssh-key-value` paramétert.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő kimeneti példa azt mutatja be, hogy a virtuális gép létrehozási művelete sikeres volt.

```output
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

Alapértelmezés szerint csak SSH-kapcsolatok vannak megnyitva, amikor Linux virtuális gépet hoz létre az Azure-ban. Használja az [az vm open-port](/cli/azure/vm) parancsot a 80-as TCP-port NGINX webkiszolgálóhoz történő megnyitásához:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

A virtuális géphez a szokásos módon kapcsolódhat SSH-val. Cserélje le **publicIpAddress** kifejezést a virtuális gép nyilvános IP-címére, amint az a virtuális gép előző kimenetében szerepel:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>A webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az NGINX-webkiszolgálót. Frissítse a csomag forrásait, majd telepítse a legújabb NGINX-csomagot.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Ha elkészült, az SSH-munkamenetből való kilépéshez írja be: `exit`.

## <a name="view-the-web-server-in-action"></a>A webkiszolgáló működésének ellenőrzése

Egy tetszőleges webböngészővel tekintse meg az alapértelmezett NGINX-kezdőlapot. Webcímként használja a virtuális gép nyilvános IP-címét. A következő példában az alapértelmezett NGINX-webhely látható:

![Az NGINX kezdőlapjának megtekintése](./media/quick-create-cli/view-the-nginx-welcome-page.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, az [az csoport törlése](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gép és az összes kapcsolódó erőforrást. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a követésével üzembe helyezett egy egyszerű virtuális gépet, megnyitott egy hálózati portot a webes forgalomnak, és telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.


> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)
