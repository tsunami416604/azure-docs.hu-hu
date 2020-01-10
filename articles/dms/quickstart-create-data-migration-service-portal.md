---
title: 'Gyors útmutató: példány létrehozása a Azure Portal használatával'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service példányának létrehozásához használja a Azure Portal.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/08/2020
ms.openlocfilehash: 42638af9f51770a9a5738cd92f91c28439af273a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750099"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Rövid útmutató: Azure Database Migration Service-példány létrehozása az Azure Portalon

Ebben a rövid útmutatóban a Azure Portal használatával hozza létre Azure Database Migration Service példányát.  A szolgáltatás létrehozása után a segítségével áttelepítheti SQL Server a helyszíni adatokból a Azure SQL Databaseba.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőjét, keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/), majd adja meg a hitelesítő adatait a Portalra való bejelentkezéshez.

Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

A Database Migration Service első példányának létrehozása előtt regisztrálja a Microsoft.DataMigration erőforrás-szolgáltatót.

1. Az Azure Portalon válassza a **Minden szolgáltatás** **Előfizetések** elemét.

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók**lehetőséget.

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>A szolgáltatás egy példányának létrehozása

1. Válassza az +**erőforrás létrehozása** lehetőséget Azure Database Migration Service-példány létrehozásához.

2. A piactéren keressen a „migration” kifejezésre, válassza ki az **Azure Database Migration Service** elemet, majd az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** parancsot.

3. A **Migrálási szolgáltatás létrehozása** képernyőn:

    - Válasszon egy emlékezetes és egyedi **szolgáltatásnevet** a Azure Database Migration Service példányának azonosításához.
    - Válassza ki azt az **Azure-előfizetést**, amelyben a példányt létre szeretné hozni.
    - Válasszon ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egy újat.
    - Válassza ki a forráshoz vagy a célkiszolgálóhoz legközelebb eső **Helyet**.
    - Válasszon ki egy meglévő **virtuális hálózatot** , vagy hozzon létre egyet.

        A virtuális hálózat Azure Database Migration Service biztosít a forrás-adatbázis és a célként megadott környezet eléréséhez.

        Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](https://aka.ms/vnet)című cikket.

    - A **Tarifacsomag** esetén válassza ki az Alapszintű: 1 virtuális mag lehetőséget.

        ![A migrálási szolgáltatás létrehozása](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Kattintson a **Létrehozás** gombra.

    Néhány pillanat múlva létrejön az Azure Database Migration Service-példány, és készen áll a használatra. Azure Database Migration Service a következő képen látható módon jelenik meg:

    ![Létrehozott migrálási szolgáltatás](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató során létrehozott erőforrásokat az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) törlésével távolíthatja el. Az erőforráscsoport törléséhez keresse meg a létrehozott Azure Database Migration Service-példányt. Válassza ki az **Erőforráscsoport** nevét, majd válassza az **Erőforráscsoport törlése** elemet. A művelettel az erőforráscsoport összes elemét és magát a csoportot is törli.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Helyi SQL Server migrálása az Azure SQL Database-be](tutorial-sql-server-to-azure-sql.md)