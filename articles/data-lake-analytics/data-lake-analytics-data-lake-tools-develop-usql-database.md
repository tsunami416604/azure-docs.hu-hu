---
title: A U-SQL database-projekt használata az Azure Data Lake U-SQL adatbázis fejlesztéséhez
description: Ismerje meg, hogyan hozhat létre a U-SQL-adatbázis, Azure Data Lake Tools for Visual Studio használatával.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 1982f974acd1e9d5e8cae91201f6503eb2ae678a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898797"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>A U-SQL database-projekt használata az Azure Data Lake U-SQL adatbázis fejlesztéséhez

U-SQL-adatbázist kínál strukturált strukturálatlan adatok és a táblák felügyelt strukturált adatokhoz. Egy általános metaadatok rendszert a rendszerezése a strukturált adatok és az egyéni kódot is tartalmazza. Az adatbázis a fogalom, amely csoportosítja a kapcsolódó objektumokkal együtt.

Tudjon meg többet [U-SQL-adatbázis és az adatok definíciós nyelv (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

A U-SQL database-projekt olyan projektet a Visual Studióban, amellyel a fejlesztők fejlesztése, kezelése és üzembe helyezése a U-SQL-adatbázisok, gyorsan és egyszerűen.

## <a name="create-a-u-sql-database-project"></a>A U-SQL database-projekt létrehozása

Az Azure Data Lake Tools for Visual Studio projekt U-SQL database-projekt nevű verzió 2.3.3000.0 után új sablont hozzá. U-SQL projekt létrehozásához, válassza ki a **fájl > Új > projekt**. A U-SQL Database-projekt területen található **az Azure Data Lake > U-SQL-csomópont**.

![A Data Lake Tools for Visual Studio – az U-SQL database-projekt létrehozása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>U-SQL-adatbázis-objektumok fejlesztése egy adatbázis-projekt használatával

Kattintson a jobb gombbal a U-SQL database-projekt. Válassza a **Hozzáadás > Új elem**. Minden új támogatott objektumtípusok a annak a **új elem hozzáadása** varázsló. 

Nem szerelvény-objektumok (például egy táblázat értékű függvény) egy új U-SQL parancsfájl jön létre egy új elem hozzáadása után. Fejlesztés az objektum a szerkesztőben a DDL-utasítást is elindítható.

Az eszköz egy szerelvény objektum egy felhasználóbarát UI-szerkesztő, amely segítséget nyújt a szerelvény regisztrálja és telepítse a DLL-fájlok és egyéb kiegészítő fájlokat biztosít. A következő lépések bemutatják, hogyan egy szerelvény objektum meghatározása a U-SQL database-projekt hozzáadása:

1.  Adja hozzá a C#-projektet, amely tartalmazza a UDO/UDAG/UDF-ben a U-SQL database-projekthez tartozó hivatkozásokat.

    ![A Data Lake Tools for Visual Studio – hozzáadása U-SQL adatbázis-projekt leírása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![A Data Lake Tools for Visual Studio – hozzáadása U-SQL adatbázis-projekt leírása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  A szerelvény Tervezés nézetében válassza ki a hivatkozott szerelvényt a **szerelvény létrehozása hivatkozás** legördülő menüből.

    ![A Data Lake Tools for Visual Studio – szerelvény létrehozása hivatkozás](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Adjon hozzá **felügyelt függőségek** és **további fájlok** ha vannak ilyenek. Amikor kiegészítő fájlokat, az eszköz a relatív elérési út használatával győződjön meg arról, hogy megtalálja a szerelvények mind a helyi gépen, és a build gépen később. 

@_DeployTempDirectory van egy előre definiált változó, amely az eszköz mutat a build kimeneti mappába. A build kimeneti mappában az összes szerelvényét rendelkezik egy almappát a szerelvény neve. Az összes DLL-EK és további fájlokat is ebbe a mappába. 
 
## <a name="build-a-u-sql-database-project"></a>A U-SQL database-projekt létrehozása

A U-SQL database-projekt van egy U-SQL-adatbázis központi telepítési csomagot, az utótag nevű kimeneti build `.usqldbpack`. A `.usqldbpack` csomag egy .zip fájlt, amely tartalmaz egy U-SQL parancsfájl az összes DDL-utasítások a **DDL** mappát, és az összes DLL-EK és további fájlok szerelvények a **Temp** mappát.

Tudjon meg többet [hogyan hozhat létre egy U-SQL-adatbázis projektet, az MSBuild a parancssor és a egy Azure-fejlesztési és üzemeltetési szolgáltatásokat hozhat létre feladat](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>U-SQL-adatbázis üzembe helyezése

A .usqldbpack csomagot a Visual Studio vagy az üzembe helyezés SDK használatával vagy egy helyi fiókot, vagy egy Azure Data Lake Analytics-fiók is telepíthető. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>A Visual Studióban U-SQL-adatbázis üzembe helyezése

Telepíthet egy U-SQL-adatbázis egy U-SQL database-projekt vagy egy .usqldbpack csomagot a Visual Studióban.

#### <a name="deploy-through-a-u-sql-database-project"></a>Keresztül egy U-SQL database-projekt üzembe helyezése

1.  Kattintson a jobb gombbal a U-SQL database-projektet, és válassza **telepítés**.
2.  Az a **U-SQL-adatbázis központi telepítése varázsló**, jelölje be a **ADLA fiók** , amely számára telepíteni kívánja az adatbázist. Helyi fiókok és az ADLA fiókok is támogatottak.
3.  **Adatbázis-forrás** automatikusan, és a .usqldbpack csomagot a projektben pontok kimeneti mappa.
4.  Adjon meg egy nevet a **adatbázisnév** hozhat létre adatbázist. Ha egy azonos nevű adatbázis már létezik a célkiszolgálón az Azure Data Lake Analytics-fiók, az adatbázis-projektben meghatározott összes objektum jönnek létre az adatbázis újbóli létrehozása nélkül.
5.  A U-SQL-adatbázis üzembe helyezéséhez válassza **küldés**. Minden erőforrás (szerelvények és további fájlokat) a rendszer feltölti, és a egy U-SQL-feladatot, amely tartalmazza az összes DDL-utasítások elküldve.

    ![A Data Lake Tools for Visual Studio – üzembe helyezése U-SQL database-projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![A Data Lake Tools for Visual Studio – üzembe helyezése U-SQL adatbázis-projekt varázsló](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Telepítés egy U-SQL-adatbázis központi telepítési csomag használatával

1.  Nyissa meg **Server Explorer**. Ezután bontsa ki a **Azure Data Lake Analytics-fiók** , amely számára telepíteni kívánja az adatbázist.
2.  Kattintson a jobb gombbal **U-SQL-adatbázisok**, és válassza a **adatbázis telepítése**.
3.  Állítsa be **adatbázis forrás** a U-SQL database központi telepítési csomag (.usqldbpack fájl) elérési utat.
4.  Adja meg a **adatbázisnév** hozhat létre adatbázist. Ha a cél az Azure Data Lake Analytics-fiók már létezik ilyen nevű adatbázis, az adatbázis-projektben meghatározott összes objektum jönnek létre az adatbázis újbóli létrehozása nélkül.

    ![A Data Lake Tools for Visual Studio – üzembe helyezése U-SQL-adatbázis csomag](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![A Data Lake Tools for Visual Studio – üzembe helyezése U-SQL database csomag varázsló](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Az SDK-val a U-SQL-adatbázis üzembe helyezése

`PackageDeploymentTool.exe` biztosítja a programozás és parancssori felületek, amelyek segítenek a U-SQL-adatbázisok üzembe helyezéséhez. Az SDK tartalmazza a [U-SQL SDK Nuget-csomagot](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), a következő helyen található `build/runtime/PackageDeploymentTool.exe`.

[További információ az SDK-t és a U-SQL-adatbázis üzembe helyezése a CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Hivatkozás egy U-SQL database-projekt

A U-SQL projekt hivatkozhat egy U-SQL database-projekt. A hivatkozás a két számítási van hatással:

- *A Project build*: A hivatkozott adatbázis környezetek beállítása előtt a U-SQL-parancsfájlok. 
- *Helyi Futtatás ellen (egy helyi-projekt) fiók*: A hivatkozott adatbázis környezetben vannak telepítve (egy helyi-projekt) fiók U-SQL parancsfájl végrehajtása előtt. [További tudnivalók a helyi Futtatás és a különbség a között (a helyi-számítógép) és a (helyi-projekt) fiók Itt](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>A U-SQL-adatbázis hivatkozás hozzáadása

1. Kattintson a jobb gombbal a U-SQL projekt **Megoldáskezelőben**, és válassza a **U-SQL-adatbázis hivatkozás hozzáadása...** .

    ![A Data Lake Tools for Visual Studio – adatbázis-projekt hivatkozás hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Az aktuális megoldáshoz vagy egy U-SQL-adatbázis csomagfájlt, állítsa be egy U-SQL-adatbázis projektből adatbázis hivatkozást.
3. Adja meg az adatbázis nevét.

    ![A Data Lake Tools for Visual Studio adatbázis projekt referencia varázsló hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Analytics egy CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md)
- [Az Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md)
- [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
