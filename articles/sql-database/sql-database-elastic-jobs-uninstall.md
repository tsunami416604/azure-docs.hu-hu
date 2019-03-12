---
title: A rugalmas feladatok eszköz eltávolítása
description: Megtudhatja, hogyan távolíthatja el a rugalmas feladatok összetevők PowerShell az Azure portal használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 12f923724616378a6ea6c83a947bd5362840a697
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768864"
---
# <a name="uninstall-elastic-database-jobs-components"></a>A rugalmas feladatok összetevőinek eltávolítása


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Elastic Database-feladatok** összetevőket is eltávolíthatók az Azure Portalon vagy a PowerShell használatával.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Távolítsa el a rugalmas adatbázis-feladatok összetevőket az Azure portal használatával
1. Nyissa meg az [Azure Portalt](https://portal.azure.com/).
2. Keresse meg, amely tartalmazza az előfizetés **rugalmas adatbázis-feladatok** összetevők, nevezetesen az előfizetés, mely Elastic Database feladatok összetevőket is telepíteni.
3. Kattintson a **Tallózás** kattintson **erőforráscsoportok**.
4. Válassza ki a "__ElasticDatabaseJob" nevű erőforráscsoportot.
5. Törölje az erőforráscsoportot.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Távolítsa el a rugalmas feladatok összetevők PowerShell-lel
1. Indítsa el a Microsoft Azure PowerShell-parancsablakot, és keresse meg az eszközök pillanatképmappa alatt a Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x mappa: Típus **cd eszközökkel**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > cd-eszközökkel
2. Hajtsa végre a.\UninstallElasticDatabaseJobs.ps1 PowerShell-parancsfájlt.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > feloldása fájl.\UninstallElasticDatabaseJobs.ps1 PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >. \ UninstallElasticDatabaseJobs.ps1

Vagy egyszerűen, hajtsa végre a következő parancsfájlt, ha az alapértelmezett értékeket, ha az összetevők telepítésére használt:

```powershell
$ResourceGroupName = "__ElasticDatabaseJob"
Switch-AzureMode AzureResourceManager

$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
if(!$resourceGroup)
{
     Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
     return
}

Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
Remove-AzureResourceGroup -Name $ResourceGroupName -Force
Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job components are now uninstalled."
```

## <a name="next-steps"></a>További lépések
Telepítse újra a rugalmas adatbázis-feladatok, lásd: [a rugalmas feladatokat végző szolgáltatás telepítése](sql-database-elastic-jobs-service-installation.md)

Rugalmas adatbázis-feladatok áttekintéséhez lásd: [Elastic Database-feladatok áttekintése](sql-database-elastic-jobs-overview.md).
