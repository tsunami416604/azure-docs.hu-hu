---
title: Oktatóanyag – Alkalmazások telepítése méretezési csoportokban az Azure PowerShell-lel | Microsoft Docs
description: Megismerheti, hogyan telepíthet alkalmazásokat virtuálisgép-méretezési csoportokban az egyéni szkriptbővítmény és az Azure PowerShell használatával
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e70e392ef137dac8087a0a76ed17d93f7d7a6d0b
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886863"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Oktatóanyag: Alkalmazások telepítése virtuálisgép-méretezési csoportok az Azure PowerShell használatával
Ha alkalmazásokat szeretne futtatni egy méretezési csoport virtuálisgép-példányán, először telepítenie kell az alkalmazás összetevőit és szükséges fájljait. Egy korábbi oktatóanyagból megtudhatta, hogyan hozhat létre és használhat egyéni virtuálisgép-rendszerképeket a virtuálisgép-példányok üzembe helyezéséhez. Ez az egyéni rendszerkép tartalmazott manuális alkalmazástelepítéseket és -konfigurációkat. Az egyes virtuálisgép-példányok üzembe helyezése után lehetősége van az alkalmazások méretezési csoportokon történő telepítésének automatizálására, vagy egy a méretezési csoporton már futó alkalmazás frissítésére. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Alkalmazások automatikus telepítése a méretezési csoporton
> * Az egyéni Azure-szkriptek bővítményének használata
> * Egy méretezési csoporton futtatott alkalmazás frissítése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 6.0.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 


## <a name="what-is-the-azure-custom-script-extension"></a>Mi az egyéni Azure-szkriptek bővítménye?
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében.

Az egyéni szkriptek futtatására szolgáló bővítmény integrálható az Azure Resource Manager-sablonok. Is használható az Azure CLI, Azure PowerShell-lel, az Azure portal vagy a REST API-t. További információ: [Az egyéni szkriptbővítmény áttekintése](../virtual-machines/windows/extensions-customscript.md).

Az egyéni szkriptbővítmény működés közben való megtekintéséhez hozzon létre egy méretezési csoportot, amely telepíti az IIS-webkiszolgálót, és a méretezési csoport virtuálisgép-példányának gazdagépnevét adja vissza. Az egyéni szkriptbővítmény definíciója letölt egy mintaszkriptet a GitHubról, telepíti a szükséges csomagokat, majd kiírja a virtuálisgép-példány gazdagépnevét egy alap HTML-oldalra.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Most hozzon létre egy virtuálisgép-méretezési csoportot a [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) paranccsal. A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A load balancer a 80-as porton a forgalom elosztását szabályokat tartalmaz. Azt is lehetővé teszi a távoli asztali forgalmat a 3389-es TCP-porton és a PowerShell távoli eljáráshívást az 5985-ös TCP-port. Amikor a rendszer kéri, a saját rendszergazdai hitelesítő adatokkal, a Virtuálisgép-példányokhoz állíthatja be a méretezési csoportba:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="create-custom-script-extension-definition"></a>Definíció létrehozása az egyéni szkriptek bővítményéhez
Az Azure PowerShell egy kivonattáblában tárolja a letöltendő fájlt és a végrehajtandó parancsot. Az alábbi példa a GitHubról származó mintaszkriptet használja. Először hozza létre ezt a konfigurációs objektumot az alábbiak szerint:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```

Most alkalmazza az egyéni szkriptbővítményt az [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension) paranccsal. A korábban definiált konfigurációs objektum át lesz adva a bővítménynek. Frissítse és futtassa a bővítményt a virtuálisgép-példányokon az [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) paranccsal.

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

A méretezési csoport összes virtuálisgép-példánya a GitHubról tölti le és futtatja a szkriptet. Egy összetettebb példában több alkalmazás-összetevő és fájl is telepíthető. A méretezési csoport vertikális felskálázása esetén az új virtuálisgép-példányok automatikusan alkalmazzák az egyéni szkriptek bővítményének definícióját, és telepítik a szükséges alkalmazást.


## <a name="allow-traffic-to-application"></a>Forgalom engedélyezése az alkalmazáshoz

Ahhoz, hogy az alapszintű webalkalmazás a hozzáférést, hozzon létre egy hálózati biztonsági csoport [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) és [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). További információkért lásd: [Azure-beli virtuálisgép-méretezési csoportok hálózatkezelése](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```



## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
Szeretné látni működés közben a webkiszolgáló, a terheléselosztó a nyilvános IP-címének lekéréséhez [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Az alábbi példa megjeleníti a létrehozott IP-címet a *myResourceGroup* erőforráscsoportot:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Alapszintű weboldal az IIS-ben](media/tutorial-install-apps-powershell/running-iis.png)

Ne zárja be a böngészőt, hogy megtekinthesse a frissített verziót a következő lépés során.


## <a name="update-app-deployment"></a>Alkalmazástelepítés frissítése
A méretezési csoport életciklusa során előfordulhat, hogy telepítenie kell az alkalmazás frissített verzióját. Az egyéni szkriptek bővítményével hivatkozhat egy frissített telepítési szkriptre, majd alkalmazhatja ismét a bővítményt a méretezési csoporton. Amikor az előző lépésben létrehozta a méretezési csoportot, az `-UpgradePolicyMode` paraméter *Automatic* (Automatikus) értékre volt állítva. Ez a beállítás lehetővé teszi a méretezési csoportban található virtuálisgép-példányok számára az automatikus frissítést és az alkalmazás legfrissebb verziójának alkalmazását.

Hozzon létre egy új konfigurációdefiníciót *customConfigv2* néven. A definíció az alkalmazástelepítési szkript frissített (*v2*) verzióját futtatja:

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Frissítse az egyéni Szkriptbővítmény konfigurációját a méretezési csoportban lévő Virtuálisgép-példányok. Az alkalmazás frissített verziója a *customConfigv2* definíció használatával alkalmazható:

```azurepowershell-interactive
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

A méretezési csoport összes virtuálisgép-példánya automatikusan frissül a minta weboldal legfrissebb verziójával. A frissített verzió megtekintéséhez frissítse a webhelyet a böngészőben:

![Frissített weboldal az IIS-ben](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és további erőforrások eltávolításához törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal. A `-Force` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat. A `-AsJob` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogy lehet alkalmazásokat automatikusan telepíteni és frissíteni a méretezési csoportokban az Azure PowerShell-lel:

> [!div class="checklist"]
> * Alkalmazások automatikus telepítése a méretezési csoporton
> * Az egyéni Azure-szkriptek bővítményének használata
> * Egy méretezési csoporton futtatott alkalmazás frissítése

A következő oktatóanyag azt mutatja be, hogyan skálázhatja automatikusan a méretezési csoportját.

> [!div class="nextstepaction"]
> [Méretezési csoportok automatikus skálázása](tutorial-autoscale-powershell.md)
