---
title: 'PowerShell: példány hozzáadása automatikus feladatátvételi csoporthoz'
titleSuffix: Azure SQL Managed Instance
description: Azure PowerShell példa parancsfájlt egy felügyelt Azure SQL-példány létrehozásához, vegye fel azt egy automatikus feladatátvételi csoportba, és tesztelje a feladatátvételt.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 0dbd6fbefababd36d87047a91d3ec320dc795e65
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053980"
---
# <a name="use-powershell-to-add-an-azure-sql-managed-instance-to-a-failover-group"></a>Azure SQL felügyelt példány hozzáadása feladatátvételi csoporthoz a PowerShell használatával 
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Ez a PowerShell-parancsfájl két SQL felügyelt példányt hoz létre, hozzáadja azokat egy feladatátvételi csoporthoz, majd az elsődleges SQL felügyelt példányról a másodlagos SQL felügyelt példányra történő feladatátvételt teszteli. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaparancsfájlok

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást. Az erőforráscsoportot kétszer kell eltávolítania. Az erőforráscsoport eltávolításakor a rendszer először eltávolítja az SQL felügyelt példányt és a virtuális fürtöket, de a hibaüzenettel meghiúsul `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.` . Futtassa a Remove-AzResourceGroup parancsot egy második alkalommal, hogy eltávolítsa a fennmaradó erőforrásokat és az erőforráscsoportot.

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
| [Új – AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Létrehoz egy felügyelt Azure SQL-példányt.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| A felügyelt SQL-példányra vonatkozó adatokat adja vissza. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre.  | 
| [Új – AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Egy Virtual Network átjáró IP-konfigurációjának létrehozása |
| [Új – AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Virtual Network átjáró létrehozása |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Kapcsolatot hoz létre a két virtuális hálózati átjáró között.   |
| [Új – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Létrehoz egy új Azure SQL felügyelt példány feladatátvételi csoportot.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Lekérdezi vagy listázza az SQL felügyelt példányának feladatátvételi csoportjait.| 
| [Kapcsoló – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Végrehajtja az SQL felügyelt példányok feladatátvételi csoportjának feladatátvételét. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot. | 

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL felügyelt példányok PowerShell-parancsfájlokat az [Azure SQL felügyelt példány PowerShell-parancsfájljaiban](../../database/powershell-script-content-guide.md)találhat.
