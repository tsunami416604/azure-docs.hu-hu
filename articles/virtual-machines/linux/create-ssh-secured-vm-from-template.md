---
title: Az Azure-ban Linux rendszerű virtuális gép létrehozása sablon alapján |} A Microsoft Docs
description: Az Azure CLI használatával egy Linux virtuális gép létrehozása Resource Manager-sablonnal
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c6e4e142025e40e77afee01d70de9cef68eca1f0
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013196"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Linux rendszerű virtuális gép létrehozása Azure Resource Manager-sablonokkal

Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan egy Linux rendszerű virtuális gép (VM) az Azure Resource Manager-sablonokkal és az Azure parancssori felület. Is elvégezheti ezeket a lépéseket a [Azure klasszikus parancssori felület](create-ssh-secured-vm-from-template-nodejs.md).


Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan egy Linux rendszerű virtuális gép (VM) az Azure Resource Manager-sablonokkal és az Azure parancssori felület. 

## <a name="templates-overview"></a>Sablonok – áttekintés
Az Azure Resource Manager-sablonok JSON-fájlok, amelyek meghatározzák az infrastruktúra és az Azure-megoldás konfigurációs. A sablonok segítségével a megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz. A sablont, és hogyan formázásával kapcsolatos további információkért lásd: [az első Azure Resource Manager-sablon létrehozása](../../azure-resource-manager/resource-manager-create-first-template.md). Az erőforrástípusok JSON-szintaxisának megtekintéséért lásd [az Azure Resource Manager-sablonokban az erőforrások meghatározásával kapcsolatos](/azure/templates/) témakört.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni. A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroupVM* a a *eastus* régió:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Az alábbi példa létrehoz egy virtuális gépről [ezen Azure Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create). Csak az SSH-hitelesítés használata engedélyezett. Amikor a rendszer kéri, adja meg az értékét a saját nyilvános SSH-kulcsot, például a tartalmát *~/.ssh/id_rsa.pub*. Ha SSH-kulcspár létrehozásához szüksége, tekintse meg [létrehozásával és SSH-kulcspár használata Linux rendszerű virtuális gépekhez az Azure-ban](mac-create-ssh-keys.md).

```azurecli
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Az előző példában megadott a Githubon tárolt sablonból. Is letöltheti vagy -sablon létrehozása és a helyi elérési útját adja meg a `--template-file` paraméter.


## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez
Szerezze be a nyilvános IP-címet a virtuális gépre SSH- [az vm show](/cli/azure/vm#az-vm-show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name sshvm \
    --show-details \
    --query publicIps \
    --output tsv
```

Akkor majd SSH, a virtuális gép szokásos módon. Adja meg, az előző parancs a saját nyilvános IP-cím:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>További lépések
Ebben a példában egy egyszerű Linux virtuális Gépet hozott létre. További Resource Manager-sablonok, amelyek tartalmazzák az alkalmazás-keretrendszerek vagy összetett környezeteket hozhat létre, tallózással keresse meg a [az Azure-gyorssablonok katalógusába](https://azure.microsoft.com/documentation/templates/).

Sablonok létrehozásával kapcsolatos további információkért tekintse meg a JSON-szintaxist és a telepített erőforrások típusok tulajdonságait:

* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
