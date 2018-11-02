---
title: Az Azure Data Lake Analytics kód tesztelése
description: Ismerje meg, hogyan vizsgálati eset hozzáadása az Azure Data Lake Analytics U-SQL és a kiterjesztett C#-kódot.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 4532e0c6e8095c9d64897410e0492e2135d8a478
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747932"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Az Azure Data Lake Analytics tesztelhetnek

Az Azure Data Lake a U-SQL nyelv, amely egyesíti a deklaratív SQL C-vel imperatív bármilyen méretű adatok feldolgozásához biztosít. Ebből a dokumentumból megismerheti, hogyan vizsgálati eset létrehozása a U-SQL és C# UDO (felhasználó által definiált operátor) kiterjesztett kódot.

## <a name="test-u-sql-scripts"></a>U-SQL-parancsfájlok tesztelése

Az U-SQL-szkriptet összeállítani és optimalizált végrehajtható kód futtatására gépeken a felhőben vagy a helyi gépen. A fordítás és optimalizálási folyamata a teljes U-SQL parancsfájl egészében kezeli. Nem hajtható végre egy hagyományos "test jednotky" minden utasítás. Azonban a U-SQL használatával SDK tesztelése, és megteheti a helyi Futtatás SDK-t, parancsfájl-szintű teszteket.

### <a name="create-test-cases-for-u-sql-script"></a>A U-SQL parancsfájl tesztelési eseteket hoznak létre.

Az Azure Data Lake Tools for Visual Studio lehetővé teszi, hogy a U-SQL parancsfájl tesztelési eseteket hozhat létre.

1.  Kattintson a jobb gombbal egy U-SQL parancsfájl a Megoldáskezelőben, és válassza **létrehozása egységtesztet**.
2.  Hozzon létre egy új tesztelési projekt, vagy helyezze be a Teszteset egy meglévő tesztelő projektet.

    ![A Data Lake Tools for Visual Studio – teszt U-SQL projekt létrehozása](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![A Data Lake Tools for Visual Studio – hozzon létre egy U-SQL projekt tesztkonfiguráció](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>A teszt adatforrás kezelése

Ha U-SQL-parancsfájlok, tesztelnie kell a bemeneti fájlok. Kezelheti a Tesztadatok konfigurálásával **tesztelése adatforrás** a U-SQL projekt tulajdonságai. 

Meghívásakor a `Initialize()` felület az U-SQL-teszt SDK-t és egy átmeneti helyi adatok legfelső szintű mappa alatt a munkakönyvtárban a tesztelési projekt jön létre, és minden fájlok és almappák (és almappák fájlokat) a teszt adatok forrásmappában található a ideiglenes helyi adatok legfelső szintű mappát, a U-SQL parancsfájl vizsgálati eset futtatása előtt. A teszt data mappa elérési útjának pontosvesszővel halmazra további vizsgálat adatok forrása mappákat is hozzáadhat.

![Data Lake Tools for Visual Studio – a projekt test-adatforrás konfigurálása](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>A tesztelési adatbázis-környezet kezelése

Ha a U-SQL-parancsfájlokat használ, vagy a lekérdezés U-SQL-adatbázis objektumokat a (például, ha a tárolt eljárások hívása) akkor szükséges, az adatbázis-környezetének inicializálása a U-SQL-vizsgálati eset futtatása előtt. A `Initialize()` felület az U-SQL-teszt SDK segítséget nyújt a U-SQL projekt a munkakönyvtárban a test-projekt helyi ideiglenes adatokat gyökérmappájába által hivatkozott összes adatbázis üzembe helyezéséhez. 

Tudjon meg többet [kezelése a U-SQL database projekt referenciáihoz U-SQL projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Vizsgálati eredmények ellenőrzése

A `Run()` felület feladat végrehajtási eredményt adja vissza. 0 azt jelenti, hogy a sikeres, és 1 azt jelenti, hogy sikertelen. Assert függvények C# használatával ellenőrizze a kimenetek. 

### <a name="run-test-cases-in-visual-studio"></a>Vizsgálati eset futtatása a Visual Studióban

A U-SQL parancsfájl tesztelési projekt egy C# egység teszt keretrendszer épül. A projekt buildjének elkészítéséhez, miután az összes vizsgálati eset segítségével futtathatja **tesztelése Explorer > lista**. Másik lehetőségként kattintson a jobb gombbal a .cs fájlt, és válassza **tesztek futtatása**.

## <a name="test-c-udos"></a>C# udo-k tesztelése

### <a name="create-test-cases-for-c-udos"></a>Vizsgálati eset létrehozása a C# udo-k

Egy C# egység teszt keretrendszer segítségével tesztelheti a C# udo-k (felhasználó által definiált operátorok). Ha teszteli az udo-k, elő kell készíteni a megfelelő **IRowset** objektumok bemenetként.

IRowset objektum létrehozása két módja van:

- Adatok betöltése IRowset létrehozása fájlból:

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

- IRowset létrehozásához használja az adatok gyűjtemény adatait:

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

### <a name="verify-test-results"></a>Vizsgálati eredmények ellenőrzése

UDO-függvények hívása, miután a séma és a sorkészlet érték ellenőrzési eredmények assert függvények C# használatával ellenőrizheti. Mintakód egy U-SQL C# UDO egység teszt mintaprojektet keresztül használhatja **fájl > Új > projekt** a Visual Studióban.

### <a name="run-test-cases-in-visual-studio"></a>Vizsgálati eset futtatása a Visual Studióban

Után a tesztelő projektet hoz létre, futtathatja az összes vizsgálati eset azonban **tesztelése Explorer > lista**, vagy kattintson a jobb gombbal a .cs fájlt, és válassza a **tesztek futtatása**.

## <a name="run-test-cases-in-azure-devops"></a>Vizsgálati eset futtatása az Azure DevOps

Mindkét **U-SQL parancsfájl tesztelési projekt** és **C# UDO tesztelési projekt** öröklik a C# egység tesztelési projekt. A [Visual Studio test feladat](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) az Azure DevOps futtathatja a vizsgálati eset. 

### <a name="run-u-sql-test-cases-in-azure-devops"></a>U-SQL-vizsgálati eset futtatása az Azure DevOps

U-SQL teszteléséhez, ellenőrizze, hogy betöltötte `CPPSDK` a build gépre, és ezután pass a `CPPSDK` USqlScriptTestRunner elérési útját (cppSdkFolderFullPath: \@"").

**Mit jelent a CPPSDK?**

CPPSDK egy csomagot, amely magában foglalja a Microsoft Visual C++ 14 és a Windows SDK 10.0.10240.0. Ez az a környezet, amely szükséges a U-SQL futtatókörnyezete. Ez a csomag az Azure Data Lake Tools for Visual Studio telepítési mappa alatt kaphat:

- A Visual Studio 2015, alatt áll `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- A Visual Studio 2017, alatt áll `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Az Azure DevOps fordító-ügynökhöz az CPPSDK előkészítése**

A leggyakoribb módja az Azure DevOps CPPSDK függőség előkészítése a következőképpen történik:

1.  A ZIP a mappát, amely a CPPSDK kódtárait is tartalmazza.
2.  Ellenőrizze, hogy a zip-fájlt a forráskódú verziókezelő rendszer. (A zip-fájlt biztosítja, hogy ellenőrizze az összes szalagtárra a CPPSDK mappában, hogy az egyes fájlok nem ".gitignore" által figyelmen kívül hagyja.)   
3.  Bontsa ki a .zip fájlt, a létrehozási folyamat.
4.  Pont `USqlScriptTestRunner` ezt a build gép kicsomagolt mappájába.

### <a name="run-c-udo-test-cases-in-azure-devops"></a>C# UDO vizsgálati eset futtatása az Azure DevOps

C# UDO teszteléséhez ügyeljen arra, hogy az alábbi szerelvényeket, amelyek szükségesek a udo-k hivatkozhat. Ha azokhoz [a Nuget-csomag Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), mindenképpen adja hozzá a NuGet-visszaállítási feladat a buildelési folyamat.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Analytics CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md)
- [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
- [Használata U-SQL database-projekt U-SQL-adatbázis fejlesztéséhez](data-lake-analytics-data-lake-tools-develop-usql-database.md)

