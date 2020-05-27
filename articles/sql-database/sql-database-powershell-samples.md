---
title: Példák Azure PowerShell parancsfájlokra
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
ms.openlocfilehash: 630769cb9b73a9cbec09085384557bb57ae9c116
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848991"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure PowerShell-minták az Azure SQL Database-hez

A Azure SQL Database lehetővé teszi az adatbázisok, példányok és készletek konfigurálását a Azure PowerShell használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="single-database-and-elastic-pools"></a>[önálló adatbázis és rugalmas készletek](#tab/single-database)

A következő táblázat az Azure SQL Database-hez elérhető Azure PowerShell-példaszkriptekre mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
|**Önálló adatbázisok és rugalmas készletek létrehozása és konfigurálása**||
| [Egyetlen adatbázis létrehozása és egy adatbázis-kiszolgáló tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egyetlen Azure SQL-adatbázist hoz létre, és konfigurál egy kiszolgálószintű tűzfalszabályt. |
| [Rugalmas készletek létrehozása és rugalmas készletbe helyezett adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript rugalmas Azure SQL Database-készleteket hoz létre, áthelyezi a rugalmas készletbe helyezett adatbázisokat, és módosítja a számítási méreteket.|
|**Georeplikáció és feladatátvétel konfigurálása**||
| [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript aktív georeplikációt állít be egyetlen Azure SQL-adatbázishoz, és átadja a feladatait a másodlagos replikának. |
| [Rugalmas készletbe helyezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript aktív georeplikációt állít be egy Azure SQL-adatbázishoz a rugalmas SQL-készletben, és átadja a feladatait a másodlagos replikának. |
|**Feladatátvételi csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása egyetlen adatbázishoz](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript létrehoz egy adatbázist és egy feladatátvételi csoportot, hozzáadja az adatbázist a feladatátvételi csoporthoz, és teszteli a feladatátvételt a másodlagos kiszolgálóra. | 
| [Feladatátvevő csoport konfigurálása rugalmas készlethez](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript létrehoz egy adatbázist, hozzáadja azt egy rugalmas készlethez, hozzáadja a rugalmas készletet a feladatátvételi csoporthoz, és teszteli a feladatátvételt a másodlagos kiszolgálóra. | 
|**Önálló adatbázis és rugalmas készlet méretezése**||
| [Önálló adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy Azure SQL-adatbázis teljesítmény-mérőszámait monitorozza, méretezi azt egy magasabb számítási mérethez, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon. |
| [Rugalmas készlet méretezése](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy rugalmas Azure SQL Database-készlet teljesítmény-mérőszámait monitorozza, méretezi azt egy magasabb számítási mérethez, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon. |
| **Naplózás és fenyegetésészlelés** |
| [Naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript naplózási és fenyegetésészlelési szabályzatokat konfigurál egy Azure SQL-adatbázishoz. |
| **Adatbázis visszaállítása, másolása és importálása**||
| [Adatbázis visszaállítása](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript visszaállít egy Azure SQL-adatbázist egy georedundáns biztonsági másolatból, és visszaállít egy törölt Azure SQL-adatbázist a legutóbbi biztonsági másolatra. |
| [Adatbázis másolása új kiszolgálóra](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript másolatot készít egy meglévő Azure SQL-adatbázisról egy új Azure SQL Serveren. |
| [Adatbázis importálása bacpac-fájlból](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript egy adatbázist importál egy Azure SQL Serverre egy BACPAC-fájlból. |
| **Adatok szinkronizálása adatbázisok között**||
| [Adatok szinkronizálása SQL-adatbázisok között](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript több Azure SQL-adatbázis közötti szinkronizáláshoz konfigurálja a Data Syncet. |
| [Adatok helyszíni szinkronizálása az SQL Database és az SQL Server között](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz konfigurálja a Data Syncet. |
| [Az SQL Data Sync szinkronizálási sémájának frissítése](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript adatokat ad hozzá a Data Sync szinkronizálási sémához, vagy eltávolítja belőle azokat. |
|||

További információ a [önálló adatbázis Azure POWERSHELL API](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)-ról.

## <a name="managed-instance"></a>[Felügyelt példány](#tab/managed-instance)

A következő táblázat a Azure SQL Database által felügyelt példányokra mutató hivatkozásokat tartalmaz Azure PowerShell parancsfájlokra.

| |  |
|---|---|
|**Felügyelt példányok létrehozása és konfigurálása**||
| [Felügyelt példány létrehozása és felügyelete](scripts/sql-database-create-configure-managed-instance-powershell.md) | Ez a PowerShell-szkript bemutatja, hogyan hozhat létre és kezelhet felügyelt példányokat az Azure PowerShell használatával. |
| [Felügyelt példány létrehozása és kezelése Azure Resource Manager sablon használatával](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript bemutatja, hogyan hozhat létre és kezelhet felügyelt példányokat a Azure PowerShell és a Azure Resource Manager sablonnal.|
| [Adatbázis visszaállítása felügyelt példányra egy másik földrajzi régióban](scripts/sql-managed-instance-restore-geo-backup.md) | Ez a PowerShell-szkript egy adatbázis biztonsági mentését hajtja végre, és egy másik régióba állítja vissza. Ezt nevezzük a vész-helyreállítási katasztrófa-helyreállítási forgatókönyvnek. |
| **Transzparens adattitkosítás konfigurálása (TDE)**||
| [Felügyelt példányon lévő transzparens adattitkosítás kezelése a saját kulcsának használatával Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl transzparens adattitkosítás (TDE) konfigurációját konfigurálja Bring Your Own Key forgatókönyvben az Azure SQL felügyelt példányaihoz, egy kulcs használatával a Azure Key Vault|
|**Feladatátvételi csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása felügyelt példányhoz](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl két felügyelt példányt hoz létre, hozzáadja azokat egy feladatátvételi csoporthoz, majd az elsődleges felügyelt példányról a másodlagos felügyelt példányra ellenőrzi a feladatátvételt. | 
|||

További információ a [felügyelt példány Azure POWERSHELL API](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)-ról.

---

## <a name="additional-resources"></a>További források

Az ezen a lapon felsorolt példák az Azure SQL-erőforrások létrehozására és kezelésére szolgáló [Azure SQL Database-parancsmagokat](/powershell/module/az.sql/) használják. A lekérdezések futtatására szolgáló további parancsmagok és számos adatbázis-feladat végrehajtása a [SQLServer](/powershell/module/sqlserver/) modulban található. További információ: [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
