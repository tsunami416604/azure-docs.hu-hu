---
title: Oktatóanyag – SQL-, IIS-, .NET stack-alapú virtuális gépek létrehozása az Azure-ban
description: Ez az oktatóanyag bemutatja, hogyan telepítheti az Azure SQL, IIS, .NET vermet Windows rendszerű virtuális gépeken az Azure-ban.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3e44236f74a5448c540c58ba730d65b412d48bd0
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101705"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Oktatóanyag: az SQL, az IIS és a .NET stack telepítése egy Windows rendszerű virtuális gépen Azure PowerShell

Ebben az oktatóanyagban egy SQL-, IIS-, .NET stack-t telepítünk Azure PowerShell használatával. Ez a verem két, Windows Server 2016-alapú virtuális gépből áll, amelyek közül az egyiken az IIS és a .NET, a másikon pedig az SQL Server fut.

> [!div class="checklist"]
> * Virtuális gép létrehozása 
> * Az IIS és a .NET Core SDK telepítése a virtuális gépen
> * SQL Servert futtató virtuális gép létrehozása
> * Az SQL Server-bővítmény telepítése

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell). A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-an-iis-vm"></a>IIS-alapú virtuális gép létrehozása 

Ebben a példában a PowerShell-Cloud Shell a [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmagot használjuk a Windows Server 2016 virtuális gép gyors létrehozásához, majd az IIS és a .NET-keretrendszer telepítéséhez. Az IIS-t és az SQL-t futtató virtuális gépek közös erőforráscsoportban és virtuális hálózaton találhatók, ezért a nevekhez változókat hozunk létre.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
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

Telepítse az IIS-t és a .NET-keretrendszert az egyéni szkriptek bővítménnyel a [set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) parancsmag használatával.

```azurepowershell-interactive
Set-AzVMExtension `
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

Hozzon létre egy második alhálózatot az SQL virtuális gép számára. A vNet lekérése a [Get-AzVirtualNetwork] {/PowerShell/Module/az.Network/Get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Az [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig)használatával hozzon létre egy konfigurációt az alhálózathoz.


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

A vNet frissítése az új alhálózati információkkal a [set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) használatával
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Azure SQL virtuális gép

Az SQL virtuális gép létrehozásához használja egy SQL Server előzetesen konfigurált, Azure Marketplace-en elérhető rendszerképét. Először létrehozzuk a virtuális gépet, majd telepítjük az SQL Server-bővítményt a gépen. 


```azurepowershell-interactive
New-AzVm `
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

A [set-AzVMSqlServerExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsqlserverextension) használatával adja hozzá a [SQL Server BŐVÍTMÉNYT](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) az SQL virtuális géphez.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
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

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan védheti meg az IIS-webkiszolgálót a TLS/SSL-tanúsítványokkal.

> [!div class="nextstepaction"]
> [AZ IIS-webkiszolgáló védelme TLS/SSL-tanúsítványokkal](tutorial-secure-web-server.md)

