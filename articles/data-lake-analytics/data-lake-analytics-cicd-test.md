---
title: A Azure Data Lake Analytics kód tesztelése
description: Megtudhatja, hogyan adhat hozzá tesztelési eseteket a C# U-SQL és a kiterjesztett kód számára a Azure Data Lake Analyticshoz.
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
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913966"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>A Azure Data Lake Analytics kód tesztelése

Azure Data Lake a [U-SQL](data-lake-analytics-u-sql-get-started.md) nyelvet biztosítja. Az U-SQL a deklaratív SQL- C# t kombinálva kényszeríti az adatfeldolgozást bármilyen méretben. Ebből a dokumentumból megtudhatja, hogyan hozhat létre tesztelési eseteket a U C# -SQL és a kiterjesztett felhasználó által definiált operátor (Udo) kódjához.

## <a name="test-u-sql-scripts"></a>U-SQL-parancsfájlok tesztelése

A U-SQL-szkript le van fordítva, és az Azure-ban vagy a helyi számítógépen futtatható végrehajtható kód futtatására van optimalizálva. A fordítási és optimalizálási folyamat a teljes U-SQL-szkriptet kezeli egészként. Minden utasításhoz nem végezheti el a hagyományos egység tesztelését. Azonban a U-SQL test SDK és a helyi Futtatás SDK használatával parancsfájl szintű teszteket végezhet.

### <a name="create-test-cases-for-u-sql-script"></a>Tesztelési esetek létrehozása U-SQL-parancsfájlhoz

A Visual studióhoz készült Azure Data Lake Tools lehetővé teszi U-SQL-parancsfájlok tesztelési eseteinek létrehozását.

1. Kattintson a jobb gombbal egy U-SQL-parancsfájlra Megoldáskezelő, majd válassza az **egység tesztelése**elemet.

1. Hozzon létre egy új tesztelési projektet, vagy szúrja be a tesztelési esetet egy meglévő tesztelési projektbe.

   ![Data Lake Tools for Visual Studio – a U-SQL-teszt projekt konfigurációjának létrehozása](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>A teszt adatforrás kezelése

U-SQL-parancsfájlok tesztelésekor be kell írnia a bemeneti fájlokat. A tesztelési célú adatkezeléshez kattintson a jobb gombbal a U-SQL projektre **megoldáskezelő**, majd válassza a **Tulajdonságok parancsot**. Megadhat egy forrást a **teszt adatforrásban**.

![A Visual Studio Data Lake eszközei – a Project test-adatforrás konfigurálása](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Ha a U- `Initialize()` SQL test SDK-ban hívja meg az illesztőfelületet, a rendszer létrehoz egy ideiglenes helyi adatgyökér-mappát a teszt projekt munkakönyvtárában. Az U-SQL-parancsfájl tesztelési eseteinek futtatása előtt a rendszer a teszt adatforrás mappában található összes fájlt és mappát átmásolja az ideiglenes helyi adatgyökér mappába. A teszt adatmappa elérési útjának pontosvesszővel való felosztásával további tesztelési adatforrás-mappákat is hozzáadhat.

### <a name="manage-the-database-environment-for-testing"></a>Az adatbázis-környezet kezelése teszteléshez

Ha a U-SQL-parancsfájlok U-SQL Database-objektumokat használnak vagy kérdeznek le, a U-SQL tesztelési esetek futtatása előtt inicializálnia kell az adatbázis-környezetet. Ez a megközelítés akkor lehet szükséges, ha tárolt eljárásokat hív meg. A `Initialize()` u-SQL test SDK felülete segít az u-SQL-projekt által hivatkozott összes adatbázis központi telepítéséhez a tesztelési projekt munkakönyvtárában lévő ideiglenes helyi adatgyökér-mappához.

További információ az u-SQL-projekthez tartozó U-SQL-adatbázis-projekt-referenciák kezeléséről: u-SQL-projektre mutató [hivatkozás](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Teszt eredményeinek ellenőrzése

Az `Run()` illesztőfelület a feladatok végrehajtási eredményét adja vissza. a *0* érték azt jelenti, hogy sikeres, és *1* hibát jelez. A kimenetek ellenőrzéséhez használhatja C# az érvényesítési függvényeket is.

### <a name="run-test-cases-in-visual-studio"></a>Tesztelési esetek futtatása a Visual Studióban

A U-SQL-szkriptek tesztelésére szolgáló projekt egy C# Unit test Framework-keretrendszerre épül. A projekt összeállítása után válassza a**Windows** > **test Explorer** **tesztelése** > elemet. Tesztelési eseteket futtathat a **test Explorerben**. Másik lehetőségként kattintson a jobb gombbal a. cs fájlra az egység tesztben, és válassza a **tesztek futtatása**lehetőséget.

## <a name="test-c-udos"></a>Udo C# tesztelése

### <a name="create-test-cases-for-c-udos"></a>Tesztelési esetek létrehozása C# a Udo

A C# C# felhasználó által definiált operátorok (Udo-k) teszteléséhez egy egység tesztelési keretrendszert használhat. A Udo tesztelésekor bemenetként kell előkészítenie a megfelelő **IRowset** -objektumokat.

Egy **IRowset** -objektumot kétféleképpen lehet létrehozni:

- Adatok betöltése fájlból **IRowset**létrehozásához:

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

- Adatgyűjtési adatok használata **IRowset**létrehozásához:

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

### <a name="verify-test-results"></a>Teszt eredményeinek ellenőrzése

A UDO függvények meghívása után az eredményeket a séma és a sorhalmaz értékének ellenőrzésével ellenőrizheti az C# érvényesítő függvények használatával. Hozzáadhat egy **U-SQL C# Udo-egység tesztelési projektet** a megoldásához. Ehhez válassza a **fájl > új > projekt** elemet a Visual Studióban.

### <a name="run-test-cases-in-visual-studio"></a>Tesztelési esetek futtatása a Visual Studióban

A projekt összeállítása után válassza a**Windows** > **test Explorer** **tesztelése** > elemet. Tesztelési eseteket futtathat a **test Explorerben**. Másik lehetőségként kattintson a jobb gombbal a. cs fájlra az egység tesztben, és válassza a **tesztek futtatása**lehetőséget.

## Tesztelési esetek futtatása az Azure-folyamatokban<a name="run-test-cases-in-azure-devops"></a>

Az **U-SQL-parancsfájlok tesztelési** projektjei és a Udo C# -  **C# tesztelési** projektek öröklik az egység tesztelési projektjeit. A [Visual Studio tesztelési feladata](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) az Azure-folyamatokban ezeket a tesztelési eseteket futtathatja.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>U-SQL-tesztelési esetek futtatása az Azure-folyamatokban

U-SQL-teszt esetén győződjön meg arról, hogy `CPPSDK` betölti a felépített számítógépet, majd `CPPSDK` adja át `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`az elérési utat a következőnek:.

#### <a name="what-is-cppsdk"></a>Mi az a CPPSDK?

A CPPSDK egy olyan csomag, amely a C++ Microsoft Visual 14 és a Windows SDK 10.0.10240.0 tartalmazza. Ez a csomag tartalmazza azt a környezetet, amelyre a U-SQL futtatókörnyezet szükséges. Ezt a csomagot a Azure Data Lake Tools for Visual Studio telepítési mappája alatt érheti el:

- A Visual Studio 2015 esetében a`C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- A Visual Studio 2017 esetében a`C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- A Visual Studio 2019 esetében a`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>CPPSDK előkészítése az Azure-folyamatok Build-ügynökével

Az Azure-folyamatokban a CPPSDK függőség előkészítésének leggyakoribb módja a következő:

1. Zip-fájl, amely tartalmazza a CPPSDK-kódtárakat.

1. Keresse meg a. zip-fájlt a verziókövetés rendszerébe. A. zip fájl gondoskodik arról, hogy a CPPSDK mappa minden könyvtárában bejelentkezzen, hogy a fájlok ne `.gitignore` legyenek figyelmen kívül hagyva egy fájl miatt.

1. Bontsa ki a. zip fájlt a Build folyamatban.

1. Mutasson `USqlScriptTestRunner` erre a kibontott mappára a Build számítógépén.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Udo C# -tesztelési esetek futtatása az Azure-folyamatokban

A C# Udo-teszt esetében ügyeljen arra, hogy a következő szerelvényekre hivatkozzon, amelyek a Udo esetében szükségesek.

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTest

Ha [a Microsoft. Azure. DataLake. USQL. interfaces Nuget-csomagon](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/)keresztül hivatkozik rájuk, ügyeljen arra, hogy a létrehozási folyamat során vegyen fel egy Nuget-visszaállítási feladatot.

## <a name="next-steps"></a>További lépések

- [CI/CD-folyamat beállítása Azure Data Lake Analyticshoz](data-lake-analytics-cicd-overview.md)
- [U-SQL-szkript futtatása a helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
- [A U-SQL Database-projekt használata a U-SQL-adatbázis fejlesztéséhez](data-lake-analytics-data-lake-tools-develop-usql-database.md)
