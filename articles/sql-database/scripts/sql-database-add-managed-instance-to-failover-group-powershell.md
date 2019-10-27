---
title: PowerShell-példa – feladatátvételi csoport – Azure SQL Database felügyelt példány | Microsoft Docs
description: Azure PowerShell például egy Azure SQL Database felügyelt példány létrehozásához, egy feladatátvételi csoporthoz való hozzáadásához és a feladatátvételi teszthez.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: f753dbce66c8db9c4098e716725f0a63fc68d4f7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72956291"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Azure SQL Database felügyelt példány hozzáadása feladatátvételi csoporthoz a PowerShell használatával 

Ez a PowerShell-parancsfájl két felügyelt példányt hoz létre, hozzáadja azokat egy feladatátvételi csoporthoz, majd az elsődleges felügyelt példányról a másodlagos felügyelt példányra ellenőrzi a feladatátvételt. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaszkriptek

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást. Az erőforráscsoportot kétszer kell eltávolítania. Az erőforráscsoport eltávolításakor a rendszer először eltávolítja a felügyelt példányt és a virtuális fürtöket, de a hibaüzenettel `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Futtassa a Remove-AzResourceGroup parancsot egy második alkalommal, hogy eltávolítsa a fennmaradó erőforrásokat és az erőforráscsoportot.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot.  |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Beolvas egy alhálózatot egy virtuális hálózatban. | 
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot hoz létre. | 
| [Új – AzRouteTable](/powershell/module/az.network/new-azroutetable) | Létrehoz egy útválasztási táblázatot. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Egy virtuális hálózat alhálózati konfigurációjának frissítése.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Frissíti a virtuális hálózatot.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hálózati biztonsági csoport beolvasása. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Egy hálózati biztonsági csoport frissítése.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Útvonalat helyez el egy útválasztási táblához. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Frissíti az útválasztási táblázatot.  |
| [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy Azure SQL Database felügyelt példányt.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Az Azure SQL felügyelt adatbázis példányával kapcsolatos információkat ad vissza. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új – AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Egy Virtual Network átjáró IP-konfigurációjának létrehozása |
| [Új – AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Virtual Network átjáró létrehozása |
| [Új – AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Kapcsolatot hoz létre a két virtuális hálózati átjáró között.   |
| [Új – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Létrehoz egy új Azure SQL Database felügyelt példány feladatátvételi csoportját.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Lekérdezi vagy felsorolja a felügyelt példányok feladatátvételi csoportjait.| 
| [Kapcsoló – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Végrehajtja a felügyelt példányok feladatátvételi csoportjának feladatátvételét. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot. | 

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
