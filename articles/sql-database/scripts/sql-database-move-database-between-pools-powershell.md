---
title: "PowerShell – példa-áthelyezési Azure SQL adatbázis-SQL rugalmas készlet |} Microsoft Docs"
description: "Az Azure PowerShell-példa parancsprogram SQL-adatbázis áthelyezése rugalmas készletek PowerShell használatával"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: f6941c3329d8405b56f4c2c9b921a6042ca09aed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-create-elastic-pools-and-move-databases-between-elastic-pools"></a>PowerShell segítségével hozza létre a rugalmas készletek, és adatbázisok áthelyezése rugalmas készletek között

A PowerShell-mintaparancsfájl hoz létre két rugalmas készletek és egy adatbázis egy rugalmas készletből áthelyezi egy másik rugalmas készlet, és majd kimozdul rugalmas készletek egy önálló adatbázis teljesítményszintjét adatbázis. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/sql-database/move-database-between-pools-and-standalone/move-database-between-pools-and-standalone.ps1?highlight=17-18 "Move database between pools")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [Új AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Adatbázis vagy a rugalmas készlet tároló logikai kiszolgáló létrehozása. |
| [Új-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool) | Létrehoz egy logikai kiszolgáló belül rugalmas készlethez. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Adatbázist hoz létre egy logikai egyetlen vagy egy készletezett adatbázis-kiszolgálót. |
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Frissíti az adatbázis-tulajdonságai, vagy azokat, kívüli vagy rugalmas készletek között adatbázis helyezi. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További SQL Database PowerShell parancsfájl minták megtalálhatók a [Azure SQL Database PowerShell-parancsfájlok](../sql-database-powershell-samples.md).
