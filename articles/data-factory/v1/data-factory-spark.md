---
title: Az Azure Data Factory Spark programok meghívása |} Microsoft Docs
description: Megtudhatja, hogyan lehet meghívni egy az Azure data factory Spark programok a MapReduce művelethez használatával.
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
ms.openlocfilehash: e775798dbaaf93d5a9b497323a3b2fa365820550
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046464"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Az Azure Data Factory folyamatok Spark programok meghívása

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-tevékenység](data-factory-hive-activity.md)
> * [A Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce művelethez](data-factory-map-reduce.md)
> * [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
> * [A Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning frissítés erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [A tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatásnak jelenlegi verzióját használja, tekintse meg [adatok átalakítása az Apache Spark tevékenység használatával a Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Bevezetés
A Spark tevékenység egyike a [adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) adat-előállító által támogatott. Ez a tevékenység fut a megadott Spark programot az Azure hdinsight Spark-fürt. 

> [!IMPORTANT]
> - A Spark tevékenység nem támogatja a HDInsight Spark-fürtjei használja az Azure Data Lake Store elsődleges tárolására.
> - A Spark tevékenység támogatja a csak meglévő HDInsight Spark-fürtjei (saját). Egy igény szerinti HDInsight társított szolgáltatás nem támogatja.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Forgatókönyv: Hozzon létre egy folyamatot egy Spark-tevékenység
A data factory-folyamat létrehozása egy Spark tevékenységet a szokásos lépései a következők: 

* Adat-előállító létrehozása
* Hozzon létre csatolásához tárhelyét, a HDInsight Spark-fürt data factoryval való társított Azure Storage társított szolgáltatásnak.
* A hdinsight Spark-fürt csatolása az adat-előállítóban HDInsight társított szolgáltatás létrehozása.
* Hozzon létre egy adatkészlet, hogy a tárolás társított szolgáltatásának hivatkozik. Jelenleg adjon meg egy kimeneti adatkészlet a tevékenység akkor is, ha nincs folyamatban létrehozott kimenet van. 
* Hozzon létre egy folyamatot Spark tevékenység, amely a létrehozott HDInsight kapcsolódó szolgáltatásra hivatkozik. A tevékenység az adatkészletben, mint egy kimeneti adatkészletet az előző lépésben létrehozott van konfigurálva. A kimeneti adatkészlet működik (óránként, naponta) az ütemezést. A kimeneti adatkészletet, ezért meg kell adnia annak ellenére, hogy a tevékenység valóban nem eredményez kimenettel.

### <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy általános célú tárfiókot utasításait követve [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

2. A HDInsight Spark-fürt létrehozása az oktatóanyagban szereplő utasítások alapján [Spark-fürt létrehozása hdinsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Társítsa az 1. lépésben a fürthöz létrehozott tárfiók.

3. Töltse le, és tekintse át a Python-parancsfájl **test.py** helyen [ https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py ](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

4. Töltse fel **test.py** számára a **pyFiles** mappájában a **adfspark** a blob Storage tárolóban. A tároló és a mappa létrehozása, ha azok még nem léteznek.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Adat-előállító létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Kattintson az **Új** > **Adatok + analitika** > **Adat-előállító** elemre.

3. Az a **új adat-előállító** panel alatt **neve**, adja meg **SparkDF**.

   > [!IMPORTANT]
   > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibát látja, "adat-előállító SparkDF nem érhető el", módosítsa az adat-előállító nevét. Például yournameSparkDFdate használja, és hozza létre újra az adat-előállítóban. További információ az elnevezési szabályokról: [A Data Factory elnevezési szabályai](data-factory-naming-rules.md).

4. Az **Előfizetés** területen válassza ki azt az Azure-előfizetést, ahol létre kívánja hozni az adat-előállítót.

5. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy Azure-erőforráscsoportot.

6. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet.

7. Kattintson a **Létrehozás** gombra.

   > [!IMPORTANT]
   > Data Factory-példányok létrehozásához a [Data Factory közreműködője](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.

8. Az Azure portál irányítópultján létrehozás láthatja az adat-előállítóban.

9. Az adat-előállító sikeres létrehozása után megjelenik az **Adat-előállító** oldal, amely megjeleníti az adat-előállító tartalmát. Ha nem látja a **adat-előállító** lapon, jelölje be az irányítópult a data factory tartozó csempére.

    ![A Data Factory panel](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben létrehoz két társított szolgáltatások. Egy szolgáltatás hivatkozik a Spark-fürt, és a data factory, és a más szolgáltatást a data factory tárhelyét hivatkozásokat tartalmaz. 

#### <a name="create-a-storage-linked-service"></a>Storage-beli társított szolgáltatás létrehozása
Ebben a lépésben társítja a tárfiókot az adat-előállítójához. A DataSet adatkészlet hoz létre, később a forgatókönyvben egy lépésben szolgáltatásnak hivatkozik. A HDInsight csatolt szolgáltatás a következő lépésben meghatározó túl szolgáltatásnak hivatkozik. 

1. Az a **adat-előállító** panelen válassza **Szerző és központi telepítése**. A Data Factory Editor jelenik meg.

2. Kattintson az **Új adattár** elemre, és válassza az **Azure Storage** lehetőséget.

   ![Új adattároló](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

3. A JSON-parancsfájl tárolási funkciókat biztosító társított szolgáltatás jelenik meg a szerkesztőben létrehozására használhatja.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

4. Cserélje le **fióknév** és **fiókkulcs** a tárfiók nevét és a hozzáférési kulccsal. A tárelérési kulcs lekéréséről [a tárfiók kezelését](../../storage/common/storage-create-storage-account.md#manage-your-storage-account) ismertető szakasz tárelérési kulcsok megtekintéséről, másolásáról és ismételt létrehozásáról szóló részében olvashat.

5. A társított szolgáltatás telepítése – válasszon ki **telepítés** a parancssávon. A társított szolgáltatás sikeres üzembe helyezése után megjelenik a Draft-1 (Vázlat-1) ablak. Az ablak bal oldalán, fanézetben látható az **AzureStorageLinkedService** szolgáltatás.

#### <a name="create-an-hdinsight-linked-service"></a>HDInsight társított szolgáltatás létrehozása
Ebben a lépésben a HDInsight Spark-fürt csatolása az adat-előállítóban HDInsight társított szolgáltatás létrehozása. A HDInsight-fürthöz, ez a példa a folyamatának Spark tevékenységben megadott Spark program futtatására szolgál. 

1. Válassza a Data Factory Editor **további** > **új számítási** > **HDInsight-fürt**.

    ![HDInsight társított szolgáltatás létrehozása](media/data-factory-spark/new-hdinsight-linked-service.png)

2. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-szerkesztőben tegye a következőket:

    a. Adja meg a HDInsight Spark-fürt URI-JÁNAK. Például: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Adja meg a felhasználó, aki hozzáfér a Spark-fürt nevét.

    c. Adja meg a felhasználó jelszavát.

    d. Adja meg a tárolás társított szolgáltatásának a HDInsight Spark-fürt társított. Az ebben a példában AzureStorageLinkedService is.

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
    > - A Spark tevékenység nem támogatja a HDInsight Spark-fürtjei használja az Azure Data Lake Store elsődleges tárolására.
    > - A Spark tevékenység támogatja a csak meglévő HDInsight Spark-fürtjei (saját). Egy igény szerinti HDInsight társított szolgáltatás nem támogatja.

    A HDInsight kapcsolódó szolgáltatással kapcsolatos további információkért lásd: [HDInsight társított szolgáltatás](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

3. A társított szolgáltatás telepítése – válasszon ki **telepítés** a parancssávon. 

### <a name="create-the-output-dataset"></a>A kimeneti adatkészlet létrehozása
A kimeneti adatkészlet működik (óránként, naponta) az ütemezést. Ezért meg kell adnia egy kimeneti adatkészletet, a Spark tevékenység az adatcsatorna annak ellenére, hogy a tevékenység nem ad kimenetet. Egy bemeneti adatkészlet a tevékenység megadása nem kötelező megadni.

1. A Data Factory Editorban válassza a **Továbbiak** > **Új adatkészlet** > **Azure Blob-tároló** lehetőséget.

2. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-részlet nevű adatkészlet meghatározása **OutputDataset**. Emellett megadhatja, hogy az eredmények kell-e tárolni a blob-tárolóban nevű **adfspark** és a mappa neve **pyFiles/kimeneti**. Ahogy korábban említettük, ez az adatkészlet egy üres adatkészlet. Ebben a példában a Spark program nem ad kimenetet. A **rendelkezésre állási** szakaszban határozza meg, hogy a kimeneti adatkészlet naponta jön létre. 

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
3. A dataset telepítése – válasszon ki **telepítés** a parancssávon.


### <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben egy HDInsightSpark tevékenységet hoz létre egy folyamatot. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. Ezért bemeneti adatkészlet ebben a példában van megadva.

1. A Data Factory Editorban válassza a **Továbbiak** > **Új folyamat** lehetőséget.

2. Cserélje le a parancsfájlt a Vázlat-1 ablakban az alábbi parancsfájlt:

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

    b. A **rootPath** tulajdonsága **adfspark\\pyFiles** ahol adfspark a blobtárolót és pyFiles fájlmappa a tárolóban. Ebben a példában a blob-tároló kiszolgáló, a társított a Spark-fürtön. Feltöltheti a fájlt egy másik tárfiókhoz. Ha így tesz, a tárolási fiók összekapcsolása az adat-előállítóban tárolás társított szolgáltatás létrehozása. Ezt követően adja meg a társított szolgáltatás neve értékeként a **sparkJobLinkedService** tulajdonság. Ez a tulajdonság és egyéb tulajdonságok Spark tevékenység által támogatott kapcsolatos további információkért lásd: [tevékenység tulajdonságai Spark](#spark-activity-properties).

    c. A **entryFilePath** tulajdonsága **test.py**, vagyis a Python-fájl.

    d. A **getDebugInfo** tulajdonsága **mindig**, ami azt jelenti, a naplófájlok helyét a rendszer mindig generált (sikeres vagy sikertelen).

    > [!IMPORTANT]
    > Azt javasoljuk, hogy nincs megadva ez a tulajdonság `Always` éles környezetben kivéve, ha a problémát elhárítani.

    e. A **kimenete** szakasz tartalmaz egy kimeneti adatkészletet. Meg kell adnia egy kimeneti adatkészletet, még akkor is, ha a Spark program nem ad kimenetet. A kimeneti adatkészlet meghajtók az adatcsatorna (óránként, naponta) ütemezését. 

    A Spark tevékenység által támogatott tulajdonságokról kapcsolatos további információkért lásd: a szakasz [tevékenység tulajdonságai Spark](#spark-activity-properties).

3. Az adatcsatornát, jelölje be **telepítés** a parancssávon.

### <a name="monitor-a-pipeline"></a>Folyamat monitorozása
1. Az a **adat-előállító** panelen válassza **figyelő & kezelése** egy másik lapon a figyelési alkalmazás indításához.

    ![Monitor & Manage csempe](media/data-factory-spark/monitor-and-manage-tile.png)

2. Módosítsa a **kezdési időpont** legfelül szűrő **2/1/2017**, és válassza ki **alkalmaz**.

3. Csak egy tevékenység ablakban jelenik meg, mert csak egy nap kezdete (2017-02-01) és befejezési időpontjával (2017-02-02) a feldolgozási folyamat között van. Győződjön meg arról, hogy az adatszelet az a **készen** állapotát.

    ![A folyamat figyelése](media/data-factory-spark/monitor-and-manage-app.png)

4. Az a **tevékenység windows** listára, válassza ki a tevékenységgel kapcsolatos részletek megtekintéséhez futtassa. Ha nem sikerül, részleteit a jobb oldali ablaktáblában tekintse meg.

### <a name="verify-the-results"></a>Az eredményeket

1. Indítsa el a Jupyter Notebook a HDInsight Spark-fürt címen [ezen a webhelyen](https://CLUSTERNAME.azurehdinsight.net/jupyter). Is megnyithatja a fürt-irányítópult a HDInsight Spark a fürt, és indítsa el a Jupyter Notebook.

2. Válassza ki **új** > **PySpark** új notebook elindításához.

    ![Új Jupyter notebook](media/data-factory-spark/jupyter-new-book.png)

3. Futtassa a következő parancsot a másolás és beillesztés, az szöveg, és nyomja le a Shift + Enter a második utasítás végén szerint:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Győződjön meg arról, hogy megjelenik-e az adatok a hvac-táblából. 

    ![Jupyter lekérdezés eredményei](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article --> Részletes útmutatásért lásd a szakasz [Spark SQL-lekérdezés futtatható](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Hibaelhárítás
Mert getDebugInfo értékre állítva **mindig**, megjelenik egy napló almappát a blobtárolókban lévő pyFiles mappában. A napló mappában a naplófájl további információkat tartalmaz. Ez a naplófájl akkor különösen akkor hasznos, ha nem sikerül. Termelési környezetben érdemes állítsa be **hiba**.

További hibaelhárítási, tegye a következőket:


1. Nyissa meg a következőt: `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Alkalmazás a YARN felhasználói felületen](media/data-factory-spark/yarnui-application.png)

2. Válassza ki **naplók** egy Futtatás kísérletek.

    ![Alkalmazások lap](media/data-factory-spark/yarn-applications.png)

3. A következő hibával kapcsolatos további információkat a napló lapon jelenik meg:

    ![Naplózási hiba](media/data-factory-spark/yarnui-application-error.png)

Az alábbi szakaszok ismertetik a data factory entitások Spark-fürt és a Spark-tevékenység használata a data factory.

## <a name="spark-activity-properties"></a>A Spark-tevékenység tulajdonságai
Ez a minta egy Spark tevékenységet adatcsatorna JSON-definícióból: 

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

A következő táblázat ismerteti a JSON-definícióból használt JSON-tulajdonságok.

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| név | A feldolgozási tevékenység nevét. | Igen |
| leírás | A tevékenység mit leíró szöveg. | Nem |
| type | Ez a tulajdonság HDInsightSpark kell állítani. | Igen |
| linkedServiceName | A Spark program fut. HDInsight társított szolgáltatás neve. | Igen |
| rootPath | A blob-tároló és a Spark-fájlt tartalmazó mappát. A fájlnév pedig kis-és nagybetűket. | Igen |
| entryFilePath | A gyökérmappában található azon a Spark kódcsomag relatív elérési útja. | Igen |
| Osztálynév | Az alkalmazás fő Java/Spark-osztály. | Nem |
| argumentumok | A Spark program parancssori argumentumokat listáját. | Nem |
| proxyUser | A felhasználói fiók megszemélyesíthet-e a Spark program végrehajtásához. | Nem |
| sparkConfig | Adja meg a felsorolt Spark konfigurációs tulajdonságok értékeit [Spark konfigurációs: alkalmazástulajdonságok](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nem |
| getDebugInfo | Határozza meg, amikor a tárolót a HDInsight-fürt által használt kerülnek a Spark naplófájlokat (vagy) leírt módon sparkJobLinkedService. Megengedett értékek: None, mindig, vagy sikertelen. Az alapértelmezett érték: nincs. | Nem |
| sparkJobLinkedService | A társított Társzolgáltatás, amely tárolja a Spark feladat fájl, a függőségeket és a naplókat. Ez a tulajdonság értékét nem adja meg, ha a HDInsight-fürthöz társított tárolót használja a rendszer. | Nem |

## <a name="folder-structure"></a>Mappaszerkezet
A Spark tevékenység nem támogatja egy beágyazott parancsfájlja, mint a Pig, vagy nem struktúra tevékenységeket. Spark feladatok akkor is több bővíthető, mint a Pig vagy Hive-feladatokat. A Spark-feladatok biztosíthat több függőség például jar (a java CLASSPATH helyezett) csomagok, Python-fájlok (a PYTHONPATH helyezve) és egyéb fájlokat.

A következő mappaszerkezet létrehozása a blob Storage HDInsight kapcsolódó szolgáltatás hivatkozik. Ezután a függő fájlok feltöltése a gyökérmappában által képviselt megfelelő almappák **entryFilePath**. Például Python-fájl feltöltése a pyFiles almappába, és jar fájlok a gyökérmappa a JAR-fájlok kivételével almappába. Futásidőben a Data Factory szolgáltatásnak vár a következő mappastruktúra a blob-tároló: 

| Útvonal | Leírás | Szükséges | Típus |
| ---- | ----------- | -------- | ---- |
| . | Az útvonalgyökér a Spark feladat, a tárolás társított szolgáltatásban. | Igen | Mappa |
| &lt;Felhasználó által definiált &gt; | A bejegyzés fájlt a Spark feladat mutató elérési utat. | Igen | Fájl |
| . / jars | Ebben a mappában található összes fájl feltöltése, és a fürt a Java classpath helyezve. | Nem | Mappa |
| ./pyFiles | Ebben a mappában található összes fájl feltöltése, és a fürt PYTHONPATH helyezve. | Nem | Mappa |
| . / fájlok | Ebben a mappában található összes fájl feltöltése, és a végrehajtó munkakönyvtár helyezve. | Nem | Mappa |
| . / archiválja | Ebben a mappában található összes fájl nincs tömörítve. | Nem | Mappa |
| . / naplói | A Spark-fürt származó naplók tárolására szolgáló mappa.| Nem | Mappa |

Íme egy példa a blob Storage a HDInsight kapcsolódó szolgáltatás által hivatkozott két Spark feladat fájlokat tartalmazó tárolására:

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
