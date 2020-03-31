---
title: U-SQL-szerelvények kezelése CI/CD-folyamatban – Azure Data Lake
description: Ismerje meg az U-SQL C# szerelvények azure DevOps-szal való CI/CD-folyamatban való kezelésével kapcsolatos gyakorlati tanácsokat.
author: yanancai
ms.author: yanacai
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: e6de10ed712688e4ee9dccc22176e81ad5e574ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315851"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Gyakorlati tanácsok az U-SQL-szerelvények CI/CD-folyamatban való kezeléséhez

Ebből a cikkből megtudhatja, hogyan kezelheti az U-SQL kódösszeállítás forráskódját az újonnan bevezetett U-SQL adatbázis-projekttel. Azt is megtudhatja, hogyan állíthat be egy folyamatos integrációs és üzembe helyezési (CI/CD) folyamat összeállítási regisztráció hoz az Azure DevOps használatával.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Az U-SQL adatbázisprojekt használata az összeállítás forráskódjának kezeléséhez

[Az U-SQL adatbázis-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md) egy olyan projekttípus a Visual Studióban, amely segít a fejlesztőknek gyorsan és egyszerűen fejleszteni, kezelni és telepíteni u-SQL-adatbázisaikat. Az U-SQL adatbázis-projekttel az összes U-SQL adatbázis-objektumot kezelheti (a hitelesítő adatok kivételével). 

A C# szerelvény forráskódjának és a DDL U-SQL-parancsfájlok összeállításának regisztrációjának kezeléséhez használja a következőt:

* U-SQL adatbázis-projekt az U-SQL-parancsfájlok szerelvényregisztrációjának kezeléséhez.
* Class Library (U-SQL alkalmazás) a C# forráskód és a függőségek a felhasználó által definiált operátorok, függvények és összesítők (UDOs, UDFs és UDAGs).
* U-SQL adatbázis-projekt a Class Library projektre való hivatkozáshoz. 

Az U-SQL adatbázis-projektek hivatkozhatnak egy osztálytár (U-SQL alkalmazáshoz) projektre. Az U-SQL adatbázisban regisztrált szerelvényeket az osztálytár (U-SQL alkalmazáshoz) projekt hivatkozott C# forráskódjával hozhat létre.

Az alábbi lépésekkel hozhat létre projekteket, és hivatkozásokat adhat hozzá.
1. Hozzon létre egy osztálytár (U-SQL alkalmazáshoz) projektet az**Új** > **projekt** **fájl** > jának kiválasztásával. A projekt az **Azure Data Lake > U-SQL** csomópont alatt található.

   ![Data Lake Tools for Visual Studio --C# osztálykönyvtár-projekt létrehozása](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Adja hozzá a felhasználó által definiált C# kódot a Class Library (U-SQL alkalmazás) projektben.

1. U-SQL-projekt létrehozása az**Új** > **projekt** **fájljának** > kiválasztásával. A projekt az **Azure Data Lake** > **U-SQL** csomópont alatt található.

   ![Data Lake Tools for Visual Studio – U-SQL adatbázis-projekt létrehozása](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Hivatkozás hozzáadása az U-SQL adatbázis-projekt C# osztálykönyvtár-projektjéhez.

    ![Data Lake Tools for Visual Studio –-U-SQL adatbázis-projekthivatkozás hozzáadása](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools for Visual Studio –-U-SQL adatbázis-projekthivatkozás hozzáadása](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Hozzon létre egy összeállítási parancsfájlt az U-SQL adatbázis-projektben úgy, hogy a jobb gombbal a projektre kattint, és az **Új elem hozzáadása parancsot**választja.

   ![Data Lake-eszközök a Visual Studio-hoz - Assembly parancsfájl hozzáadása](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Nyissa meg az összeállítási parancsfájlt az összeállítás tervező nézetében. Válassza ki a hivatkozott kódösszeállítást a Hivatkozás legördülő **menüből a Szerelvény létrehozása menüből.**

    ![Data Lake Tools for Visual Studio - összeállítás létrehozása referenciaból](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. **Felügyelt függőségek** és **további fájlok**hozzáadása, ha vannak ilyenek. Ha további fájlokat ad hozzá, az eszköz a relatív elérési utat használja annak érdekében, hogy a helyi gépen és a buildgépen később is megtalálja az összeállításokat.

_DeployTempDirectory szerkesztőablakalján egy előre definiált változó, amely az eszközt a build kimeneti mappába helyezi. ** \@** A buildkimeneti mappában minden szerelvénynek van egy almappája, amelynek neve a szerelvény nevével van elnevezve. Az almappában minden DIL és további fájl található.

## <a name="build-a-u-sql-database-project"></a>U-SQL adatbázis-projekt létrehozása

Az U-SQL adatbázis-projekt buildkimenete egy U-SQL adatbázis-telepítési csomag. Az utótaggal van elnevezve. `.usqldbpack` A `.usqldbpack` csomag egy .zip fájl, amely a DDL mappában egyetlen U-SQL parancsfájlban található összes DDL utasítást tartalmazza. Az összes beépített .dll fájl és az összeállításhoz szükséges további fájlok a Temp mappában találhatók.

## <a name="deploy-a-u-sql-database"></a>U-SQL-adatbázis telepítése

A `.usqldbpack` csomag helyi fiókba vagy Egy Azure Data Lake Analytics-fiókba telepíthető. Használja a Visual Studio vagy a központi telepítési SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>U-SQL-adatbázis telepítése a Visual Studióban

U-SQL-adatbázist U-SQL adatbázis-projekt vagy `.usqldbpack` csomag használatával telepíthet a Visual Studióban.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Telepítés U-SQL adatbázis-projekttel

1.  Kattintson a jobb gombbal az U-SQL adatbázis-projektre, és válassza **a Telepítés parancsot.**
2.  Az **U-SQL-adatbázis telepítése** varázslóban jelölje ki azt az **ADLA-fiókot,** amelyre telepíteni szeretné az adatbázist. Mind a helyi fiókok, mind az ADLA-fiókok támogatottak.
3.  **Az adatbázis-forrás** kitöltése automatikusan megtörténik. A projekt build kimeneti mappájában található .usqldbpack csomagra mutat.
4.  Az adatbázis létrehozásához írjon be egy nevet az **Adatbázis név mezőbe.** Ha egy ilyen nevű adatbázis már létezik a cél Azure Data Lake Analytics-fiókban, az adatbázis-projektben definiált összes objektum az adatbázis újbóli létrehozása nélkül jön létre.
5.  Az U-SQL adatbázis telepítéséhez válassza a **Küldés gombot.** A rendszer feltölti az összes erőforrást, például az összeállításokat és a további fájlokat. Egy U-SQL-feladat, amely tartalmazza az összes DDL-utasítások elküldésre kerül.

    ![Data Lake Tools for Visual Studio – U-SQL adatbázis-projekt telepítése](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools for Visual Studio – U-SQL adatbázis-projekt varázsló telepítése](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>U-SQL-adatbázis üzembe helyezése az Azure DevOps-ban

`PackageDeploymentTool.exe`biztosítja az U-SQL adatbázisok központi telepítését segítő programozási és parancssori felületeket. Az SDK a [(U-SQL SDK Nuget) csomag](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)részét képezi, amely a helyen `build/runtime/PackageDeploymentTool.exe`található.

Az Azure DevOps-ban egy parancssori feladatot és ezt az SDK-t használhat az U-SQL adatbázis frissítéséhez egy automatizálási folyamat beállításához. [További információ az SDK-ról és az U-SQL adatbázis-telepítéshez való CI/CD-folyamat beállításáról.](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines)

## <a name="next-steps"></a>További lépések

* [CI/CD-folyamat beállítása az Azure Data Lake Analytics szolgáltatáshoz](data-lake-analytics-cicd-overview.md)
* [Az Azure Data Lake Analytics-kód tesztelése](data-lake-analytics-cicd-test.md)
* [U-SQL-parancsfájl futtatása a helyi számítógépen](data-lake-analytics-data-lake-tools-local-run.md)
