---
title: Az Azure Analysis Services kezelése a PowerShell segítségével | Microsoft dokumentumok
description: Az Azure Analysis Services PowerShell-parancsmagjai olyan gyakori felügyeleti feladatokhoz, mint a kiszolgálók létrehozása, a műveletek felfüggesztése vagy a szolgáltatási szint módosítása.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572695"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Az Azure Analysis Services kezelése a PowerShell-lel

Ez a cikk az Azure Analysis Services-kiszolgáló és az adatbázis-kezelési feladatok végrehajtásához használt PowerShell-parancsmagokat ismerteti. 

Kiszolgálói erőforrás-kezelési feladatok, például kiszolgáló létrehozása vagy törlése, a kiszolgálói műveletek felfüggesztése vagy folytatása, illetve a szolgáltatási szint (szint) módosítása az Azure Analysis Services parancsmagjainak használatával. Az adatbázisok kezeléséhez, például szerepkörtagok hozzáadásához vagy eltávolításához, a feldolgozáshoz vagy a particionáláshoz használt parancsmagok használata ugyanabban az SqlServer modulban, mint az SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Engedélyek

A legtöbb PowerShell-feladat hoz rendszergazdai jogosultságokat a jelenleg kezelendő Analysis Services-kiszolgálón. Az ütemezett PowerShell-feladatok felügyelet nélküli műveletek. Az ütemezőt futtató fióknak vagy szolgáltatásnak rendszergazdai jogosultságokkal kell rendelkeznie az Analysis Services kiszolgálón. 

Az Azure PowerShell-parancsmagokkal végzett kiszolgálói műveletekhez a fióknak vagy a futó ütemezőnek is az erőforrás tulajdonosi szerepköréhez kell tartoznia az [Azure szerepköralapú hozzáférés-vezérlési (RBAC)](../role-based-access-control/overview.md)rendszerben. 

## <a name="resource-and-server-operations"></a>Erőforrás- és kiszolgálóműveletek 

Telepítő modul - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentáció - [Az.AnalysisServices hivatkozás](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Adatbázis-műveletek

Az Azure Analysis Services adatbázis-műveletei ugyanazt az SqlServer-modult használják, mint az SQL Server Analysis Services. Azonban nem minden parancsmag támogatott az Azure Analysis Services számára. 

Az SqlServer modul feladatspecifikus adatbázis-kezelési parancsmagokat, valamint általános célú Meghívás-ASCmd parancsmagamcélt biztosít, amely elfogadja a táblázatos modellparancsfájl-parancsfájl (TMSL) lekérdezést vagy parancsfájlt. Az SqlServer modulban a következő parancsmagok támogatottak az Azure Analysis Services számára.

Telepítő modul - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentáció - [SqlServer referencia](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Támogatott parancsmagok

|Parancsmag|Leírás|
|------------|-----------------| 
|[Bővítmény-szerepkörtag](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Tag hozzáadása adatbázis-szerepkörhöz.| 
|[Biztonsági másolat asdatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Az Analysis Services adatbázisának biztonsági mentése.|  
|[Eltávolítás-szerepkörtag](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Tag eltávolítása adatbázis-szerepkörből.|   
|[Meghívás-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|TMSL-parancsfájl végrehajtása.|
|[ProcessASDatabase meghívása](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Adatbázis feldolgozása.|  
|[Meghívás-processpartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Partíció feldolgozása.| 
|[Meghívás-folyamattábla](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Dolgozzon fel egy táblázatot.|  
|[Egyesítés-partíció](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Partíció egyesítése.|  
|[AsDatabase visszaállítása](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Az Analysis Services adatbázisának visszaállítása.| 
  

## <a name="related-information"></a>Kapcsolódó információk

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [SQL Server PowerShell modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-modul a PowerShell-galériában](https://www.powershellgallery.com/packages/SqlServer)    
* [Táblázatos modellprogramozás az 1200-as és magasabb kompatibilitási szinthez](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
