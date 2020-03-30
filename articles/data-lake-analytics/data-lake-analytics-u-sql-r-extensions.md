---
title: Az U-SQL-parancsfájlok kiterjesztése az R-rel az Azure Data Lake Analytics-ben
description: Ismerje meg, hogyan futtathat R-kódot U-SQL-parancsfájlokban az Azure Data Lake Analytics használatával. R-kód beágyazása szövegközi vagy hivatkozás fájlokból.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672698"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Az U-SQL-parancsfájlok kiterjesztése R-kóddal az Azure Data Lake Analytics szolgáltatásban

A következő példa az R-kód telepítésének alapvető lépéseit mutatja be:
* Használja `REFERENCE ASSEMBLY` az utasítást az R-bővítmények engedélyezéséhez az U-SQL parancsfájlhoz.
* A `REDUCE` művelet segítségével particionálja a bemeneti adatokat egy kulcson.
* Az U-SQL R-bővítményei tartalmaznak egy`Extension.R.Reducer`beépített szűkítőt ( ), amely r-kódot futtat a szűkítőhöz rendelt minden csúcsponton. 
* Dedikált névvel ellátott `inputFromUSQL` adatkeretek használata, illetve `outputToUSQL` az U-SQL és az R közötti adatátatovábbakerülés. A bemeneti és kimeneti DataFrame-azonosítónevek rögzítettek (azaz a felhasználók nem módosíthatják a bemeneti és kimeneti DataFrame-azonosítók előre definiált neveit).

## <a name="embedding-r-code-in-the-u-sql-script"></a>R-kód beágyazása az U-SQL parancsfájlba

Az R-kódot a `Extension.R.Reducer`. Például deklarálhatja az R parancsfájlt karakterláncváltozóként, és paraméterként átadhatja a Szűkítőnek.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Tartsa meg az R-kódot egy külön fájlban, és hivatkozzon rá az U-SQL parancsfájlra

A következő példa összetettebb használatot szemléltet. Ebben az esetben az R-kód az U-SQL parancsfájl erőforrásként van telepítve.

Mentse ezt az R-kódot külön fájlként.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

U-SQL-parancsfájl használatával telepítse az R-parancsfájlt a DEPLOY RESOURCE utasítással.

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

## <a name="how-r-integrates-with-u-sql"></a>Hogyan integrálható az R az U-SQL-rel?

### <a name="datatypes"></a>Adattípusok
* Az U-SQL karakterlánc- és numerikus oszlopai az R DataFrame `double`és `string` `bool`az `integer` `byte`U-SQL között az R DataFrame és az U-SQL [támogatott típusok: , , , . ] között konvertálódnak.
* Az `Factor` u-SQL nem támogatja az adattípust.
* `byte[]`base64-kódolású szerializálásra `string`van kell, hogy legyen.
* Az U-SQL karakterláncok átalakíthatók Az R-kód ban szereplő tényezőkké, miután `stringsAsFactors: true`az U-SQL létrehozza az R bemeneti adatkeretet, vagy a szűkítő paraméter beállításával.

### <a name="schemas"></a>Sémák
* Az U-SQL adatkészletek nem rendelkezhetnek ismétlődő oszlopnevekkel.
* Az U-SQL adatkészletek oszlopnevének karakterláncnak kell lennie.
* Az oszlopneveknek meg kell egyeznek az U-SQL és r parancsfájlokban.
* A csak olvasható oszlop nem lehet része a kimeneti adatkeretnek. Mivel az írásvédett oszlopok automatikusan visszakerülnek az U-SQL táblába, ha az az UDO kimeneti sémájának része.

### <a name="functional-limitations"></a>Funkcionális korlátozások
* Az R motor t nem lehet kétszer példányosan ugyanabban a folyamatban. 
* Jelenleg az U-SQL nem támogatja a Combiner UDOs előrejelzési használatával létrehozott particionálási modellek csökkentése rezsi udos használatával. A felhasználók a particionált modelleket erőforrásként deklarálhatják, és az R-parancsfájljukban használhatják őket (lásd a mintakódot) `ExtR_PredictUsingLMRawStringReducer.usql`

### <a name="r-versions"></a>R verziók
Csak az R 3.2.2 támogatott.

### <a name="standard-r-modules"></a>Szabványos R modulok

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

### <a name="input-and-output-size-limitations"></a>Bemeneti és kimeneti méretkorlátozások
Minden csúcsponthoz korlátozott mennyiségű memória van hozzárendelve. Mivel a bemeneti és kimeneti DataFrames-eknek létezniük kell az R-kód memóriájában, a bemenet és a kimenet teljes mérete nem haladhatja meg az 500 MB-ot.

### <a name="sample-code"></a>Példakód
Az U-SQL Advanced Analytics-bővítmények telepítése után további mintakód érhető el a Data Lake Áruházbeli fiókjában. A további mintakód `<your_account_address>/usqlext/samples/R`elérési útja: . 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Egyéni R-modulok telepítése U-SQL-rel

Először hozzon létre egy R egyéni modult, és zip, majd töltse fel a tömörített R egyéni modul fájlt az ADL tárolni. A példában a magittr_1.5.zip fájlt az általunk használt ADLA-fiók alapértelmezett ADLS-fiókjának gyökerébe töltjük fel. Miután feltöltötte a modult az ADL-tárolóba, deklarálja deploy erőforrásként, hogy elérhetővé tegye az U-SQL parancsfájlban, és hívja meg `install.packages` a telepítéséhez.

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

## <a name="next-steps"></a>Következő lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL ablakfüggvények használata az Azure Data Lake Analytics-feladatokhoz](data-lake-analytics-use-window-functions.md)
