---
title: A PowerShell segítségével az Azure Analysis Services kezelése |} Microsoft Docs
description: Az Azure Analysis Services kezelése a PowerShell használatával.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b4e819bdce971e92e4b2d99e68f51ddbf8a22182
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597467"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>A PowerShell segítségével az Azure Analysis Services kezelése

Ez a cikk ismerteti az Azure Analysis Services-kiszolgáló és adatbázis-felügyeleti feladatok végrehajtásához használt PowerShell-parancsmagokkal. 

Server kezelési feladatainak például létrehozása kiszolgáló törlése, felfüggesztése vagy folytatása a kiszolgáló műveletek vagy módosítása a szolgáltatási szint (réteg) Azure Resource Manager (erőforrás) parancsmagok és Analysis Services (kiszolgáló)-parancsmagokat használja. Egyéb feladatok hozzáadásával vagy eltávolításával szerepkör tagjai például adatbázisok kezeléséhez, feldolgozás, vagy a particionálás parancsmagokat használja az SQL Server Analysis Services, ugyanazon SQL Server-moduljában található.

## <a name="permissions"></a>Engedélyek
A legtöbb PowerShell feladatok szükséges rendszergazdai jogosultságokkal rendelkezik az Analysis Services-kiszolgáló felügyeli. Felügyelet nélküli műveletek PowerShell ütemezett feladatok, amelyek. Az ütemező futtató fiók vagy szolgáltatás elve az Analysis Services-kiszolgálón rendszergazdai jogosultságokkal kell rendelkeznie. 

A kiszolgáló műveleteket AzureRm-parancsmagok használatával, a fiók vagy a Feladatütemező futtató fiók is szerepkörhöz kell tartoznia a tulajdonos az erőforrás a [átruházásához hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Erőforrás-kezelési műveletek 
A modul - [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Parancsmag|Leírás| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Lekérdezi a server-példány részletes adatait.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Hoz létre egy kiszolgálópéldányt.|   
|[Új AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Létrehoz egy új Analysis Services-tűzfal config.|   
|[Új AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Létrehoz egy új Analysis Services tűzfalszabályt.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Eltávolítja a server-példányt.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|A server-példány folytatja.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Felfüggeszti a server-példányt.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|A server-példány módosítása.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Teszteli a kiszolgálópéldány megléte.| 

## <a name="server-management-operations"></a>Kiszolgáló felügyeleti műveletei

A modul - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Parancsmag|Leírás| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Az Azure Analysis Services-kiszolgáló parancsmag kérelmekhez használandó hitelesített fiókot ad.| 
|[Export-AzureAnalysisServicesInstance]()|Exportálja a napló a jelenleg bejelentkezett környezetben Add-AzureAnalysisServicesAccount parancsban megadott az Analysis Services-kiszolgáló egy példánya|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Analysis Services-kiszolgáló példányának újraindítja az aktuálisan bejelentkezett környezetben; Add-AzureAnalysisServicesAccount parancsban megadott.|  
|[Szinkronizálási-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|A megadott adatbázis az Analysis Services-kiszolgáló az a jelenleg bejelentkezett környezetben Add-AzureAnalysisServicesAccount parancsban megadott összes lekérdezés scaleout példányára megadott példányán szinkronizálása|  

## <a name="database-operations"></a>Adatbázis-műveletek

Az Azure Analysis Services-adatbázis üzemeltetése azonos használja [SqlServer](https://www.powershellgallery.com/packages/SqlServer) SQL Server Analysis Services modul. Azonban nem minden parancsmagok Azure Analysis Services használata támogatott. 

Az SQL Server modul adja meg a tevékenység-specifikus adatbázis parancsmagokat, valamint az általános célú Invoke-ASCmd parancsmag, amely egy táblázatos modell Scripting Language (TMSL) lekérdezés vagy parancsfájl fogadja. Az SQL Server modulban a következő parancsmagok Azure Analysis Services használata támogatott.

  
|Parancsmag|Leírás|
|------------|-----------------| 
|[Adja hozzá RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Tag hozzáadása egy adatbázis-szerepkör.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Analysis Services-adatbázis biztonsági mentése.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Tag eltávolítása egy adatbázis-szerepkör.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Hajtsa végre a TMSL parancsfájlt.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Egy adatbázis feldolgozni.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|A partíció feldolgozásához.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Egy tábla feldolgozni.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Egy partíció egyesíteni.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Analysis Services-adatbázis visszaállítása.| 
  

## <a name="related-information"></a>Kapcsolódó információk

* [SQL-kiszolgáló PowerShell-modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Töltse le a szolgáltatáshoz az SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SQL Server-modul a PowerShell-galériában](https://www.powershellgallery.com/packages/SqlServer)    
* [A táblázatos modell programozási kompatibilitási szintet 1200-as és újabb rendszer](https://msdn.microsoft.com/library/mt712541.aspx)
