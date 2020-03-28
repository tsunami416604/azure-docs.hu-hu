---
title: Példák az Azure PowerShell-parancsfájlra
description: Az Azure PowerShell-példaszkriptek segítenek az Azure SQL-adatbáziskiszolgálók, rugalmas készletek, adatbázisok és tűzfalak létrehozásában és felügyeletében.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: dd16753a9b057e441884b0a6a019701766aaa321
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73821394"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure PowerShell-minták az Azure SQL Database-hez

Az Azure SQL Database lehetővé teszi az adatbázisok, példányok és készletek konfigurálását az Azure PowerShell használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az AZ PowerShell 1.4.0-s vagy újabb szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="single-database-and-elastic-pools"></a>[Egyetlen adatbázis- és rugalmaskészlet](#tab/single-database)

A következő táblázat az Azure SQL Database-hez elérhető Azure PowerShell-példaszkriptekre mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
|**Egyetlen adatbázisok és rugalmas készletek létrehozása és konfigurálása**||
| [Egyetlen adatbázis létrehozása és adatbázis-kiszolgáló tűzfalszabályának konfigurálása](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egyetlen Azure SQL-adatbázist hoz létre, és konfigurál egy kiszolgálószintű tűzfalszabályt. |
| [Rugalmas készletek létrehozása és rugalmas készletbe helyezett adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript rugalmas Azure SQL Database-készleteket hoz létre, áthelyezi a rugalmas készletbe helyezett adatbázisokat, és módosítja a számítási méreteket.|
|**Georeplikáció és feladatátvétel konfigurálása**||
| [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript aktív georeplikációt állít be egyetlen Azure SQL-adatbázishoz, és átadja a feladatait a másodlagos replikának. |
| [Rugalmas készletbe helyezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript aktív georeplikációt állít be egy Azure SQL-adatbázishoz a rugalmas SQL-készletben, és átadja a feladatait a másodlagos replikának. |
|**Feladatátvevő csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása egyetlen adatbázishoz](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl létrehoz egy adatbázist, és egy feladatátvételi csoport, hozzáadja az adatbázist a feladatátvételi csoporthoz, és teszteli a feladatátvételt a másodlagos kiszolgálóhoz. | 
| [Feladatátvételi csoport konfigurálása rugalmas készlethez](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl létrehoz egy adatbázist, hozzáadja azt egy rugalmas készlethez, hozzáadja a rugalmas készletet a feladatátvételi csoporthoz, és teszteli a feladatátvételt a másodlagos kiszolgálóhoz. | 
|**Önálló adatbázis és rugalmas készlet méretezése**||
| [Önálló adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy Azure SQL-adatbázis teljesítmény-mérőszámait monitorozza, méretezi azt egy magasabb számítási mérethez, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon. |
| [Rugalmas készlet méretezése](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy rugalmas Azure SQL Database-készlet teljesítmény-mérőszámait monitorozza, méretezi azt egy magasabb számítási mérethez, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon. |
| **Naplózás és fenyegetésészlelés** |
| [Naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript naplózási és fenyegetésészlelési szabályzatokat konfigurál egy Azure SQL-adatbázishoz. |
| **Adatbázis visszaállítása, másolása és importálása**||
| [Adatbázis visszaállítása](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript visszaállít egy Azure SQL-adatbázist egy georedundáns biztonsági másolatból, és visszaállít egy törölt Azure SQL-adatbázist a legutóbbi biztonsági másolatra. |
| [Adatbázis másolása új kiszolgálóra](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript másolatot készít egy meglévő Azure SQL-adatbázisról egy új Azure SQL Serveren. |
| [Adatbázis importálása BACPAC-fájlból](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript egy adatbázist importál egy Azure SQL Serverre egy BACPAC-fájlból. |
| **Adatok szinkronizálása adatbázisok között**||
| [Adatok szinkronizálása SQL-adatbázisok között](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript több Azure SQL-adatbázis közötti szinkronizáláshoz konfigurálja a Data Syncet. |
| [Adatok helyszíni szinkronizálása az SQL Database és az SQL Server között](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz konfigurálja a Data Syncet. |
| [Az SQL Data Sync szinkronizálási sémájának frissítése](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript adatokat ad hozzá a Data Sync szinkronizálási sémához, vagy eltávolítja belőle azokat. |
|||

További információ az [egyadatbázisos Azure PowerShell API-ról.](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)

## <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

Az alábbi táblázat az Azure PowerShell-parancsfájlok mintájára mutató hivatkozásokat tartalmaz az Azure SQL Database – Felügyelt példány számára.

| |  |
|---|---|
|**Felügyelt példányok létrehozása és konfigurálása**||
| [Felügyelt példány létrehozása és felügyelete](scripts/sql-database-create-configure-managed-instance-powershell.md) | Ez a PowerShell-szkript bemutatja, hogyan hozhat létre és kezelhet felügyelt példányokat az Azure PowerShell használatával. |
| [Felügyelt példány létrehozása és kezelése az Azure Resource Manager-sablon használatával](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl bemutatja, hogyan hozhat létre és kezelhet felügyelt példányt az Azure PowerShell és az Azure Resource Manager sablon használatával.|
| [Adatbázis visszaállítása egy másik földrajzi régió felügyelt példányára](scripts/sql-managed-instance-restore-geo-backup.md) | Ez a PowerShell-parancsfájl egy adatbázis biztonsági mentését készíti, és visszaállítja azt egy másik régióba. Ez az úgynevezett Geo-visszaállítási vész-helyreállítási forgatókönyv. |
| **Transzparens adattitkosítás (TDE) konfigurálása**||
| [Átlátható adattitkosítás kezelése felügyelt példányban saját kulccsal az Azure Key Vaultból](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl az Azure SQL-felügyelt példány saját kulcsának (Hozd a saját kulcsa) forgatókönyvében konfigurálja az átlátszó adattitkosítást (TDE) az Azure Key Vault egyik kulcsának használatával|
|**Feladatátvevő csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása felügyelt példányhoz](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl két felügyelt példányt hoz létre, hozzáadja őket egy feladatátvételi csoporthoz, majd teszteli a feladatátvételt az elsődleges felügyelt példányból a másodlagos felügyelt példányba. | 
|||

További információ az [Azure PowerShell API felügyelt példányáról.](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)

---

## <a name="additional-resources"></a>További források

Az ezen a lapon felsorolt példák az [Azure SQL Database-parancsmagokkal](/powershell/module/az.sql/) hozlétre és kezelhető Azure SQL-erőforrások. A lekérdezések futtatásához és számos adatbázis-feladat végrehajtásához további parancsmagok találhatók az [sqlserver](/powershell/module/sqlserver/) modulban. További információt az [SQL Server PowerShell című témakörben talál.](/sql/powershell/sql-server-powershell/)
