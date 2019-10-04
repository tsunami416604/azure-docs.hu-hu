---
title: Rövid útmutató – Windows rendszerű virtuális gép létrehozása az Azure CLI használatával | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Windows rendszerű virtuális gépet az Azure CLI használatával
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e6709a6efff80df01d7504db8b39f8ff5c2c5e49
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088840"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>Gyors útmutató: Windowsos virtuális gép létrehozása az Azure parancssori felülettel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe az Azure CLI segítségével Windows Server 2016 rendszerű virtuális gépeket (VM-eket) az Azure-ban. A virtuális gép működésének ellenőrzéséhez ezután RDP-kapcsolaton keresztül csatlakozzon a géphez, és telepítse az IIS-webkiszolgálót.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd nyomja le az **ENTER** billentyűt a futtatásához.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hoz létre. Ez a példa egy rendszergazdai felhasználónévhez tartozó azureuser-t használ. 

Meg kell változtatnia a értékét `--admin-password` , vagy sikertelen lesz. Módosítsa olyan jelszóra, amely megfelel az [Azure-beli virtuális gépek](/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm
)jelszavára vonatkozó követelményeknek. A felhasználónevet és a jelszót később fogja használni a rendszer a virtuális géphez való csatlakozáskor.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword
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
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Vegye észere a saját `publicIpAddress` címét a virtuális gép kimenetében. A következő lépésekben ez a cím használható a virtuális gép eléréséhez.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Alapértelmezés szerint csak RDP-kapcsolatok vannak megnyitva, amikor Windows rendszerű virtuális gépet hoz létre az Azure-ban. Használja az [az vm open-port](/cli/azure/vm) parancsot a 80-as TCP-port IIS webkiszolgálóhoz történő megnyitására:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az alábbi parancs használatával hozzon létre egy távoli asztali munkamenetet a helyszíni számítógépéről. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. Amikor a rendszer kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat:

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>A webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az IIS-webkiszolgálót. Nyisson meg egy PowerShell-parancssort a virtuális gépen, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha elkészült, zárja be a virtuális géphez nyitott RDP-kapcsolatot.

## <a name="view-the-web-server-in-action"></a>A webkiszolgáló megtekintése működés közben

Miután az IIS telepítve lett, és a 80-as port meg van nyitva a virtuális gépen az internet irányából, egy tetszőleges böngésző használatával megtekintheti az alapértelmezett IIS-kezdőlapot. Használja a virtuális gép az egyik előző lépésben megkapott nyilvános IP-címét. Az alábbi példa az alapértelmezett IIS-webhelyet mutatja:

![Alapértelmezett IIS-webhely](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, megnyitott egy hálózati portot a webes forgalom számára, valamint telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
