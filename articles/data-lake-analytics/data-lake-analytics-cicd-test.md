---
title: Az Azure Data Lake Analytics kód tesztelése |} A Microsoft Docs
description: Ismerje meg, hogyan vizsgálati eset az U-SQL és a kiterjesztett C#-kód hozzáadása az Azure Data Lake Analytics.
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
ms.openlocfilehash: bfb314348caf1d5bf83c940c0bce79e87d6d2593
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889591"
---
# <a name="how-to-test-your-azure-data-lake-analytics-code"></a>Az Azure Data Lake Analytics kód tesztelése

Az Azure Data Lake U-SQL nyelv, amely ötvözi az deklaratív SQL C-vel imperatív bármilyen méretű adatok feldolgozásához biztosít. Ebből a dokumentumból megismerheti, hogyan vizsgálati eset létrehozása a U-SQL és C# UDO (felhasználó által megadott operátor) kiterjesztett kódot.

## <a name="test-u-sql-scripts"></a>U-SQL-parancsfájlok tesztelése

A U-SQL parancsfájl lefordított és végrehajtható kód optimalizálva, és gépek futtatása a felhőben vagy a helyi gépen. A fordítás és optimalizálási folyamata a teljes U-SQL parancsfájl végrehajtására teljes kezeli. Nem hajtható végre a hagyományos "test jednotky" minden utasítás. Azonban U-SQL-teszt SDK-t és a helyi Futtatás SDK használatával teheti parancsfájl-szintű teszteket.

### <a name="create-test-cases-for-u-sql-script"></a>A U-SQL parancsfájl tesztelési eseteket hoznak létre.

Az Azure Data Lake Tools for Visual Studio hozhat létre a U-SQL parancsfájl tesztelési esetek jó élményt nyújt.

1.  Kattintson a jobb gombbal egy U-SQL parancsfájl a Megoldáskezelőben, és válassza a **létrehozása egységtesztet**.
2.  Konfigurálja úgy, hogy hozzon létre egy új tesztelő projektet, vagy helyezze be a vizsgálati eset egy meglévő tesztelő projektet.

    ![A Data Lake Tools for Visual Studio u-sql tesztelési projekt létrehozása](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![A Data Lake Tools for Visual Studio létrehozásához u-sql projekt tesztkonfiguráció](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-test-data-source"></a>Teszt adatforrás kezelése

Ha a U-SQL-parancsfájlok tesztelése, a teszt bemeneti fájlok szükségesek. Ezek kezelheti Tesztadatok konfigurálásával **tesztelése adatforrás** U-SQL projekt tulajdonság. Meghívásakor a `Initialize()` felület SDK-t és egy átmeneti helyi adatok legfelső szintű mappát U-SQL-teszt alatt tesztelő projektet munkakönyvtár jön létre, és minden fájlok és almappák (és almappák fájlokat) a teszt adatok forrásmappában található másolja az ideiglenes helyi adatok legfelső szintű mappa U-SQL parancsfájl vizsgálati eset futtatása előtt. További vizsgálat adatok forrása mappákat is hozzáadhat köpködni teszt data mappa elérési útja pontosvesszővel válassza el.

![A Data Lake Tools for Visual Studio projekt test-adatforrás konfigurálása](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-database-environment-for-test"></a>Tesztelési adatbázis-környezet kezelése

Ha a U-SQL-parancsfájlok használata vagy a lekérdezés U-SQL adatbázis-objektumok, például a tárolt eljárások hívása akkor szüksége az adatbázis-környezetének inicializálása a U-SQL-vizsgálati eset futtatása előtt. A `Initialize()` felület SDK-U-SQL-teszt segítséget nyújt a U-SQL-projektet az ideiglenes helyi adatok gyökérmappájában tesztelő projektet munkakönyvtár által hivatkozott összes adatbázis üzembe helyezéséhez. 

Tudjon meg többet [hogyan kezelheti a U-SQL database-projektek, U-SQL projekt mutató hivatkozások](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Vizsgálati eredmények ellenőrzése

A `Run()` felület feladat-végrehajtási eredményt adja vissza, a 0 azt jelenti, hogy sikeres legyen, és 1 azt jelenti, hogy nem sikerült. Assert függvények C# használatával ellenőrizze a kimenetek. 

### <a name="execute-test-cases-in-visual-studio"></a>Hajtsa végre a vizsgálati eset a Visual Studióban

U-SQL parancsfájl tesztelési projekt C# egység teszt keretrendszer épül. A build után a projekt futtatása az összes vizsgálati eset **tesztelése Explorer > lista**, vagy kattintson a jobb gombbal a .cs fájlt, és válassza a **tesztek futtatása**.

## <a name="test-c-udos"></a>C# udo-k tesztelése

### <a name="create-test-cases-for-c-udos"></a>Vizsgálati eset létrehozása a C# udo-k

A C# UDOs(User-Defined Operator) teszteléséhez használhatja a C# egység teszt keretében. Ha teszteli az udo-k, elő kell készíteni a megfelelő **IRowset** objektum bemenetként.

Kétféleképpen hozhat létre IRowset:

1.  Adatok betöltése a IRowset létrehozása fájlból

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

2.  Adatgyűjtés IRowset létrehozása származó adatok használata

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

Po volání UDO-funkciók, az eredmény séma és a sorkészlet érték ellenőrzési C# Vyhodnocení függvények használatával ellenőrizheti. Mintakód a U-SQL C# UDO egység teszt Mintaprojektet keresztül lehet **fájl > Új > projekt** a Visual Studióban.

### <a name="execute-test-cases-in-visual-studio"></a>Hajtsa végre a vizsgálati eset a Visual Studióban

A build után a tesztelési projekt, futtathatja az összes vizsgálati eset azonban **tesztelése Explorer > lista**, vagy kattintson a jobb gombbal a .cs fájlt, és válassza a **tesztek futtatása**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>A Visual Studio Team Service vizsgálati eset futtatása

Mindkét **U-SQL parancsfájl tesztelési projekt** és **C# UDO tesztelő projektet** öröklik a C# egység tesztelő projektet. [A Visual Studio Test feladat](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) a Visual Studio Team Service futtathatja a vizsgálati eset. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>A Visual Studio Team Service U-SQL vizsgálati eset futtatása

U-SQL-teszt, ellenőrizze, hogy betöltötte `CPPSDK` a build-gép, és adja meg a `CPPSDK` USqlScriptTestRunner elérési útját (cppSdkFolderFullPath: @"").

**Mit jelent a CPPSDK?**

CPPSDK a csomag tartalmazza a Microsoft Visual C++ 14 és a Windows SDK 10.0.10240.0, amelyet a környezet a U-SQL futtatókörnyezet igényli. Megtekintheti a csomag alatt az Azure Data Lake Tools for Visual Studio telepítési mappa:

- A Visual Studio 2015, alatt áll `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- A Visual Studio 2017, alatt áll `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**A Visual Studio Team Services build ügynök CPPSDK előkészítése**

A közös való előkészítéséhez a CPPSDK függőség a Visual Studio Team Service módja:

1.  A mappa zip CPPSDK kódtárait is tartalmazza.
2.  Ellenőrizze, hogy a zip-fájlt a forráskódú verziókezelő rendszer. (Zip-fájlt is győződjön meg arról, ellenőrizheti a tárakat CPPSDK mappában, vagy egyes fájlok ".gitignore" figyelmen kívül.)
3.  Csomagolja ki a zip-fájl felépítési folyamat.
4.  Pont `USqlScriptTestRunner` ezt a build gép kicsomagolt mappájába.

### <a name="run-c-udo-test-cases-in-visual-studio-team-service"></a>A Visual Studio Team Services a C# UDO vizsgálati eset futtatása

C# UDO-teszt ügyeljen arra, hogy hivatkozik az alábbi szerelvényeket, udo-k esetén szükséges. Ha azokhoz [a Nuget-csomag Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), mindenképpen adja hozzá a NuGet-visszaállítási feladat a buildelési folyamat.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Analytics CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md)
- [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
- [Használata U-SQL database-projekt U-SQL-adatbázis fejlesztéséhez](data-lake-analytics-data-lake-tools-develop-usql-database.md)

