---
title: PowerShell-példa – felügyelt példány létrehozása az Azure SQL Database-ben
description: Azure PowerShell példaparancsfájl felügyelt példány létrehozásához az Azure SQL Database-ben
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
ms.openlocfilehash: 3e72a2f6754ad8e9c5bcfabe7eeee299468fa8f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691642"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példányának létrehozása a PowerShell használatával

Ez a PowerShell-parancsfájl-példa egy Azure SQL Database által felügyelt példányt hoz létre egy új virtuális hálózaton belüli dedikált alhálózatban. Egy útvonaltáblát és egy hálózati biztonsági csoportot is konfigurál a virtuális hálózathoz. A parancsfájl sikeres futtatása után a felügyelt példány a virtuális hálózaton belülről vagy egy helyszíni környezetből érhető el. Lásd: [Az Azure VM konfigurálása egy Azure SQL Database felügyelt példányhoz való csatlakozáshoz,](../sql-database-managed-instance-configure-vm.md) és [konfigurálása egy pont-hely kapcsolat egy Azure SQL-adatbázis felügyelt példánya a helyszíni.](../sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> A korlátozásokat a [Támogatott régiók](../sql-database-managed-instance-resource-limits.md#supported-regions) és a [támogatott előfizetéstípusok című témakörben](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)tésszet.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az AZ PowerShell 1.4.0-s vagy újabb szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az erőforráscsoport és a hozzá tartozó összes erőforrás eltávolításához használja a következő parancsot.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja.
| [Új-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózat létrehozása |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Alhálózati konfiguráció hozzáadása virtuális hálózathoz |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Virtuális hálózat bekéselése egy erőforráscsoportban |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Virtuális hálózat célállapotának beállítja |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Alhálózat bekéselése egy virtuális hálózatban |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | A virtuális hálózat alhálózati konfigurációjának célállapotának konfigurálása |
| [Új-Azroutetable](/powershell/module/az.network/New-AzRouteTable) | Útvonaltábla létrehozása |
| [Get-Azroutetable](/powershell/module/az.network/Get-AzRouteTable) | Útvonaltáblák leválasztása |
| [Set-Azroutetable](/powershell/module/az.network/Set-AzRouteTable) | Útvonaltábla célállapotának beállítja |
| [Új-Azsqlinstance](/powershell/module/az.sql/New-AzSqlInstance) | Létrehoz egy Azure SQL Database felügyelt példányt |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
