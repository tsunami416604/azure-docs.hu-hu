---
title: Zónákba sorolt, Windows rendszerű virtuális gép létrehozása – Azure PowerShell | Microsoft Docs
description: Windows rendszerű virtuális gép létrehozása egy rendelkezésre állási zónában az Azure PowerShell használatával
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 23c53982919ad29c639a6441f206abb35ddb7a1b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430791"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-powershell"></a>Windows rendszerű virtuális gép létrehozása egy rendelkezésre állási zónában a PowerShell használatával

Ez a cikk részletesen bemutatja, hogyan lehet egy Windows Server 2016-ot futtató Azure virtuális gépet létrehozni egy Azure rendelkezésre állási zónában az Azure PowerShell használatával. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

Rendelkezésre állási zóna használatához egy [támogatott Azure-régióban](../../availability-zones/az-overview.md#regions-that-support-availability-zones) hozza létre a virtuális gépet.

Győződjön meg arról, hogy az Azure PowerShell-modul legújabb verziója van telepítve a gépén. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

## <a name="check-vm-sku-availability"></a>A VM-termékváltozatok rendelkezésre állásának ellenőrzése
A virtuális gépek méretének vagy termékváltozatainak rendelkezésre állása régiónként és zónánként eltérhet. Ha fel szeretne készülni a rendelkezésre állási zónák használatára, megtekintheti a virtuális gépek termékváltozatainak listáját Azure-régió és zóna szerint. Ezáltal megfelelő virtuálisgép-méretet választhat, valamint biztosíthatja a zónák közötti rugalmasság kívánt szintjét. További információ a virtuális gépek különböző típusairól és méreteiről: [Virtuálisgép-méretek – áttekintés](sizes.md).

Az elérhető virtuális gépek termékváltozatait a [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) paranccsal tekintheti meg. Az alábbi példa az *eastus2* régióban található virtuális gépek elérhető termékváltozatait listázza:

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

A kimenet a következő sűrített példához hasonló, amelyben azok a rendelkezésre állási zónák láthatók, amelyekben minden virtuálisgép-méret elérhető:

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ebben a példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre az *eastus2* régióban. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>Hálózati erőforrások létrehozása

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Virtuális hálózat, alhálózat és nyilvános IP-cím létrehozása 
Ezek az erőforrások a virtuális gép hálózati csatlakoztatásának biztosítására, illetve az internethez csatlakoztatására használatosak. Ebben a példában a nyilvános IP-címet a *2.* rendelkezésre állási zónában hozzuk létre. Egy későbbi lépésben ugyanabban a zónában fog virtuális gépet létrehozni, amelyet az IP-cím létrehozásához használt.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Hálózati biztonsági csoport és hálózati biztonsági csoportszabály létrehozása 
A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális gépet. Ebben az esetben létrejön egy bejövő szabály a 3389-es porthoz, amely lehetővé teszi a bejövő távoli asztali kapcsolatokat. Egy bejövő szabályt is létre szeretnénk hozni a 80-as porthoz, amely lehetővé teszi a bejövő webes forgalmat.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Hálózati kártya létrehozása a virtuális géphez 
Hozzon létre egy hálózati kártyát a virtuális géphez a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) parancsmaggal. A hálózati kártya csatlakoztatja a virtuális gépet egy alhálózathoz, egy hálózati biztonsági csoporthoz és egy nyilvános IP-címhez.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuálisgép-konfigurációt. Ebben a konfigurációban a virtuális gép üzembe helyezése során használt beállítások szerepelnek, például a virtuális gép rendszerképe, mérete és hitelesítési konfigurációja. A példában szereplő *Standard_DS1_v2* méretet a rendelkezésre állási zónák is támogatják. Ebben a konfigurációban az IP-cím létrehozásakor beállított rendelkezésre állási zóna is szerepel. Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>Felügyelt lemez zónájának megerősítése

A virtuális gép IP-címerőforrását ugyanabban a rendelkezésre állási zónában hozta létre, mint a virtuális gépet. A virtuális gép felügyeltlemez-erőforrása ugyanebben a rendelkezésre állási zónában jött létre. Ezt a [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk) használatával ellenőrizheti:

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

A kimenetben az látható, hogy a felügyelt lemez és a virtuális gép azonos rendelkezésre állási zónában található:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan hozható létre virtuális gép egy rendelkezésre állási zónában. Itt további információkat talál az Azure-beli virtuális gépek [régióiról és rendelkezésre állásáról](regions-and-availability.md).
