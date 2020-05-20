---
title: Azure Analysis Services kezelése a PowerShell használatával | Microsoft Docs
description: Ismerteti Azure Analysis Services PowerShell-parancsmagokat a gyakori felügyeleti feladatokhoz, például a kiszolgálók létrehozásához, a műveletek felfüggesztéséhez vagy a szolgáltatási szint módosításához.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dfa9de94665c0ee98d0252be6de807f18e39b087
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697978"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Az Azure Analysis Services kezelése a PowerShell-lel

Ez a cikk a Azure Analysis Services kiszolgáló-és adatbázis-felügyeleti feladatainak végrehajtásához használt PowerShell-parancsmagokat ismerteti. 

A kiszolgáló erőforrás-kezelési feladatai, például a kiszolgálók létrehozása vagy törlése, a kiszolgálói műveletek felfüggesztése vagy folytatása, vagy a szolgáltatási szint (szint) módosítása Azure Analysis Services parancsmagokat használ. Egyéb feladatok olyan adatbázisok kezeléséhez, mint például a szerepkörök tagjainak hozzáadása vagy eltávolítása, a feldolgozás vagy a particionálási parancsmagok, amelyek ugyanabban a SqlServer-modulban szerepelnek, mint a SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Engedélyek

A legtöbb PowerShell-feladat megköveteli, hogy rendszergazdai jogosultságokkal rendelkezzen a kezelt Analysis Services-kiszolgálón. Az ütemezett PowerShell-feladatok felügyelet nélküli műveletek. Az ütemező szolgáltatást futtató fióknak vagy szolgáltatásnak rendszergazdai jogosultságokkal kell rendelkeznie a Analysis Services kiszolgálón. 

Azure PowerShell parancsmagokat használó kiszolgálói műveletek esetén a fióknak vagy a Feladatütemezőt futtató fióknak az erőforrás tulajdonosi szerepköréhez is tartoznia kell az [Azure szerepköralapú Access Control (RBAC)](../role-based-access-control/overview.md)szolgáltatásban. 

## <a name="resource-and-server-operations"></a>Erőforrás-és kiszolgálói műveletek 

Modul telepítése – [az. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentáció – [az. AnalysisServices-hivatkozás](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Adatbázis-műveletek

Azure Analysis Services adatbázis-műveletek ugyanazt a SqlServer modult használják, mint SQL Server Analysis Services. Azonban nem minden parancsmag támogatott a Azure Analysis Services. 

A SqlServer modul a feladat-specifikus adatbázis-kezelési parancsmagokat, valamint az általános célú meghívó-ASCmd parancsmagot is tartalmazza, amely a táblázatos modell parancsnyelv (TMSL) lekérdezését vagy parancsfájlját fogadja el. Azure Analysis Services a következő parancsmagok támogatottak a SqlServer modulban.

Modul telepítése – [SQLServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentáció – [SQLServer-hivatkozás](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Támogatott parancsmagok

|Parancsmag|Description|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Vegyen fel egy tagot egy adatbázis-szerepkörbe.| 
|[Backup – ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Analysis Services adatbázis biztonsági mentése.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Egy tag eltávolítása egy adatbázis-szerepkörből.|   
|[Meghívás – ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|TMSL-szkript végrehajtása.|
|[Meghívás – ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Adatbázis feldolgozása|  
|[Meghívás – ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Partíció feldolgozása.| 
|[Meghívás – ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Tábla feldolgozása|  
|[Egyesítés – partíció](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Partíció egyesítése.|  
|[Visszaállítás – ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Analysis Services adatbázis visszaállítása.| 
  

## <a name="related-information"></a>Kapcsolódó információk

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [SQL Server PowerShell-modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer modul a PowerShell-galéria](https://www.powershellgallery.com/packages/SqlServer)    
* [Táblázatos modellek programozása a 1200-es és magasabb kompatibilitási szinthez](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
