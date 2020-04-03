---
title: Folyamatos integráció és üzembe helyezés
description: Nagyvállalati szintű Database DevOps-élmény adattárházhoz, beépített támogatással az Azure Pipelines használatával történő folyamatos integrációhoz és üzembe helyezéshez.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 39501cef3bb2f7e4a0e061968520f687cf97ecc5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584204"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Folyamatos integráció és üzembe helyezés az adattárházhoz

Ez az egyszerű oktatóanyag bemutatja, hogyan integrálhatja az SQL Server Data tools (SSDT) adatbázis-projektjét az Azure DevOps-szal, és hogyan használhatja ki az Azure Pipelines-t a folyamatos integráció és üzembe helyezés beállításához. Ez az oktatóanyag a második lépés a folyamatos integrációs és üzembe helyezési folyamat létrehozásában az adattárház. 

## <a name="before-you-begin"></a>Előkészületek

- A [forrásvezérlés integrációs oktatóanyagának végigmenni](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Az Azure DevOps beállítása és csatlakozás


## <a name="continuous-integration-with-visual-studio-build"></a>Folyamatos integráció a Visual Studio buildelésével

1. Keresse meg az Azure-folyamatokat, és hozzon létre egy új buildfolyamatot.

      ![Új folyamat](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Új folyamat")

2. Válassza ki a forráskód-tárházat (Azure Repos Git), és válassza ki a .NET Asztali alkalmazássablont.

      ![Csővezeték beállítása](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Csővezeték beállítása") 

3. Szerkessze a YAML-fájlt az ügynök megfelelő készletének használatához. A YAML-fájlnak valahogy így kell kinéznie:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Ezen a ponton van egy egyszerű környezet, ahol minden beadás a forrásvezérlő tárház főága automatikusan elindítja a sikeres Visual Studio-build az adatbázis-projekt. Ellenőrizze, hogy az automatizálás a helyi adatbázis-projekt módosításával és a főág módosításával működik.Validate the automation is working end by making a change in your local database project and checking in that change to your master branch.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Folyamatos üzembe helyezés az Azure SQL Data Warehouse (vagy Database) telepítési feladattal

1. Adjon hozzá egy új feladatot az [Azure SQL Database telepítési feladatával,](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) és töltse ki a szükséges mezőket a céladattártárházhoz való csatlakozáshoz. A feladat futtatásakor az előző buildfolyamatból létrehozott DACPAC a céladattár-raktárba lesz telepítve. Használhatja az [Azure SQL Data Warehouse telepítési feladatot](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)is. 

      ![Telepítési feladat](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Telepítési feladat")

2. Ha saját üzemeltetésű ügynököt használ, győződjön meg arról, hogy a környezeti változót a megfelelő SqlPackage.exe sql data warehouse-hoz való használatára állította be. Az elérési útnak valahogy így kell kinéznie:

      ![Környezeti változó](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Környezeti változó")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Futtassa és érvényesítse a folyamatot. Helyileg módosíthatja a módosításokat, és beadhat olyan módosításokat a forrásvezérlőn, amelyek automatikus létrehozást és telepítést generálnak.

## <a name="next-steps"></a>További lépések

- Fedezze fel [a Synapse SQL pool MPP architektúráját](massively-parallel-processing-mpp-architecture.md)
- [SQL-készlet gyors létrehozása](create-data-warehouse-portal.md)
- [Mintaadatok betöltése](load-data-from-azure-blob-storage-using-polybase.md)
- [Videók felfedezése](/azure/sql-data-warehouse/sql-data-warehouse-videos)
