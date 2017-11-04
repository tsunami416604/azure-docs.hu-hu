---
title: "Az Azure Data Factory Spark programok meghívása |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet meghívni egy az Azure data factory használatával a MapReduce művelethez Spark programok."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 0eff48ec65a01a2fc3fa9f7652dd8e1a0fc8dd2a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Az Azure Data Factory folyamatok Spark programok meghívása

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-tevékenység](data-factory-hive-activity.md)
> * [A Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce művelethez](data-factory-map-reduce.md)
> * [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
> * [A Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk vonatkozik 1 a Azure Data Factory, amelyek általánosan elérhető (GA). Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [spark tevékenység adat-előállítóban 2-es verzióját használó adatok](../transform-data-using-spark.md).

## <a name="introduction"></a>Bevezetés
Spark tevékenység egyike a [adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) Azure Data Factory által támogatott. Ez a tevékenység fut a megadott Spark program Azure hdinsight az Apache Spark-fürt.    

> [!IMPORTANT]
> - Spark tevékenység nem támogatja a HDInsight Spark-fürtjei használja az Azure Data Lake Store elsődleges tárolására.
> - Spark tevékenység támogatja a csak meglévő HDInsight Spark-fürtjei (saját). Egy igény szerinti HDInsight társított szolgáltatás nem támogatja.

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>Forgatókönyv: hozzon létre egy folyamatot Spark tevékenység
A Data Factory-folyamat létrehozása egy Spark tevékenységet a szokásos lépései a következők.  

1. Adat-előállító létrehozása
2. Az az Azure storage társított a HDInsight Spark-fürt data factoryval való csatolásához Azure Storage társított szolgáltatás létrehozása.     
2. Az Azure HDInsight az Apache Spark-fürt összekapcsolása a data factory kapcsolt Azure HDInsight szolgáltatás létrehozása.
3. Hozzon létre egy adatkészlet hivatkozik az Azure tárolás társított szolgáltatása. Jelenleg adjon meg egy kimeneti adatkészlet a tevékenység akkor is, ha nincs folyamatban létrehozott kimenet van.  
4. Hozzon létre egy folyamatot Spark tevékenység, amely a #2 létrehozott HDInsight kapcsolódó szolgáltatásra hivatkozik. A tevékenység az adatkészletben, mint egy kimeneti adatkészletet az előző lépésben létrehozott van konfigurálva. A kimeneti adatkészlet működik az ütemezés (óránként, naponta, stb.). A kimeneti adatkészletet, ezért meg kell adnia annak ellenére, hogy a tevékenység nem eredményez valóban kimenettel.

### <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy **általános célú Azure Storage-fiók** a forgatókönyv a következő utasítással: [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account).  
2. Hozzon létre egy **Azure HDInsight az Apache Spark-fürt** által az oktatóanyag utasításai a következő: [Azure HDInsight létrehozása Apache Spark-fürt](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Társítsa a #1. lépésben a fürthöz létrehozott Azure-tárfiókot.  
3. Töltse le, és tekintse át a python-parancsfájl **test.py** helyen található: [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).  
3.  Töltse fel **test.py** számára a **pyFiles** mappájában a **adfspark** az Azure Blob Storage tárolóban. A tároló és a mappa létrehozása, ha azok nem léteznek.

### <a name="create-data-factory"></a>Data factory létrehozása
Ebben a lépésben létrehozzuk a data factoryt.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Kattintson a **NEW** (Új) lehetőségre a bal oldali menüben, kattintson az **Data + Analytics** (Adatok + analitika) pontra, majd a **Data Factory** elemre.
3. Az a **új adat-előállító** panelen adjon meg **SparkDF** nevét.

   > [!IMPORTANT]
   > Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a hibát látja: **nem érhető el adat-előállító "SparkDF"**. Az adat-előállítóban (például yournameSparkDFdate, és próbálja meg újra létrehozni nevének módosítása. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.   
4. Jelölje ki azt az **Azure-előfizetést**, ahol létre szeretné hozni a data factoryt.
5. Válasszon ki egy létező **erőforráscsoport** , vagy hozzon létre egy Azure-erőforráscsoportot.
6. Válassza ki **rögzítés az irányítópulton** lehetőséget.  
6. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.

   > [!IMPORTANT]
   > Data Factory-példány létrehozásához a [Data Factory közreműködője](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.
7. Megjelenik a data factory létrehozása a **irányítópult** az Azure portál, az alábbiak szerint:   
8. A data factory sikeres létrehozása után megjelenik a data factory oldal, amely megjeleníti a data factory tartalmát. Ha nem látja a data factory lap, kattintson az irányítópulton a data factory tartozó csempére.

    ![A Data Factory panel](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben létrehoz két társított szolgáltatások, a Spark-fürt összekapcsolása a data factory, míg a másik az Azure tárterületet összekapcsolása a data factory egyik.  

#### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
Ebben a lépésben társítja az Azure Storage-fiókot a data factoryjához. A DataSet adatkészlet hoz létre, később a forgatókönyvben egy lépésben szolgáltatásnak hivatkozik. A HDInsight csatolt szolgáltatás a következő lépésben meghatározó túl szolgáltatásnak hivatkozik.  

1. Kattintson a **Szerző és központi telepítése** a a **adat-előállító** a data factory paneljét. Ekkor meg kell jelennie a Data Factory Editornak.
2. Kattintson a **New data store** (Új adattár) elemre, és válassza az **Azure Storage** elemet.

   ![Új adattár – Azure Storage – menü](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. Megjelenik a **JSON-parancsfájl** létrehozásához egy Azure Storage társított a szolgáltatás-szerkesztőben.

   ![Azure Storage társított szolgáltatás](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. Cserélje le **fióknév** és **fiókkulcs** az Azure storage-fiók nevét és a hozzáférési kulccsal. A tárelérési kulcs lekéréséről többet is megtudhat, ha elolvassa a tárelérési kulcsok megtekintésével, másolásával és újragenerálásával kapcsolatos információkat [A tárfiók kezelése](../../storage/common/storage-create-storage-account.md#manage-your-storage-account) című részben.
5. A társított szolgáltatás telepítéséhez kattintson **telepítés** a parancssávon. A társított szolgáltatás sikeres üzembe helyezését követően megjelenik a **Draft-1** (Vázlat-1) ablak, amelynek bal oldalán, fanézetben látható az **AzureStorageLinkedService** szolgáltatás.

#### <a name="create-hdinsight-linked-service"></a>A HDInsight társított szolgáltatás létrehozása
Ebben a lépésben hozzon létre csatolt Azure HDInsight szolgáltatást a HDInsight Spark-fürt csatolása az adat-előállítóban. A HDInsight-fürthöz, ez a példa a folyamatának Spark tevékenységben megadott Spark program futtatására szolgál.  

1. Ha nem látja ezt a gombot, kattintson a három pontot  **További** kattintson az eszköztár **új számítási**, és kattintson a **HDInsight-fürt**.

    ![A HDInsight társított szolgáltatás létrehozása](media/data-factory-spark/new-hdinsight-linked-service.png)
2. Másolja és illessze be a következő kódrészletet a **Draft-1** (Vázlat-1) ablakba. A JSON-szerkesztőben tegye a következőket:
    1. Adja meg a **URI** a HDInsight Spark-fürt számára. Például: `https://<sparkclustername>.azurehdinsight.net/`.
    2. Adja meg a nevét a **felhasználói** kik férhetnek hozzá a Spark-fürtön.
    3. Adja meg a **jelszó** felhasználó számára.
    4. Adja meg a **Azure Storage társított szolgáltatásnak** társított a HDInsight Spark-fürt. Az ebben a példában is: **AzureStorageLinkedService**.

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
    > - Spark tevékenység nem támogatja a HDInsight Spark-fürtjei használja az Azure Data Lake Store elsődleges tárolására.
    > - Spark tevékenység támogatja a csak meglévő HDInsight Spark-fürt (saját). Egy igény szerinti HDInsight társított szolgáltatás nem támogatja.

    Lásd: [HDInsight társított szolgáltatás](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) a HDInsight adatait a társított szolgáltatás.
3.  A társított szolgáltatás telepítéséhez kattintson **telepítés** a parancssávon.  

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása
A kimeneti adatkészlet működik az ütemezés (óránként, naponta, stb.). Ezért meg kell adnia egy kimeneti adatkészletet, a spark tevékenység az adatcsatorna annak ellenére, hogy a tevékenység valóban nem ad kimenetet. Egy bemeneti adatkészlet a tevékenység megadása nem kötelező megadni.

1. A **Data Factory Editorban** kattintson ide: **... More** (... További) a parancssávon, kattintson a **New dataset** (Új adathalmaz) elemre, és válassza az **Azure Blob Storage** lehetőséget.  
2. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-részlet nevű adatkészlet meghatározása **OutputDataset**. Emellett megadhatja, hogy az eredmények kell-e tárolni a blob-tárolóban nevű **adfspark** és a mappa neve **pyFiles/kimeneti**. Amint azt korábban említettük, ez az adatkészlet egy üres adatkészlet. Ebben a példában a Spark program nem ad kimenetet. A **rendelkezésre állási** szakaszban határozza meg, hogy a kimeneti adatkészlet naponta jön létre.  

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
3. A dataset telepítéséhez kattintson **telepítés** a parancssávon.


### <a name="create-pipeline"></a>Folyamat létrehozása
Ebben a lépésben a folyamat létrehoz egy **HDInsightSpark** tevékenység. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. Ezért bemeneti adatkészlet ebben a példában van megadva.

1. Az a **Data Factory Editor**, kattintson a **... További** a parancssávon, majd **új adatcsatorna**.
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
    - A **típus** tulajdonsága **HDInsightSpark**.
    - A **rootPath** értéke **adfspark\\pyFiles** ahol adfspark az Azure Blob-tárolóba, pyFiles pedig az adott tároló finom mappát. Ebben a példában az Azure Blob Storage lesz a Spark-fürt társított. Feltöltheti a fájlt egy másik Azure-tárhelyre. Ha így tesz, hozzon létre egy Azure tárolás társított szolgáltatása, hogy a storage-fiók összekapcsolása az adat-előállítóban. Ezt követően adja meg a társított szolgáltatás neve értékeként a **sparkJobLinkedService** tulajdonság. Lásd: [Spark-tevékenység tulajdonságai](#spark-activity-properties) Ez a tulajdonság és az egyéb Spark tevékenység által támogatott tulajdonságokról.  
    - A **entryFilePath** értékre van állítva a **test.py**, vagyis a python-fájl.
    - A **getDebugInfo** tulajdonsága **mindig**, ami azt jelenti, a naplófájlok helyét a rendszer mindig generált (sikeres vagy sikertelen).

        > [!IMPORTANT]
        > Azt javasoljuk, hogy nincs megadva ez a tulajdonság `Always` éles környezetben kivéve, ha a probléma hibaelhárítást.
    - A **kimenete** szakasz tartalmaz egy kimeneti adatkészletet. Meg kell adnia egy kimeneti adatkészletet, még akkor is, ha a spark program nem ad kimenetet. A kimeneti adatkészlet meghajtók az ütemezés a következő feldolgozási sor (óránként, naponta, stb.).  

        A Spark tevékenység által támogatott tulajdonságokról kapcsolatos részletekért lásd: [tevékenység tulajdonságai Spark](#spark-activity-properties) szakasz.
3. Kattintson újra az adatcsatornát, **telepítés** a parancssávon.

### <a name="monitor-pipeline"></a>Folyamat figyelése
1. Kattintson a **X** zárja be a Data Factory Editor paneleken, és navigáljon a Data Factory kezdőlapján. Kattintson a **figyelése és kezelése** a figyelési alkalmazást egy másik lapon.

    ![Megfigyelés és kezelés csempe](media/data-factory-spark/monitor-and-manage-tile.png)
2. Módosítsa a **kezdési időpont** legfelül szűrő **2/1/2017**, és kattintson a **alkalmaz**.
3. Mivel csak egy nap kezdete (2017-02-01) és befejezési időpontjával (2017-02-02) a feldolgozási folyamat között csak egy tevékenység ablakban kell megjelennie. Győződjön meg arról, hogy az adatszelet az **készen** állapotát.

    ![A folyamat figyelése](media/data-factory-spark/monitor-and-manage-app.png)    
4. Válassza ki a **tevékenység ablakban** a tevékenységfuttatási kapcsolatos részletek megtekintéséhez. Ha nem sikerül, részleteit a jobb oldali ablaktáblában tekintse meg.

### <a name="verify-the-results"></a>Az eredményeket

1. Indítsa el **Jupyter notebook** navigáljon a HDInsight Spark-fürt esetében: https://CLUSTERNAME.azurehdinsight.net/jupyter. Is indítsa el a fürt-irányítópult a HDInsight Spark-fürt számára, majd indítsa el **Jupyter Notebook**.
2. Kattintson a **új** -> **PySpark** új notebook elindításához.

    ![Új Jupyter notebook](media/data-factory-spark/jupyter-new-book.png)
3. Futtassa a következő parancsot a másolás/beillesztés a szöveget, majd nyomja le az **SHIFT + ENTER** a második utasítás végén.  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Győződjön meg arról, hogy megjelenik-e a hvac tábla adatait:  

    ![Jupyter lekérdezés eredményei](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Lásd: [Spark SQL-lekérdezés futtatható](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) szakaszban részletes információkra van szüksége. 

### <a name="troubleshooting"></a>Hibaelhárítás
Óta beállított **getDebugInfo** való **mindig**, megjelenik egy **napló** almappájában a **pyFiles** mappa az Azure Blob-tárolóban. A napló mappában a naplófájl további részleteket biztosít. Ez a naplófájl akkor különösen akkor hasznos, ha nem sikerül. Termelési környezetben érdemes lehet állítsa **hiba**.

További hibaelhárítási, tegye a következőket:


1. Navigáljon a `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Alkalmazás a YARN felhasználói felületen](media/data-factory-spark/yarnui-application.png)  
2. Kattintson a **naplók** egy Futtatás kísérletek.

    ![Alkalmazások lap](media/data-factory-spark/yarn-applications.png)
3. Meg kell jelennie a hibával kapcsolatos további információkat a napló lapon.

    ![Naplózási hiba](media/data-factory-spark/yarnui-application-error.png)

Az alábbi szakaszok ismertetik a Data Factory entitások Apache Spark-fürt és a Spark-tevékenység használata a data factory.

## <a name="spark-activity-properties"></a>A Spark-tevékenység tulajdonságai
Ez a minta Spark tevékenység az adatcsatorna JSON-definícióból:    

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

A következő táblázat a JSON-definícióból használt JSON-tulajdonságok:

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| név | A feldolgozási tevékenység nevét. | Igen |
| leírás | A tevékenység mit leíró szöveg. | Nem |
| type | Ez a tulajdonság HDInsightSpark kell állítani. | Igen |
| linkedServiceName | A Spark program fut. HDInsight társított szolgáltatás neve. | Igen |
| rootPath | Az Azure Blob-tároló és a Spark-fájlt tartalmazó mappát. A fájlnév pedig kis-és nagybetűket. | Igen |
| entryFilePath | A gyökérmappában található azon a Spark kódcsomag relatív elérési útja. | Igen |
| Osztálynév | Az alkalmazás Java/Spark fő osztály | Nem |
| Argumentumok | A Spark program parancssori argumentumokat listáját. | Nem |
| proxyUser | A Spark program végrehajtásának megszemélyesíteni a felhasználói fiók | Nem |
| sparkConfig | Adja meg a témakörben ismertetett Spark konfigurációs tulajdonságok értékeit: [Spark konfigurációs - alkalmazás tulajdonságainak](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nem |
| getDebugInfo | Itt adhatja meg, amikor a Spark naplófájlok kerülnek a HDInsight-fürt által használt Azure storage (vagy) leírt módon sparkJobLinkedService. Megengedett értékek: None, mindig, vagy sikertelen. Alapértelmezett érték: nincs. | Nem |
| sparkJobLinkedService | Az Azure Storage társított szolgáltatás, amely tárolja a Spark feladat fájl, a függőségeket és a naplókat.  Ha nem ad meg egy értéket ehhez a tulajdonsághoz, a HDInsight-fürthöz társított tárolót használja a rendszer. | Nem |

## <a name="folder-structure"></a>Mappaszerkezet
A Spark tevékenység nem támogatja a Pig-egy sor parancsprogramot, és Hive tevékenységek tegye. Spark feladatok akkor is több bővíthető, mint a Pig vagy Hive-feladatokat. A Spark-feladatok biztosíthat több függőség például jar (a java CLASSPATH helyezett) csomagok, python-fájlok (a PYTHONPATH helyezve) és egyéb fájlokat.

A következő mappaszerkezet létrehozása az Azure Blob Storage HDInsight kapcsolódó szolgáltatás hivatkozik. Ezután a megfelelő mappákat a gyökérmappában által képviselt függő fájlok feltöltése **entryFilePath**. Python pyFiles almappájába és jar-fájlok például a legfelső szintű JAR-fájlok kivételével almappája feltöltése. Futásidőben a Data Factory szolgáltatásnak a következő gyökérmappa-szerkezetében vár az Azure Blob Storage:     

| Útvonal | Leírás | Szükséges | Típus |
| ---- | ----------- | -------- | ---- |
| . | A Spark feladatot a tárolás társított szolgáltatásának elérési útjának gyökeréhez  | Igen | Mappa |
| &lt;felhasználó által definiált&gt; | Az elérési út a Spark feladat bejegyzés fájlra hivatkozik | Igen | Fájl |
| . / jars | Ebben a mappában található összes fájl feltöltése, és a fürt a java classpath helyezve | Nem | Mappa |
| . / pyFiles | Ebben a mappában található összes fájl feltöltése, és a fürt PYTHONPATH helyezve | Nem | Mappa |
| . / fájlok | Ebben a mappában található összes fájl feltöltése és végrehajtó munkakönyvtár helyezve | Nem | Mappa |
| . / archiválja | Ebben a mappában található összes fájl tömörítetlen. | Nem | Mappa |
| . / naplói | A Spark-fürt származó naplók tárolására szolgáló mappa.| Nem | Mappa |

Íme egy példa egy tárolás az Azure Blob Storage a HDInsight kapcsolódó szolgáltatás által hivatkozott két Spark feladat fájlt tartalmazza.

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
