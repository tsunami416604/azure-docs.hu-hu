---
title: Folyamatos integráció és üzembe helyezés
description: Nagyvállalati szintű adatbázis-DevOps az adattárház-kezelés beépített támogatásával az Azure-folyamatok használatával történő folyamatos integrációhoz és üzembe helyezéshez.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 131811ffd268f001a047a7031170f0723770d24c
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462326"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Az adattárházak folyamatos integrációja és üzembe helyezése

Ez az egyszerű oktatóanyag azt ismerteti, hogyan integrálható a SQL Server Adateszköz-(SSDT-) adatbázis-projekt az Azure DevOps, és hogyan használhat Azure-folyamatokat a folyamatos integráció és üzembe helyezés beállításához. Ez az oktatóanyag a folyamatos integrációs és üzembe helyezési folyamat kiépítésének második lépése az adattárházak esetében.

## <a name="before-you-begin"></a>Előkészületek

- Átugorja a [verziókövetés integrációs oktatóanyagát](sql-data-warehouse-source-control-integration.md)

- Az Azure DevOps beállítása és kapcsolódás

## <a name="continuous-integration-with-visual-studio-build"></a>Folyamatos integráció a Visual Studio buildtel

1. Navigáljon az Azure-folyamatokhoz, és hozzon létre egy új Build-folyamatot.

      ![Új folyamat](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Új folyamat")

2. Válassza ki a forráskód-tárházat (Azure Repos git), és válassza ki a .NET Desktop app-sablont.

      ![Folyamat beállítása](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Folyamat beállítása")

3. Szerkessze a YAML-fájlt, hogy az ügynök megfelelő készletét használja. A YAML-fájlnak a következőhöz hasonlóan kell kinéznie:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Ezen a ponton olyan egyszerű környezettel rendelkezik, ahol a forrás-felügyeleti tárház főágának beadása automatikusan elindítja az adatbázis-projekt sikeres Visual Studio-buildjét. Ellenőrizze, hogy az automatizálás működik-e, ha módosítja a helyi adatbázis-projektet, és ellenőrzi, hogy a változás a fő ágra esik-e.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Folyamatos üzembe helyezés az Azure szinapszis Analytics-(vagy adatbázis-) telepítési feladattal

1. Vegyen fel egy új feladatot a [Azure SQL Database központi telepítési feladattal](/azure/devops/pipelines/targets/azure-sqldb) , és töltse ki a szükséges mezőket a cél adattárházhoz való kapcsolódáshoz. A feladat futtatásakor a rendszer az előző összeállítási folyamatból generált DACPAC telepíti a cél adattárházba. Az [Azure szinapszis Analytics üzembe helyezési feladatát](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)is használhatja.

      ![Üzembe helyezési feladat](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Üzembe helyezési feladat")

2. Ha önkiszolgáló ügynököt használ, győződjön meg róla, hogy a környezeti változót az Azure szinapszis Analytics megfelelő SqlPackage.exe használatára állítja be. Az elérési útnak a következőhöz hasonlóan kell kinéznie:

      ![Környezeti változó](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Környezeti változó")

   C:\Program Files (x86) \Microsoft vizuális Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   A folyamat futtatása és ellenőrzése. Helyben végezheti a módosításokat, és ellenőrizheti a verziókövetés módosításait, amelyeknek automatikus buildet és üzembe helyezést kell létrehozniuk.

## <a name="next-steps"></a>Következő lépések

- A [SZINAPSZIS SQL Pool MPP architektúrájának](massively-parallel-processing-mpp-architecture.md) megismerése
- [SQL-készlet gyors létrehozása](create-data-warehouse-portal.md)
- [Mintaadatok betöltése](load-data-from-azure-blob-storage-using-polybase.md)
- [Videók](sql-data-warehouse-videos.md) megismerése
