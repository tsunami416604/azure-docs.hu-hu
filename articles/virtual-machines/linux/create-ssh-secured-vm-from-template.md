---
title: "Linux virtuális gép létrehozása az Azure-ban sablonból |} Microsoft Docs"
description: "Linux virtuális gép létrehozása egy Resource Manager sablon használata az Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 938304efe5e4a13736a50348bd0531c475149aec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>A Linux virtuális gép létrehozása Azure Resource Manager-sablonok
Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan Linux virtuális gépek (VM) az Azure Resource Manager-sablonok és az Azure CLI 2.0. Az [Azure CLI 1.0-s](create-ssh-secured-vm-from-template-nodejs.md) verziójával is elvégezheti ezeket a lépéseket.


## <a name="templates-overview"></a>Sablonok – áttekintés
Az Azure Resource Manager-sablonok JSON-fájlokat, amelyek meghatározzák az infrastruktúra és az Azure-megoldás konfigurációját is. A sablonok segítségével a megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz. A sablont, és hogyan összeállításához formátuma kapcsolatos további információkért lásd: [az első Azure Resource Manager-sablon létrehozása](../../azure-resource-manager/resource-manager-create-first-template.md). Az erőforrástípusok JSON-szintaxisának megtekintéséért lásd [az Azure Resource Manager-sablonokban az erőforrások meghatározásával kapcsolatos](/azure/templates/) témakört.


## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Egy erőforráscsoportot a virtuális gépek előtt létre kell hozni. Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupVM* a a *eastus* régió:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
Az alábbi példakód létrehozza a virtuális gép [Azure Resource Manager sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) rendelkező [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#create). Adja meg a saját nyilvános SSH-kulcs, például a tartalmát értékének *~/.ssh/id_rsa.pub*. Ha egy SSH-kulcspárral létrehozásához szüksége, tekintse meg [létrehozása, és egy SSH-kulcspárral használata a Linux virtuális gépek Azure-ban](mac-create-ssh-keys.md).

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
  --parameters '{"sshKeyData": {"value": "ssh-rsa AAAAB3N{snip}B9eIgoZ"}}'
```

Ebben a példában a Githubon tárolt sablon adott meg. Is letöltheti vagy -sablon létrehozása és adja meg a helyi elérési út azonos `--template-file` paraméter.

SSH-kapcsolatot a virtuális Gépet, szerezze be a nyilvános IP-cím [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#show):

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name sshPublicIP \
    --query [ipAddress] \
    --output tsv
```

Akkor majd SSH, a virtuális gép szokásos módon. Adja meg azt az előző parancs saját nyilvános IP-címet:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Következő lépések
Ebben a példában létrehozott egy alapszintű Linux virtuális Gépet. További Resource Manager-sablonok, amelyek tartalmazzák az alkalmazás-keretrendszerek számára, vagy hozzon létre összetettebb környezetben, keresse meg a [Azure gyors üzembe helyezés sablontárban](https://azure.microsoft.com/documentation/templates/).