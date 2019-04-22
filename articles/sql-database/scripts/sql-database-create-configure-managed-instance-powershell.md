---
title: PowerShell-példa – az Azure SQL Database felügyelt példány létrehozása |} A Microsoft Docs
description: Az Azure PowerShell-példaszkript – az Azure SQL Database felügyelt példány létrehozása
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: c85b967615e866635cb4dd93be5ddeb78a8c7129
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357010"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>A felügyelt példány létrehozása az Azure SQL Database PowerShell-lel

Ez a PowerShell-példaszkript egy Azure SQL Database felügyelt példány új virtuális hálózaton belüli kijelölt alhálózatot hoz létre. Konfigurál továbbá egy útválasztási táblázatot, és a egy hálózati biztonsági csoportot a virtuális hálózathoz. A szkript sikeres futtatása után a következő felügyelt példányt is elérhetők a virtuális hálózaton belül, vagy egy a helyszíni környezetből. Lásd: [konfigurálása Azure virtuális gépek csatlakozni egy Azure SQL Database felügyelt példányába](../sql-database-managed-instance-configure-vm.md) és [pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányába való helyszíni](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> További korlátozások: [támogatott régiók](../sql-database-managed-instance-resource-limits.md#supported-regions) és [támogatott típusú előfizetésessel](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha helyi telepítése és használata a PowerShell, az oktatóanyaghoz AZ PowerShell 1.4.0-s vagy újabb. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítsa el az erőforráscsoportot és az ahhoz kapcsolódó összes erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehoz egy virtuális hálózatot |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Hozzáad egy alhálózati konfigurációt a virtuális hálózathoz |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Lekérdezi egy virtuális hálózat egy erőforráscsoportban |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Egy virtuális hálózat célállapotát beállítása |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Lekérdezi egy alhálózatot a virtuális hálózaton |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Egy alhálózati konfigurációt a célállapotát konfigurálja a virtuális hálózaton |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Létrehoz egy útvonaltáblát |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Lekérdezi az útvonaltáblák |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Beállítja egy útválasztási táblázatot cél állapota |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Létrehoz egy Azure SQL Database felügyelt példány |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
