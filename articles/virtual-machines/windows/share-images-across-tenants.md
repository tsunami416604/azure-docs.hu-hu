---
title: Katalógusbeli rendszerképek megosztása az Azure-beli bérlők között
description: Megtudhatja, hogyan oszthat meg virtuálisgép-lemezképeket az Azure-bérlők között megosztott képtárakkal.
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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065519"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Katalógusbeli virtuálisgép-rendszerképek megosztása Azure-bérlők között

A megosztott képtárak lehetővé teszik a képek megosztását a RBAC használatával. A RBAC segítségével megoszthatja a bérlőn belüli képeket, és akár a bérlőn kívüli személyeket is. További információ erről az egyszerű megosztási lehetőségről: a [gyűjtemény megosztása](/azure/virtual-machines/windows/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> A portálon nem lehet virtuális gépet üzembe helyezni egy másik Azure-bérlő lemezképéről. Ha a bérlők között megosztott rendszerképből szeretne virtuális gépet létrehozni, az [Azure CLI](../linux/share-images-across-tenants.md) -t vagy a PowerShellt kell használnia.

## <a name="create-a-vm-using-powershell"></a>Virtuális gép létrehozása a PowerShell használatával

Jelentkezzen be mindkét bérlőbe az alkalmazás-azonosító, a titkos kulcs és a bérlő azonosítója használatával. 

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

Hozza létre a virtuális gépet abban az erőforráscsoporthoz, amely jogosult az alkalmazás regisztrálására. Cserélje le az ebben a példában szereplő adatokat a saját adataira.



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

A [Azure Portal](shared-images-portal.md)használatával közös rendszerkép-katalógusbeli erőforrásokat is létrehozhat.