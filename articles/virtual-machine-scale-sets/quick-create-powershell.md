---
title: Rövid útmutató – Virtuálisgép-méretezési csoport létrehozása az Azure PowerShell használatával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre gyorsan virtuálisgép-méretezési csoportot az Azure PowerShellel
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/18
ms.author: cynthn
ms.openlocfilehash: 6e59c6ead364c77e3659b9c2a92106cb55abf744
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885965"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-azure-powershell"></a>Gyors útmutató: Hozzon létre egy virtuálisgép-méretezési csoport az Azure PowerShell használatával
Egy virtuálisgép-méretezési csoportot üzembe helyezheti és kezelheti az azonos, automatikus méretezést végző virtuális gépekről teszi lehetővé. A méretezési csoportban lévő virtuális gépek számát beállíthatja manuálisan, de automatikus méretezési szabályokat is megadhat az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Egy Azure-terheléselosztó ezután elosztja a forgalmat a méretezési csoportban lévő virtuálisgép-példányok között. Ebben a rövid útmutatóban egy virtuálisgép-méretezési csoportot hozunk létre, és üzembe helyezünk egy mintaalkalmazást az Azure PowerShell-lel.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 6.0.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja PowerShell, is futtatni szeretné `Connect-AzureRmAccount` kapcsolat létrehozása az Azure-ral.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Hozzon létre egy virtuálisgép-méretezési csoportot a [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) paranccsal. A következő példa létrehoz egy *myScaleSet* nevű méretezési csoportot, amely a *Windows Server 2016 Datacenter* platformrendszerképet használja. A rendszer automatikusan létrehozza az Azure-beli hálózati erőforrásokat a virtuális hálózathoz, a nyilvános IP-címhez és a terheléselosztóhoz. Amikor a rendszer kéri, a saját rendszergazdai hitelesítő adatokkal, a Virtuálisgép-példányokhoz állíthatja be a méretezési csoportba:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="deploy-sample-application"></a>Mintaalkalmazás üzembe helyezése
A méretezési csoport teszteléséhez telepítsen egy alapszintű webalkalmazást. Az egyéni Azure-szkriptbővítménnyel egy olyan szkriptet tölthet le és futtathat, amely az IIS-t telepíti a virtuálisgép-példányokon. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. További információ: [Az egyéni szkriptbővítmény áttekintése](../virtual-machines/windows/extensions-customscript.md).

Telepítsen egy alapszintű IIS-webkiszolgálót az egyéni szkriptbővítménnyel. Alkalmazza az IIS-t telepítő egyéni szkriptbővítményt a következő módon:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

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
Ha ellenőrizni szeretné, hogyan működik a méretezési csoport, lépjen egy böngészőben a mintául szolgáló webalkalmazáshoz. A terheléselosztó a nyilvános IP-címének lekéréséhez [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Az alábbi példa megjeleníti a létrehozott IP-címet a *myResourceGroup* erőforráscsoportot:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Futó IIS-hely](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rájuk szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás. A `-Force` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat. A `-AsJob` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egy alapszintű méretezési csoportot hoztunk létre, valamint az egyéni szkriptbővítménnyel egy alapszintű IIS-webkiszolgálót telepítettünk a VM-példányokon. Ha bővebb információra van szüksége, lépjen tovább az Azure-beli virtuálisgép-méretezési csoportok létrehozásáról és kezeléséről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli virtuálisgép-méretezési csoportok létrehozása és kezelése](tutorial-create-and-manage-powershell.md)
