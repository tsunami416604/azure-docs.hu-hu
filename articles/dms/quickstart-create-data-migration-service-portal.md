---
title: 'Rövid útmutató: Példány létrehozása az Azure Portalhasználatával'
titleSuffix: Azure Database Migration Service
description: Az Azure-portál használatával hozzon létre egy példányt az Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/08/2020
ms.openlocfilehash: efbf7d9db91b8c23151ca4337bd4d58f814a7096
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78254978"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Rövid útmutató: Azure Database Migration Service-példány létrehozása az Azure Portalon

Ebben a gyorsútmutatóban az Azure Portal használatával hozza létre az Azure Database Migration Service egy példányát.  A szolgáltatás létrehozása után használhatja az adatok áttelepítése az SQL Server helyszíni Azure SQL Database.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőjét, keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/), majd adja meg a hitelesítő adatait a Portalra való bejelentkezéshez.

Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

A Database Migration Service első példányának létrehozása előtt regisztrálja a Microsoft.DataMigration erőforrás-szolgáltatót.

1. Az Azure Portalon válassza a **Minden szolgáltatás****Előfizetések** elemét.

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>A szolgáltatás egy példányának létrehozása

1. Válassza a +**Erőforrás létrehozása az** Azure Database Migration Service egy példányának létrehozásához lehetőséget.

2. A piactéren keressen a „migration” kifejezésre, válassza ki az **Azure Database Migration Service** elemet, majd az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** parancsot.

3. A **Migrálási szolgáltatás létrehozása** képernyőn:

    - Válasszon egy **olyan szolgáltatásnevet,** amely emlékezetes és egyedi az Azure Database Migration Service példányának azonosításához.
    - Válassza ki azt az **Azure-előfizetést**, amelyben a példányt létre szeretné hozni.
    - Válasszon ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egy újat.
    - Válassza ki a forráshoz vagy a célkiszolgálóhoz legközelebb eső **Helyet**.
    - Jelöljön ki egy meglévő **virtuális hálózatot,** vagy hozzon létre egyet.

        A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrásadatbázishoz és a célkörnyezethez.

        A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/vnet)

    - A **Tarifacsomag** esetén válassza ki az Alapszintű: 1 virtuális mag lehetőséget.

        ![A migrálási szolgáltatás létrehozása](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Kattintson a **Létrehozás** gombra.

    Néhány pillanat múlva létrejön az Azure Database Migration szolgáltatás példánya, és készen áll a használatra. Az Azure Database Migration Service az alábbi képen látható módon jelenik meg:

    ![Létrehozott migrálási szolgáltatás](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató során létrehozott erőforrásokat az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) törlésével távolíthatja el. Az erőforráscsoport törléséhez keresse meg a létrehozott Azure Database Migration Service-példányt. Válassza ki az **Erőforráscsoport** nevét, majd válassza az **Erőforráscsoport törlése** elemet. A művelettel az erőforráscsoport összes elemét és magát a csoportot is törli.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyi SQL Server migrálása az Azure SQL Database-be](tutorial-sql-server-to-azure-sql.md)