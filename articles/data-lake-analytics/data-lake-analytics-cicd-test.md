---
title: Az Azure Data Lake Analytics-kód tesztelése
description: Ismerje meg, hogyan adhat hozzá teszteseteket az U-SQL-hez és a kiterjesztett C# kódot az Azure Data Lake Analytics-hez.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70913966"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Az Azure Data Lake Analytics-kód tesztelése

Az Azure Data Lake biztosítja az [U-SQL](data-lake-analytics-u-sql-get-started.md) nyelvet. Az U-SQL egyesíti a deklaratív SQL-t a C# elengedhetetlennel az adatok bármilyen méretű feldolgozásához. Ebben a dokumentumban megtudhatja, hogyan hozhat létre teszteseteket az U-SQL és a kiterjesztett C# felhasználó által definiált operátori (UDO) kódhoz.

## <a name="test-u-sql-scripts"></a>U-SQL-szkriptek tesztelése

Az U-SQL-parancsfájl fordítása és optimalizálása végrehajtható kód futtatásához az Azure-ban vagy a helyi számítógépen. A fordítási és optimalizálási folyamat a teljes U-SQL-parancsfájlt kezeli egészében. Nem csinálhatsz hagyományos egységtesztet minden állításhoz. Az U-SQL teszt SDK és a helyi sdk használatával azonban parancsfájlszintű teszteket is elérhet.

### <a name="create-test-cases-for-u-sql-script"></a>Tesztesetek létrehozása U-SQL parancsfájlhoz

Az Azure Data Lake Tools for Visual Studio lehetővé teszi az U-SQL parancsfájl-tesztesetek létrehozását.

1. Kattintson a jobb gombbal egy U-SQL parancsfájlra a Megoldáskezelőben, és válassza **az Egységteszt létrehozása parancsot.**

1. Hozzon létre egy új tesztprojektet, vagy helyezze be a tesztesetet egy meglévő tesztprojektbe.

   ![Data Lake Tools for Visual Studio – U-SQL tesztprojekt-konfiguráció létrehozása](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>A tesztadatforrás kezelése

U-SQL-parancsfájlok tesztelése során tesztbemeneti fájlokra van szükség. A tesztadatok kezeléséhez a **Megoldáskezelőben**kattintson a jobb gombbal az U-SQL projektre, és válassza a **Tulajdonságok parancsot.** A forrást a Teszt adatforrás mezőbe **adhatja**meg.

![Data Lake Tools for Visual Studio – projektteszt adatforrásának konfigurálása](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Amikor meghívja `Initialize()` a felületet az U-SQL teszt SDK-ban, egy ideiglenes helyi adatgyökér mappa jön létre a tesztprojekt munkakönyvtára alatt. A tesztadatforrás-mappában lévő összes fájlt és mappát a rendszer az ideiglenes helyi adatgyökérmappába másolja az U-SQL parancsfájl-tesztesetek futtatása előtt. További tesztadatforrás-mappák hozzáadásához a tesztadatok mappa elérési útját pontosvesszővel osztja fel.

### <a name="manage-the-database-environment-for-testing"></a>Az adatbázis-környezet kezelése teszteléshez

Ha az U-SQL-parancsfájlok u-SQL adatbázis-objektumokat használnak vagy lekérdezést használnak, az U-SQL tesztesetek futtatása előtt inicializálnia kell az adatbázis-környezetet. Erre a megközelítésre a tárolt eljárások hívása kor lehet szükség. Az `Initialize()` U-SQL teszt SDK-ban lévő felület segítségével telepítheti az U-SQL projekt által hivatkozott összes adatbázist a tesztprojekt munkakönyvtárában található ideiglenes helyi adatgyökérmappába.

Az U-SQL-projektek U-SQL adatbázis-hivatkozásainak kezeléséről további információt [a Hivatkozás egy U-SQL adatbázisprojektben](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)talál.

### <a name="verify-test-results"></a>A vizsgálati eredmények ellenőrzése

A `Run()` kapcsolat egy feladat-végrehajtási eredményt ad vissza. *A 0* a sikert, *az 1* pedig a hibát jelenti. A C# assert függvények segítségével is ellenőrizheti a kimeneteket.

### <a name="run-test-cases-in-visual-studio"></a>Tesztesetek futtatása a Visual Studióban

Az U-SQL parancsfájl-tesztprojekt egy C# egység tesztkeretrendszerre épül. A projekt létrehozása után válassza a**Windows** > **Tesztkezelő** **tesztelése** > lehetőséget. A teszteseteket a **Test Explorer**programból futtathatja. Másik lehetőségként kattintson a jobb gombbal a .cs fájlra az egységtesztben, és válassza a **Tesztek futtatása parancsot.**

## <a name="test-c-udos"></a>C# UdOs teszt

### <a name="create-test-cases-for-c-udos"></a>C# OSO-k tesztesetének létrehozása

A C# egység tesztkereta c# felhasználó által definiált operátorok (ISO) teszteléséhez használhatja. Az IDO-k tesztelése során elő kell készítenie a megfelelő **IRowset** objektumokat bemenetként.

Az **IRowset** objektumok kétféleképpen hozhatnak létre:

- Adatok betöltése fájlból az **IRowset**létrehozásához:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Az IRowset létrehozásához használjon adatgyűjtési **adatokat:**

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>A vizsgálati eredmények ellenőrzése

Az UDO-függvények hívása után ellenőrizheti az eredményeket a séma és a Rowset érték ellenőrzése c# assert függvények használatával. Hozzáadhat egy **U-SQL C# UDO Unit Test Project-et** a megoldáshoz. Ehhez válassza **a Fájl > Új > Projekt** lehetőséget a Visual Studióban.

### <a name="run-test-cases-in-visual-studio"></a>Tesztesetek futtatása a Visual Studióban

A projekt létrehozása után válassza a**Windows** > **Tesztkezelő** **tesztelése** > lehetőséget. A teszteseteket a **Test Explorer**programból futtathatja. Másik lehetőségként kattintson a jobb gombbal a .cs fájlra az egységtesztben, és válassza a **Tesztek futtatása parancsot.**

## <a name="run-test-cases-in-azure-pipelines"></a>Tesztesetek futtatása az Azure-folyamatokban<a name="run-test-cases-in-azure-devops"></a>

Mind **az U-SQL script tesztprojektek** és **a C# UDO teszt projektek** öröklik C# egység teszt projektek. A [Visual Studio tesztfeladat](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) az Azure-folyamatok futtathatja ezeket a teszteseteket.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>U-SQL tesztesetek futtatása az Azure-folyamatokban

U-SQL-teszt esetén győződjön `CPPSDK` meg arról, hogy betölti a buildszámítógépet, majd adja át az `CPPSDK` elérési utat. `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`

#### <a name="what-is-cppsdk"></a>Mi az a CPPSDK?

A CPPSDK egy Microsoft Visual C++ 14 és Windows SDK 10.0.10240.0 csomagot tartalmazó csomag. Ez a csomag tartalmazza az U-SQL-futásidejű által szükséges környezetet. Ez a csomag az Azure Data Lake Tools for Visual Studio telepítési mappájában szerezhető be:

- A Visual Studio 2015 esetében a`C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- A Visual Studio 2017 esetében a`C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- A Visual Studio 2019 esetében a`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>CPPSDK előkészítése az Azure-folyamatok buildügynökében

A CPPSDK-függőség előkészítésének leggyakoribb módja az Azure Pipelines-ban a következő:

1. A CPPSDK-könyvtárakat tartalmazó mappa zip.

1. Adja be a .zip fájlt a forrásvezérlő rendszerbe. A .zip fájl biztosítja, hogy a CPPSDK mappa összes könyvtárát beadják, így a fájlokat a `.gitignore` fájl nem hagyja figyelmen kívül.

1. Csomagolja ki a .zip fájlt a buildfolyamatból.

1. Mutasson `USqlScriptTestRunner` erre a kibontott mappára a buildszámítógépen.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>C# UDO-tesztesetek futtatása az Azure-folyamatokban

C# UDO-teszt esetén győződjön meg arról, hogy hivatkozik a következő szerelvények, amelyek szükségesek az OSO-k.

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTeszt

Ha [a Microsoft.Azure.DataLake.USQL.Interfaces Nuget csomagon](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/)keresztül hivatkozik rájuk, győződjön meg arról, hogy nuget-visszaállítási feladatot ad hozzá a buildfolyamathoz.

## <a name="next-steps"></a>További lépések

- [CI/CD-folyamat beállítása az Azure Data Lake Analytics szolgáltatáshoz](data-lake-analytics-cicd-overview.md)
- [U-SQL-parancsfájl futtatása a helyi számítógépen](data-lake-analytics-data-lake-tools-local-run.md)
- [U-SQL adatbázis létrehozása U-SQL adatbázis sal](data-lake-analytics-data-lake-tools-develop-usql-database.md)
