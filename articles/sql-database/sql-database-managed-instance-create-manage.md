---
title: Hozzon létre, illetve kezelheti az Azure SQL felügyelt példánya |} A Microsoft Docs
description: Ismerje meg a létrehozása és kezelése az Azure SQL Database felügyelt példányain.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 3e714df775d316ceaafe1a0ce9b55c4e986804cd
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856059"
---
# <a name="create-and-manage-azure-sql-database-managed-instances"></a>Létrehozása és kezelése az Azure SQL Database felügyelt példányain

Hozzon létre, és kezelheti az Azure SQL Database felügyelt példányain a az Azure portal, PowerShell, Azure CLI-vel, REST API-t és a Transact-SQL használatával.

## <a name="azure-portal-create-a-managed-instance"></a>Az Azure portal: a felügyelt példány létrehozása

A rövid útmutató bemutatja, hogyan hozzon létre egy Azure SQL Database felügyelt példányába, lásd: [a rövid útmutató: hozzon létre egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-a-managed-instance"></a>PowerShell: Létrehozása és kezelése a felügyelt példány

Létrehozása és kezelése az Azure SQL server, adatbázisok és tűzfalak az Azure PowerShell használatával, a következő PowerShell-parancsmagokat használja. Ha telepíteni vagy frissíteni a PowerShell, lásd: kell [Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps).

> [!TIP]
> PowerShell-példa szkriptek, lásd: [gyors üzembe helyezési szkript: Azure SQL felügyelt példány létrehozása PowerShell-könyvtár használatával](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Parancsmag | Leírás |
| --- | --- |
|[Új AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|Létrehoz egy Azure SQL Database felügyelt példány |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Get-AzureRmSqlInstance)|Az Azure SQL felügyelt példány adatait adja vissza|
|[Set-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlInstance)|Egy Azure SQL Database felügyelt példánya tulajdonságainak beállítása|
|[Remove-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlInstance)|Eltávolít egy Azure SQL felügyelt adatbázis-példány|

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Az Azure CLI: Logikai kiszolgálóiról és adatbázisairól kezelése

Létrehozása és kezelése az Azure SQL server, adatbázisok és tűzfalak az [Azure CLI-vel](/cli/azure), használja a következő [Azure CLI SQL felügyelt példánya](/cli/azure/sql/mi) parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren.

> [!TIP]
> Az Azure CLI-vel a rövid útmutatóban talál [SQL felügyelt példányt az Azure CLI-vel](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql buszpéldány létrehozása](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-create) |Létrehoz egy felügyelt példányt|
|[az sql mi lista](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-list)|Listák elérhető felügyelt példányok|
|[az sql buszpéldány show](/cli/azure/sql/db#az-sql-mi-show)|Részletes információkkal szolgál a felügyelt példányhoz|
|[az sql-buszpéldány frissítés](/cli/azure/sql/db#az-sql-mi-update)|Felügyelt példány frissítése|
|[az sql buszpéldány delete](/cli/azure/sql/db#az-sql-mi-delete)|Eltávolítja a felügyelt példány|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>A Transact-SQL: Logikai kiszolgálóiról és adatbázisairól kezelése

Létrehozása és kezelése az Azure SQL Database felügyelt példányába adatbázis, a felügyelt példány létrehozása után használja a következő T-SQL-parancsokat. Ezek a parancsok az Azure portal használatával adhat ki [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [A Visual Studio Code](https://code.visualstudio.com/docs), vagy bármely más programot, amely egy Azure SQL Database-kiszolgálóhoz csatlakozni és adja át a Transact-SQL-parancsokat.

> [!TIP]
> További kell konfigurálni, és csatlakozhat egy SQL Server Management Studio használatával a Microsoft Windows, a felügyelt példányhoz bemutató útmutatók: [a rövid útmutató: Azure virtuális gép konfigurálása szeretne csatlakozni egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-configure-vm.md) és [ Rövid útmutató: Pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányába való a helyszíni](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Nem hozható létre, vagy törölje a felügyelt példány Transact-SQL használatával.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Új adatbázist hoz létre felügyelt példányt. Új adatbázis létrehozásához a master adatbázishoz internetkapcsolatra.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Módosítja egy Azure SQL felügyelt példánya adatbázis.|

## <a name="rest-api-manage-logical-servers-and-databases"></a>REST API-val: Logikai kiszolgálóiról és adatbázisairól kezelése

Hozhat létre és kezelheti az Azure SQL Database felügyelt példányába, használja ezeket a REST API-kéréseket.

| Parancs | Leírás |
| --- | --- |
|[Felügyelt példányok – létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Létrehozza vagy frissíti a felügyelt példány.|
|[Felügyelt példányok – törlés](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Felügyelt példány törlése.|
|[Felügyelt példányok – Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Lekérdezi egy felügyelt példányt.|
|[Felügyelt példányok – lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Egy előfizetésben található felügyelt példányok listáját adja vissza.|
|[Felügyelt példányok - lista erőforráscsoport alapján](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Felügyelt példányok listáját adja vissza egy erőforráscsoportban.|
|[Felügyelt példányok – frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Felügyelt példány frissíti.|

## <a name="next-steps"></a>További lépések

- Az SQL Server-adatbázis áttelepítése az Azure-bA kapcsolatos további információkért lásd: [áttelepítése az Azure SQL Database](sql-database-cloud-migrate.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
