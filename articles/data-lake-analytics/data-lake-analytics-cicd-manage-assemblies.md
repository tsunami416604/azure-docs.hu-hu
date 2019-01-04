---
title: A CI/CD-folyamat az Azure Data Lake U-SQL-szerelvények kezelésének ajánlott eljárásai
description: U-SQL kezelésével kapcsolatos ajánlott eljárások megismerése C# szerelvények a CI/CD Azure DevOps-folyamat.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0d9192e5ca4dba202ca5287481072bb0f8ae5621
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598519"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>U-SQL-szerelvények a CI/CD-folyamat kezelésére vonatkozó ajánlott eljárások

Ebben a cikkben megismerheti, hogyan kezelheti a U-SQL szerelvény forráskódját az újonnan bevezetett U-SQL-adatbázis projekttel. Azt is megtudhatja, hogyan állíthatja be a folyamatos integráció és készregyártás (CI/CD) folyamatot szerelvényregisztráló az Azure DevOps használatával.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>A U-SQL database-projekt segítségével kezelheti a szerelvény forráskód

[A U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md) egy projekt típusa, amellyel a fejlesztők fejlesztése, kezelése és üzembe helyezése a U-SQL-adatbázisok, gyorsan és könnyedén a Visual studióban. Az összes U-SQL adatbázis-objektumok (kivéve a hitelesítő adatok) az U-SQL-adatbázis projekttel kezelheti. 

Kezelheti a C# szerelvény forráskód és a szerelvény eszközregisztrációs DDL U-SQL szkriptek, használja a:

* U-SQL-adatbázis projekt szerelvény regisztrációs U-SQL-parancsfájlok kezelése.
* Osztálytár (a U-SQL-alkalmazás) kezelése a C# kódjainak és függőségeinek forrása nem felhasználó által definiált operátorok, a functions és a naplózási gyűjtők (udo-k, felhasználói függvényeket és UDAGs).
* U-SQL-adatbázis projekt való hivatkozáshoz a osztálytár projektet. 

A U-SQL database-projekt hivatkozhat egy (U-SQL-alkalmazás) osztálytár projektet. Szerelvények a U-SQL database-ben a hivatkozott regisztrálva hozhat létre C# forráskódját a projekt osztálytár (U-SQL-alkalmazás).

Kövesse az alábbi lépéseket hozható létre, és mutató hivatkozásokat tudjon felvenni.
1. Hozzon létre egy (U-SQL-alkalmazás) osztálytár projektet kiválasztásával **fájl** > **új** > **projekt**. A projekt alatt áll a **az Azure Data Lake > U-SQL** csomópont.

   ![Data Lake Tools for Visual Studio – hozzon létre C# hordozhatóosztálytár-projektjének](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Adja hozzá a felhasználó által definiált C# kódot a osztálytár (U-SQL-alkalmazás) projektben.

1. Hozzon létre egy U-SQL projekt kiválasztásával **fájl** > **új** > **projekt**. A projekt alatt áll a **az Azure Data Lake** > **U-SQL** csomópont.

   ![A Data Lake Tools for Visual Studio – létrehozásához U-SQL database-projekt](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Vegyen fel egy hivatkozást a C# hordozhatóosztálytár-projektjének a U-SQL-adatbázis projekt.

    ![A Data Lake Tools for Visual Studio – hozzáadása U-SQL adatbázis-projekt leírása](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![A Data Lake Tools for Visual Studio – hozzáadása U-SQL adatbázis-projekt leírása](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Egy szerelvény parancsfájl létrehozása a U-SQL database-projektben kattintson a jobb gombbal a projektre, majd válasszon **új elem hozzáadása**.

   ![Data Lake Tools for Visual Studio – szerelvény szkript hozzáadása](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. A szerelvény tervezési nézetben nyissa meg a szerelvény parancsfájlt. Válassza ki a hivatkozott szerelvényt a **szerelvény létrehozása hivatkozás** legördülő menüből.

    ![A Data Lake Tools for Visual Studio – szerelvény létrehozása hivatkozás](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Adjon hozzá **felügyelt függőségek** és **további fájlok**, ha vannak ilyenek. Amikor kiegészítő fájlokat, az eszköz a relatív elérési út használatával győződjön meg arról, hogy megtalálja a szerelvények a helyi gépen, és a build gépen később. 

**@_DeployTempDirectory** a szerkesztőben az ablak alján egy előre definiált változó, amely az eszköz mutat a build kimeneti mappába. A build kimeneti mappában az összes szerelvényét rendelkezik egy almappát a szerelvény neve. Az összes DLL-EK és további fájlokat is ebbe a mappába. 

## <a name="build-a-u-sql-database-project"></a>A U-SQL database-projekt létrehozása

A U-SQL-adatbázis project build kimenete egy U-SQL-adatbázis központi telepítési csomagot. Alkalmazás neve az utótaggal `.usqldbpack`. A `.usqldbpack` csomag egy .zip fájlt, amely tartalmazza az összes DDL-utasítást egy U-SQL parancsfájl a DDL-mappában. Beépített dll-fájlok és a további fájlok szerelvények a Temp mappában találhatók.

## <a name="deploy-a-u-sql-database"></a>U-SQL-adatbázis üzembe helyezése

A `.usqldbpack` csomag is telepíthető egy helyi fiókot vagy egy Azure Data Lake Analytics-fiók. A Visual Studio vagy az üzembe helyezés SDK-t használja. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>A Visual Studióban U-SQL-adatbázis üzembe helyezése

U-SQL-adatbázis egy U-SQL database-projekt segítségével telepíthet, vagy egy `.usqldbpack` csomagot a Visual Studióban.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>A U-SQL database-projekt üzembe helyezése

1.  Kattintson a jobb gombbal a U-SQL database-projektet, és válassza **telepítés**.
2.  Az a **U-SQL-adatbázis telepítése** varázslóban válassza a **ADLA fiók** , amely számára telepíteni kívánja az adatbázist. Helyi fiókok és az ADLA fiókok is támogatottak.
3.  **Adatbázis-forrás** automatikusan kitölti a rendszer. A project build kimeneti mappa a .usqldbpack csomagra mutat.
4.  Adjon meg egy nevet a **adatbázisnév** hozhat létre adatbázist. Ha egy azonos nevű adatbázis már létezik a célkiszolgálón az Azure Data Lake Analytics-fiók, az adatbázis-projektben meghatározott összes objektum jönnek létre az adatbázis újbóli létrehozása nélkül.
5.  A U-SQL-adatbázis üzembe helyezéséhez válassza **küldés**. Minden erőforrások, például szerelvényeket és további fájlok lesznek feltöltve. A U-SQL-feladatot, amely tartalmazza az összes DDL-utasítások elküldve.

    ![A Data Lake Tools for Visual Studio – üzembe helyezése U-SQL database-projekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![A Data Lake Tools for Visual Studio – üzembe helyezése U-SQL adatbázis-projekt varázsló](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Az Azure DevOps U-SQL-adatbázis üzembe helyezése

`PackageDeploymentTool.exe` biztosítja a programozás és parancssori felületek, amelyek segítenek a U-SQL-adatbázisok üzembe helyezéséhez. Az SDK tartalmazza a [U-SQL SDK Nuget-csomagot](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), a következő helyen található `build/runtime/PackageDeploymentTool.exe`.

Az Azure DevOps használhatja a parancssori feladatot, és ez az SDK állíthat be egy automation-folyamat számára a U-SQL-adatbázis frissítése. [További információ az SDK-t és a U-SQL-adatbázis üzembe helyezése a CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics egy CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md)
* [Az Azure Data Lake Analytics tesztelhetnek](data-lake-analytics-cicd-test.md)
* [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
