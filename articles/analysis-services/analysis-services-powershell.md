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
ms.openlocfilehash: 9e7683883963db2cf1911405225fcdbf289de2bb
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187538"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>A PowerShell-lel az Azure Analysis Services kezelése

Ez a cikk ismerteti az Azure Analysis Services-kiszolgáló és adatbázis-kezelési feladat elvégzéséhez használt PowerShell-parancsmagokat. 

Server felügyeleti feladatokat, mint a létrehozásakor vagy törölni a kiszolgálót, felfüggesztése vagy folytatása a kiszolgálói műveletek vagy módosítása a szolgáltatási szint (réteg) használata Azure Resource Manager (erőforrás) parancsmagok és az Analysis Services (kiszolgáló) parancsmagok. Az adatbázis-kezelés egyéb feladatok, például hozzáadása vagy eltávolítása a szerepkör tagjai, feldolgozása, illetve a particionálás használata modulok parancsmagjaival együtt az azonos SQL Server, SQL Server Analysis Services.

## <a name="permissions"></a>Engedélyek

A legtöbb PowerShell-feladatok szükséges rendszergazdai jogosultságokkal rendelkezik a kezelt Analysis Services-kiszolgálón. Felügyelet nélküli műveletek, amelyek ütemezett PowerShell-feladatok. A fiók vagy szolgáltatásnév futtatja az ütemezőt, rendszergazdai jogosultságokkal kell rendelkeznie az Analysis Services-kiszolgálóhoz. 

Kiszolgálói műveletek AzureRm-parancsmagok használatával, a fiókját vagy az ütemező futtató fiók is szerepkörhöz kell tartoznia a tulajdonos erőforráshoz tartozó [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Erőforrás-kezelési műveletek 

Modul - [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Parancsmag|Leírás| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Lekérdezi egy server-példány részleteit.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Létrehoz egy kiszolgálópéldányt.|   
|[Új AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Létrehoz egy új Analysis Services tűzfala config.|   
|[Új AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Létrehoz egy Analysis Services új tűzfalszabályt.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Eltávolít egy kiszolgálópéldányt.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Folytatja a kiszolgáló példánya.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Felfüggeszti a kiszolgáló példánya.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Módosítja egy kiszolgálópéldányt.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Teszteli, server-példány létezik-e.| 

## <a name="server-management-operations"></a>Kiszolgálói műveletek

Modul - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Parancsmag|Leírás| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Azure Analysis Services parancsmag kiszolgálókérelmek egy hitelesített fiók hozzáadása.| 
|[Exportálás – AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/export-azureanalysisservicesinstancelog)|A napló exportál egy példányát a jelenleg bejelentkezett környezet az Add-AzureAnalysisServicesAccount parancsban megadott Analysis Services-kiszolgáló|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Újraindítja az Analysis Services-kiszolgáló példányát az aktuálisan bejelentkezett környezetben; Add-AzureAnalysisServicesAccount parancsban megadott.|  
|[Szinkronizálási-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|A megadott Analysis Services-kiszolgálón lévő a jelenleg bejelentkezett a környezetben az Add-AzureAnalysisServicesAccount parancsban megadott összes lekérdezés horizontális felskálázási példányok-példány a megadott adatbázis szinkronizálása|  

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
* [Táblázatos modell programozási kompatibilitási szintje 1200-as és újabb](https://msdn.microsoft.com/library/mt712541.aspx)
