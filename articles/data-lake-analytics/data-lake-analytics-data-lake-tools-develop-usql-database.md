---
title: U-SQL adatbázis-projekt fejlesztése – Azure Data Lake
description: Ismerje meg, hogyan fejleszthet U-SQL-adatbázist az Azure Data Lake Tools for Visual Studio használatával.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: a9b271b5f7d4e53dbf871d03dd43b62b9299aa53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309927"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>U-SQL adatbázis-projekt használata U-SQL-adatbázis fejlesztéséhez az Azure Data Lake-hez

Az U-SQL adatbázis strukturált nézeteket biztosít a strukturálatlan adatokról és a táblákban kezelt strukturált adatokról. Általános metaadatkatalógus-rendszert is biztosít a strukturált adatok és az egyéni kódok rendszerezéséhez. Az adatbázis az a fogalom, amely ezeket a kapcsolódó objektumokat csoportosítja.

További információ az [U-SQL adatbázisról és adatdefiníciós nyelvről (DDL).](/u-sql/data-definition-language-ddl-statements) 

Az U-SQL adatbázis-projekt egy olyan projekttípus a Visual Studióban, amely segít a fejlesztőknek gyorsan és egyszerűen fejleszteni, kezelni és telepíteni u-SQL-adatbázisaikat.

## <a name="create-a-u-sql-database-project"></a>U-SQL adatbázis-projekt létrehozása

Az Azure Data Lake Tools for Visual Studio a 2.3.3000.0-s verzió után hozzáadott egy új projektsablont U-SQL adatbázis-projekt néven. U-SQL-projekt létrehozásához válassza **a Fájl > új > Project**lehetőséget. Az U-SQL adatbázis-projekt az **Azure Data Lake > U-SQL csomópont**alatt található.

![Data Lake Tools for Visual Studio – U-SQL adatbázis-projekt létrehozása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>U-SQL adatbázis-objektumok fejlesztése adatbázisprojekt teljítése

Kattintson a jobb gombbal az U-SQL adatbázis-projektre. Válassza **a Hozzáadás > új elem lehetőséget.** Az **új elem hozzáadása** varázslóban az összes új támogatott objektumtípus megtalálható. 

Nem összeállítási objektum (például egy táblaértékű függvény) esetén egy új U-SQL parancsfájl jön létre egy új elem hozzáadása után. Megkezdheti a DDL-utasítás fejlesztését az adott objektumhoz a szerkesztőben.

Összeállítási objektum esetén az eszköz egy felhasználóbarát felhasználói felület-szerkesztőt biztosít, amely segít a kódösszeállítás regisztrálásában és a DLL-fájlok és más további fájlok központi telepítésében. A következő lépések bemutatják, hogyan adhat hozzá egy összeállítási objektumdefiníciót az U-SQL adatbázisprojekthez:

1.  Adjon hozzá hivatkozásokat a C# projekthez, amelyek tartalmazzák az UDO/UDAG/UDF-et az U-SQL adatbázis-projekthez.

    ![Data Lake Tools for Visual Studio –-U-SQL adatbázis-projekthivatkozás hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools for Visual Studio –-U-SQL adatbázis-projekthivatkozás hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  A szerelvény tervező nézetében válassza ki a hivatkozott szerelvényt a **Keretösszeállítás létrehozása menüből** a referencia legördülő menüből.

    ![Data Lake Tools for Visual Studio - összeállítás létrehozása referenciaból](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  **Felügyelt függőségek** és **további fájlok** hozzáadása, ha vannak ilyenek. Ha további fájlokat ad hozzá, az eszköz a relatív elérési utat használja annak érdekében, hogy a helyi gépen és később a buildgépen is megtalálja az összeállításokat. 

@_DeployTempDirectoryegy előre definiált változó, amely az eszközt a build kimeneti mappába helyezi. A buildkimeneti mappában minden szerelvénynek van egy almappája, amelynek neve a szerelvény nevével van elnevezve. Az almappában minden DIL és további fájl található. 
 
## <a name="build-a-u-sql-database-project"></a>U-SQL adatbázis-projekt létrehozása

Az U-SQL adatbázis-projekt buildkimenete egy U-SQL adatbázis-telepítési csomag, amelynek neve az utótaggal van elnevezve. `.usqldbpack` A `.usqldbpack` csomag egy .zip fájl, amely tartalmazza az összes DDL utasítást egyetlen U-SQL parancsfájlban a **DDL** mappában, valamint az összes DL-t és további fájlt a **Temp** mappában lévő szerelvényekhez.

További információ [arról, hogyan hozhat létre U-SQL adatbázis-projektet az MSBuild parancssorból és egy Azure DevOps Services buildfeladattal.](data-lake-analytics-cicd-overview.md)

## <a name="deploy-a-u-sql-database"></a>U-SQL-adatbázis telepítése

A .usqldbpack csomag helyi fiókra vagy Azure Data Lake Analytics-fiókra telepíthető a Visual Studio vagy a központi telepítési SDK használatával. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>U-SQL-adatbázis telepítése a Visual Studióban

U-SQL-adatbázist U-SQL adatbázis-projekten vagy .usqldbpack csomagon keresztül telepíthet a Visual Studióban.

#### <a name="deploy-through-a-u-sql-database-project"></a>Telepítés U-SQL adatbázis-projekten keresztül

1.  Kattintson a jobb gombbal az U-SQL adatbázis-projektre, és válassza **a Telepítés parancsot.**
2.  Az **U-SQL adatbázis telepítése varázslóban**válassza ki azt az **ADLA-fiókot,** amelyre az adatbázist telepíteni szeretné. Mind a helyi fiókok, mind az ADLA-fiókok támogatottak.
3.  **Az adatbázis-forrás** automatikusan kitöltődik, és a projekt build kimeneti mappájában lévő .usqldbpack csomagra mutat.
4.  Az adatbázis létrehozásához írjon be egy nevet az **Adatbázis név mezőbe.** Ha egy ilyen nevű adatbázis már létezik a cél Azure Data Lake Analytics-fiókban, az adatbázis-projektben definiált összes objektum az adatbázis újbóli létrehozása nélkül jön létre.
5.  Az U-SQL adatbázis telepítéséhez válassza a **Küldés gombot.** Minden erőforrás (szerelvények és további fájlok) feltöltésre kerül, és egy U-SQL feladat, amely tartalmazza az összes DDL utasítások elküldésre kerül.

    ![Data Lake Tools for Visual Studio – U-SQL adatbázis-projekt telepítése](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools for Visual Studio – U-SQL adatbázis-projekt varázsló telepítése](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Telepítés U-SQL adatbázis-telepítési csomagon keresztül

1.  Nyissa meg **a Kiszolgálókezelőt**. Ezután bontsa ki azt az **Azure Data Lake Analytics-fiókot,** amelyre az adatbázist telepíteni szeretné.
2.  Kattintson a jobb gombbal **az U-SQL adatbázisok**elemre, és válassza **az Adatbázis telepítése parancsot.**
3.  Állítsa az **adatbázisforrást** az U-SQL adatbázis központi telepítési csomag (.usqldbpack fájl) elérési útjára.
4.  Adatbázis létrehozásához írja be az **adatbázis nevét.** Ha van egy adatbázis az azonos nevű, amely már létezik a cél Azure Data Lake Analytics-fiók, az adatbázis-projektben definiált összes objektum jön létre az adatbázis újbóli létrehozása nélkül.

    ![Data Lake Tools for Visual Studio – U-SQL adatbáziscsomag telepítése](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Data Lake Tools for Visual Studio – U-SQL adatbáziscsomag telepítése varázsló](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>U-SQL-adatbázis telepítése az SDK használatával

`PackageDeploymentTool.exe`biztosítja az U-SQL adatbázisok központi telepítését segítő programozási és parancssori felületeket. Az SDK a [(U-SQL SDK Nuget) csomag](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)részét képezi, amely a helyen `build/runtime/PackageDeploymentTool.exe`található.

[További információ az SDK-ról és a CI/CD-folyamat u-SQL-adatbázis-telepítéshez való beállításáról.](data-lake-analytics-cicd-overview.md)

## <a name="reference-a-u-sql-database-project"></a>U-SQL adatbázis-projektre való hivatkozás

Egy U-SQL projekt hivatkozhat egy U-SQL adatbázis-projektre. A hivatkozás két számítási feladatot érint:

- *Projektbuild*: Az U-SQL-parancsfájlok létrehozása előtt állítsa be a hivatkozott adatbázis-környezeteket. 
- *Helyi futtatás (helyi projekt) fiók*ellen: A hivatkozott adatbázis-környezetek az U-SQL parancsfájlok végrehajtása előtt (helyi projekt) fiókba vannak telepítve. [Tudjon meg többet a helyi fut, és a különbség (a helyi-gép) és (a helyi projekt) számla itt](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>U-SQL adatbázis-hivatkozás hozzáadása

1. Kattintson a jobb gombbal az U-SQL projektre a **Megoldáskezelőben,** majd válassza **az U-SQL adatbázis-hivatkozás hozzáadása...** parancsot.

    ![Data Lake Tools for Visual Studio – adatbázis-projekthivatkozás hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfiguráljon egy U-SQL adatbázis-projekt adatbázis-hivatkozását az aktuális megoldásban vagy egy U-SQL adatbáziscsomag-fájlban.
3. Adja meg az adatbázis nevét.

    ![Data Lake Tools for Visual Studio adatbázis-projekthivatkozás-varázsló](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>További lépések

- [CI/CD-folyamat beállítása az Azure Data Lake Analytics szolgáltatáshoz](data-lake-analytics-cicd-overview.md)
- [Az Azure Data Lake Analytics-kód tesztelése](data-lake-analytics-cicd-test.md)
- [U-SQL-parancsfájl futtatása a helyi számítógépen](data-lake-analytics-data-lake-tools-local-run.md)
