---
title: Oktatóanyag – Alkalmazások telepítése Windows rendszerű virtuális gépre az Azure-ban | Microsoft Docs
description: Ennek az oktatóanyagnak a követésével elsajátíthatja, hogyan használja az szkriptbővítményt szkriptek futtatására és alkalmazások Windows rendszerű virtuális gépekre való telepítésére az Azure-ban
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2a365321e35c84d5b09570a7eac41bec6cdeb143
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928964"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Oktatóanyag – Alkalmazások telepítése Windows virtuális gépekre az Azure-ban az egyéni szkriptbővítménnyel

A virtuális gépek gyors és következetes konfigurálásához általában valamilyen automatizálásra van szükség. A Windows rendszerű virtuális gépek testreszabásának általánosan használt megközelítése az [egyéni szkriptek Windows-bővítményének](extensions-customscript.md) használata. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * IIS telepítése az egyéni szkriptek bővítményével
> * Az egyéni szkriptek bővítményét használó virtuális gép létrehozása
> * Futó IIS-hely megtekintése a bővítmény alkalmazása után

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


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
