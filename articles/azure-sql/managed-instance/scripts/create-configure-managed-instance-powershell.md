---
title: 'PowerShell: példány létrehozása'
titleSuffix: Azure SQL Managed Instance
description: Példa Azure PowerShell parancsfájl létrehozására egy felügyelt Azure SQL-példány létrehozásához
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 9dfe6177ed46f133772a46930d1e56c6007b33f3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053974"
---
# <a name="use-powershell-to-create-an-azure-sql-managed-instance"></a>Az Azure SQL felügyelt példányának létrehozása a PowerShell használatával
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Ez a PowerShell-parancsfájl például egy Azure SQL felügyelt példányt hoz létre egy dedikált alhálózatban egy új virtuális hálózaton belül. Emellett egy útválasztási táblázatot és egy hálózati biztonsági csoportot is konfigurál a virtuális hálózathoz. A szkript sikeres futtatása után az SQL felügyelt példánya a virtuális hálózatról vagy egy helyszíni környezetből is elérhető. Lásd: az [Azure virtuális gép konfigurálása Azure SQL Database felügyelt példányhoz való kapcsolódáshoz](../connect-vm-instance-configure.md) és [pont – hely kapcsolat konfigurálása egy Azure SQL felügyelt példányhoz a helyszínen](../point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](../resource-limits.md#supported-regions) és [támogatott előfizetési típusok](../resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

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
| [Új – AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Létrehoz egy felügyelt Azure SQL-példányt |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL felügyelt példányok PowerShell-parancsfájlokat az [Azure SQL felügyelt példány PowerShell-parancsfájljaiban](../../database/powershell-script-content-guide.md)találhat.
