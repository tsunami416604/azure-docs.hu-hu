---
title: "Hozzon létre egy SQL &#92; IIS &#92; futó virtuális gépeket. Az Azure-ban a hálózati verem |} Microsoft Docs"
description: "Útmutató – Windows virtuális gépek verem egy Azure SQL, az IIS, a .NET telepítése."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6f7ef46d9c40138c211427845423783fefde5dc3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>SQL &#92; IIS &#92; telepítése. HÁLÓZATI verem az Azure-ban

Ebben az oktatóanyagban az telepítjük SQL &#92; IIS &#92;. HÁLÓZATI verem Azure PowerShell használatával. A verem két futó virtuális gép Windows Server 2016, az IIS és a .NET és a másik SQL Server áll.

> [!div class="checklist"]
> * Új-AzVM virtuális gép létrehozása
> * Az IIS és a .NET Core SDK telepítése a virtuális gépen
> * SQL Server rendszert futtató virtuális gép létrehozása
> * Az SQL Server-bővítményének telepítése



## <a name="create-a-iis-vm"></a>Az IIS virtuális gép létrehozása 

A jelen példában használjuk a [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) parancsmag a PowerShell felhő rendszerhéj gyorsan a Windows Server 2016 virtuális gép létrehozása és telepítése az IIS és a .NET-keretrendszer. Az IIS és az SQL virtuális gépek megoszthatja egy erőforráscsoport és a virtuális hálózat, így ezeket a neveket változói létrehozhatunk.

Kattintson a **, próbálja** gombra a képernyő jobb felső sarkában a kódblokk elindíthatja a felhő rendszerhéj ebben az ablakban. Rendszer kéri, hogy a hitelesítő adatok megadása a virtuális gépet, a parancssort.

```azurepowershell-interactive
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm -Name myIISVM -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

Telepítse az IIS és a .NET-keretrendszer használatával az egyéni parancsprogramok futtatására szolgáló bővítmény.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName myIISVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>Azure SQL-virtuális gép

Egy SQL Server előre konfigurált Azure Piactéri rendszerkép használatával az SQL virtuális gép létrehozása. Azt először létre kell hoznia a virtuális Gépet, majd azt az SQL Server-bővítmény telepítése a virtuális Gépre. 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

Használjon [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) hozzáadása a [SQL Server-bővítmény](/sql/virtual-machines-windows-sql-server-agent-extension.md) az SQL-virtuális géphez.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban telepítette, egy SQL &#92; IIS &#92;. HÁLÓZATI verem Azure PowerShell használatával. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új-AzVM virtuális gép létrehozása
> * Az IIS és a .NET Core SDK telepítése a virtuális gépen
> * SQL Server rendszert futtató virtuális gép létrehozása
> * Az SQL Server-bővítményének telepítése

Előzetes tudnivalók megtekintéséhez SSL-tanúsítványokat az IIS-webkiszolgáló biztonságos következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Biztonságos SSL-tanúsítványokat az IIS-webkiszolgáló](tutorial-secure-web-server.md)

