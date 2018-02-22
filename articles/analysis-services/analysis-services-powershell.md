---
title: "A PowerShell segítségével az Azure Analysis Services kezelése |} Microsoft Docs"
description: "Az Azure Analysis Services kezelése a PowerShell használatával."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 0e154c7789b38843cd3784daa8cf60d078bdc546
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="manage-azure-analysis-services-with-powershell"></a>A PowerShell segítségével az Azure Analysis Services kezelése

Ez a cikk ismerteti az Azure Analysis Services-kiszolgáló és adatbázis-felügyeleti feladatok végrehajtásához használt PowerShell-parancsmagokkal. 

Server kezelési feladatainak létrehozása kiszolgáló törlése, felfüggesztése vagy folytatása a kiszolgáló műveletek vagy módosítása a szolgáltatási szint (réteg) például az Azure Resource Manager (AzureRM) parancsmagok használata. Egyéb feladatok hozzáadásával vagy eltávolításával szerepkör tagjai például adatbázisok kezeléséhez, feldolgozás, vagy a particionálás parancsmagokat használja az SQL Server Analysis Services, ugyanazon SQL Server-moduljában található.

## <a name="permissions"></a>Engedélyek
A legtöbb PowerShell feladatok szükséges rendszergazdai jogosultságokkal rendelkezik az Analysis Services-kiszolgáló felügyeli. Felügyelet nélküli műveletek PowerShell ütemezett feladatok, amelyek. Az ütemező futtatásához használt fiók rendszergazdai jogosultságokkal kell rendelkeznie az Analysis Services-kiszolgálóhoz. 

A kiszolgáló műveleteket AzureRm-parancsmagok használatával, a fiók vagy a Feladatütemező futtató fiók is szerepkörhöz kell tartoznia a tulajdonos az erőforrás a [átruházásához hozzáférés-vezérlés (RBAC)](../active-directory/role-based-access-control-what-is.md). 

## <a name="server-operations"></a>Kiszolgáló műveletei 
Az Azure Analysis Services-parancsmagok szerepelnek a [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) összetevő modul. AzureRM parancsmag-modulok telepítése, lásd: [Azure Resource Manager parancsmagjainak](/powershell/azure/overview) a PowerShell-galériában.

|Parancsmag|Leírás| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azurerm.analysisservices/add-azureanalysisservicesaccount)|Az Azure Analysis Services-kiszolgáló parancsmag kérelmekhez használandó hitelesített fiókot ad.| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Lekérdezi a server-példány részletes adatait.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Hoz létre egy kiszolgálópéldányt.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Eltávolítja a server-példányt.|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Analysis Services-kiszolgáló példányának újraindítja az aktuálisan bejelentkezett környezetben; Add-AzureAnalysisServicesAccount parancsban megadott.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|A server-példány folytatja.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Felfüggeszti a server-példányt.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|A server-példány módosítása.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Teszteli a kiszolgálópéldány megléte.| 

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
