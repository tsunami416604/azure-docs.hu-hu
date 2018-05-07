---
title: Linux virtuális gép létrehozása Azure-készletben a PowerShell használatával |} Microsoft Docs
description: Linux virtuális gép létrehozása a PowerShell Azure-készletben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 03EE5929-4F05-47D7-B246-EA93D6FC47CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 1e2dbc6020dd317e96c4116811f8e3bf87680bfb
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Gyors üzembe helyezés: Linux server virtuális gép létrehozása Azure-készletben a PowerShell használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Ubuntu Server 16.04 LTS virtuális gép Azure verem PowerShell használatával is létrehozhat. Kövesse a cikkben történő létrehozásáról és használatáról a virtuális gép.  Ez a cikk is lehetővé teszi a lépéseket:

* Csatlakoztassa a virtuális gép egy távoli ügyfélhez.
* A NGINX-webkiszolgáló telepítéséhez, és az alapértelmezett kezdőlapja a lapnak a megtekintésére.
* Nem használt erőforrások törlése.

## <a name="prerequisites"></a>Előfeltételek

* **A Linux-lemezkép a verem Azure piactéren**

   A verem Azure piactér nem tartalmazza a Linux-lemezkép alapértelmezés szerint. Adja meg az Azure-verem operátorral beolvasása a **Ubuntu Server 16.04 LTS** lemezkép van szüksége. Az operátor használhatja a leírt lépéseket a [Piactéri elemek töltse le az Azure-ból Azure verem](../azure-stack-download-azure-marketplace-item.md) cikk.

* Az Azure verem hozzon létre és kezelheti az erőforrásokat az Azure PowerShell adott verziója szükséges. Ha még nem rendelkezik konfigurált Azure verem PowerShell, jelentkezzen be a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) és kövesse a lépéseket a [ telepítés](azure-stack-powershell-install.md) és [konfigurálása](azure-stack-powershell-configure-user.md) PowerShell.

* Egy nyilvános SSH-kulcs az a név id_rsa.pub menti a felhasználói profil .ssh könyvtárában. SSH-kulcsok létrehozásával kapcsolatos részletes információkért lásd: [létrehozása SSH kulcsok Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Erőforráscsoport egy olyan logikai tároló, ahol telepítheti és kezelheti az Azure-verem erőforrásokat. A szoftverfejlesztői készlet vagy az integrált Azure verem rendszer futtassa a következő kódblokk egy erőforráscsoport létrehozásához. Ebben a dokumentumban a változók értékek vannak hozzárendelve, használhatja ezeket az értékeket, vagy új értéket hozzárendelni.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Tároló-erőforrások létrehozása

Hozzon létre egy tárfiókot, és ezután hozzon létre egy tárolót az Ubuntu Server 16.04 LTS lemezkép.

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

A hálózati biztonsági csoport a virtuális gép bejövő és kimenő szabályok segítségével biztonságossá tételére. Bejövő szabály a 3389-es bejövő távoli asztali kapcsolatok lehetővé tételéhez és a bejövő webes forgalomra engedélyezéséhez a 80-as portot a bejövő szabály létrehozása.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location local `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
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

Hozzon létre egy virtuálisgép-konfigurációt. A konfiguráció tartalmazza a beállításokat a virtuális gép telepítésekor használja. Például: felhasználói hitelesítő adatokat, méretének és a virtuális gép lemezképe.

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
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

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

A virtuális gép telepítése után konfigurálja az SSH-kapcsolat a virtuális géphez. A virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-4.3.1) parancsot.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Az SSH telepített ügyfél rendszerből használja a következő parancsot a virtuális géphez történő csatlakozáshoz. Ha Windows dolgozik, akkor használhatja [Putty](http://www.putty.org/) a VPN-kapcsolat létrehozásához.

```
ssh <Public IP Address>
```

Amikor a rendszer kéri, adja meg a bejelentkezési felhasználói azureuser. Ha az SSH-kulcsok létrehozásakor használt jelszó szükséges, adja meg a jelszót kell.

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

Csomag erőforrásokat, és telepítse a legújabb NGINX-csomagot, futtassa a következő parancsfájlt:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Az NGINX telepítve, és a virtuális gépen nyissa meg a 80-as porton a webkiszolgálón, a virtuális gép nyilvános IP-cím használatával végezheti el. Nyisson meg egy webböngészőt, és keresse meg a ```http://<public IP address>```.

![NGINX web server kezdőlap](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyek többé nem kell törölni. Használhatja a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup?view=azurermps-4.3.1) parancs beírásával távolítsa el ezeket az erőforrásokat. Az erőforráscsoport és az ahhoz tartozó összes erőforrást törli, a következő parancsot:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

A gyors üzembe helyezés telepített Linux server alapvető virtuális gépek. Virtuális gépek Azure-verem kapcsolatos további tudnivalókért keresse fel [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).
