---
title: "Windows virtuális gép létrehozása Azure-készletben a PowerShell használatával |} Microsoft Docs"
description: "Windows virtuális gép létrehozása a PowerShell Azure-készletben."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 42e126ffefd75669d90ea3ff1d3939028f71159c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-by-using-powershell-in-azure-stack"></a>Windows virtuális gép létrehozása Azure-készletben a PowerShell használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez az útmutató adatokat PowerShell használatával Windows Server 2016-os virtuális gép létrehozása Azure-készletben. Ha a VPN-en keresztül kapcsolódik a cikkben az Azure verem szoftverfejlesztői készlet, vagy a Windows-alapú külső ügyfél leírt lépéseket is futtathatja. 

## <a name="prerequisites"></a>Előfeltételek 

* Győződjön meg arról, hogy az Azure-verem operátor hozzá van adva a "Windows Server 2016" kép a verem Azure piactéren.  

* Az Azure verem Azure PowerShell-lel hozhatja létre és kezelheti az erőforrásokat adott verziója szükséges. Ha nincs PowerShell Azure verem konfigurálva, kövesse a lépéseket [telepítése](azure-stack-powershell-install.md) és [konfigurálása](azure-stack-powershell-configure-user.md) PowerShell.    

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Erőforráscsoport egy olyan logikai tároló mely Azure-verembe erőforrások telepítése és kezelése. A szoftverfejlesztői készlet vagy az integrált Azure verem rendszer futtassa a következő kódblokk egy erőforráscsoport létrehozásához. A jelen dokumentum összes változók értékeit hozzárendelt igazolnia, használhatja őket, vagy adjon meg másik értéket.  

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Tároló-erőforrások létrehozása 

Hozzon létre egy tárfiókot, és egy tárolót a Windows Server 2016-lemezkép mentéséhez.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>Hálózati erőforrások létrehozása

Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezeket az erőforrásokat segítségével adja meg a hálózati kapcsolat a virtuális géphez.  

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Hálózati biztonsági csoport és hálózati biztonsági csoportszabály létrehozása

A hálózati biztonsági csoport a virtuális gép bejövő és kimenő szabályok segítségével biztonságossá tételére. Lehetővé teszi, hogy a bejövő szabály a 3389-es bejövő távoli asztali kapcsolatok lehetővé tételéhez és a bejövő webes forgalomra engedélyezéséhez a 80-as portot a bejövő szabály létrehozása.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
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
  -Name myNetworkSecurityGroupRuleWWW `
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
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb 
```
 
### <a name="create-a-network-card-for-the-virtual-machine"></a>Hálózati kártya létrehozása a virtuális géphez

A hálózati kártya csatlakoztatja a virtuális gépet egy alhálózathoz, egy hálózati biztonsági csoporthoz és egy nyilvános IP-címhez.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id 
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuálisgép-konfigurációt. A konfiguráció tartalmazza a beállításokat, például egy virtuális gép lemezképére, méret, hitelesítő adatokat a virtuális gép telepítésekor használt.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize 

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential 

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine. 
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id 

#Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

Távoli azokat a virtuális gép, amelyet az előző lépésben hozott létre szüksége a nyilvános IP-cím. Futtassa a következő parancs használatával beszerezheti a virtuális gép nyilvános IP-címe: 

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```
 
A következő parancs segítségével távoli asztali munkamenetet létrehozni a virtuális gép. Cserélje le a virtuális gép nyilvános IP-címét. Amikor a rendszer kéri, adja meg a felhasználónevet és jelszót, amely a virtuális gép létrehozásakor használt.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>IIS telepítése a PowerShell használatával

Miután bejelentkezett az Azure-beli virtuális gépre, egyetlen PowerShell-utasítással telepítheti az IIS-t, és engedélyezheti, hogy a helyi tűzfalszabály átengedje a webforgalmat. Nyisson meg egy PowerShell-parancssort, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

Miután az IIS telepítve lett, és a 80-as port meg van nyitva a virtuális gépen az internet irányából, egy tetszőleges böngésző használatával megtekintheti az alapértelmezett IIS-kezdőlapot. Ügyeljen arra, hogy az alapértelmezett oldalt a fentebb dokumentált *publicIPAddress* használatával keresse fel. 

![Alapértelmezett IIS-webhely](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png) 


## <a name="delete-the-virtual-machine"></a>A virtuális gép törlése

Ha már nem szükséges, távolítsa el az erőforráscsoportot, amely a virtuális gép és azok kapcsolódó erőforrásait tartalmazza a következő paranccsal:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Következő lépések

A gyors egyszerű Windows rendszerű virtuális gép telepítése után. További információt a verem Azure virtuális gépek, továbbra is [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).

