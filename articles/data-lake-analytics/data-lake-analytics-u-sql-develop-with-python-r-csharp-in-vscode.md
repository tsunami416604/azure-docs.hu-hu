---
title: U-SQL-feladatok futtatása Pythonban, R-ben és C# nyelven – Azure Data Lake Analytics
description: Ismerje meg, hogyan használhatja a python, R és C# használatával a Python mögötti kódot az Azure Data Lake-ben.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: cb3ddf0c4147fa982e8ab0f9d440292d12803d35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309707"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Az U-SQL fejlesztése Python, R és C# nyelven az Azure Data Lake Analytics szolgáltatáshoz a Visual Studio-kódban
Megtudhatja, hogyan használhatja a Visual Studio Code (VSCode) python- és R- és C# kódot az U-SQL-fájllal, és hogyan küldhet idát el az Azure Data Lake szolgáltatásba. Az Azure Data Lake Tools for VSCode szolgáltatásról [az Azure Data Lake-eszközök használata a Visual Studio-kódhoz című témakörben](data-lake-analytics-data-lake-tools-for-vscode.md)talál további információt.

A kód mögötti egyéni kód írása előtt meg kell nyitnia egy mappát vagy munkaterületet a VSCode-ban.


## <a name="prerequisites-for-python-and-r"></a>A Python és az R előfeltételei
Regisztráljon Python- és R-bővítményekszerelvényeket az ADL-fiókhoz. 
1. Nyissa meg fiókját a portálon.
   - Válassza az **Áttekintés** lehetőséget. 
   - Kattintson **a Mintaparancsfájl gombra.**
2. Kattintson a **Továbbiak** gombra.
3. Válassza **az U-SQL extensions telepítése**lehetőséget. 
4. Az U-SQL-bővítmények telepítése után megerősítő üzenet jelenik meg. 

   ![A környezet beállítása python és R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > A Python és R nyelvi szolgáltatás legjobb felhasználói élménye érdekében telepítse a VSCode Python és R bővítményt. 

## <a name="develop-python-file"></a>Python-fájl fejlesztése
1. Kattintson az **Új fájl elemre** a munkaterületen.
2. Írja be a kódot az U-SQL.Write your code in U-SQL. Az alábbi kódminta látható.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Kattintson a jobb gombbal egy parancsfájlra, majd válassza **az ADL: Pythonkód létrehozása a fájl mögöttparancsot.** 
4. A **xxx.usql.py** fájl a munkamappában jön létre. Írja be a kódot python fájlba. Az alábbi kódminta látható.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Kattintson a jobb gombbal az **USQL** fájlban, és kattintson **a Script fordítása** vagy **a Feladat küldése** a futó feladatba parancsra.

## <a name="develop-r-file"></a>R-fájl fejlesztése
1. Kattintson az **Új fájl elemre** a munkaterületen.
2. Írja be a kódot az U-SQL fájlba. Az alábbi kódminta látható.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Kattintson a **USQL** jobb gombbal az USQL-fájlra, majd válassza az **ADL: Létrehozás R kód a fájl mögöttparancsot.** 
4. Az **xxx.usql.r** fájl a munkamappában jön létre. Írja be a kódot az R fájlba. Az alábbi kódminta látható.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Kattintson a jobb gombbal az **USQL** fájlban, és kattintson **a Script fordítása** vagy **a Feladat küldése** a futó feladatba parancsra.

## <a name="develop-c-file"></a>C# fájl fejlesztése
A háttérkódegyetlen U-SQL-parancsfájlhoz társított C# fájl. Az UDO, UDA, UDT és UDF parancsfájlt a háttérkódfájlban adhatja meg. Az UDO, UDA, UDT és UDF közvetlenül használható a parancsfájlban anélkül, hogy először regisztrálna a szerelvényt. A háttérkódfájl ugyanabba a mappába kerül, mint a társviszony-létesítési U-SQL parancsfájl. Ha a szkript neve xxx.usql, a kód mögött neve xxx.usql.cs. Ha manuálisan törli a háttérkódfájlt, a kód-mögötti funkció le van tiltva a kapcsolódó U-SQL parancsfájlesetében. Az U-SQL parancsfájl ügyfélkódjának írásáról az [Egyéni kód írása és használata az U-SQL: Felhasználó által definiált függvények]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)című témakörben talál további információt.

1. Kattintson az **Új fájl elemre** a munkaterületen.
2. Írja be a kódot az U-SQL fájlba. Az alábbi kódminta látható.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Kattintson a jobb gombbal az **USQL** fájlban, majd válassza az **ADL: Létrehozási CS-kód a fájl mögött parancsot.** 
4. A **xxx.usql.cs** fájl a munkamappában jön létre. Írja meg a kódot a CS fájlban. Az alábbi kódminta látható.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Kattintson a jobb gombbal az **USQL** fájlban, és kattintson **a Script fordítása** vagy **a Feladat küldése** a futó feladatba parancsra.

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL helyi futtatás és helyi hibakeresés a Visual Studio-kóddal](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Ismerkedés a Data Lake Analytics szolgáltatással a PowerShell használatával](data-lake-analytics-get-started-powershell.md)
* [Ismerkedés a Data Lake Analytics szolgáltatással az Azure Portal használatával](data-lake-analytics-get-started-portal.md)
* [A Data Lake Tools for Visual Studio használata U-SQL alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
* [A Data Lake Analytics (U-SQL) katalógus használata](data-lake-analytics-use-u-sql-catalog.md)
