---
title: Galériaképek megosztása az Azure-beli bérlők között
description: Megtudhatja, hogyan oszthatja meg a virtuálisgép-lemezképeket az Azure-bérlők között a megosztott képgalériák használatával.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/15/2019
ms.author: cynthn
ms.openlocfilehash: 9b7e7066f186017b7cc4408cd4f7edcc7e5f0dcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74065519"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Galéria virtuálisgép-lemezképeinek megosztása az Azure-bérlők között

A megosztott képgalériák lehetővé teszik a képek megosztását az RBAC használatával. Az RBAC segítségével megoszthatja a lemezképeket a bérlőn belül, és még a bérlőn kívüli személyek számára is. Erről az egyszerű megosztási lehetőségről a [Galéria megosztása című témakörben](/azure/virtual-machines/windows/shared-images-portal#share-the-gallery)talál további információt.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> A portál nem használhatja a virtuális gép üzembe helyezéséhez egy lemezkép egy másik azure-bérlő. Virtuális gép létrehozása a bérlők között megosztott lemezképből, az [Azure CLI](../linux/share-images-across-tenants.md) vagy a Powershell kell használnia.

## <a name="create-a-vm-using-powershell"></a>Virtuális gépek létrehozása a PowerShell-lel

Jelentkezzen be mindkét bérlőaz alkalmazásazonosító, titkos és bérlőazonosító használatával. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Hozza létre a virtuális gép az erőforráscsoportban, amely rendelkezik az alkalmazás regisztrációjának engedéllyel. Cserélje le a példában szereplő információkat a sajátjára.



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Set a variable for the image version in Tenant 1 using the full image ID of the shared image version
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Networking pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using the $image variable to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $image | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="next-steps"></a>További lépések

Megosztott képgaléria-erőforrásokat is létrehozhat az [Azure Portalon.](shared-images-portal.md)