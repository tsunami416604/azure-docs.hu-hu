---
title: A rugalmas feladatok eszköz eltávolítása
description: Megtudhatja, hogyan távolíthatja el a rugalmas feladatok összetevők PowerShell az Azure portal használatával.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: sstein
ms.openlocfilehash: 395bbf50373d3a6e3848fba9fd3db0d6989023f4
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "35645831"
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
1. Indítsa el a Microsoft Azure PowerShell-parancsablakot, és keresse meg az eszközök pillanatképmappa alatt a Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x mappa: típus **cd eszközökkel**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > cd-eszközökkel
2. Hajtsa végre a.\UninstallElasticDatabaseJobs.ps1 PowerShell-parancsfájlt.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > feloldása fájl.\UninstallElasticDatabaseJobs.ps1 PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >. \ UninstallElasticDatabaseJobs.ps1

Vagy egyszerűen, hajtsa végre a következő parancsfájlt, ha az alapértelmezett értékeket, ha az összetevők telepítésére használt:

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
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>További lépések
Telepítse újra a rugalmas adatbázis-feladatok, lásd: [a rugalmas feladatokat végző szolgáltatás telepítése](sql-database-elastic-jobs-service-installation.md)

Rugalmas adatbázis-feladatok áttekintéséhez lásd: [Elastic Database-feladatok áttekintése](sql-database-elastic-jobs-overview.md).

<!--Image references-->


