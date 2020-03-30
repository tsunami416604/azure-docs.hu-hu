---
title: Felügyeleti API-hivatkozás felügyelt példányhoz
description: Ismerje meg az Azure SQL Database felügyelt példányainak létrehozását és kezelését.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268859"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Felügyelt API-hivatkozás az Azure SQL Database felügyelt példányaihoz

Az Azure Portal, a PowerShell, az Azure CLI, a REST API és a Transact-SQL használatával azure SQL-adatbázis által felügyelt példányokat hozhat létre és kezelhet. Ebben a cikkben áttekintést talál a függvények és API- létrehozásához és konfigurálásához kezelhető függvények ről.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Felügyelt példány létrehozása

Az Azure SQL Database Felügyelt példány létrehozásáról a [rövid útmutató: Azure SQL Database Felügyelt példány létrehozása című](sql-database-managed-instance-get-started.md)témakörben ismer.

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Felügyelt példányok létrehozása és kezelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

Felügyelt példányok létrehozásához és kezeléséhez az Azure PowerShell használatával a következő PowerShell-parancsmagokkal. Ha telepítenie vagy frissítenie kell a PowerShellt, olvassa [el az Azure PowerShell-modul telepítése című témakört.](/powershell/azure/install-az-ps)

> [!TIP]
> A PowerShell-példaparancsfájlok, lásd: [Rövid indítási parancsfájl: Hozzon létre Azure SQL felügyelt példány tantár használatával.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/)

| Parancsmag | Leírás |
| --- | --- |
|[Új-Azsqlinstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Létrehoz egy Azure SQL Database felügyelt példányt |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Információt ad vissza az Azure SQL felügyelt példányáról|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Azure SQL-adatbázis felügyelt példányának tulajdonságainak beállítása|
|[Eltávolítás-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Eltávolítja az Azure SQL felügyelt adatbázispéldányát|
|[Új-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Létrehoz egy Azure SQL Database felügyelt példány adatbázist|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Információt ad vissza az Azure SQL felügyelt példány adatbázisáról|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Eltávolítja az Azure SQL felügyelt adatbázispéldány-adatbázist|
|[Visszaállítás-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Az Azure SQL felügyelt adatbázispéldány-adatbázisának visszaállítása|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: Felügyelt példányok létrehozása és kezelése

Felügyelt példányok létrehozásához és kezeléséhez az [Azure CLI](/cli/azure)használatával használja a következő [Azure CLI SQL felügyelt példány](/cli/azure/sql/mi) parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren.

> [!TIP]
> Az Azure CLI rövid útmutatója: [Az SQL felügyelt példány használata az Azure CLI használatával](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)című témakörben.

| Parancsmag | Leírás |
| --- | --- |
|[az sql mi létrehozása](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Felügyelt példány létrehozása|
|[az sql mi lista](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Elérhető felügyelt példányok listázása|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Felügyelt példány részleteinek beszereznie|
|[az sql mi frissítés](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Felügyelt példány frissítése|
|[az sql mi törlés](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Felügyelt példány eltávolítása|
|[az sql midb létrehozása](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Felügyelt adatbázis létrehozása|
|[az sql midb lista](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Az elérhető felügyelt adatbázisok listázása|
|[az sql midb visszaállítás](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Felügyelt adatbázis visszaállítása|
|[az sql midb törlés](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Felügyelt adatbázis eltávolítása|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Példányadatbázisok létrehozása és kezelése

A felügyelt példány létrehozása után a példányadatbázis létrehozásához és kezeléséhez használja a következő T-SQL parancsokat. Ezeket a parancsokat az Azure Portal, az [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), az Azure Data [Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)segítségével adhatja ki. [Visual Studio Code](https://code.visualstudio.com/docs), vagy bármely más program, amely képes csatlakozni egy Azure SQL Database-kiszolgálóhoz, és átad Transact-SQL parancsokat.

> [!TIP]
> A microsoftos SQL Server Management Studio használatával felügyelt példánykonfigurálását és az ahhoz való csatlakozást bemutató rövid útmutató: [Az Azure VM konfigurálása azure SQL Database felügyelt példányhoz való csatlakozáshoz](sql-database-managed-instance-configure-vm.md) és [a gyorsútmutatóban: Pont-hely kapcsolat konfigurálása egy Azure SQL Database Felügyelt példányhoz a helyszíni környezetből.](sql-database-managed-instance-configure-p2s.md)
> [!IMPORTANT]
> A Transact-SQL használatával nem hozható létre és nem törölhető felügyelt példány.

| Parancs | Leírás |
| --- | --- |
|[ADATBÁZIS LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Új felügyelt példány-adatbázislétrehozása. Új adatbázis létrehozásához kapcsolódnia kell a fő adatbázishoz.|
| [ADATBÁZIS MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Módosítja az Azure SQL felügyelt példány adatbázis.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: Felügyelt példányok létrehozása és kezelése

Felügyelt példányok létrehozásához és kezeléséhez használja ezeket a REST API-kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Felügyelt példányok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Felügyelt példánylétrehozása vagy frissítése.|
|[Kezelt példányok – Törlés](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Felügyelt példány törlése.|
|[Felügyelt példányok – Bekés](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Felügyelt példány beszerzése.|
|[Felügyelt példányok – Lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Egy előfizetésben lévő felügyelt példányok listáját adja vissza.|
|[Felügyelt példányok – Lista erőforráscsoport szerint](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Egy erőforráscsoport felügyelt példányainak listáját adja vissza.|
|[Felügyelt példányok – frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Egy felügyelt példány frissítése.|

## <a name="next-steps"></a>További lépések

- Az SQL Server-adatbázisok Azure-ba való áttelepítéséről az [Áttelepítés az Azure SQL-adatbázisba](sql-database-single-database-migrate.md)című témakörben olvashat.
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
