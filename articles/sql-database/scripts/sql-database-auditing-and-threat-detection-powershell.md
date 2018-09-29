---
title: PowerShell példaszkript a naplózás és fenyegetésészlelés az Azure SQL Database-ben való konfigurálásához  | Microsoft Docs
description: Azure PowerShell-példaszkript a naplózás és a fenyegetésészlelés konfigurálásához egy Azure SQL Database-adatbázisban
services: sql-database
ms.service: sql-database
ms.subservice: threat-detection
ms.custom: security
ms.devlang: PowerShell
ms.topic: sample
author: giladmit
ms.author: giladm
ms.reviewer: carlrab
manager: craigg
ms.date: 09/07/2018
ms.openlocfilehash: 6942577c2bf6d031ded9ba6af93a2ab592fe9976
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47053809"
---
# <a name="use-powershell-to-configure-sql-database-auditing-and-threat-detection"></a>Naplózás és fenyegetésészlelés konfigurálása egy SQL Database-adatbázisban a PowerShell használatával

Ez a PowerShell-példaszkript az SQL Database naplózását és fenyegetésészlelését konfigurálja. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1?highlight=13-14 "Configure auditing and threat detection")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Olyan logikai kiszolgálót hoz létre, amely egy adatbázist vagy rugalmas készletet üzemeltet. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Létrehoz egy adatbázist egy logikai kiszolgálón egyetlen vagy készletezett adatbázisként. |
| [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Létrehoz egy tárfiókot. |
| [Set-AzureRmSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabaseauditingpolicy) | Beállítja egy adatbázis naplózási szabályzatát. |
| [Set-AzureRmSqlDatabaseThreatDetectionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasethreatdetectionpolicy) | Beállítja egy adatbázis fenyegetésészlelési szabályzatát. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
