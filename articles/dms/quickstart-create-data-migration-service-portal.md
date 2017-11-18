---
title: "Hozza létre a Azure-adatbázis áttelepítése szolgáltatás példányát az Azure portál használatával |} Microsoft Docs"
description: "Az Azure-adatbázis áttelepítési szolgáltatás példányának létrehozása az Azure-portál használatával"
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/17/2017
ms.openlocfilehash: 9faac0716334d627cdde4c0ef16262670333b5d4
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Az Azure-adatbázis áttelepítési szolgáltatás példányának létrehozása az Azure-portál használatával
A a gyors üzembe helyezési segítségével az Azure-portálon az Azure-adatbázis áttelepítési szolgáltatás egy példányának létrehozásakor.  A szolgáltatás létrehozása után lesz áttelepíti az adatokat a helyszíni SQL Server Azure SQL-adatbázis használatával.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.
Nyissa meg a webböngészőjét, és keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása
A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása, az első adatbázis áttelepítési szolgáltatás létrehozása előtt kell.

1. Válassza ki az Azure-portálon **minden szolgáltatás**, majd válassza ki **előfizetések**.

1. Válassza ki az előfizetést, amelyben az Azure-adatbázis áttelepítési szolgáltatás példányának létrehozása, majd válassza ki a kívánt **erőforrás-szolgáltató**.

1. Keresse meg az áttelepítést, és jobb oldalán Microsoft.DataMigration, válassza **regisztrálása**.

![Erőforrás-szolgáltató regisztrálása](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-azure-database-migration-service"></a>Azure-adatbázis áttelepítése szolgáltatás létrehozása
1. Kattintson a  **+**  új szolgáltatás létrehozása.  Adatbázis-áttelepítés szolgáltatás még előzetes verzió van.  

1. Keresés a piactér "áttelepítés", "Adatbázis áttelepítési szolgáltatás (előzetes verzió)", majd kattintson **létrehozása**.

    ![Áttelepítési szolgáltatás létrehozása](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - Válasszon egy **szolgáltatásnév** megjegyezhető és egyedi azonosításához az Azure-adatbázis áttelepítési szolgáltatáspéldány ez.
    - Válassza ki az Azure **előfizetés** a amely szeretne létrehozni az adatbázis áttelepítési szolgáltatás.
    - Hozzon létre egy új **hálózati** egyedi névvel.
    - Válassza ki a **hely** , hogy a forrás vagy a cél-kiszolgálóhoz legközelebbi.
    - Jelölje be Basic: 1 vCore a a **tarifacsomag**.

1. Kattintson a **Create** (Létrehozás) gombra.

Néhány másodpercen belül az Azure-adatbázis áttelepítése szolgáltatás létrehozott és használatra kész lesz.  Az adatbázis áttelepítési szolgáltatás látni fogja, az ábrán látható módon.

![Áttelepítési szolgáltatás létrehozása](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az erőforrások létrehozott a gyors üzembe helyezés törlésével eltávolíthatja a [Azure erőforráscsoport](../azure-resource-manager/resource-group-overview.md).  Az erőforráscsoport törléséhez keresse meg a létrehozott adatbázis-áttelepítés szolgáltatást, kattintson a a **erőforráscsoport** nevet, és válassza ki **erőforrás csoport törlése**.  Ez a művelet törli az erőforráscsoportot, valamint maga a csoport az eszközök mindegyikét.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Telepítse át az SQL Server helyszíni az Azure SQL Adatbázishoz](tutorial-sql-server-to-azure-sql.md)