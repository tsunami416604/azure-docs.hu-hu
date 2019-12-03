---
title: Folyamatos integráció és üzembe helyezés
description: A nagyvállalati szintű adatbázis-DevOps az Azure-folyamatok használatával történő folyamatos integráció és üzembe helyezés beépített támogatásával SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e8d7e7764a01dbd0169efae093bac4d984982108
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708668"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>A Azure SQL Data Warehouse folyamatos integrációja és üzembe helyezése

Ez az egyszerű oktatóanyag azt ismerteti, hogyan integrálható a SQL Server Adateszköz-(SSDT-) adatbázis-projekt az Azure DevOps, és hogyan használhat Azure-folyamatokat a folyamatos integráció és üzembe helyezés beállításához. Ez az oktatóanyag a folyamatos integrációs és üzembe helyezési folyamat kiépítésének második lépése a SQL Data Warehouse. 

## <a name="before-you-begin"></a>Előzetes teendők

- Átugorja a [verziókövetés integrációs oktatóanyagát](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Az Azure DevOps beállítása és kapcsolódás


## <a name="continuous-integration-with-visual-studio-build"></a>Folyamatos integráció a Visual Studio buildtel

1. Navigáljon az Azure-folyamatokhoz, és hozzon létre egy új Build-folyamatot

      ![Új folyamat](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Új folyamat")

2. Válassza ki a forráskód-tárházat (Azure Repos git), és válassza ki a .NET Desktop alkalmazás sablonját.

      ![Folyamat beállítása](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Folyamat beállítása") 

3. Szerkessze a YAML-fájlt, hogy az ügynök megfelelő készletét használja. A YAML-fájlnak a következőhöz hasonlóan kell kinéznie:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Ezen a ponton olyan egyszerű környezettel rendelkezik, ahol a forrás-felügyeleti tárház főágának beadása automatikusan elindítja az adatbázis-projekt sikeres Visual Studio-buildjét. Ellenőrizze, hogy az automatizálás működik-e, ha módosítja a helyi adatbázis-projektet, és ellenőrzi, hogy a változás a fő ágra esik-e.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Folyamatos üzembe helyezés a Azure SQL Data Warehouse-(vagy adatbázis-) telepítési feladattal

1. Vegyen fel egy új feladatot a [Azure SQL Database központi telepítési feladattal](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) , és töltse ki a szükséges mezőket a cél adattárházhoz való kapcsolódáshoz. A feladat futtatásakor a rendszer az előző összeállítási folyamatból generált DACPAC telepíti a cél adattárházba. Használhatja az [Azure SQL Datawarehouse üzembe helyezési feladatát](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment) is 

      ![Üzembe helyezési feladat](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Üzembe helyezési feladat")

2. Ha önkiszolgáló ügynököt használ, győződjön meg róla, hogy a környezeti változót a megfelelő SqlPackage. exe használatára állítja be SQL Data Warehouse. Az elérési útnak a következőhöz hasonlóan kell kinéznie:

      ![Környezeti változó](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Környezeti változó")

   C:\Program Files (x86) \Microsoft vizuális Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   A folyamat futtatása és ellenőrzése. Helyben végezheti a módosításokat, és ellenőrizheti a verziókövetés módosításait, amelyeknek automatikus buildet és üzembe helyezést kell létrehozniuk.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg [Azure SQL Data Warehouse architektúrával](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- [SQL Data Warehouse gyors létrehozása][create a SQL Data Warehouse]
- [Mintaadatok betöltése][load sample data].
- [Videók](/azure/sql-data-warehouse/sql-data-warehouse-videos) megismerése



<!--Image references-->

[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Feature requests]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
