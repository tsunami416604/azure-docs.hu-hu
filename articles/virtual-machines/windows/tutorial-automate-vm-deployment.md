---
title: Windows rendszerű virtuális gép testreszabása az Azure-ban | Microsoft Docs
description: Megtudhatja, hogyan használható Azure-beli windowsos virtuális gépeken az egyéni szkriptek bővítményét az alkalmazástelepítések automatizálásához.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6afb5757e11108eec856d821d92afed9681a2668
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Windowsos virtuális gép testreszabása az Azure-ban
A virtuális gépek gyors és következetes konfigurálásához általában valamilyen automatizálásra van szükség. A Windows rendszerű virtuális gépek testreszabásának általánosan használt megközelítése az [egyéni szkriptek Windows-bővítményének](extensions-customscript.md) használata. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * IIS telepítése az egyéni szkriptek bővítményével
> * Az egyéni szkriptek bővítményét használó virtuális gép létrehozása
> * Futó IIS-hely megtekintése a bővítmény alkalmazása után

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.3-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 


## <a name="custom-script-extension-overview"></a>Az egyéni szkriptek bővítményének áttekintése
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében.

Az egyéni szkriptek bővítménye az Azure Resource Manager-sablonokkal van integrálva, és az Azure CLI, a PowerShell, az Azure Portal vagy az Azure Virtual Machine REST API használatával is futtatható.

Az egyéni szkriptek bővítménye Windows és Linux rendszerű virtuális gépeken is használható.


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre az *USA keleti régiója* helyen. Ha még nem léteznek, létrejön a *myResourceGroupAutomate* nevű erőforráscsoport és a támogató hálózati erőforrások. A webes forgalom engedélyezéséhez a parancsmag megnyitja a *80*-as portot is.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Az erőforrások és a virtuális gép létrehozása néhány percig tart.


## <a name="automate-iis-install"></a>Az IIS-telepítés automatizálása
A [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) paranccsal telepítse az egyéni szkriptbővítményt. A bővítmény a `powershell Add-WindowsFeature Web-Server` parancs futtatásával telepíti az IIS-webkiszolgálót, majd a *Default.htm* lapot frissítve megjeleníti a virtuális gép eszköznevét:

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Webhely tesztelése
Kérje le a terheléselosztó nyilvános IP-címét a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) paranccsal. A következő példa a korábban létrehozott *myPublicIPAddress* IP-címét kéri le:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

A nyilvános IP-címet beírhatja egy böngészőbe. Ekkor megjelenik a webhely, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![IIS-webhely futtatása](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az IIS telepítését automatizálta egy virtuális gépen. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * IIS telepítése az egyéni szkriptek bővítményével
> * Az egyéni szkriptek bővítményét használó virtuális gép létrehozása
> * Futó IIS-hely megtekintése a bővítmény alkalmazása után

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre egyéni virtuálisgép-rendszerképeket.

> [!div class="nextstepaction"]
> [Egyéni virtuálisgép-rendszerképek létrehozása](./tutorial-custom-images.md)
