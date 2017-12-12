---
title: "U-SQL Python, R és C# fejlesztése az Azure Data Lake Analytics a Visual Studio a kód |} Microsoft Docs"
description: "Megtudhatja, hogyan használható mögötti kódban Python, R és C# elküldeni a feladatot az Azure Data Lake."
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: 
editor: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/22/2017
ms.author: jejiang
ms.openlocfilehash: 8fd8decfde1220be6aaa099c3afb24e8c1eecce4
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Python, R és C# a Visual Studio Code az Azure Data Lake Analytics U-SQL fejlesztése
Visual Studio Code (VSCode) használata a Python írni, R és C# kód mögé U-SQL és Azure Data Lake szolgáltatás feladatok elküldéséhez. VSCode az Azure Data Lake Tools kapcsolatos további információkért lásd: [használja az Azure Data Lake Tools Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Háttérkód egyéni kód írása, előtt nyissa meg a mappa vagy egy munkaterület VSCode kell.


## <a name="prerequisites-for-python-and-r"></a>Python és R előfeltételei
Regisztrálja a Python és R bővítmények szerelvények ADL fiókjához. 
1. Nyissa meg a fiók portálon.
   - Válassza ki **áttekintése**. 
   - Kattintson a **mintaparancsfájl**.
2. Kattintson a **további**.
3. Válassza ki **U-SQL-bővítmények telepítése**. 
4. Jóváhagyást kérő üzenet jelenik meg a U-SQL-bővítmények telepítése után. 

  ![A környezet a python és R beállítása](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

  > [!Note]
  > A Python és az R nyelv szolgáltatás legjobb élmény telepítse VSCode Python és R bővítmény. 

## <a name="develop-python-file"></a>Python-fájl fejlesztése
1. Kattintson a **új fájl** a munkaterületen.
2. Írja be a kódot a U-SQL. A kód a minta a következő:
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
    
3. Kattintson a jobb gombbal egy olyan parancsfájlt, majd válassza ki **ADL: készítése Python mögött kódfájl**. 
4. A **xxx.usql.py** fájl a működő mappában jön létre. Írja be a kódot a Python-fájl. A kód a minta a következő:

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
5. Kattintson a jobb gombbal a **USQL** fájlt, kattintson **parancsfájl összeállításához** vagy **feladat elküldése** a feladat futtatását.

## <a name="develop-r-file"></a>R fájl fejlesztése
1. Kattintson a **új fájl** a munkaterületen.
2. Írja be a kódot a U-SQL-fájlban. A kód a minta a következő:
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
3. Kattintson a jobb gombbal a **USQL** fájlt, és válassza ki **ADL: készítése R mögött kódfájl**. 
4. A **xxx.usql.r** fájl a működő mappában jön létre. Írja be a kódot R fájlban. A kód a minta a következő:

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Kattintson a jobb gombbal a **USQL** fájlt, kattintson **parancsfájl összeállításához** vagy **feladat elküldése** a feladat futtatását.

## <a name="develop-c-file"></a>C# fájlban fejlesztése
A háttérkód fájlt egy U-SQL parancsfájl társított C# fájlban. Meghatározhatja egy dedikált parancsfájl UDO, uda-Értékeket, UDT vagy a háttérkód fájlban UDF. A UDO, uda-Értékeket, UDT és UDF használható közvetlenül a parancsfájlban a szerelvény először regisztrálása nélkül. A háttérkód fájl kerül, a mappában, amelyben a társviszony-létesítési U-SQL-parancsfájlt. Ha a parancsfájl xxx.usql neve, a háttérkód xxx.usql.cs, neve. Törölje kézzel a háttérkód fájlt, ha a háttérkód funkció le van tiltva, a társított U-SQL parancsfájlt. További információ a felhasználói kódot a U-SQL parancsfájl: [írási és egyéni kód használata U-SQL: felhasználó által definiált függvényeket]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Kattintson a **új fájl** a munkaterületen.
2. Írja be a kódot a U-SQL-fájlban. A kód a minta a következő:
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
3. Kattintson a jobb gombbal a **USQL** fájlt, és válassza ki **ADL: készítése CS mögött kódfájl**. 
4. A **xxx.usql.cs** fájl a működő mappában jön létre. Írja be a kódot a CS-fájlban. A kód a minta a következő:

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
5. Kattintson a jobb gombbal a **USQL** fájlt, kattintson **parancsfájl összeállításához** vagy **feladat elküldése** a feladat futtatását.

## <a name="next-steps"></a>Következő lépések
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL helyi futtatásával és a helyi hibakeresése a Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Azure Data Lake Analytics-feladatok U-SQL-szerelvények fejlesztése](data-lake-analytics-u-sql-develop-assemblies.md)
* [A Data Lake Analytics PowerShell használatának első lépései](data-lake-analytics-get-started-powershell.md)
* [Ismerkedés a Data Lake Analytics az Azure portál használatával](data-lake-analytics-get-started-portal.md)
* [Használja a Data Lake Tools for Visual Studio a U-SQL-alkalmazások fejlesztésével](data-lake-analytics-data-lake-tools-get-started.md)
* [Használja a Data Lake Analytics(U-SQL) katalógus](data-lake-analytics-use-u-sql-catalog.md)
