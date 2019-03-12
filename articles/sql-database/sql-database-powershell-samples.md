---
title: Azure PowerShell-példaszkriptek az SQL Database-hez | Microsoft Docs
description: Az Azure PowerShell-példaszkriptek segítenek az Azure SQL Database-kiszolgálók, rugalmas készletek, adatbázisok és tűzfalak létrehozásában és felügyeletében.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/07/2019
ms.openlocfilehash: 90775f007f6d5e97e30cb89b8d4bfbdb88cf34c1
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575937"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure PowerShell-minták az Azure SQL Database-hez

Az Azure SQL Database lehetővé teszi az adatbázisok, a példányok és a készletek Azure PowerShell-lel konfigurálja.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha helyi telepítése és használata a PowerShell, az oktatóanyaghoz AZ PowerShell 1.4.0-s vagy újabb. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="single-database-and-elastic-pools"></a>Önálló adatbázisok és a rugalmas készletek

A következő táblázat az Azure SQL Database-hez elérhető Azure PowerShell-példaszkriptekre mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
|**Önálló adatbázisok és rugalmas készletek létrehozása és konfigurálása**||
| [Önálló adatbázis létrehozása és a egy adatbázis-kiszolgáló tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egyetlen Azure SQL-adatbázist hoz létre, és konfigurál egy kiszolgálószintű tűzfalszabályt. |
| [Rugalmas készletek létrehozása és készletezett adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript rugalmas Azure SQL Database-készleteket hoz létre, áthelyezi a készletezett adatbázisokat, és módosítja a számítási méreteket.|
|**Georeplikáció és feladatátvétel konfigurálása**||
| [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript aktív georeplikációt állít be egyetlen Azure SQL-adatbázishoz, és átadja a feladatait a másodlagos replikának. |
| [Készletezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript aktív georeplikációt állít be egy Azure SQL-adatbázishoz a rugalmas SQL-készletben, és átadja a feladatait a másodlagos replikának. |
| [Önálló adatbázis feladatátvételi csoportjának konfigurálása és feladatainak átvétele](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript feladatátvételi csoportot állít be az Azure SQL Database egy kiszolgálópéldányához, hozzáad egy adatbázist a feladatátvételi csoporthoz, és átadja a feladatait a másodlagos kiszolgálónak |
|**Önálló adatbázis és rugalmas készlet méretezése**||
| [Önálló adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy Azure SQL-adatbázis teljesítmény-mérőszámait monitorozza, méretezi azt egy magasabb számítási mérethez, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon. |
| [Rugalmas készlet méretezése](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy rugalmas Azure SQL Database-készlet teljesítmény-mérőszámait monitorozza, méretezi azt egy magasabb számítási mérethez, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon.  |
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

Tudjon meg többet a [egy adatbázist az Azure PowerShell API](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Felügyelt példány

A következő táblázat az Azure SQL Database – felügyelt példány az Azure PowerShell-példaszkriptekre mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
|**Létrehozása és konfigurálása a felügyelt példány**||
| [Felügyelt példány létrehozása és felügyelete](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/) | Ez a PowerShell-szkript bemutatja, hogyan hozhat létre és kezelhet felügyelt példányokat az Azure PowerShell használatával. |
| [Hozzon létre és kezelheti a felügyelt példányhoz az Azure Resource Manager-sablon](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A PowerShell-példaszkript bemutatja, hogyan hozhat létre és kezelhet a felügyelt példány az Azure PowerShell és az Azure Resource Manager-sablon használatával.|
| **Transzparens adattitkosítás (TDE) konfigurálása**||
| [Transzparens adattitkosítás a felügyelt példány az Azure Key vaultból saját kulcs használata kezelheti](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az Azure SQL felügyelt példány az Azure Key Vaultban lévő kulcsot használ a PowerShell-parancsprogram konfigurálja a Bring Your Own Key-forgatókönyvben transzparens adattitkosítási (TDE)|
|||

Tudjon meg többet a [felügyelt példány az Azure PowerShell API](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).
