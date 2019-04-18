---
title: Az r nyelv, az Azure Data Lake Analytics U-SQL-szkriptek kiterjesztése
description: Ismerje meg az R-kód használata Azure Data Lake Analytics U-SQL-parancsfájlok futtatása
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 59a52b2aeb83732a608f1fcf5bc4de907d25dfd1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885026"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Az R-kód az Azure Data Lake Analytics U-SQL-szkriptek kiterjesztése

Az alábbi példa az R-kód telepítésének alapvető lépéseit mutatja be:
* Használja a `REFERENCE ASSEMBLY` R-bővítmények engedélyezéséhez a U-SQL parancsfájl-utasítást.
* Használja a `REDUCE` művelet egy kulcsot a bemeneti adatok particionálásához.
* A U-SQL R-bővítmények közé tartozik egy beépített nyomáscsökkentő (`Extension.R.Reducer`) futó R-kód minden csúcspont a nyomáscsökkentő rendelve. 
* Dedikált használatának nevű nevű adatkeretek `inputFromUSQL` és `outputToUSQL` jelölik, U-SQL, r-bemenet közötti átadni az adatokat, és azonosító nevek rögzítettek DataFrame kimeneti (azt jelenti, felhasználókat nem lehet módosítani ezeket előre definiált nevek a bemeneti és kimeneti DataFrame azonosítók).

## <a name="embedding-r-code-in-the-u-sql-script"></a>A U-SQL-szkript R-kód beágyazása

Az R-kód a U-SQL parancsfájl, a parancs paraméter használatával beágyazott is a `Extension.R.Reducer`. Például deklaráljon egy karakterlánc-változóhoz, az R-szkript, és a egy paraméterként átadni a nyomáscsökkentő.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Az R-kód tartsa egy különálló fájlban, és hivatkozzon arra a U-SQL parancsfájl

A következő példa bemutatja egy összetettebb használatára. Ebben az esetben az R-kód, amely a U-SQL parancsfájl erőforrásként van telepítve.

Mentse az R-kód egy különálló fájlban.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

A U-SQL parancsfájl használatával, hogy az erőforrás üzembe helyezése utasítással R-szkript üzembe helyezése.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Hogy az R hogyan integrálható a U-SQL

### <a name="datatypes"></a>Adattípusok
* Konvertálja a karakterlánc és a numerikus oszlopok a U-SQL-R DataFrame és a U-SQL közötti [támogatott típusok: `double`, `string`, `bool`, `integer`, `byte`].
* A `Factor` U-SQL-adattípus nem támogatott.
* `byte[]` kézjegyként base64-kódolású `string`.
* U-SQL-karakterláncok tényezők az R-kódot, miután a U-SQL R bemeneti dataframe hozzon létre vagy nyomáscsökkentő paraméterének beállításával lehet alakítani `stringsAsFactors: true`.

### <a name="schemas"></a>Sémák
* U-SQL-adatkészletek nem lehet ismétlődő oszlopneveket tartalmaz.
* U-SQL-adatkészletek oszlopnevek karakterláncokat kell lennie.
* Oszlopnevek a U-SQL, R-szkriptek azonosnak kell lennie.
* Csak olvasható oszlopot nem lehet a kimeneti dataframe része. Mivel a csak olvasható oszlopot a rendszer automatikusan alkalmazza újra az U-SQL-táblát Ha UDO kimeneti sémája része.

### <a name="functional-limitations"></a>Működési korlátai
* Az R-motort kétszer ugyanazt a folyamatot a nem hozható létre. 
* Jelenleg U-SQL nem támogatja a particionált, nyomáscsökkentő udo-k használatával létrehozott modellek segítségével előrejelzési egyesítő udo-k. A felhasználók a particionált modellek deklarálja erőforrásként és használja azokat saját R-szkript (lásd: mintakód `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>R-verziók
Csak R 3.2.2 támogatott.

### <a name="standard-r-modules"></a>Standard szintű R-modulok

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Bemeneti és kimeneti fájlméretre vonatkozó korlátozások
Minden csúcspont korlátozott mennyiségű memória rendelve van. A bemeneti és kimeneti DataFrames szerepelniük kell az R-kód a memória, mert a bemeneti és kimeneti teljes mérete legfeljebb 500 MB.

### <a name="sample-code"></a>Mintakód
További mintakód a Data Lake Store-fiókban érhető el, a U-SQL Advanced Analytics extensions telepítését követően. További mintakód elérési útja: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Az U-SQL egyéni R-modulok telepítése

Először hozzon létre egy egyéni R modult és azt a zip, és majd töltse fel a tömörített R modul egyéni fájlt az ADL-tároló. A példában azt magittr_1.5.zip feltölti az ADLA fiókhoz fogjuk használni az alapértelmezett ADLS-fiók gyökérmappájában. Miután feltölti a modul az ADL-tároló, deklarujte ho használati üzembe helyezése erőforrás elérhető legyen a U-SQL-szkript és a hívás `install.packages` telepítheti.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>További lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL-ablakfunkciók használata Azure Data Lake Analytics-feladatok](data-lake-analytics-use-window-functions.md)
