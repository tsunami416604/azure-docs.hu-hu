---
title: Az Azure Data Factory-ből a Spark-programok meghívása |} A Microsoft Docs
description: Ismerje meg, hogyan lehet egy Azure data factoryt a Spark-programok meghívása a MapReduce-tevékenység használatával.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: eb46347e82063d2e990b319ab108cf257c7e6b88
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440626"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Az Azure Data Factory-folyamatok Spark-programok meghívása

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-tevékenység](data-factory-hive-activity.md)
> * [Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce-tevékenység](data-factory-map-reduce.md)
> * [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
> * [Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update Resource-tevékenységek](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás régebbi verzióját használja, lásd: [adatátalakítás az Apache Spark-tevékenység használatával a Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Bevezetés
A Spark-tevékenység az egyik a [adat-átalakítási tevékenységeket](data-factory-data-transformation-activities.md) Data Factory által támogatott. Ezt a tevékenységet a megadott Spark-programot futtat az Azure HDInsight Spark-fürthöz. 

> [!IMPORTANT]
> - A Spark-tevékenység, amely elsődleges tárolóként használnia az Azure Data Lake Store HDInsight Spark-fürtökön nem támogatja.
> - A Spark-tevékenységet támogatja a csak meglévő (saját) HDInsight Spark-fürtökön. Egy igény szerinti HDInsight társított szolgáltatás nem támogatott.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Forgatókönyv: Egy Spark-tevékenységgel rendelkező folyamat létrehozása
Hozzon létre egy data factory-folyamatot egy Spark-tevékenységgel a tipikus lépései a következők: 

* Adat-előállító létrehozása
* Hozzon létre egy Azure Storage társított szolgáltatás, a tároló a HDInsight Spark-fürt a data factoryhoz társított tárolót.
* A Spark-fürt a HDInsight összekapcsolása a data factory a HDInsight társított szolgáltatás létrehozása.
* Hozzon létre egy adatkészletet, amely a Storage-beli társított szolgáltatásra vonatkozik. Jelenleg kell adnia egy tevékenység kimeneti adatkészletet akkor is, ha nincs semmilyen kimenet előállítása. 
* A létrehozott HDInsight-beli társított szolgáltatásra vonatkozik, Spark-tevékenységgel rendelkező folyamat létrehozása. A tevékenység az adatkészlet kimeneti adatkészletként az előző lépésben létrehozott van konfigurálva. A rendszer a kimeneti adatkészlet vezérli az ütemezést (óránként, naponta). A kimeneti adatkészlet tehát meg kell adnia annak ellenére, hogy a tevékenység nem igazán állít elő kimenetet.

### <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy általános célú tárfiókot utasításait követve [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

1. A HDInsight Spark-fürt létrehozása az oktatóanyag utasításait követve [a HDInsight Spark-fürt létrehozása](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). A fürt az 1. lépésben létrehozott storage-fiók társításához.

1. Töltse le és tekintse át a Python-parancsfájl tárolásához **test.py** található [ https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py ](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Töltse fel **test.py** , a **pyFiles** mappájában a **adfspark** tárolót a blob storage-ban. Hozzon létre a tárolót és a mappa, ha azok nem léteznek.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Adat-előállító létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Kattintson az **Új** > **Adatok + analitika** > **Adat-előállító** elemre.

1. Az a **új adat-előállító** panel alatt **neve**, adja meg **SparkDF**.

   > [!IMPORTANT]
   > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibát látja, "Data factory SparkDF név nem érhető el", módosítsa az adat-előállító nevét. Például yournameSparkDFdate használja, és hozza létre újra az adat-előállítóban. További információ az elnevezési szabályokról: [A Data Factory elnevezési szabályai](data-factory-naming-rules.md).

1. Az **Előfizetés** területen válassza ki azt az Azure-előfizetést, ahol létre kívánja hozni az adat-előállítót.

1. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy Azure-erőforráscsoportot.

1. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet.

1. Kattintson a **Létrehozás** gombra.

   > [!IMPORTANT]
   > Data Factory-példányok létrehozásához a [Data Factory közreműködője](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.

1. Létrehozás az Azure portal irányítópultján láthatja az adat-előállítóban.

1. Az adat-előállító sikeres létrehozása után megjelenik az **Adat-előállító** oldal, amely megjeleníti az adat-előállító tartalmát. Ha nem látja a **adat-előállító** lapon, kattintson a csempére a data Factory, az irányítópulton.

    ![A Data Factory panel](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben két társított szolgáltatást hoz létre. Az adat-előállító egy szolgáltatás hivatkozik a Spark-fürthöz, és a többi szolgáltatás hivatkozásokat tartalmaz a tároló az adat-előállító. 

#### <a name="create-a-storage-linked-service"></a>Storage-beli társított szolgáltatás létrehozása
Ebben a lépésben társítja a tárfiókot az adat-előállítójához. Az útmutató későbbi részeiben egy lépésben létrehoz egy adatkészletet a társított szolgáltatásra vonatkozik. A HDInsight társított szolgáltatás a következő lépésben meghatározó túl a társított szolgáltatás hivatkozik. 

1. Az a **adat-előállító** panelen válassza ki **készítése és üzembe helyezése**. A Data Factory Editor jelenik meg.

1. Kattintson az **Új adattár** elemre, és válassza az **Azure Storage** lehetőséget.

   ![Új adattároló](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. A JSON-parancsfájl használatával hozzon létre egy Storage társított szolgáltatás jelenik meg a szerkesztőben.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Cserélje le **fióknév** és **fiókkulcs** az a tárfiók nevét és hívóbetűjét. A tárelérési kulcs lekéréséről [a tárfiók kezelését](../../storage/common/storage-create-storage-account.md#manage-your-storage-account) ismertető szakasz tárelérési kulcsok megtekintéséről, másolásáról és ismételt létrehozásáról szóló részében olvashat.

1. A társított szolgáltatás üzembe helyezéséhez válassza **telepítés** a parancssávon. A társított szolgáltatás sikeres üzembe helyezése után megjelenik a Draft-1 (Vázlat-1) ablak. Az ablak bal oldalán, fanézetben látható az **AzureStorageLinkedService** szolgáltatás.

#### <a name="create-an-hdinsight-linked-service"></a>HDInsight társított szolgáltatás létrehozása
Ebben a lépésben hozzon létre egy HDInsight társított szolgáltatást, hogy a HDInsight Spark-fürt a data factoryhoz. A HDInsight-fürt futtatására a Spark-tevékenységet a folyamat ebben a példában a megadott Spark-program szolgál. 

1. A Data Factory Editorban válassza **további** > **új számítási** > **HDInsight-fürt**.

    ![HDInsight társított szolgáltatás létrehozása](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-szerkesztő tegye a következőket:

    a. Adja meg az URI-t a HDInsight Spark-fürt. Például: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Adja meg a felhasználó, aki hozzáfér a Spark-fürt nevére.

    c. Adja meg a felhasználó jelszava.

    d. Adja meg a Storage-beli társított szolgáltatás, amely a HDInsight Spark-fürt társítva van. Ebben a példában az AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - A Spark-tevékenység, amely elsődleges tárolóként használnia az Azure Data Lake Store HDInsight Spark-fürtökön nem támogatja.
    > - A Spark-tevékenységet támogatja a csak meglévő (saját) HDInsight Spark-fürtökön. Egy igény szerinti HDInsight társított szolgáltatás nem támogatott.

    A HDInsight társított szolgáltatással kapcsolatos további információkért lásd: [HDInsight társított szolgáltatás](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. A társított szolgáltatás üzembe helyezéséhez válassza **telepítés** a parancssávon. 

### <a name="create-the-output-dataset"></a>A kimeneti adatkészlet létrehozása
A rendszer a kimeneti adatkészlet vezérli az ütemezést (óránként, naponta). Ezért meg kell adnia a Spark-tevékenység a kimeneti adatkészlet a folyamat annak ellenére, hogy a tevékenység nem állít elő semmilyen kimenetet. A tevékenység bemeneti adatkészlet megadása nem kötelező.

1. A Data Factory Editorban válassza a **Továbbiak** > **Új adatkészlet** > **Azure Blob-tároló** lehetőséget.

1. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-kódrészlet definiál egy nevű adatkészletet **OutputDataset**. Emellett megadhatja, hogy az eredmények tárolása nevű blob-tárolóban **adfspark** és a mappa neve **pyFiles/kimeneti**. Ahogy korábban említettük, ez az adatkészlet egy helyőrző adatkészletet. Ebben a példában a Spark-program nem állít elő semmilyen kimenetet. A **rendelkezésre állási** szakasz meghatározza, hogy a kimeneti adatkészlet előállítása naponta. 

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Az adatkészlet üzembe helyezéséhez válassza **telepítés** a parancssávon.


### <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy folyamatot egy olyan HDInsightSpark tevékenységgel. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. Nincsenek bemeneti adatkészlet tehát ebben a példában van megadva.

1. A Data Factory Editorban válassza a **Továbbiak** > **Új folyamat** lehetőséget.

1. Cserélje le a parancsfájlt a Draft-1 ablakban az alábbi parancsfájlt:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Vegye figyelembe a következő szempontokat:

    a. A **típus** tulajdonsága **HDInsightSpark**.

    b. A **rootPath** tulajdonsága **adfspark\\pyFiles** ahol adfspark a blob-tároló, pyFiles pedig fájl mappát a tárolóban. Ebben a példában a blob storage lesz, amely a Spark-fürt társítva van. Feltöltheti a fájlt egy másik tárfiókba. Ha így tesz, a storage-fiók összekapcsolása a data factory Storage társított szolgáltatás létrehozása. Ezután adja meg a társított szolgáltatás neve értékeként a **sparkJobLinkedService** tulajdonság. Ez a tulajdonság és egyéb tulajdonságok, a Spark-tevékenység által támogatott kapcsolatos további információkért lásd: [Spark-tevékenység tulajdonságai](#spark-activity-properties).

    c. A **entryFilePath** tulajdonsága **test.py**, azaz a Python-fájlt.

    d. A **getDebugInfo** tulajdonsága **mindig**, ami azt jelenti, a naplófájlok mindig létrehozott (sikeres vagy sikertelen).

    > [!IMPORTANT]
    > Azt javasoljuk, hogy nem ezzel a tulajdonsággal `Always` éles környezetben, ha a problémát elhárítani.

    e. A **kimenete** szakasz tartalmaz egy kimeneti adatkészletet. Meg kell adnia egy kimeneti adatkészletet, akkor is, ha a Spark-program nem állít elő semmilyen kimenetet. A kimeneti adatkészlet a folyamat (óránként, naponta) az ütemezés határozza meg. 

    A Spark-tevékenység által támogatott tulajdonságaival kapcsolatos további információkért lásd a szakasz [Spark-tevékenység tulajdonságai](#spark-activity-properties).

1. A folyamat üzembe helyezéséhez válassza **telepítés** a parancssávon.

### <a name="monitor-a-pipeline"></a>Folyamat monitorozása
1. Az a **adat-előállító** panelen válassza ki **Monitor & Manage** egy másik lapon a figyelési alkalmazás elindításához.

    ![Monitor & Manage csempe](media/data-factory-spark/monitor-and-manage-tile.png)

1. Módosítsa a **kezdési idő** tetején a szűrő **2/1/2017**, és válassza ki **alkalmaz**.

1. Csak egyetlen tevékenység ablak jelenik meg, mert nincs között a kezdési (2017-02-01) és befejezési (2017-02-02) a folyamat csak egyetlen napon. Győződjön meg arról, hogy az adatszelet az **készen** állapota.

    ![A folyamat figyelése](media/data-factory-spark/monitor-and-manage-app.png)

1. Az a **tevékenységablakok** válasszon ki egy tevékenységet a részleteinek a megtekintéséhez. Ha hiba történik, akkor részleteinek a megtekintéséhez a jobb oldali ablaktáblán.

### <a name="verify-the-results"></a>Az eredmények ellenőrzése

1. Indítsa el a HDInsight Spark-fürthöz tartozó Jupyter Notebookot a [ezen a webhelyen](https://CLUSTERNAME.azurehdinsight.net/jupyter). Is megnyithatja a fürt irányítópultja a HDInsight Spark-fürt, és indítsa el a Jupyter Notebookot.

1. Válassza ki **új** > **PySpark** új notebook elindításához.

    ![Új Jupyter notebook](media/data-factory-spark/jupyter-new-book.png)

1. Futtassa a következő parancsot a másolása és beillesztése a szöveget, és nyomja le a Shift + Enter billentyűkombinációt a második utasítás végén:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Győződjön meg arról, hogy látja-e az adatokat a hvac táblából. 

    ![Jupyter-lekérdezés eredményei](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article --> Részletes útmutatásért lásd a szakasz [futtatása Spark SQL-lekérdezés](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Hibaelhárítás
Mivel Ön getDebugInfo beállítása **mindig**, láthatja a pyFiles mappában a blobtárolókban található napló almappája. A log mappában a naplófájl további információkat tartalmaz. Ez a naplófájl különösen hasznos, ha hiba történik. Éles környezetben érdemes állítani **hiba**.

További hibaelhárítási, tegye a következőket:


1. Nyissa meg a következőt: `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Alkalmazás a YARN felhasználói felületén](media/data-factory-spark/yarnui-application.png)

1. Válassza ki **naplók** megkísérli egy, a Futtatás.

    ![Alkalmazások lap](media/data-factory-spark/yarn-applications.png)

1. A következő további információ a hibáról a oldalon láthatja:

    ![Naplózási hiba](media/data-factory-spark/yarnui-application-error.png)

Az alábbi szakaszok ismertetik a Spark-fürt és a Spark-tevékenység használata az adat-előállító a data factory-entitások.

## <a name="spark-activity-properties"></a>Spark-tevékenység tulajdonságai
Itt látható a minta JSON-definícióját egy Spark-tevékenységgel rendelkező folyamatot: 

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

A következő táblázat ismerteti a JSON-definíciójában használt JSON-tulajdonságokat.

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| név | A folyamat a tevékenység neve. | Igen |
| leírás | Az szöveg, amely leírja, hogy a tevékenység leírása. | Nem |
| type | Ez a tulajdonság a HDInsightSpark kell beállítani. | Igen |
| linkedServiceName | A HDInsight társított szolgáltatás, amelyen futtatja a Spark-program neve. | Igen |
| rootPath | A blobtárolót és a Spark-fájlt tartalmazó mappát. A fájlnév formátuma a kis-és nagybetűket. | Igen |
| entryFilePath | A gyökérmappában található azon a Spark kódcsomag relatív elérési útja. | Igen |
| Osztálynév | Alkalmazás fő Java/Spark-osztálya. | Nem |
| argumentumok | A Spark-program parancssori argumentumokat listája. | Nem |
| proxyUser | A felhasználói fiók megszemélyesítése a Spark-program végrehajtásához. | Nem |
| sparkConfig | Adja meg a Spark-konfiguráció szereplő tulajdonságok értékeit [Spark-konfigurációja: alkalmazástulajdonságok](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nem |
| getDebugInfo | Itt adhatja meg, ha a Spark log fájlokat másolja a HDInsight-fürt által használt tárterület (vagy) sparkJobLinkedService által megadott. Megengedett értékek: None, mindig, vagy hiba. Az alapértelmezett értéke None. | Nem |
| sparkJobLinkedService | A Storage társított szolgáltatás, amely tartalmazza a Spark, feladat-fájlt, a függőségeket és a naplókat. Ez a tulajdonság értékét nem adja meg, ha a tárolót a HDInsight-fürthöz társított szolgál. | Nem |

## <a name="folder-structure"></a>gyökérmappa-szerkezetében
A Spark-tevékenység nem támogatja a Pig, egy beágyazott parancsfájlja, és Hive-tevékenység végezze el. Spark-feladatok akkor is, mint a Pig/Hive-feladatok több bővíthető. A Spark-feladatok, megadhat több függőség például jar (a java OSZTÁLYÚTVONAL helyezett) csomagok, a Python-fájlok (a PYTHONPATH helyezett) és a többi fájlt.

A következő mappa-struktúra létrehozása a blob Storage a HDInsight társított szolgáltatás által hivatkozott. Ezután töltse fel a függő fájlokat a megfelelő almappákba által képviselt gyökérmappájában **entryFilePath**. Python-fájlok feltöltése a pyFiles almappa és a jar-fájlok a JAR-fájlok kivételével almappába a gyökérmappa. Futásidőben a Data Factory szolgáltatás vár a következő mappastruktúra a blob storage-ban: 

| Útvonal | Leírás | Szükséges | Típus |
| ---- | ----------- | -------- | ---- |
| . | A meghajtógyökér elérési útja a Spark-feladat, a storage-beli társított szolgáltatás. | Igen | Mappa |
| &lt;felhasználó által definiált &gt; | A Spark-feladat fájlja mutató elérési utat. | Igen | Fájl |
| . / jars | Ebben a mappában található összes fájlt feltölteni és a fürt a Java osztályútvonal elhelyezett. | Nem | Mappa |
| ./pyFiles | Ebben a mappában található összes fájlt feltölteni és a fürt a PYTHONPATH elhelyezett. | Nem | Mappa |
| . / fájlok | Ebben a mappában található összes fájlt feltölteni és a végrehajtó munkakönyvtár helyezni. | Nem | Mappa |
| . / archívum | Ebben a mappában található összes fájlt nincs tömörítve. | Nem | Mappa |
| . / naplók | A Spark-fürt naplóinak tárolására szolgáló mappa.| Nem | Mappa |

Íme egy példa, Storage, amely tartalmazza a két Spark-feladat fájljait a blob Storage a HDInsight társított szolgáltatás által hivatkozott:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
