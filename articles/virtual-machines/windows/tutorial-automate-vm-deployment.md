---
title: "A Windows Azure-ban testreszabása |} Microsoft Docs"
description: "Útmutató az egyéni parancsprogramok futtatására szolgáló bővítmény és a Key Vault segítségével testre szabhatja a Windows-alapú virtuális gépek Azure-ban"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e0503cef234b01c4eefb1c1de3d88d9a812c4c39
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>A Windows Azure virtuális gép testreszabása
Virtuális gépek (VM) gyors és egységes módon konfigurálásához valamilyen automatizált művelettel általában van szükség. A Windows virtuális gépek testreszabása általános gyakorlatként javasolt, hogy használja [egyéni parancsfájl kiterjesztése a Windows](extensions-customscript.md). Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával az IIS telepítése
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használó virtuális gép létrehozása
> * Egy futó IIS-webhely megtekintheti a bővítmény alkalmazása után

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 


## <a name="custom-script-extension-overview"></a>Egyéni parancsfájl-bővítmény áttekintése
Az egyéni parancsprogramok futtatására szolgáló bővítmény és hajtanak végre a parancsfájlok Azure virtuális gépeken. A bővítmény akkor hasznos, ha a feladás egy vagy több központi telepítés konfigurálása, a szoftver telepítése vagy a más beállításokat / kezelési feladatot. Parancsfájlok le: az Azure storage vagy a Githubon, vagy megadott futásidejű bővítmény: az Azure portálon.

Az egyéni parancsprogramok futtatására szolgáló bővítmény integrálódik az Azure Resource Manager-sablonok, és is futtathat az Azure parancssori felület, PowerShell, Azure-portálon vagy az Azure virtuális gép REST API használatával.

Az egyéni parancsprogramok futtatására szolgáló bővítmény Windows és Linux virtuális gépek egyaránt használható.


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
A virtuális gépek létrehozása előtt hozzon létre egy erőforráscsoportot, a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupAutomate* a a *EastUS* helye:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupAutomate -Location EastUS
```

Állítsa a Rendszergazda felhasználónévvel és jelszóval rendelkező virtuális gépek [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Most a virtuális Géphez a hozhat létre [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példa hoz létre a szükséges virtuális hálózati összetevők, az operációs rendszer konfigurációja, és létrehoz egy nevű virtuális gép *myVM*:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
New-AzureRmVM -ResourceGroupName myResourceGroupAutomate -Location EastUS -VM $vmConfig
```

Az erőforrások és a virtuális gép, létre kell néhány percet vesz igénybe.


## <a name="automate-iis-install"></a>Az IIS-telepítés automatizálása
Használjon [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) az egyéni parancsprogramok futtatására szolgáló bővítmény telepítése. A bővítmény fut `powershell Add-WindowsFeature Web-Server` az IIS webkiszolgálót, és a frissítések telepítése a *Default.htm* a virtuális gép állomásnevét lapjait:

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName myResourceGroupAutomate `
    -ExtensionName IIS `
    -VMName myVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
```


## <a name="test-web-site"></a>Teszt webhely
Szerezze be a terheléselosztó a nyilvános IP-címe [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Az alábbi példa beolvassa az IP-címek *myPublicIP* korábban létrehozott:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Name myPublicIP | select IpAddress
```

Beírhatja a nyilvános IP-címet a webböngésző. A webhely jelenik meg, beleértve az állomásnevet, a virtuális gép, amelyek a terheléselosztó felé irányuló forgalom az alábbi példában látható módon:

![Futó IIS-webhely](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az IIS telepítése a virtuális gép automatikus. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával az IIS telepítése
> * Az egyéni parancsprogramok futtatására szolgáló bővítmény használó virtuális gép létrehozása
> * Egy futó IIS-webhely megtekintheti a bővítmény alkalmazása után

Továbblépés a következő oktatóanyag áttekintésével megismerheti, hogyan hozza létre egyéni Virtuálisgép-lemezképeket.

> [!div class="nextstepaction"]
> [Egyéni virtuálisgép-rendszerképek létrehozása](./tutorial-custom-images.md)
