---
title: Spark-programok meghívása az Azure Data Factory-ból
description: Ismerje meg, hogyan hívhatja meg a Spark-programokat egy Azure-adat-előállítóból a MapReduce tevékenység használatával.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: ce5fb014c7d954b3e8430a86430c6a666adff204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969233"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Spark-programok meghívása az Azure Data Factory folyamataiból

> [!div class="op_single_selector" title1="Átalakítási tevékenységek"]
> * [Hive-tevékenység](data-factory-hive-activity.md)
> * [Sertésaktivitás](data-factory-pig-activity.md)
> * [MapReduce tevékenység](data-factory-map-reduce.md)
> * [Hadoop streaming tevékenység](data-factory-hadoop-streaming-activity.md)
> * [Szikraaktivitás](data-factory-spark.md)
> * [Gépi tanulási kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Gépi tanulási frissítési erőforrás-tevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el [az Adatok átalakítása az Apache Spark-tevékenység használatával a Data Factory szolgáltatásban című témakört.](../transform-data-using-spark.md)

## <a name="introduction"></a>Bevezetés
A Spark-tevékenység a Data Factory által támogatott [adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) egyike. Ez a tevékenység futtatja a megadott Spark-program ot a Spark-fürtön az Azure HDInsightban.

> [!IMPORTANT]
> - A Spark-tevékenység nem támogatja a HDInsight Spark-fürtök, amelyek az Azure Data Lake Store elsődleges tárolóként.
> - A Spark-tevékenység csak meglévő (saját) HDInsight Spark-fürtöket támogat. Nem támogatja az igény szerinti HDInsight-hivatkozott szolgáltatást.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Forgatókönyv: Folyamat létrehozása Spark-tevékenységgel
Az alábbiakban a Spark-tevékenységgel rendelkező adatfeldolgozó folyamat létrehozásának tipikus lépéseit ismertetheti:

* Adat-előállító létrehozása
* Hozzon létre egy Azure Storage-hoz kapcsolódó szolgáltatást a HDInsight Spark-fürthöz társított tároló és az adat-gyár összekapcsolásához.
* Hozzon létre egy HDInsight-kapcsolt szolgáltatást a Spark-fürt HDInsight-ban való összekapcsolásához az adat-előállítóhoz.
* Hozzon létre egy adatkészletet, amely a Storage csatolt szolgáltatásra hivatkozik. Jelenleg meg kell adnia egy kimeneti adatkészletet egy tevékenységhez, még akkor is, ha nincs kimenet.
* Hozzon létre egy folyamatot a Spark-tevékenységgel, amely a létrehozott HDInsight-csatolt szolgáltatásra hivatkozik. A tevékenység az előző lépésben kimeneti adatkészletként létrehozott adatkészlettel van konfigurálva. A kimeneti adatkészlet az, ami hajtja az ütemezést (óránként, naponta). Ezért meg kell adnia a kimeneti adatkészletet annak ellenére, hogy a tevékenység nem igazán hoz létre kimenetet.

### <a name="prerequisites"></a>Előfeltételek
1. Általános célú tárfiókot hozhat létre a [Tárfiók létrehozása](../../storage/common/storage-account-create.md)című részben található utasításokat követve.

1. Hozzon létre egy Spark-fürthdInsight az oktatóanyag utasításokat [követve Hozzon létre egy Spark-fürthdInsight.](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) Társítsa az 1.

1. Töltse le és tekintse **test.py** át [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py)a Python parancsfájlt test.py a helyen található .

1. Töltsön fel **test.py** a blobstorage **adfspark-tárolójának** **pyFiles** mappájába. Hozza létre a tárolót és a mappát, ha azok nem léteznek.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Adat-előállító létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Válassza az **Új** > **adatok + Elemzési** > **adatok gyárát**.

1. Az **Új adatgyár** panel **neve csoportban**írja be a **SparkDF parancsot.**

   > [!IMPORTANT]
   > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a "Data factory name SparkDF" (Az adatgyár neve SparkDF nem érhető el) hibaüzenet jelenik meg, módosítsa az adat-előállító nevét. Használja például a nameSparkDFdate nevet, és hozza létre újra az adat-előállító. További információ az elnevezési szabályokról: [A Data Factory elnevezési szabályai](data-factory-naming-rules.md).

1. Az **Előfizetés** területen válassza ki azt az Azure-előfizetést, ahol létre kívánja hozni az adat-előállítót.

1. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy Azure-erőforráscsoportot.

1. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet.

1. Kattintson a **Létrehozás** gombra.

   > [!IMPORTANT]
   > Data Factory-példányok létrehozásához az előfizetés/erőforráscsoport szintjén a [Data Factory közreműködői](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie.

1. Az Azure Portal irányítópultján létrehozott adatgyár at láthatja.

1. Az adat-előállító sikeres létrehozása után megjelenik az **Adat-előállító** oldal, amely megjeleníti az adat-előállító tartalmát. Ha nem látja a **Data factory** oldalt, válassza ki az adatgyár csempéjét az irányítópulton.

    ![A Data Factory panel](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben két összekapcsolt szolgáltatást hoz létre. Az egyik szolgáltatás a Spark-fürtöt az adat-előállítóhoz kapcsolja, a másik pedig a tárhelyet az adat-előállítóhoz kapcsolja.

#### <a name="create-a-storage-linked-service"></a>Storage-beli társított szolgáltatás létrehozása
Ebben a lépésben társítja a tárfiókot az adat-előállítójához. A forgatókönyv egy későbbi részében létrehozott adatkészlet erre a csatolt szolgáltatásra hivatkozik. A következő lépésben megadott HDInsight-csatolt szolgáltatás is erre a csatolt szolgáltatásra hivatkozik.

1. A **Data factory** panelen válassza a **Szerző lehetőséget, és telepítse a lehetőséget.** Megjelenik a Data Factory Editor.

1. Kattintson az **Új adattár** elemre, és válassza az **Azure Storage** lehetőséget.

   ![Új adattár](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. A storage-alapú szolgáltatás létrehozásához használt JSON-parancsfájl megjelenik a szerkesztőben.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Cserélje le **a fiók nevét** és a **fiókkulcsot** a tárfiók nevére és hozzáférési kulcsára. A tárfiók hozzáférési kulcsának [kezelése( Tárfiók-hozzáférési kulcsok kezelése)](../../storage/common/storage-account-keys-manage.md)témakörből megtudhatja, hogy miként szerezheti be a tárfiók hozzáférési kulcsait.

1. A csatolt szolgáltatás központi telepítéséhez válassza a **Telepítés** lehetőséget a parancssávon. A társított szolgáltatás sikeres üzembe helyezése után megjelenik a Draft-1 (Vázlat-1) ablak. Az ablak bal oldalán, fanézetben látható az **AzureStorageLinkedService** szolgáltatás.

#### <a name="create-an-hdinsight-linked-service"></a>HDInsight társított szolgáltatás létrehozása
Ebben a lépésben hozzon létre egy HDInsight-csatolt szolgáltatást a HDInsight Spark-fürt és az adat-előállító összekapcsolásához. A HDInsight-fürt a mintában a folyamat Spark-tevékenységében megadott Spark-program futtatására szolgál.

1. A Data Factory Editor ban válassza a **További** > **új számítási** > **HDInsight-fürt lehetőséget.**

    ![HDInsight társított szolgáltatás létrehozása](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-szerkesztőben tegye a következő lépéseket:

    a. Adja meg a HDInsight Spark-fürt URI-ját. Például: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Adja meg a Spark-fürthöz hozzáféréssel rendelkező felhasználó nevét.

    c. Adja meg a felhasználó jelszavát.

    d. Adja meg a STORAGE-alapú szolgáltatás, amely a HDInsight Spark-fürthöz van társítva. Ebben a példában az AzureStorageLinkedService.

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
    > - A Spark-tevékenység nem támogatja a HDInsight Spark-fürtök, amelyek az Azure Data Lake Store elsődleges tárolóként.
    > - A Spark-tevékenység csak meglévő (saját) HDInsight Spark-fürtöket támogat. Nem támogatja az igény szerinti HDInsight-hivatkozott szolgáltatást.

    A HDInsight-csatolt szolgáltatásról további információt a [HDInsight-csatolt szolgáltatás ban](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)talál.

1. A csatolt szolgáltatás központi telepítéséhez válassza a **Telepítés** lehetőséget a parancssávon.

### <a name="create-the-output-dataset"></a>A kimeneti adatkészlet létrehozása
A kimeneti adatkészlet az, ami hajtja az ütemezést (óránként, naponta). Ezért meg kell adnia egy kimeneti adatkészletet a Spark-tevékenység hez a folyamatban, még akkor is, ha a tevékenység nem hoz létre kimenetet. A tevékenység bemeneti adatkészletének megadása nem kötelező.

1. A Data Factory Editor ban válassza az **Újabb** > **új adatkészlet** > **et, az Azure Blob storage lehetőséget.**

1. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-kódrészlet egy **OutputDataset**nevű adatkészletet határoz meg. Emellett megadhatja, hogy az eredmények az **adfspark** nevű blobtárolóban és a **pyFiles/output**nevű mappában tárolódjanak. Mint korábban említettük, ez az adatkészlet egy dummy adatkészlet. A Spark-program ebben a példában nem hoz létre kimenetet. A **rendelkezésre állási** szakasz azt határozza meg, hogy a kimeneti adatkészlet naponta keletkezik.

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
1. Az adatkészlet központi telepítéséhez válassza a **Telepítés** a parancssávon lehetőséget.


### <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben hozzon létre egy folyamatot egy HDInsightSpark-tevékenységgel. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. Ezért ebben a példában nincs megadva bemeneti adatkészlet.

1. A Data Factory Editor ban válassza az **Újabb** > **új folyamat lehetőséget.**

1. Cserélje le a Piszkozat-1 ablakparancsfájlt a következő parancsfájlra:

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

    a. A **típustulajdonság** **HDInsightSpark**értékre van állítva.

    b. A rootPath tulajdonság **adfspark\\pyFiles** értékre van állítva, ahol az adfspark a blobtároló, a pyFiles pedig a tárolóban lévő fájlmappa. **rootPath** Ebben a példában a blob storage a Spark-fürthöz társított tároló. Feltöltheti a fájlt egy másik tárfiókba. Ha így tesz, hozzon létre egy storage-hoz kapcsolódó szolgáltatást, amely a tárfiókot az adat-előállítóhoz kapcsolja. Ezután adja meg a csatolt szolgáltatás nevét a **sparkJobLinkedService** tulajdonság értékeként. A tulajdonságról és a Spark-tevékenység által támogatott egyéb tulajdonságokról a [Spark-tevékenység tulajdonságai című témakörben](#spark-activity-properties)talál további információt.

    c. A **entryFilePath** tulajdonság **test.py**test.py , amely a Python-fájl.

    d. A **getDebugInfo** tulajdonság mindig , ami azt **jelenti,** hogy a naplófájlok mindig létrejönnek (sikeres vagy sikertelen).

    > [!IMPORTANT]
    > Azt javasoljuk, hogy éles környezetben csak akkor állítsa be ezt a tulajdonságot, `Always` ha egy problémát elhárít.

    e. A **kimenetek** szakasz egy kimeneti adatkészlettel rendelkezik. Meg kell adnia egy kimeneti adatkészletet akkor is, ha a Spark-program nem hoz létre kimenetet. A kimeneti adatkészlet hajtja a folyamat ütemezését (óránként, naponta).

    A Spark-tevékenység által támogatott tulajdonságokról a [Spark-tevékenység tulajdonságai](#spark-activity-properties)című szakaszban talál további információt.

1. A folyamat üzembe helyezéséhez válassza a **Központi telepítés** lehetőséget a parancssávon.

### <a name="monitor-a-pipeline"></a>Folyamat monitorozása
1. A **Data factory** panelen válassza a **Monitor & Kezelése lehetőséget** a figyelési alkalmazás egy másik lapon történő elindításához.

    ![Monitor & Manage csempe](media/data-factory-spark/monitor-and-manage-tile.png)

1. Módosítsa a **kezdési időszűrőt** felül **2/1/2017-re,** és válassza **az Alkalmaz**lehetőséget.

1. Csak egy tevékenységablak jelenik meg, mert csak egy nap van a folyamat kezdete (2017-02-01) és a befejezési idők (2017-02-02) között. Ellenőrizze, hogy az adatszelet Kész állapotban **van-e.**

    ![A folyamat figyelése](media/data-factory-spark/monitor-and-manage-app.png)

1. A **Tevékenység ablakok** listájában válasszon ki egy tevékenységfuttatást a róla kapcsolatos részletek megtekintéséhez. Ha hiba történik, a jobb oldali ablaktáblában láthatja a részleteket.

### <a name="verify-the-results"></a>Az eredmények ellenőrzése

1. Indítsa el a Jupyter-jegyzetfüzetet a HDInsight Spark-fürthöz [a webhely](https://CLUSTERNAME.azurehdinsight.net/jupyter)webhelyén. Megnyithat egy fürtirányítópultot a HDInsight Spark-fürthöz, majd elindíthatja a Jupyter-jegyzetfüzetet.

1. Új jegyzetfüzet indításához válassza az **Új** > **PySpark** lehetőséget.

    ![Jupyter új notebook](media/data-factory-spark/jupyter-new-book.png)

1. Futtassa a következő parancsot a szöveg másolásával és beillesztésével, majd a Shift+Enter billentyűkombinációval a második utasítás végén:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Erősítse meg, hogy látja a hvac tábla adatait.

    ![Jupyter lekérdezés eredményei](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
A részletes utasításokat a [Spark SQL-lekérdezés futtatása](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című szakaszban találja.

### <a name="troubleshooting"></a>Hibaelhárítás
Mivel a getDebugInfo beállítást **Mindig**beállításra állítja, megjelenik egy naplóalmappa a blobtároló pyFiles mappájában. A naplómappában lévő naplófájl további információkat tartalmaz. Ez a naplófájl különösen akkor hasznos, ha hiba történt. Éles környezetben érdemes lehet a hiba beállítását **beállítani.**

További hibaelhárításhoz kövesse az alábbi lépéseket:


1. Nyissa meg a következőt: `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![YARN UI alkalmazás](media/data-factory-spark/yarnui-application.png)

1. Válassza **a Naplók lehetőséget** az egyik futtatási kísérlethez.

    ![Alkalmazás lapja](media/data-factory-spark/yarn-applications.png)

1. A naplólapon a következő további hibainformációk találhatók:

    ![Naplóhiba](media/data-factory-spark/yarnui-application-error.png)

A következő szakaszok az adat-gyári entitások a Spark-fürt és a Spark-tevékenység az adat-előállító használata információkat tartalmaznak.

## <a name="spark-activity-properties"></a>Spark-tevékenység tulajdonságai
Itt van a spark-tevékenységgel rendelkező folyamat JSON-definíciója:

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
                    },
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

Az alábbi táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti.

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| név | A folyamatban lévő tevékenység neve. | Igen |
| leírás | A tevékenység et leíró szöveg. | Nem |
| type | Ezt a tulajdonságot HDInsightSpark értékre kell állítani. | Igen |
| linkedServiceName | Annak a HDInsight-csatolt szolgáltatásnak a neve, amelyen a Spark-program fut. | Igen |
| rootPath | A blob tároló és a Spark-fájlt tartalmazó mappa. A fájlnév nem imitátus. | Igen |
| entryFilePath | A Spark-kód/-csomag gyökérmappájának relatív elérési útja. | Igen |
| Osztálynév | Az alkalmazás Java/Spark főosztálya. | Nem |
| Érvek | A Spark-program parancssori argumentumainak listája. | Nem |
| proxyUser | A Spark-program végrehajtásához megszemélyesített felhasználói fiók. | Nem |
| sparkConfig | Adja meg a Spark konfigurációs tulajdonságainak értékeit [a Spark-konfigurációban: Alkalmazástulajdonságai](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nem |
| getDebugInfo | Itt adható meg, hogy a Spark-naplófájlok at másolja-e a sparkJobLinkedService által megadott HDInsight-fürt (vagy) által használt tárolóba. Az engedélyezett értékek: Nincs, Mindig vagy Sikertelen. Az alapértelmezett érték nincs. | Nem |
| sparkJobLinkedService | A Storage csatolt szolgáltatás, amely rendelkezik a Spark-feladat fájl, függőségek és naplók. Ha nem ad meg értéket ehhez a tulajdonsághoz, a HDInsight-fürthöz társított tároló t használja a rendszer. | Nem |

## <a name="folder-structure"></a>Mappastruktúra
A Spark-tevékenység nem támogatja a szövegközi parancsfájlt, mint a Pig és a Hive tevékenységek. Spark-feladatok is bővíthetőbb, mint a Pig / Hive munkahelyek. A Spark-feladatok, több függőségek, például jar csomagok (a java CLASSPATH helyezik el), Python fájlok (a PYTHONPATH) és bármely más fájlokat.

Hozza létre a következő mappastruktúrát a HDInsight-csatolt szolgáltatás által hivatkozott blobstorage-ban. Ezután töltse fel a függő fájlokat a filePath által képviselt gyökérmappában lévő megfelelő **almappákba.** Például töltsön fel Python-fájlokat a pyFiles almappába és a jar fájlokba a gyökérmappa jars almappájába. Futásidőben a Data Factory szolgáltatás a következő mappastruktúrát várja a blobstorage-ban:

| Útvonal | Leírás | Kötelező | Típus |
| ---- | ----------- | -------- | ---- |
| . | A Spark-feladat gyökérelérési útja a storage-hoz csatolt szolgáltatásban. | Igen | Mappa |
| &lt;felhasználó által definiált&gt; | Az elérési út, amely a Spark-feladat beviteli fájljára mutat. | Igen | Fájl |
| ./üvegek | A mappa összes fájlja feltöltésre kerül, és a fürt Java osztályára kerül. | Nem | Mappa |
| ./pyFiles | A mappa összes fájlja feltöltésre kerül, és a fürt PYTHONPATH-jára kerül. | Nem | Mappa |
| ./fájlok | A mappa összes fájlja feltöltésre kerül, és a végrehajtó munkakönyvtárába kerül. | Nem | Mappa |
| ./archívumok | A mappa összes fájlja tömörítetlen. | Nem | Mappa |
| ./naplók | Az a mappa, ahol a Spark-fürtből származó naplók tárolódnak.| Nem | Mappa |

Íme egy példa a tároló, amely két Spark-feladat fájlokat a blob storage által hivatkozott HDInsight csatolt szolgáltatás:

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
