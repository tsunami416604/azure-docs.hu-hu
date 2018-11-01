---
title: Ajánlott eljárás szerint az CI/CD-folyamat az Azure Data Lake U-SQL-szerelvények kezelése
description: Ismerje meg, az ajánlott eljárás szerint az U-SQL kezelése C# szerelvények a CI/CD Azure DevOps-folyamat.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0f6f581d90b025dd538eb9b79da1a0addd4259f7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634248"
---
# <a name="best-practice-of-managing-u-sql-assemblies-in-cicd-pipeline"></a>Ajánlott eljárás szerint az U-SQL-szerelvények a CI/CD-folyamat kezelése

Ebből a cikkből megismerheti, hogyan kezelheti a U-SQL szerelvény forráskódját az új bevezetett U-SQL-adatbázis projekttel. Ismerje meg a folyamatos integráció és készregyártás (CI/CD) folyamatot az Azure DevOps használatával szerelvényregisztráló beállítása is.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>A U-SQL database-projekt segítségével kezelheti a szerelvény forráskód

[A U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md) egy projekt típusa, amellyel a fejlesztők fejlesztése, kezelése és üzembe helyezése a U-SQL-adatbázisok, gyorsan és könnyedén a Visual studióban. A U-SQL database-projekt összes U-SQL adatbázis-objektum (kivéve a hitelesítő adatok) is felügyelhetők. 

Az ajánlott módszer a kezelésével a C# szerelvény forrás kód és a szerelvény regisztrációs DDL U-SQL szkriptek, hogy:

* Használat **U-SQL Database-projekt** szerelvény regisztrációs U-SQL-parancsfájlok kezelése.
* Használat **osztálytár (U-SQL-alkalmazás)** kezelheti a C# forrás-kód és a függőségek a felhasználó által definiált operátorok, a functions és összesítők adatait (udo-k/UDF/UDAGs).
* A U-SQL database-projekt használatával a hordozhatóosztálytár-projektjének hivatkozhat. 

A U-SQL Database-projekt hivatkozhat egy (U-SQL-alkalmazás) osztálytár projektet. A szerelvények a U-SQL database-ben regisztrált hozható létre a hivatkozott C# forráskódját a projekt osztálytár (U-SQL-alkalmazás).

Az alábbi lépéseket, hozzon létre projekteket és hivatkozások hozzáadását követheti:
1. Hozzon létre egy (U-SQL-alkalmazás) osztálytár projektet keresztül **File > New > Project**. A projekt alatt áll **az Azure Data Lake > U-SQL** csomópont.
   ![Data Lake Tools for Visual Studio – hozzon létre C# hordozhatóosztálytár-projektjének](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
2. Adja hozzá a felhasználó által definiált C# kódot a osztálytár (U-SQL-alkalmazás) projektben. 
3. Hozzon létre egy U-SQL projekt keresztül **fájl > Új > projekt**. A projekt alatt áll **az Azure Data Lake > U-SQL** csomópont.
   ![A Data Lake Tools for Visual Studio – létrehozásához U-SQL database-projekt](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
4. Hivatkozás hozzáadása a C# hordozhatóosztálytár-projektjének a U-SQL-adatbázis projekt.

    ![A Data Lake Tools for Visual Studio – hozzáadása U-SQL adatbázis-projekt leírása](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![A Data Lake Tools for Visual Studio – hozzáadása U-SQL adatbázis-projekt leírása](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)
5. Hozzon létre egy szerelvény parancsprogramot a U-SQL-adatbázis projekt keresztül **kattintson jobb gombbal a Projekt > Új elem hozzáadása**.
   ![Data Lake Tools for Visual Studio – szerelvény szkript hozzáadása](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)
6. Nyissa meg a szerelvény parancsfájlt a szerelvény Tervezés nézetében válassza ki a hivatkozott szerelvényt a **szerelvény létrehozása hivatkozás** legördülő menüből.

    ![A Data Lake Tools for Visual Studio – szerelvény létrehozása hivatkozás](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Adjon hozzá **felügyelt függőségek** és **további fájlok** ha vannak ilyenek. Amikor kiegészítő fájlokat, az eszköz a relatív elérési út használatával győződjön meg arról, hogy megtalálja a szerelvények mind a helyi gépen, és a build gépen később. 

**@_DeployTempDirectory** a szerkesztőben az ablak alján egy előre definiált változó, amely az eszköz mutat a build kimeneti mappába. A build kimeneti mappában az összes szerelvényét rendelkezik egy almappát a szerelvény neve. Az összes DLL-EK és további fájlokat is ebbe a mappába. 

## <a name="build-a-u-sql-database-project"></a>A U-SQL database-projekt létrehozása

A U-SQL database-projekt van egy U-SQL-adatbázis központi telepítési csomagot, az utótag nevű kimeneti build `.usqldbpack`. A `.usqldbpack` csomag egy .zip fájlt, amely tartalmaz egy U-SQL parancsfájl az összes DDL-utasítások a **DDL** mappát, és minden létrehozott DLL-EK és további fájlok szerelvények a **Temp** mappát.

## <a name="deploy-a-u-sql-database"></a>U-SQL-adatbázis üzembe helyezése

A `.usqldbpack` csomag telepíthetők egy helyi fiókot vagy egy Azure Data Lake Analytics-fiókot a Visual Studio vagy az üzembe helyezés SDK használatával. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>A Visual Studióban U-SQL-adatbázis üzembe helyezése

Telepíthet egy U-SQL-adatbázis egy U-SQL-adatbázis projekten keresztül, vagy egy `.usqldbpack` csomagot a Visual Studióban.

#### <a name="deploy-through-a-u-sql-database-project"></a>Keresztül egy U-SQL database-projekt üzembe helyezése

1.  Kattintson a jobb gombbal a U-SQL database-projektet, és válassza **telepítés**.
2.  Az a **U-SQL-adatbázis központi telepítése varázsló**, jelölje be a **ADLA fiók** , amely számára telepíteni kívánja az adatbázist. Helyi fiókok és az ADLA fiókok is támogatottak.
3.  **Adatbázis-forrás** automatikusan, és a .usqldbpack csomagot a projektben pontok kimeneti mappa.
4.  Adjon meg egy nevet a **adatbázisnév** hozhat létre adatbázist. Ha egy azonos nevű adatbázis már létezik a célkiszolgálón az Azure Data Lake Analytics-fiók, az adatbázis-projektben meghatározott összes objektum jönnek létre az adatbázis újbóli létrehozása nélkül.
5.  A U-SQL-adatbázis üzembe helyezéséhez válassza **küldés**. Minden erőforrás (szerelvények és további fájlokat) a rendszer feltölti, és a egy U-SQL-feladatot, amely tartalmazza az összes DDL-utasítások elküldve.

    ![A Data Lake Tools for Visual Studio – üzembe helyezése U-SQL database-projekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![A Data Lake Tools for Visual Studio – üzembe helyezése U-SQL adatbázis-projekt varázsló](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-u-sql-database-in-azure-devops"></a>Az Azure DevOps U-SQL-adatbázis üzembe helyezése

`PackageDeploymentTool.exe` biztosítja a programozás és parancssori felületek, amelyek segítenek a U-SQL-adatbázisok üzembe helyezéséhez. Az SDK tartalmazza a [U-SQL SDK Nuget-csomagot](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), a következő helyen található `build/runtime/PackageDeploymentTool.exe`.

Az Azure DevOps segítségével parancssori feladatot, és ez az SDK a U-SQL-adatbázis frissítése az automation-folyamat beállítása. [További információ az SDK-t és a U-SQL-adatbázis üzembe helyezése a CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Analytics egy CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md)
* [Az Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md)
* [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
