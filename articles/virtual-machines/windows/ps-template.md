---
title: Windows virtuális gép létrehozása sablonból az Azure-ban
description: Az Erőforrás-kezelő sablon és a PowerShell segítségével egyszerűen hozhat létre egy új Windows virtuális gép.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99e292930414ae027c9cbbf3a901d550041899d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74065550"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Windows virtuális gép létrehozása Erőforrás-kezelő sablonból

Ismerje meg, hogyan hozhat létre egy Windows virtuális gépet egy Azure Resource Manager-sablon és az Azure Cloud shellből származó Azure PowerShell használatával. A cikkben használt sablon egyetlen, Windows Server rendszert futtató virtuális gépet telepít egyetlen alhálózattal rendelkező új virtuális hálózatba. Linuxos virtuális gép létrehozásáról a [Linux-alapú virtuális gép létrehozása az Azure Resource Manager-sablonokkal című témakörben olvashat.](../linux/create-ssh-secured-vm-from-template.md)

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az Azure virtuális gépek létrehozása általában két lépést tartalmaz:

- Hozzon létre egy erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni.
- Virtuális gépet hoz létre.

A következő példa létrehoz egy virtuális gép egy [Azure-gyorsindítási sablonból.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) Itt van egy példányát a sablon:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

A PowerShell-parancsfájl futtatásához válassza **a Próbálja ki** az Azure Cloud rendszerhéj megnyitásához. A parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés parancsot:**

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

Ha úgy dönt, hogy az Azure Cloud-rendszerhéj helyett helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az Azure PowerShell-modult igényli. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Connect-AzAccount` az Azure-ral való kapcsolat létrehozásához is futnia kell.

Az előző példában megadott egy sablont a GitHubon tárolt. Le is tölthet i vagy létrehozhat egy sablont, és megadhatja a helyi elérési utat a `--template-file` paraméterrel.

Néhány további információforrás:

- Az Erőforrás-kezelő-sablonok fejlesztéséről az [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/)olvashat.
- Az Azure virtuálisgép-sémák megtekintéséhez tekintse meg az [Azure-sablon hivatkozási](/azure/templates/microsoft.compute/allversions).
- További virtuálisgép-sablonminták megtekintéséhez tekintse meg [az Azure gyorsindítási sablonjait.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

Az előző parancsfájl utolsó PowerShell-parancsa a virtuális gép nevét jeleníti meg. A virtuális géphez való csatlakozásról a [Csatlakozás és bejelentkezés Windows rendszerű Azure-alapú virtuális gépre](./connect-logon.md)című témakörben található.

## <a name="next-steps"></a>Következő lépések

- Ha problémák merültek fel a központi telepítéssel kapcsolatban, megnézheti az [Azure Resource Manager gyakori Azure-telepítési hibáinak elhárítása című](../../resource-manager-common-deployment-errors.md)témakört.
- Megtudhatja, hogyan hozhat létre és kezelhet virtuális gépet a [Windows virtuális gépek létrehozása és kezelése az Azure PowerShell-modullal.](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

A sablonok létrehozásáról a Telepített erőforrástípusok JSON-szintaxisát és tulajdonságait tekintheti meg:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
