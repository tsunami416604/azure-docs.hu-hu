---
title: "A rugalmas adatbázis-feladatok eszköz eltávolítása"
description: "A rugalmas feladatok eszköz eltávolítása"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: bfc9d820-edbd-4fca-bfbf-1f339cfcc448
ms.service: sql-database
ms.workload: sql-database
ms.custom: scale out apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: ae7f0bce452a0a86f6f1e4d9b0c93a0fa1727f21
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="uninstall-elastic-database-jobs-components"></a>A rugalmas adatbázis-feladatok összetevőinek eltávolítása
**Rugalmas adatbázis-feladatok** összetevőket is el kell távolítani a portál vagy a PowerShell használatával.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Az Azure portál használatával rugalmas adatbázis-feladatok összetevőinek eltávolítása
1. Nyissa meg az [Azure portált](https://portal.azure.com/).
2. Keresse meg az előfizetés tartalmazó **rugalmas adatbázis-feladatok** összetevők, nevezetesen az előfizetés, mely rugalmas adatbázis feladatok összetevők megtörtént-e.
3. Kattintson a **Tallózás** kattintson **erőforráscsoportok**.
4. Válassza ki a "__ElasticDatabaseJob" nevű erőforráscsoport.
5. Törölje a csoportot.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Távolítsa el a PowerShell használatával rugalmas adatbázis-feladatok összetevői
1. Indítsa el a Microsoft Azure PowerShell-parancsablakot, és keresse meg az eszközök alkönyvtárát Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x mappában lévő: típus **cd eszközök**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > cd-eszközök
2. A PowerShell-parancsfájl.\UninstallElasticDatabaseJobs.ps1 hajtható végre.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > feloldása fájl.\UninstallElasticDatabaseJobs.ps1 PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >. \ UninstallElasticDatabaseJobs.ps1

Vagy egyszerűen, hajtsa végre a következő parancsfájlt, ha az alapértelmezett értékeket, ha az összetevők telepítéséhez használja:

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

## <a name="next-steps"></a>Következő lépések
Telepítse újra a rugalmas feladatok, lásd: [a rugalmas adatbázis feladat szolgáltatás telepítése](sql-database-elastic-jobs-service-installation.md)

A rugalmas feladatok áttekintéséhez lásd: [rugalmas adatbázis-feladatok áttekintése](sql-database-elastic-jobs-overview.md).

<!--Image references-->


