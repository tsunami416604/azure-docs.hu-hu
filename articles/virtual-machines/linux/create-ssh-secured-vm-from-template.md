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
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 334f69390e4506c6db76c1814f8ec8f1e4417ee9
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372335"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Linux rendszerű virtuális gép létrehozása Azure Resource Manager-sablonokkal

Megtudhatja, hogyan hozhat létre egy Linux rendszerű virtuális gép (VM) az Azure Resource Manager-sablon és az Azure CLI, az Azure Cloud shell használatával. Windows virtuális gép létrehozásához lásd: [Windows virtuális gép létrehozása Resource Manager-sablonból](../windows/ps-template.md).

## <a name="templates-overview"></a>Sablonok – áttekintés

Az Azure Resource Manager-sablonok JSON-fájlok, amelyek meghatározzák az infrastruktúra és az Azure-megoldás konfigurációs. A sablonok segítségével a megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz. A sablont, és hogyan formázásával kapcsolatos további információkért lásd: [a rövid útmutató: Létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). Az erőforrástípusok JSON-szintaxisának megtekintéséért lásd [az Azure Resource Manager-sablonokban az erőforrások meghatározásával kapcsolatos](/azure/templates/microsoft.compute/allversions) témakört.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az Azure virtuális gép létrehozása általában két lépésekből áll:

1. Hozzon létre egy erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni.
1. Virtuális gépet hoz létre.

Az alábbi példa létrehoz egy virtuális Gépet egy [Azure gyorsindítási sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). A központi telepítés csak az SSH-hitelesítés engedélyezett. Amikor a rendszer kéri, adja meg az értékét a saját nyilvános SSH-kulcsot, például a tartalmát *~/.ssh/id_rsa.pub*. Ha SSH-kulcspár létrehozásához szüksége, tekintse meg [létrehozásával és SSH-kulcspár használata Linux rendszerű virtuális gépekhez az Azure-ban](mac-create-ssh-keys.md). Itt látható a sablon egy példányát:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

A CLI-szkript futtatásához válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey='$key' &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

A legutóbbi Azure CLI-parancsot az újonnan létrehozott virtuális gép nyilvános IP-címét jeleníti meg. A nyilvános IP-címet a virtuális gép csatlakozni kell. Ez a cikk a következő szakaszban talál.

Az előző példában megadott a Githubon tárolt sablonból. Is letöltheti vagy -sablon létrehozása és a helyi elérési útját adja meg a `--template-file` paraméter.

Az alábbiakban néhány további erőforrást:

- Megtudhatja, hogyan fejleszthet Resource Manager-sablonokat, lásd: [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/).
- Az Azure-beli virtuálisgép-sémákat, olvassa el [Azure sablonreferenciája](/azure/templates/microsoft.compute/allversions).
- Több virtuális gép sablonminták megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Akkor majd SSH, a virtuális gép szokásos módon. Adja meg, az előző parancs a saját nyilvános IP-cím:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>További lépések

Ebben a példában egy egyszerű Linux virtuális Gépet hozott létre. További Resource Manager-sablonok, amelyek tartalmazzák az alkalmazás-keretrendszerek vagy összetett környezeteket hozhat létre, tallózással keresse meg a [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Sablonok létrehozásával kapcsolatos további információkért tekintse meg a JSON-szintaxist és a telepített erőforrások típusok tulajdonságait:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
