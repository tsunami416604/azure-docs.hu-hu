---
title: Az Azure SQL Database felügyelt példányain felügyeleti API-referenciája |} A Microsoft Docs
description: Ismerje meg a létrehozása és kezelése az Azure SQL Database felügyelt példányain.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6362084c11ce7aa9078823758700239694162765
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359152"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Az Azure SQL Database felügyelt példányain a felügyelt API-referencia

Hozzon létre, és kezelheti az Azure SQL Database felügyelt példányain a az Azure portal, PowerShell, Azure CLI-vel, REST API-t és a Transact-SQL használatával. Ebben a cikkben található függvények és API-t, amellyel létrehozása és konfigurálása a felügyelt példány áttekintését.

## <a name="azure-portal-create-a-managed-instance"></a>Az Azure Portalon: Felügyelt példány létrehozása

A rövid útmutató bemutatja, hogyan hozzon létre egy Azure SQL Database felügyelt példányába, lásd: [a rövid útmutató: Hozzon létre egy Azure SQL Database felügyelt példány](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Felügyelt példányok létrehozása és kezelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

Hozhat létre és kezelheti a felügyelt példányok az Azure PowerShell-lel, használja a következő PowerShell-parancsmagokat. Ha telepíteni vagy frissíteni a PowerShell, lásd: kell [Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps).

> [!TIP]
> PowerShell-példa szkriptek, lásd: [gyors üzembe helyezési parancsfájlt: Az Azure SQL felügyelt példány használatával a PowerShell-könyvtár létrehozása](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Parancsmag | Leírás |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Létrehoz egy Azure SQL Database felügyelt példány |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Az Azure SQL felügyelt példány adatait adja vissza|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Egy Azure SQL Database felügyelt példánya tulajdonságainak beállítása|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Eltávolít egy Azure SQL felügyelt adatbázis-példány|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Létrehoz egy Azure SQL Database felügyelt példányába database|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Az Azure SQL felügyelt példánya adatbázis adatait adja vissza|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Egy felügyelt Azure SQL Database példány adatbázis eltávolítása|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Visszaállít egy Azure SQL Database Felügyeltpéldány-adatbázist|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: Felügyelt példányok létrehozása és kezelése

Hozhat létre és kezelhet a felügyelt példányok [Azure CLI-vel](/cli/azure), használja a következő [Azure CLI SQL felügyelt példánya](/cli/azure/sql/mi) parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren.

> [!TIP]
> Az Azure CLI-vel a rövid útmutatóban talál [SQL felügyelt példányt az Azure CLI-vel](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Parancsmag | Leírás |
| --- | --- |
|[az sql buszpéldány létrehozása](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Létrehoz egy felügyelt példányt|
|[az sql mi lista](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Listák elérhető felügyelt példányok|
|[az sql buszpéldány show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Részletes információkkal szolgál a felügyelt példányhoz|
|[az sql-buszpéldány frissítés](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Felügyelt példány frissítése|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Eltávolítja a felügyelt példány|
|[az sql KÖZÉP2 létrehozása](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Létrehoz egy felügyelt adatbázis|
|[az sql KÖZÉP2 listája](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Elérhető lista felügyelt adatbázisok|
|[az sql KÖZÉP2 visszaállítása](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Felügyelt adatbázis visszaállítása|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Eltávolít egy felügyelt adatbázis|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Példány adatbázisok létrehozása és kezelése

Hozzon létre és példány adatbázis kezelése a felügyelt példány létrehozása után használja a következő T-SQL-parancsokat. Ezek a parancsok az Azure portal használatával adhat ki [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [A Visual Studio Code](https://code.visualstudio.com/docs), vagy bármely más programot, amely egy Azure SQL Database-kiszolgálóhoz csatlakozni és adja át a Transact-SQL-parancsokat.

> [!TIP]
> További kell konfigurálni, és csatlakozhat egy SQL Server Management Studio használatával a Microsoft Windows, a felügyelt példányhoz bemutató útmutatók: [a rövid útmutató: Konfigurálja az Azure-beli virtuális gép csatlakozni egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-configure-vm.md) és [a rövid útmutató: Pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányába való helyszíni](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Nem hozható létre, vagy törölje a felügyelt példány Transact-SQL használatával.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Új adatbázist hoz létre felügyelt példányt. Új adatbázis létrehozásához a master adatbázishoz internetkapcsolatra.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Módosítja egy Azure SQL felügyelt példánya adatbázis.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: Felügyelt példányok létrehozása és kezelése

Hozhat létre és kezelheti a felügyelt példányok, használja ezeket a REST API-kéréseket.

| Parancs | Leírás |
| --- | --- |
|[Felügyelt példányok – létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Létrehozza vagy frissíti a felügyelt példány.|
|[Felügyelt példányok – törlés](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Felügyelt példány törlése.|
|[Felügyelt példányok – Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Lekérdezi egy felügyelt példányt.|
|[Felügyelt példányok – lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Egy előfizetésben található felügyelt példányok listáját adja vissza.|
|[Felügyelt példányok - lista erőforráscsoport alapján](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Felügyelt példányok listáját adja vissza egy erőforráscsoportban.|
|[Felügyelt példányok – frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Felügyelt példány frissíti.|

## <a name="next-steps"></a>További lépések

- Az SQL Server-adatbázis áttelepítése az Azure-bA kapcsolatos további információkért lásd: [áttelepítése az Azure SQL Database](sql-database-single-database-migrate.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
