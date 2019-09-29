---
title: U-SQL-parancsfájlok kiterjesztése R-Azure Data Lake Analytics
description: Megtudhatja, hogyan futtathat R-kódokat az U-SQL-szkriptekben Azure Data Lake Analytics használatával. R-kód beágyazása vagy hivatkozás a fájlokból.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672698"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>U-SQL-parancsfájlok kiterjesztése R-kóddal Azure Data Lake Analytics

Az alábbi példa az R-kód üzembe helyezésének alapvető lépéseit szemlélteti:
* A `REFERENCE ASSEMBLY` utasítás használatával engedélyezze az R-bővítményeket a U-SQL-parancsfájlhoz.
* A `REDUCE` művelettel particionálhatja a bemeneti adatokat egy kulcson.
* Az U-SQL R-bővítményei közé tartozik egy beépített szűkítő (`Extension.R.Reducer`), amely az R-kódot futtatja a szűkítőhöz rendelt minden egyes csúcsponton. 
* A `inputFromUSQL` és `outputToUSQL` nevű dedikált adatkeretek használata az U-SQL és az R közötti adatok továbbításához. a bemeneti és kimeneti DataFrame-azonosítók neve rögzített (azaz a felhasználók nem változtathatják meg a bemeneti és kimeneti DataFrame-azonosítók előre megadott nevét).

## <a name="embedding-r-code-in-the-u-sql-script"></a>R-kód beágyazása a U-SQL-parancsfájlba

A U-SQL-szkript R-kódját a `Extension.R.Reducer` parancssori paraméterrel lehet beágyazottan használni. Deklarálhatja például az R-szkriptet karakterlánc-változóként, és paraméterként átadhatja a Szűkítőnek.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Az R-kód megtartása egy külön fájlban, és hivatkozása a U-SQL-parancsfájlra

Az alábbi példa egy összetettebb használatot mutat be. Ebben az esetben az R-kód a U-SQL-szkriptként üzemelő ERŐFORRÁSként van telepítve.

Az R-kód mentése különálló fájlként.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Egy U-SQL-parancsfájllal telepítse az R-szkriptet az üzembe helyezési erőforrás-utasítással.

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

## <a name="how-r-integrates-with-u-sql"></a>Az R integrálása az U-SQL-sel

### <a name="datatypes"></a>Adattípusok
* A U-SQL karakterlánc-és numerikus oszlopai az R DataFrame és U-SQL [támogatott típusok: `double`, `string`, `bool`, `integer`, `byte`].
* A `Factor` adattípus nem támogatott az U-SQL-ben.
* a `byte[]` Base64 kódolású `string` kell legyen szerializálva.
* Az u-SQL-karakterláncok az R-kódban szereplő tényezőkre konvertálhatók, ha U-SQL-t hoz létre az R bemeneti dataframe, vagy a csökkentő paramétert `stringsAsFactors: true` értékre állítja.

### <a name="schemas"></a>Sémák
* Az U-SQL-adatkészletek nem rendelkezhetnek ismétlődő oszlopnevek.
* Az U-SQL-adatkészletek oszlopainak karakterláncoknak kell lenniük.
* Az oszlopnevek a U-SQL és az R parancsfájlok esetében azonosnak kell lenniük.
* A írásvédett oszlop nem lehet a kimeneti dataframe része. Mivel az írásvédett oszlopok automatikusan bekerülnek a U-SQL-táblába, ha az a UDO kimeneti sémájának része.

### <a name="functional-limitations"></a>Működési korlátai
* Az R-motor nem hozható létre kétszer ugyanabban a folyamatban. 
* Jelenleg az U-SQL nem támogatja a Udo-t a redukáló Udo használatával generált particionált modellek használatával. A felhasználók a particionált modelleket erőforrásként deklarálják, és az R-szkriptben használhatják őket (lásd: mintakód @no__t – 0)

### <a name="r-versions"></a>R-verziók
Csak az R 3.2.2 támogatott.

### <a name="standard-r-modules"></a>Standard R-modulok

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

### <a name="input-and-output-size-limitations"></a>Bemeneti és kimeneti méretre vonatkozó korlátozások
Minden csúcspont korlátozott mennyiségű memóriát rendel hozzá. Mivel a bemeneti és a kimeneti DataFrames léteznie kell az R-kód memóriájában, a bemenet és a kimenet teljes mérete nem haladhatja meg a 500 MB-ot.

### <a name="sample-code"></a>Mintakód
Az U-SQL Advanced Analytics Extensions telepítése után több mintakód is elérhető a Data Lake Store-fiókjában. A további mintakód elérési útja a következő: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Egyéni R-modulok üzembe helyezése U-SQL-vel

Először hozzon létre egy egyéni R-modult és zip-fájlt, majd töltse fel a tömörített R egyéni modult az ADL áruházba. A példában a magittr_ 1.5. zip-fájlt feltöltjük az alapértelmezett ADLS-fiók gyökerébe az általunk használt ADLA-fiókhoz. Miután feltöltötte a modult az ADL Store-ba, állapítsa meg, hogy használja az erőforrás üzembe helyezése lehetőséget, hogy elérhető legyen a U-SQL-parancsfájlban, és hívja meg a `install.packages` parancsot a telepítéshez.

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
* [A U-SQL Window functions használata Azure Data Lake Analytics feladatokhoz](data-lake-analytics-use-window-functions.md)
