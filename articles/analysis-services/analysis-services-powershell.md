---
title: A PowerShell-lel az Azure Analysis Services kezelése |} A Microsoft Docs
description: Az Azure Analysis Services kezelése a PowerShell segítségével.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1f9c30f1c914f6c8d42967e014d967ba0d5b85cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893843"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>A PowerShell-lel az Azure Analysis Services kezelése

Ez a cikk ismerteti az Azure Analysis Services-kiszolgáló és adatbázis-kezelési feladat elvégzéséhez használt PowerShell-parancsmagokat. 

Server felügyeleti feladatokat, mint a létrehozásakor vagy törölni a kiszolgálót, felfüggesztése vagy folytatása a kiszolgálói műveletek vagy módosítása a szolgáltatási szint (réteg) használata Azure Resource Manager (erőforrás) parancsmagok és az Analysis Services (kiszolgáló) parancsmagok. Az adatbázis-kezelés egyéb feladatok, például hozzáadása vagy eltávolítása a szerepkör tagjai, feldolgozása, illetve a particionálás használata modulok parancsmagjaival együtt az azonos SQL Server, SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Engedélyek

A legtöbb PowerShell-feladatok szükséges rendszergazdai jogosultságokkal rendelkezik a kezelt Analysis Services-kiszolgálón. Felügyelet nélküli műveletek, amelyek ütemezett PowerShell-feladatok. A fiók vagy szolgáltatásnév futtatja az ütemezőt, rendszergazdai jogosultságokkal kell rendelkeznie az Analysis Services-kiszolgálóhoz. 

Kiszolgálói műveletek az Azure PowerShell-parancsmagok használatával, a fiókját vagy az ütemező futtató fiók is szerepkörhöz kell tartoznia a tulajdonos erőforráshoz tartozó [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Erőforrás-kezelési műveletek 

Modul - [Az.AnalysisServices](/powershell/module/az.analysisservices)

|Parancsmag|Leírás| 
|------------|-----------------| 
|[Get-AzAnalysisServicesServer](/powershell/module/az.analysisservices/get-azanalysisservicesserver)|Lekérdezi egy server-példány részleteit.|  
|[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)|Létrehoz egy kiszolgálópéldányt.|   
|[New-AzAnalysisServicesFirewallConfig](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallconfig)|Létrehoz egy új Analysis Services tűzfala config.|   
|[New-AzAnalysisServicesFirewallRule](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallrule)|Létrehoz egy Analysis Services új tűzfalszabályt.|   
|[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/remove-azanalysisservicesserver)|Eltávolít egy kiszolgálópéldányt.|  
|[Resume-AzAnalysisServicesServer](/powershell/module/az.analysisservices/resume-azanalysisservicesserver)|Folytatja a kiszolgáló példánya.|  
|[Suspend-AzAnalysisServicesServer](/powershell/module/az.analysisservices/suspend-azanalysisservicesserver)|Felfüggeszti a kiszolgáló példánya.| 
|[Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)|Módosítja egy kiszolgálópéldányt.|   
|[Test-AzAnalysisServicesServer](/powershell/module/az.analysisservices/test-azanalysisservicesserver)|Teszteli, server-példány létezik-e.| 

## <a name="server-management-operations"></a>Kiszolgálói műveletek

Modul - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Parancsmag|Leírás| 
|------------|-----------------| 
|[Add-AzAnalysisServicesAccount](/powershell/module/az.analysisservices/add-AzAnalysisServicesaccount)|Azure Analysis Services parancsmag kiszolgálókérelmek egy hitelesített fiók hozzáadása.| 
|[Export-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/export-AzAnalysisServicesinstancelog)|A napló exportál egy példányát a jelenleg bejelentkezett környezet az Add-AzAnalysisServicesAccount parancsban megadott Analysis Services-kiszolgáló|  
|[Restart-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Újraindítja az Analysis Services-kiszolgáló példányát az aktuálisan bejelentkezett környezetben; Add-AzAnalysisServicesAccount parancsban megadott.|  
|[Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|A megadott Analysis Services-kiszolgálón lévő a jelenleg bejelentkezett a környezetben az Add-AzAnalysisServicesAccount parancsban megadott összes lekérdezés horizontális felskálázási példányok-példány a megadott adatbázis szinkronizálása|  

## <a name="database-operations"></a>Adatbázis-műveletek

Az Azure Analysis Services adatbázis-műveletek használata azonos [SqlServer modul](https://www.powershellgallery.com/packages/SqlServer) , SQL Server Analysis Services. Azonban nem minden parancsmagok az Azure Analysis Services használata támogatott. További tudnivalókért lásd: [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

Az SqlServer modul biztosít feladatspecifikus adatbázis felügyeleti parancsmagok, valamint az általános célú Invoke-ASCmd parancsmag, amely egy táblázatos modell parancsnyelv (TMSL) lekérdezést vagy parancsfájlt. A következő parancsmagokat az SqlServer modul az Azure Analysis Services használata támogatott.

  
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

* [Az SQL Server PowerShell-modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Töltse le az ssms használatával](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [A PowerShell-galériában SqlServer modul](https://www.powershellgallery.com/packages/SqlServer)    
* [Táblázatos modell programozási kompatibilitási szintje 1200-as és újabb](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
