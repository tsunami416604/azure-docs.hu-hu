---
title: "Kiterjesztése az r az Azure Data Lake Analytics U-SQL-parancsfájlok |} Microsoft Docs"
description: "Megtudhatja, hogyan R-kód U-SQL-parancsfájlok futtatása"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/20/2017
ms.author: saveenr
ms.openlocfilehash: d479af515566f497d9611e75426f6acb8f8276d9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>Oktatóanyag: Ismerkedés a U-SQL r kiterjesztése

A következő példa az R-kód telepítésének lépéseit mutatja be:
* Használja a `REFERENCE ASSEMBLY` R bővítmények engedélyezése a U-SQL parancsfájl utasítást.
* Használja a` REDUCE` művelet a bemeneti adatok a kulcs particionálásához.
* A U-SQL-R bővítmények közé tartozik egy beépített nyomáscsökkentő (`Extension.R.Reducer`) mindegyik csomópontra a nyomáscsökkentő rendelt futó R-kód. 
* Dedikált használatát nevű nevű adatkeretek `inputFromUSQL` és `outputToUSQL `rendre mechanizmusok adatok átadására közötti U-SQL és a R. bemeneti és kimeneti típusú azonosító neveket fix DataFrame számára (Ez azt jelenti, hogy felhasználók nem módosítható ezen előre meghatározott bemeneti neve és kimeneti DataFrame azonosítók).

## <a name="embedding-r-code-in-the-u-sql-script"></a>R-kód beágyazása a U-SQL-parancsfájl

Az R-kód a U-SQL parancsfájl parancs paraméterének használatával beágyazott is a `Extension.R.Reducer`. Például az R-parancsfájl, mint egy karakterlánc-változó deklarálható, és adja át paraméterként a nyomáscsökkentő.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Az R-kód tartsa külön fájlt, és hivatkozzon a U-SQL-parancsfájl

Az alábbi példában látható egy összetettebb használat. Ebben az esetben az R-kód, amely a U-SQL parancsfájl erőforrásként van telepítve.

Az R-kód külön fájlt mentse.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

U-SQL parancsfájl segítségével, hogy az erőforrás telepítése utasítással R-parancsfájl telepítése.

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

## <a name="how-r-integrates-with-u-sql"></a>Hogyan integrálható az R U-SQL

### <a name="datatypes"></a>Adattípusok
* Konvertálja a karakterláncot és a numerikus oszlopot a U-SQL-R DataFrame és a U-SQL közötti [támogatott típusok: `double`, `string`, `bool`, `integer`, `byte`].
* A `Factor` adattípus nem támogatott a U-SQL.
* `byte[]`a base64-kódolású kell szerializálhatók `string`.
* U-SQL-karakterláncok konvertálhatók tényezők az R-kód, amennyiben az U-SQL R bemeneti dataframe létrehozása vagy nyomáscsökkentő paramétert `stringsAsFactors: true`.

### <a name="schemas"></a>Sémák
* U-SQL adatkészletek nem lehet ismétlődő oszlopneveket tartalmaz.
* U-SQL adatkészletek oszlopnevek karakterláncoknak kell lenniük.
* Oszlopnév a U-SQL és az R parancsfájlok azonosnak kell lennie.
* Csak olvasható oszlop nem lehet a kimeneti dataframe része. Mivel a csak olvasható oszlop automatikusan beszúrta vissza a U-SQL táblázatban, ha UDO kimeneti sémája része.

### <a name="functional-limitations"></a>Működési korlátai
* Az R-motor kétszer ugyanazt a folyamatot a példánya nem hozható létre. 
* Jelenleg U-SQL nem támogatja nyomáscsökkentő udo-k segítségével generált particionált modellek segítségével előrejelzés egyesítő udo-k. Felhasználók deklarálja a particionált modellek erőforrásként és azok az R-parancsfájl használatát (lásd: mintakód `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>R-verziók
Csak R 3.2.2 esetén támogatott.

### <a name="standard-r-modules"></a>Standard R modul

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

### <a name="input-and-output-size-limitations"></a>Bemeneti és kimeneti méretkorlátai
Minden csomópont csak korlátozott mennyiségű memória rendelve van. A bemeneti és kimeneti DataFrames már léteznie kell az R-kód memóriája, mert a bemeneti és kimeneti teljes mérete legfeljebb 500 MB.

### <a name="sample-code"></a>Mintakód
További mintakód áll rendelkezésre a Data Lake Store-fiókot, a U-SQL Advanced Analytics extensions telepítését követően. További mintakód elérési útja: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>A U-SQL egyéni R modul telepítése

Először hozzon létre egy egyéni R modult, és azt a zip-, majd töltse fel az R egyéni modul zip fájlt az ADL áruházba. A példában magittr_1.5.zip feltölti azt használjuk ADLA fiók alapértelmezett ADLS-fiók gyökérkönyvtárában. Ha a modul ADL tárolójába feltöltött, deklarálja azt, tegye elérhetővé a U-SQL parancsfájl és hívás erőforrás központi telepítése segítségével `install.packages` a telepítéshez.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomePackages.txt";

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

## <a name="next-steps"></a>Következő lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL ablak függvények használata az Azure Data Lake Analytics-feladatok](data-lake-analytics-use-window-functions.md)
