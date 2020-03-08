---
title: Folyamatos integráció és üzembe helyezés
description: Nagyvállalati szintű adatbázis-DevOps az adattárház-kezelés beépített támogatásával az Azure-folyamatok használatával történő folyamatos integrációhoz és üzembe helyezéshez.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: how-to
ms.subservice: integration
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a72ca29c2386035adaa6ebd58c4177f0f4e8ba11
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673599"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Az adattárházak folyamatos integrációja és üzembe helyezése

Ez az egyszerű oktatóanyag azt ismerteti, hogyan integrálható a SQL Server Adateszköz-(SSDT-) adatbázis-projekt az Azure DevOps, és hogyan használhat Azure-folyamatokat a folyamatos integráció és üzembe helyezés beállításához. Ez az oktatóanyag a folyamatos integrációs és üzembe helyezési folyamat kiépítésének második lépése az adattárházak esetében. 

## <a name="before-you-begin"></a>Előkészületek

- Átugorja a [verziókövetés integrációs oktatóanyagát](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Az Azure DevOps beállítása és kapcsolódás


## <a name="continuous-integration-with-visual-studio-build"></a>Folyamatos integráció a Visual Studio buildtel

1. Navigáljon az Azure-folyamatokhoz, és hozzon létre egy új Build-folyamatot.

      ![Új folyamat](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Új folyamat")

2. Válassza ki a forráskód-tárházat (Azure Repos git), és válassza ki a .NET Desktop app-sablont.

      ![Folyamat beállítása](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Folyamat beállítása") 

3. Szerkessze a YAML-fájlt, hogy az ügynök megfelelő készletét használja. A YAML-fájlnak a következőhöz hasonlóan kell kinéznie:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Ezen a ponton olyan egyszerű környezettel rendelkezik, ahol a forrás-felügyeleti tárház főágának beadása automatikusan elindítja az adatbázis-projekt sikeres Visual Studio-buildjét. Ellenőrizze, hogy az automatizálás működik-e, ha módosítja a helyi adatbázis-projektet, és ellenőrzi, hogy a változás a fő ágra esik-e.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Folyamatos üzembe helyezés a Azure SQL Data Warehouse-(vagy adatbázis-) telepítési feladattal

1. Vegyen fel egy új feladatot a [Azure SQL Database központi telepítési feladattal](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) , és töltse ki a szükséges mezőket a cél adattárházhoz való kapcsolódáshoz. A feladat futtatásakor a rendszer az előző összeállítási folyamatból generált DACPAC telepíti a cél adattárházba. Használhatja a [Azure SQL Data Warehouse központi telepítési feladatot](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)is. 

      ![Üzembe helyezési feladat](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Üzembe helyezési feladat")

2. Ha önkiszolgáló ügynököt használ, győződjön meg róla, hogy a környezeti változót a megfelelő SqlPackage. exe használatára állítja be SQL Data Warehouse. Az elérési útnak a következőhöz hasonlóan kell kinéznie:

      ![Környezeti változó](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Környezeti változó")

   C:\Program Files (x86) \Microsoft vizuális Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   A folyamat futtatása és ellenőrzése. Helyben végezheti a módosításokat, és ellenőrizheti a verziókövetés módosításait, amelyeknek automatikus buildet és üzembe helyezést kell létrehozniuk.

## <a name="next-steps"></a>További lépések

- Ismerkedés az [SQL Analytics MPP architektúrával](massively-parallel-processing-mpp-architecture.md)
- [SQL-készlet gyors létrehozása](create-data-warehouse-portal.md)
- [Mintaadatok betöltése](sql-data-warehouse-load-sample-databases.md)
- [Videók](/azure/sql-data-warehouse/sql-data-warehouse-videos) megismerése
