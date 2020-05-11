---
title: PowerShell-példa – felügyelt példány létrehozása Azure SQL Database
description: Azure PowerShell példa parancsfájlt a felügyelt példányok létrehozásához Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: a4ba4d69b560307e52c277f5c695bbba718cd1d3
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005823"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példány létrehozása a PowerShell használatával

Ez a PowerShell-parancsfájl például egy Azure SQL Database felügyelt példányt hoz létre egy dedikált alhálózaton egy új virtuális hálózaton belül. Emellett egy útválasztási táblázatot és egy hálózati biztonsági csoportot is konfigurál a virtuális hálózathoz. A szkript sikeres futtatása után a felügyelt példány a virtuális hálózatról vagy egy helyszíni környezetből is elérhető. Lásd: az [Azure virtuális gép konfigurálása Azure SQL Database felügyelt példányhoz való kapcsolódáshoz](../sql-database-managed-instance-configure-vm.md) , valamint [pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányhoz a helyszínen](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](../sql-database-managed-instance-resource-limits.md#supported-regions) és [támogatott előfizetési típusok](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja. Az alábbi táblázatban szereplő használattal és egyéb parancsokkal kapcsolatos további információkért kattintson a parancsokra vonatkozó dokumentációra mutató hivatkozásokra.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja.
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehoz egy virtuális hálózatot |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Alhálózat konfigurációjának feladása egy virtuális hálózathoz |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Egy erőforráscsoport virtuális hálózatának beolvasása |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Egy virtuális hálózat cél állapotának beállítása |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Alhálózatot kap egy virtuális hálózatban |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Egy virtuális hálózatban lévő alhálózat konfigurációjának cél állapotát konfigurálja. |
| [Új – AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Útválasztási táblázat létrehozása |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Útválasztási táblák beolvasása |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Egy útválasztási táblázat cél állapotának beállítása |
| [Új – AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Létrehoz egy Azure SQL Database felügyelt példányt |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
