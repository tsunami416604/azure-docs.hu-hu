---
title: Fejlesztés Python, R és a C# nyelv, a Visual Studio Code-ban az Azure Data Lake Analytics U-SQL
description: Ismerje meg a háttérkód használatát a Python, R és a C# elküldeni a feladatot az Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 6c234ad6756f4e65e172bf0ffc0ae5a1d35d109b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087482"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Fejlesztés Python, R és a C# nyelv, a Visual Studio Code-ban az Azure Data Lake Analytics U-SQL
Írhat a Python használata a Visual Studio Code (VSCode), az R- és C# code mögött az U-SQL és a feladatok az Azure Data Lake-szolgáltatásokba való elküldéséhez. VSCode-Azure Data Lake Tools kapcsolatos további információkért lásd: [használata az Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Mielőtt háttérkód egyéni kód írása, meg kell nyitnia egy mappa vagy a munkaterület a vscode-ban.


## <a name="prerequisites-for-python-and-r"></a>Python- és R előfeltételei
Python és az R bővítmények szerelvényeket az ADL-fiók regisztrálása. 
1. Nyissa meg a fiók portálon.
   - Válassza az **Áttekintés** lehetőséget. 
   - Kattintson a **mintaparancsfájl**.
2. Kattintson a **további**.
3. Válassza ki **U-SQL-bővítmények telepítése**. 
4. Megerősítő üzenet jelenik meg a U-SQL-bővítmények telepítése után. 

   ![A python és az R környezet beállítása](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > A Python és az R nyelv szolgáltatás a lehető legjobb élményt telepítse a VSCode Python- és R-bővítményt. 

## <a name="develop-python-file"></a>Fejlesztés Python-fájlt
1. Kattintson a **új fájl** a munkaterületén.
2. A kód írása az U-SQL-ben. Az alábbiakban látható mintakódot.
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
    
3. Kattintson a jobb gombbal egy olyan parancsfájlt, és válassza **ADL: Készítése a Python fájl mögötti kódban**. 
4. A **xxx.usql.py** fájl jön létre a munkamappában. A kód írása a Python-fájlt. Az alábbiakban látható mintakódot.

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
5. Kattintson a jobb gombbal a **USQL** fájlt, kattintson **összeállítása parancsfájl** vagy **feladat elküldése** futó feladat.

## <a name="develop-r-file"></a>R-fájl fejlesztése
1. Kattintson a **új fájl** a munkaterületén.
2. A kód írása a U-SQL-fájlban. Az alábbiakban látható mintakódot.
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
3. Kattintson a jobb gombbal a **USQL** fájlt, és válassza ki **ADL: Fájl mögötti R-kód előállítása**. 
4. A **xxx.usql.r** fájl jön létre a munkamappában. A kód írása az R-fájlban. Az alábbiakban látható mintakódot.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Kattintson a jobb gombbal a **USQL** fájlt, kattintson **összeállítása parancsfájl** vagy **feladat elküldése** futó feladat.

## <a name="develop-c-file"></a>Fejlesztés C# fájlban
A háttérkód-fájl egy U-SQL parancsfájl társított C# fájlban. Dedikált parancsfájl UDO, UDA, UDT és UDF-ben a háttérkód-fájlban definiálhat. A UDO, UDA, UDT és UDF-ben használható közvetlenül a szkriptben a szerelvény először regisztrálása nélkül. A háttérkód fájlt helyezni, a társviszony-létesítési U-SQL parancsfájl ugyanabban a mappában. Ha a parancsfájl xxx.usql neve, a háttérkód xxx.usql.cs, neve. Ha manuálisan törli a háttérkód-fájlt, a háttérkód funkció le van tiltva a társított U-SQL parancsfájl. További információ az ügyfél kódot a U-SQL-szkript: [írása és a U-SQL egyéni kód használatával: Felhasználó által definiált függvények]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Kattintson a **új fájl** a munkaterületén.
2. A kód írása a U-SQL-fájlban. Az alábbiakban látható mintakódot.
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
3. Kattintson a jobb gombbal a **USQL** fájlt, és válassza ki **ADL: Hozzon létre a fájl mögötti kódban CS**. 
4. A **xxx.usql.cs** fájl jön létre a munkamappában. A kód írása a CS-fájlban. Az alábbiakban látható mintakódot.

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
5. Kattintson a jobb gombbal a **USQL** fájlt, kattintson **összeállítása parancsfájl** vagy **feladat elküldése** futó feladat.

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL helyi futtatása és a hibakeresés a Visual Studio Code-dal](data-lake-tools-for-vscode-local-run-and-debug.md)
* [PowerShell-lel a Data Lake Analytics használatának első lépései](data-lake-analytics-get-started-powershell.md)
* [Ismerkedés a Data Lake Analytics az Azure portal használatával](data-lake-analytics-get-started-portal.md)
* [A Data Lake Tools for Visual Studio használata a U-SQL-alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake használata Analytics(U-SQL) katalógus](data-lake-analytics-use-u-sql-catalog.md)
