---
title: Azure Database Migration Service-példány létrehozása az Azure Portallal | Microsoft Docs
description: Az Azure Portallal létrehozhat egy Azure Database Migration Service-példányt.
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/01/2018
ms.openlocfilehash: 7669ee678f4049c938c6e249c26997d993fd7c6a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772194"
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Az Azure Portallal létrehozhat egy Azure Database Migration Service-példányt.
Ebben a rövid útmutatóban az Azure Portal használatával létrehozhat egy Azure Database Migration Service-példányt.  A szolgáltatás létrehozása után a használatával a helyi SQL Serverből migrálhat adatokat egy Azure SQL-adatbázisba.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Nyissa meg a webböngészőjét, keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/), majd adja meg a hitelesítő adatait a Portalra való bejelentkezéshez.

Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása
A Database Migration Service első példányának létrehozása előtt regisztrálja a Microsoft.DataMigration erőforrás-szolgáltatót.

1. Az Azure Portalon válassza a **Minden szolgáltatás** **Előfizetések** elemét.

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

3. Keressen a „migration” kifejezésre, majd a Microsoft.DataMigration jobb oldalán válassza a **Regisztrálás** elemet.

![Erőforrás-szolgáltató regisztrálása](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>A szolgáltatás egy példányának létrehozása
1. Kattintson az **+ Erőforrás létrehozása** elemre az Azure Database Migration Service egy példányának létrehozásához.

2. A piactéren keressen a „migration” kifejezésre, válassza ki az **Azure Database Migration Service** elemet, majd az **Azure Database Migration Service** képernyőn kattintson a **Létrehozás** parancsra.

3. A **Database Migration Service** képernyőn: 

    - Válasszon ki egy olyan **Szolgáltatásnevet**, amelyet könnyen meg tud jegyezni, és alkalmas az Azure Database Migration Service-példány egyedi azonosítására.
    - Válassza ki azt az **Azure-előfizetést**, amelyben a példányt létre szeretné hozni.
    - Hozzon létre egy új, egyéni névvel rendelkező **Hálózatot**.
    - Válassza ki a forráshoz vagy a célkiszolgálóhoz legközelebb eső **Helyet**.
    - A **Tarifacsomag** esetén válassza ki az Alapszintű: 1 virtuális mag lehetőséget.

    ![A migrálási szolgáltatás létrehozása](media/quickstart-create-data-migration-service-portal/dms-create-service.png)
4. Kattintson a **Létrehozás** gombra.

Pár pillanat múlva létrejön a használatra kész Azure Database Migration Service-példány. A Database Migration Service a következő képen látható módon jelenik meg:

![Létrehozott migrálási szolgáltatás](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A rövid útmutató során létrehozott erőforrásokat az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) törlésével távolíthatja el.  Az erőforráscsoport törléséhez keresse meg a létrehozott Azure Database Migration Service-példányt. Válassza ki az **Erőforráscsoport** nevét, majd válassza az **Erőforráscsoport törlése** elemet.  A művelettel az erőforráscsoport összes elemét és magát a csoportot is törli.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Helyi SQL Server migrálása az Azure SQL Database-be](tutorial-sql-server-to-azure-sql.md)