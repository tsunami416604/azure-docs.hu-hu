---
title: U-SQL database projekt használata a fejleszthet az Azure Data Lake U-SQL-adatbázis |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure Data Lake Tools for Visual Studio használatával U-SQL-adatbázis.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a8099a8c9bcec8ed5a7bb480dae58d2c077b8fe0
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889596"
---
# <a name="use-u-sql-database-project-to-develop-u-sql-database-for-azure-data-lake"></a>Fejlesztése az Azure Data Lake U-SQL-adatbázis adatbázis-projekt U-SQL használatával

U-SQL-adatbázis strukturált nézeteket biztosít a strukturálatlan adatok, táblák strukturált adatok kezelésére és rendszerezheti a strukturált adatok és az egyéni kód általános metaadatok katalógus rendszert biztosít. Az adatbázis a fogalom, amely csoportosítja a kapcsolódó objektumokkal együtt.

Tudjon meg többet [U-SQL-adatbázis és az adatok definíciós nyelv (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

U-SQL database-projekt olyan projektet a Visual Studióban, amellyel a fejlesztők fejlesztése, kezelése és üzembe helyezése a U-SQL-adatbázisok, gyors és egyszerű.

## <a name="create-a-u-sql-database-project"></a>A U-SQL database-projekt létrehozása

Az Azure Data Lake Tools for Visual Studio projekt U-SQL database-projekt nevű verzió 2.3.3000.0 után új sablont hozzá. U-SQL projekt létrehozásához, meg **fájl > Új > projekt**, a U-SQL Database-projekt területen található **az Azure Data Lake > U-SQL-csomópont**.

![A Data Lake Tools for Visual Studio u-sql database-projekt létrehozása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-using-database-project"></a>U-SQL adatbázis-objektumok használatával adatbázisprojekthez fejlesztése

Kattintson a jobb gombbal a U-SQL database-projekt, kattintson a **Hozzáadás > Új elem**, minden támogatott objektumtípusok tekintheti meg az új elem hozzáadása varázslót. 

1.  Nem assembly objektum például táblázat értékű függvényben, új U-SQL parancsfájl jön létre új elem hozzáadása után. Fejlesztés az objektum a szerkesztőben a DDL-utasítást is elindítható.
2.  Szerelvény objektumhoz az eszköz biztosít egy felhasználóbarát UI-szerkesztő, amely segítséget nyújt a szerelvény regisztrálja és telepítse a .dll és további fájlokat. Kövesse az alábbi lépéseket egy szerelvény objektum meghatározása a U-SQL database-projekt hozzáadása:

1.  Adja hozzá a C#-projektet, amely tartalmazza a UDO/UDAG/UDF-ben a U-SQL-adatbázis projekt hivatkozásait.

    ![A Data Lake Tools for Visual Studio u-sql database projekt hivatkozás hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![A Data Lake Tools for Visual Studio u-sql database projekt hivatkozás hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  Szerelvény Tervező nézetben válassza ki a hivatkozott szerelvényt a **szerelvény létrehozása hivatkozás** listából.

    ![A Data Lake Tools for Visual Studio szerelvény létrehozása hivatkozás](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Adjon hozzá **felügyelt függőségek** és **további fájlok** ha van ilyen. További fájlok felvételével, amikor az eszköz használatával a következő relatív elérési győződjön meg arról, keresse meg a szerelvényeket, mind a helyi gépén, és a build gép. @_DeployTempDirectory van egy előre definiált változó, amely az eszköz mutat a build kimeneti mappába. A felépítési művelet kimenetében összes szerelvényét rendelkezik egy almappát, mert a szerelvény neve, az összes DLL-EK és további fájlokat található hiba abba az almappájába. 
 
## <a name="build-u-sql-database-project"></a>U-SQL database-projekt létrehozása

A U-SQL database-projekt van egy U-SQL-adatbázis központi telepítési csomagot, utótaggal rendelkező nevű kimeneti build `.usqldbpack`. A `.usqldbpack` csomag egy zip-fájl tartalmaz egy U-SQL parancsfájl a DDL-utasítások **DDL** mappát, és az összes DLL-EK és további fájlok szerelvények **Temp** mappát.

Tudjon meg többet [hogyan hozhat létre az MSBuild parancssor és a Visual Studio Team Services build feladat U-SQL-adatbázis projekt](data-lake-analytics-cicd-overview.md#build-u-sql-database-project).

## <a name="deploy-u-sql-database"></a>U-SQL-adatbázis üzembe helyezése

A .usqldbpack csomag is üzembe helyezhetők az helyi fiók vagy a Visual Studio vagy az üzembe helyezés SDK használata Azure Data Lake Analytics-fiók. 

### <a name="deploy-u-sql-database-in-visual-studio"></a>A Visual Studióban a U-SQL-adatbázis üzembe helyezése

Telepíthet egy U-SQL-adatbázis egy U-SQL database-projekt vagy egy .usqldbpack csomagot a Visual Studióban.

#### <a name="deploy-through-u-sql-database-project"></a>U-SQL-adatbázis projekten keresztül üzembe helyezése

1.  Kattintson a jobb gombbal a U-SQL database-projektet, és válassza a **telepítés**.
2.  A U-SQL-adatbázis telepítése varázslóban válassza ki a **ADLA fiók** szeretné telepíteni az adatbázist. (Helyi) fiókot és az ADLA fiók támogatott.
3.  **Adatbázis-forrás** ki vannak töltve a .usqldbpack csomagot a projekt felépítési kimeneti mappa automatikusan mutató
4.  Adja meg **adatbázisnév** az adatbázis létrehozásához. Ha a cél az Azure Data Lake Analytics-fiók azonos meglévő adatbázist, az összes meghatározott adatbázis-projektben nélkül jön létre az adatbázis újbóli létrehozását.
5.  Kattintson a **küldés** az U-SQL-adatbázis üzembe helyezéséhez. Minden erőforrás (szerelvények és további fájlokat) feltöltése és a egy U-SQL projekt tartalmazza az összes DDL-utasítások nyújtják.

    ![A Data Lake Tools for Visual Studio u-sql database-projekt üzembe helyezése](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![A Data Lake Tools for Visual Studio telepítése a u-sql database-projekt varázsló](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-u-sql-database-deployment-package"></a>Telepítés a U-SQL-adatbázis központi telepítési csomag használatával

1.  Nyissa meg **Server Explorer**, bontsa ki a **Azure Data Lake Analytics-fiók** szeretné telepíteni az adatbázist.
2.  Kattintson a jobb gombbal a U-SQL-adatbázisok, és válassza a **adatbázis telepítése**.
3.  Állítsa be **adatbázis forrás** a U-SQL database központi telepítési csomag (.usqldbpack fájl) elérési utat.
4.  Adja meg a **adatbázisnév** az adatbázis létrehozásához. Ha a cél az Azure Data Lake Analytics-fiók azonos meglévő adatbázist, az összes adatbázis-projektben meghatározott nélkül jön létre az adatbázis újbóli létrehozása.

    ![A Data Lake Tools for Visual Studio u-sql-adatbázis csomag üzembe helyezése](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![A Data Lake Tools for Visual Studio telepítése a u-sql database csomag varázsló](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-using-sdk"></a>SDK-val U-SQL-adatbázis üzembe helyezése

`PackageDeploymentTool.exe` biztosítja a programozás és parancssori felületek, amelyek segítenek a U-SQL-adatbázisok üzembe helyezéséhez. Az SDK tartalmazza a [U-SQL SDK Nuget-csomagot](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), helyének meghatározásával, `build/runtime/PackageDeploymentTool.exe`.

[További információ az SDK-t és a U-SQL-adatbázis üzembe helyezése a CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Hivatkozás egy U-SQL database-projekt

A U-SQL projekt hivatkozhat egy U-SQL database-projekt. A hivatkozás a két számítási van hatással:

- A Project build: A hivatkozott adatbázis környezetek beállítása előtt a U-SQL-parancsfájlok. 
- Helyi futtatás (helyi-projekt) fiók ellen: A hivatkozott adatbázis környezetben vannak telepítve (helyi-projekt) fiók U-SQL parancsfájl végrehajtása előtt. [További tudnivalók a helyi Futtatás és a különbség a (helyi-számítógép) és a (helyi-projekt) fiókot](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-u-sql-database-reference"></a>U-SQL-adatbázis hivatkozás hozzáadása

1. Kattintson a jobb gombbal a U-SQL projekt **Megoldáskezelőben**, és válassza a **U-SQL-adatbázis hivatkozás hozzáadása...** .

    ![A Data Lake Tools for Visual Studio adatbázis projekt hivatkozás hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Adja meg adatbázis hivatkozás egy U-SQL database-projektet az aktuális megoldást, és a egy U-SQL-adatbázis csomagfájlt.
3. Adja meg az adatbázis nevét.

    ![A Data Lake Tools for Visual Studio adatbázis projekt referencia varázsló hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Analytics CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md)
- [Az Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md)
- [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
