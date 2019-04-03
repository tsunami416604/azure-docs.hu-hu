---
title: Windows virtuális gép létrehozása az Azure-ban sablon alapján |} A Microsoft Docs
description: A Resource Manager-sablon és a PowerShell használatával egyszerűen létrehozhat egy új Windows virtuális Gépet.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1ba96c76c51abcfe5bb5ef9bd66eb8a50afdfda
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846617"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Windows virtuális gép létrehozása Resource Manager-sablonból

Útmutató a Windows virtuális gép létrehozása az Azure Cloud shell egy Azure Resource Manager-sablon és az Azure PowerShell használatával. Ebben a cikkben használt sablon telepít egy új virtuális hálózatot egyetlen alhálózattal rendelkező Windows Server rendszerű egyetlen virtuális gépet. Linux rendszerű virtuális gép létrehozására, tekintse meg a [Linux rendszerű virtuális gép létrehozása Azure Resource Manager-sablonokkal](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az Azure virtuális gép létrehozása általában két lépésekből áll:

- Hozzon létre egy erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni.
- Virtuális gépet hoz létre.

Az alábbi példa létrehoz egy virtuális Gépet egy [Azure gyorsindítási sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Itt látható a sablon egy példányát:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

A PowerShell-szkript futtatásához válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Ha az Azure Cloud shellből helyileg helyett, a PowerShell telepítése és használata, az oktatóanyaghoz az Azure PowerShell modult. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja PowerShell, is futtatni szeretné `Connect-AzAccount` kapcsolat létrehozása az Azure-ral.

Az előző példában megadott a Githubon tárolt sablonból. Is letöltheti vagy -sablon létrehozása és a helyi elérési útját adja meg a `--template-file` paraméter.

Az alábbiakban néhány további erőforrást:

- Megtudhatja, hogyan fejleszthet Resource Manager-sablonokat, lásd: [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/).
- Az Azure-beli virtuálisgép-sémákat, olvassa el [Azure sablonreferenciája](/azure/templates/microsoft.compute/allversions).
- Több virtuális gép sablonminták megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

A legutóbbi PowerShell-parancsot az előző parancsfájlt a virtuális gép nevét jeleníti meg. Ha csatlakozni szeretne a virtuális gépet, tekintse meg [hogyan csatlakozhat, és jelentkezzen be az Azure Windows rendszerű virtuális gép](./connect-logon.md).

## <a name="next-steps"></a>További lépések

- Ha a központi telepítési problémái vannak, előfordulhat, hogy vessen egy pillantást [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Ismerje meg, hogyan hozhat létre és kezelhet egy virtuális géphez a [létrehozása és a Windows virtuális gépek kezelése az Azure PowerShell modullal](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Sablonok létrehozásával kapcsolatos további információkért tekintse meg a JSON-szintaxist és a telepített erőforrások típusok tulajdonságait:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
