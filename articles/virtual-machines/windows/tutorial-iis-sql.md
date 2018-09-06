---
title: Oktatóanyag – SQL&#47;IIS&#47;.NET vermet futtató virtuális gépek létrehozása az Azure-ban | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan telepítheti az Azure SQL, IIS, .NET vermet Windows rendszerű virtuális gépeken az Azure-ban.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f8f91a4c039e07b636395ab953b8031d5ffe9958
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43343506"
---
# <a name="tutorial-install-the-sql47iis47net-stack-in-a-windows-vm-with-azure-powershell"></a>Oktatóanyag: Az SQL&#47;IIS&#47;.NET verem telepítése Windows rendszerű virtuális gépeken az Azure PowerShell használatával

Ebben az oktatóanyagban egy SQL&#47;IIS&#47;.NET vermet fogunk telepíteni az Azure PowerShell-lel. Ez a verem két, Windows Server 2016-alapú virtuális gépből áll, amelyek közül az egyiken az IIS és a .NET, a másikon pedig az SQL Server fut.

> [!div class="checklist"]
> * Virtuális gép létrehozása 
> * Az IIS és a .NET Core SDK telepítése a virtuális gépen
> * SQL Servert futtató virtuális gép létrehozása
> * Az SQL Server-bővítmény telepítése

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AzureRM.Compute modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM.Compute`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="create-a-iis-vm"></a>IIS rendszerű virtuális gép létrehozása 

Ebben a példában a [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmagot használjuk a PowerShell Cloud Shellben egy Windows Server 2016 rendszerű virtuális gép gyors létrehozásához, majd az IIS és a .NET-keretrendszer telepítéséhez. Az IIS-t és az SQL-t futtató virtuális gépek közös erőforráscsoportban és virtuális hálózaton találhatók, ezért a nevekhez változókat hozunk létre.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Telepítse az IIS-t és a .NET-keretrendszert az egyéni szkriptbővítmény segítségével.

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Másik alhálózat létrehozása

Hozzon létre egy második alhálózatot az SQL virtuális gép számára. Kérje le a vNetet a következő parancsmaggal: [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Hozza létre az alhálózat konfigurációját az [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) parancsmaggal.


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Frissítse a vNetet az új alhálózat adataival a [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) parancsmag használatával.
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>Azure SQL virtuális gép

Az SQL virtuális gép létrehozásához használja egy SQL Server előzetesen konfigurált, Azure Marketplace-en elérhető rendszerképét. Először létrehozzuk a virtuális gépet, majd telepítjük az SQL Server-bővítményt a gépen. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

A [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) parancsmag segítségével adja hozzá az [SQL Server-bővítményt](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) az SQL virtuális géphez.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy SQL&#92;IIS&#92;.NET vermet telepített az Azure PowerShell-lel. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális gép létrehozása 
> * Az IIS és a .NET Core SDK telepítése a virtuális gépen
> * SQL Servert futtató virtuális gép létrehozása
> * Az SQL Server-bővítmény telepítése

Folytassa a következő oktatóanyaggal, amelyből megismerheti, hogyan biztosítható az IIS-webkiszolgáló védelme SSL-tanúsítványok használatával.

> [!div class="nextstepaction"]
> [Az IIS-webkiszolgáló védelme SSL-tanúsítványokkal](tutorial-secure-web-server.md)

