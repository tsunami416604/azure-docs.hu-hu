---
title: A PowerShell-lel az Azure Analysis Services kezelése |} A Microsoft Docs
description: Az Azure Analysis Services kezelése a PowerShell segítségével.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 07/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a958c33e173c881a3ad09a49fe9f71ddb0c9df56
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508944"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>A PowerShell-lel az Azure Analysis Services kezelése

Ez a cikk ismerteti az Azure Analysis Services-kiszolgáló és adatbázis-kezelési feladat elvégzéséhez használt PowerShell-parancsmagokat. 

Kiszolgáló erőforrás felügyeleti feladatokat, mint a létrehozásakor vagy törölni a kiszolgálót, felfüggesztése vagy folytatása kiszolgálói műveletek vagy a szolgáltatási szint (réteg) módosítása az Azure Analysis Services-parancsmagokat használják. Az adatbázis-kezelés egyéb feladatok, például hozzáadása vagy eltávolítása a szerepkör tagjai, feldolgozása, illetve a particionálás használata modulok parancsmagjaival együtt az azonos SQL Server, SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Engedélyek

A legtöbb PowerShell-feladatok szükséges rendszergazdai jogosultságokkal rendelkezik a kezelt Analysis Services-kiszolgálón. Felügyelet nélküli műveletek, amelyek ütemezett PowerShell-feladatok. A fiók vagy szolgáltatásnév futtatja az ütemezőt, rendszergazdai jogosultságokkal kell rendelkeznie az Analysis Services-kiszolgálóhoz. 

Kiszolgálói műveletek az Azure PowerShell-parancsmagok használatával, a fiókját vagy az ütemező futtató fiók is szerepkörhöz kell tartoznia a tulajdonos erőforráshoz tartozó [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Erőforrás- és műveletek 

Telepítse a modul - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentációja – [Az.AnalysisServices referencia](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Adatbázis-műveletek

Az Azure Analysis Services adatbázis-műveleteket az SQL Server Analysis Services ugyanazon SqlServer modul használja. Azonban nem minden parancsmagok az Azure Analysis Services használata támogatott. 

Az SqlServer modul biztosít feladatspecifikus adatbázis felügyeleti parancsmagok, valamint az általános célú Invoke-ASCmd parancsmag, amely egy táblázatos modell parancsnyelv (TMSL) lekérdezést vagy parancsfájlt. A következő parancsmagokat az SqlServer modul az Azure Analysis Services használata támogatott.

Telepítse a modul - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentációja – [SqlServer-referencia](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Támogatott parancsmagok

|Parancsmag|Leírás|
|------------|-----------------| 
|[Adjon hozzá RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Egy tag hozzáadása egy adatbázis-szerepkörhöz.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Analysis Services-adatbázis biztonsági mentése.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Tag eltávolítása egy adatbázis-szerepkörhöz.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Hajtsa végre a TMSL-parancsfájlt.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Egy adatbázis feldolgozni.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|A partíció feldolgozásához.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Egy tábla feldolgozása.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Egy partíció egyesíteni.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Analysis Services-adatbázis visszaállítása.| 
  

## <a name="related-information"></a>Kapcsolódó információk

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Az SQL Server PowerShell-modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Töltse le az ssms használatával](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [A PowerShell-galériában SqlServer modul](https://www.powershellgallery.com/packages/SqlServer)    
* [Táblázatos modell programozási kompatibilitási szintje 1200-as és újabb](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
