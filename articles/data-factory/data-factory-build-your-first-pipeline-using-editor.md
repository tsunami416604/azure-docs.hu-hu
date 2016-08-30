<properties
    pageTitle="Az első data factory létrehozása (Azure Portal) | Microsoft Azure"
    description="Az oktatóanyag során létre fog hozni egy minta Azure Data Factory-folyamatot az Azure Portal Data Factory Editor eszközével."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="05/16/2016"
    ms.author="spelluru"/>

# Az első Azure data factory létrehozása az Azure Portal/Data Factory Editor használatával
> [AZURE.SELECTOR]
- [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md)
- [A Data Factory Editor használata](data-factory-build-your-first-pipeline-using-editor.md)
- [A PowerShell használata](data-factory-build-your-first-pipeline-using-powershell.md)
- [A Visual Studio használata](data-factory-build-your-first-pipeline-using-vs.md)
- [A Resource Manager-sablon használata](data-factory-build-your-first-pipeline-using-arm.md)

Ez az oktatóanyag bemutatja, hogyan hozhatja létre első Azure data factoryját az[Azure Portal](https://portal.azure.com/) használatával. 

## Előfeltételek

1. Mielőtt továbblépne, **mindenképpen** olvassa el [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című cikket, és hajtsa végre az előfeltételként felsorolt lépéseket.
2. Ez a cikk nem nyújt fogalmi áttekintést az Azure Data Factory szolgáltatásról. Javasoljuk, hogy a szolgáltatás részletes áttekintéséhez olvassa el az [Introduction to Azure Data Factory](data-factory-introduction.md) (Az Azure Data Factory bemutatása) című cikket.  

## Data factory létrehozása
A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Például a másolási tevékenység, amely adatokat másol a forrásadattárból a céladattárba, és a HDInsight Hive tevékenység, amely Hive-parancsfájlt futtat a bemeneti adatok kimeneti adatokká történő átalakításához. Ebben a lépésben létrehozzuk a data factoryt. 

1.  Miután bejelentkezett az [Azure Portalra](https://portal.azure.com/), tegye a következőket:
    1.  A bal oldali menüben kattintson a **NEW** (ÚJ) elemre. 
    2.  Kattintson a **Data analytics** (Adatelemzés) elemre a **Create** (Létrehozás) panelen.
    3.  Kattintson a **Data Factory** elemre a **Data analytics** (Adatelemzés) panelen.

        ![A Create (Létrehozás) panel](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.  A **New data factory** (Új data factory) panelen adja meg a **GetStartedDF** nevet.

    ![A New data factory (Új data factory) panel](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

    > [AZURE.IMPORTANT] Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a **Data factory name “GetStartedDF” is not available** (A „GetStartedDF” data factory-név nem érhető el) hibaüzenetet kapja, módosítsa a data factory nevét (például sajátnévGetStartedDF-re), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
    > 
    > A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.
    > 
    > Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.


3.  Jelölje ki azt az **Azure-előfizetést**, ahol létre szeretné hozni a data factoryt. 
4.  Jelöljön ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egy újat. Az oktatóanyag elvégzéséhez hozzon létre egy erőforráscsoportot a következő névvel: **ADFGetStartedRG**.    
5.  Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
6.  A létrehozás alatt álló data factory a következőképpen jelenik meg az Azure Portal **kezdőpultján**:   

    ![A data factory létrehozásának állapota](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Gratulálunk! Sikeresen létrehozta első data factoryjét. A data factory sikeres létrehozása után megjelenik a data factory oldal, amely megjeleníti a data factory tartalmát.  

    ![A Data Factory panel](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

A folyamat létrehozása előtt először létre kell hoznia néhány Data Factory-entitást. Először társított szolgáltatásokat kell létrehoznia, amelyek adattárakat/számítási szolgáltatásokat társítanak az adattárhoz, majd bemeneti és kimeneti adatkészleteket kell meghatároznia, amelyek a társított adattárakban lévő adatokat képviselik, végül létrehozhatja a folyamatot egy olyan tevékenységgel, amely ezeket az adatkészleteket használja. 

## Társított szolgáltatások létrehozása
Ebben a lépésben társítani fogja az Azure Storage-fiókját és egy igény szerinti Azure HDInsight-fürtöt a data factoryjához. Ebben a példában az Azure Storage-fiók a bemeneti és a kimeneti adatokat tárolja a folyamathoz, a HDInsight társított szolgáltatás pedig a folyamat tevékenységében meghatározott Hive-parancsfájlt futtatja. Meg kell határoznia, hogy mely adattárat/számítási szolgáltatásokat használja a forgatókönyvben, és társított szolgáltatások létrehozásával a data factoryhoz kell társítania őket.  

### Azure Storage társított szolgáltatás létrehozása
Ebben a lépésben társítani fogja az Azure Storage-fiókját a data factoryjához. A jelen oktatóanyag esetében ugyanazt az Azure Storage-fiókot fogja használni a bemeneti/kimeneti adatok és a HQL-parancsfájl tárolásához. 

1.  A **GetStartedDF** **DATA FACTORY** panelén kattintson az **Author and deploy** (Fejlesztés és üzembe helyezés) elemre. Ezzel elindítja a Data Factory Editort. 
     
    ![Az Author and deploy (Fejlesztés és üzembe helyezés) csempe](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.  Kattintson a **New data store** (Új adattár) elemre, és válassza az **Azure Storage** elemet.
    
    ![Azure Storage társított szolgáltatás](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

    A szerkesztőben megjelenik az Azure Storage társított szolgáltatás létrehozására szolgáló JSON-parancsfájl. 
4. Az **account name** kifejezést cserélje az Azure Storage-fiókja nevére, az **account key** kifejezést pedig az Azure Storage-fiók kulcsára. A tárelérési kulcs lekérésével kapcsolatos információk: [Tárelérési kulcsok megtekintése, másolása és újragenerálása](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
5. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

    ![A Deploy (Üzembe helyezés) gomb](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   A társított szolgáltatás sikeres üzembe helyezését követően megjelenik a **Draft-1** (Vázlat-1) ablak, amelynek bal oldalán, fanézetben látható az **AzureStorageLinkedService** szolgáltatás. 
    ![Storage társított szolgáltatás a menüben](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)   

 
### Azure HDInsight társított szolgáltatás létrehozása
Ebben a lépésben egy igény szerinti HDInsight-fürtöt fog társítani data factoryjához. A HDInsight-fürtöt a rendszer automatikusan létrehozza a futásidő során, majd törli a feldolgozás befejezését követően, miután egy adott ideig tétlen volt. 

1. A **Data Factory Editorban** kattintson a parancssáv **New compute** (Új számítás) elemére, és válassza az **On-demand HDInsight cluster** (Igény szerinti HDInsight-fürt) lehetőséget.

    ![A New compute (Új számítás) elem](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Másolja és illessze be a **Draft-1** (Vázlat-1) ablak alatti kódrészletet. A JSON-kódrészlet megadja az igény szerinti HDInsight-fürt létrehozásához használt tulajdonságokat. 

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
    
    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:
    
  	| Tulajdonság | Leírás |
  	| :------- | :---------- |
  	| Verzió | Megadja, hogy a HDInsight-fürtnek 3.2-es verziójúnak kell lennie. | 
  	| ClusterSize | Egy csomópontos HDInsight-fürtöt hoz létre. | 
  	| TimeToLive | Megadja, hogy a HDInsight-fürt mennyi ideig lehet tétlen, mielőtt törölné a rendszer. |
  	| linkedServiceName | Megadja a HDInsight által előállított naplók tárolására szolgáló tárfiókot. |

    Vegye figyelembe a következőket: 
    
    - A Data Factory létrehoz egy **Windows-alapú** HDInsight-fürtöt a fenti JSON-fájllal. A szolgáltatás **Linux-alapú** HDInsight-fürtöt is képes létrehozni. További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás). 
    - Igény szerinti HDInsight-fürt helyett **saját HDInsight-fürtöt** is használhat. További információ: [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) (HDInsight társított szolgáltatás).
    - A HDInsight-fürt létrehoz egy **alapértelmezett tárolót ** a JSON-fájlban megadott blob-tárolóban (**linkedServiceName**). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez az elvárt működés. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer mindig létrehoz egy HDInsight-fürt, amikor fel kell dolgozni egy szeletet, kivéve, ha van meglévő élő fürt (**timeToLive**), majd a feldolgozás végén a rendszer törli a fürtöt.
    
        Ahogy egyre több szelet lesz feldolgozva, egyre több tároló jelenik meg az Azure Blob Storage-tárban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: „adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”. Az Azure Blob Storage-tárból olyan eszközökkel törölheti a tárolókat, mint például a [Microsoft Storage Explorer](http://storageexplorer.com/).

    További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás).
3. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére. 
4. Győződjön meg arról, hogy a bal oldali fanézetben megjelenik az **AzureStorageLinkedService** és a **HDInsightOnDemandLinkedService**.

    ![Fanézet a társított szolgáltatásokkal](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## Adatkészletek létrehozása
Ebben a lépésben adatkészleteket fog létrehozni, amelyek a Hive-feldolgozás bemeneti és kimeneti adatait képviselik. Ezek az adatkészletek az oktatóanyag során korábban létrehozott **AzureStorageLinkedService** szolgáltatásra hivatkoznak. A társított szolgáltatás egy Azure Storage-fiókra mutat, az adatkészletek pedig meghatározzák a bemeneti és kimeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet.   

### Bemeneti adatkészlet létrehozása

1. A **Data Factory Editorban** kattintson a parancssáv **New dataset** (Új adatkészlet) elemére, és válassza az **Azure Blob storage** (Azure Blob-tároló) lehetőséget.

    ![New dataset (Új adatkészlet)](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Másolja és illessze be a Draft-1 (Vázlat-1) ablak alatti kódrészletet. A JSON-kódrészletben hozza létre az **AzureBlobInput** nevű adatkészletet, amely a folyamat egyik tevékenységének bemeneti adatait képviseli. Emellett határozza meg azt is, hogy a bemeneti adatok az **adfgetstarted** nevű blob-tárolóban és az **inputdata** nevű mappában találhatók.
        
        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService",
                "typeProperties": {
                    "fileName": "input.log",
                    "folderPath": "adfgetstarted/inputdata",
                    "format": {
                        "type": "TextFormat",
                        "columnDelimiter": ","
                    }
                },
                "availability": {
                    "frequency": "Month",
                    "interval": 1
                },
                "external": true,
                "policy": {}
            }
        } 

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

  	| Tulajdonság | Leírás |
  	| :------- | :---------- |
  	| type | A tulajdonság beállítása AzureBlob, mert az adatok az Azure Blob Storage-tárban találhatók. |  
  	| linkedServiceName | A korábban létrehozott AzureStorageLinkedService szolgáltatásra hivatkozik. |
  	| fileName | Ez a tulajdonság nem kötelező. Ha kihagyja, az összes fájl ki lesz választva a folderPath útvonalról. Ebben az esetben csak az input.log fájl lesz feldolgozva. |
  	| type | A naplófájlok szövegformátumúak, ezért a TextFormat típust használjuk. | 
  	| columnDelimiter | A naplófájlokban vesszővel (,) vannak elválasztva az oszlopok. |
  	| frequency/interval | A frequency (gyakoriság) beállítása Month (Hónap), az interval (időköz) beállítása pedig 1, ami azt jelenti, hogy a bemeneti szeletek havonta érhetők el. | 
  	| external | Ez a tulajdonság true (igaz) értékre van állítva, ha a bemeneti adatokat nem a Data Factory szolgáltatás hozta létre. | 
      
    
3. Az újonnan létrehozott adatkészlet üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére. Az adatkészletnek meg kell jelennie a bal oldali fanézetben. 


### Kimeneti adatkészlet létrehozása
Most a kimeneti adatkészletet fogja létrehozni, amely az Azure Blob Storage-tárban tárolt kimeneti adatokat jelöli. 

1. A **Data Factory Editorban** kattintson a parancssáv **New dataset** (Új adatkészlet) elemére, és válassza az **Azure Blob storage** (Azure Blob-tároló) lehetőséget.  
2. Másolja és illessze be a Draft-1 (Vázlat-1) ablak alatti kódrészletet. A JSON-kódrészletben hozza létre az **AzureBlobOutput** nevű adatkészletet, és határozza meg a Hive-parancsfájl által előállított adatok szerkezetét. Emellett határozza meg azt is, hogy az eredmények tárolása az **adfgetstarted** nevű blob-tárolóban és a **partitioneddata** nevű mappában történjen. Az **availability** (rendelkezésre állás) szakasz meghatározza, hogy a kimeneti adatkészlet előállítása havonta történik.
    
        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
              "folderPath": "adfgetstarted/partitioneddata",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Month",
              "interval": 1
            }
          }
        }

    A tulajdonságok leírását a **Bemeneti adatkészlet létrehozása** című szakaszban tekintheti meg. Külső adatkészlet esetén nem kell beállítani az external (külső) tulajdonságot, mert az adatkészletet a Data Factory szolgáltatás állítja elő.
3. Az újonnan létrehozott adatkészlet üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.
4. Ellenőrizze az adatkészlet létrehozása sikeres volt-e.

    ![Fanézet a társított szolgáltatásokkal](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Folyamat létrehozása
Ebben a lépésben létrehozza a **HDInsightHive** tevékenységgel rendelkező első folyamatát. Vegye figyelembe, hogy a bemeneti szelet havonta érhető el (frequency: Month, interval: 1), a kimeneti szelet előállítása havonta történik, és a tevékenység scheduler (ütemező) tulajdonsága szintén a hónap értékre van beállítva (lásd alább). A kimeneti adatkészlet és a tevékenységütemező beállításainak egyezniük kell. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. Az alábbi JSON-fájlban használt tulajdonságok magyarázata a szakasz végén található. 

1. A **Data Factory Editorban** kattintson a **további parancsokat jelölő három pontra (...)**, majd kattintson a **New pipeline** (Új folyamat) elemre.
    
    ![A New pipeline (Új folyamat) gomb](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Másolja és illessze be a Draft-1 (Vázlat-1) ablak alatti kódrészletet.

    > [AZURE.IMPORTANT] A JSON-fájlban cserélje a **storageaccountname** kifejezést a tárfiókja nevére.
        
        {
            "name": "MyFirstPipeline",
            "properties": {
                "description": "My first Azure Data Factory pipeline",
                "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                            "scriptLinkedService": "AzureStorageLinkedService",
                            "defines": {
                                "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                                "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                            }
                        },
                        "inputs": [
                            {
                                "name": "AzureBlobInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "AzureBlobOutput"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "HDInsightOnDemandLinkedService"
                    }
                ],
                "start": "2016-04-01T00:00:00Z",
                "end": "2016-04-02T00:00:00Z",
                "isPaused": false
            }
        }
 
    A JSON-kódrészletben létrehoz egy folyamatot, amely egyetlen tevékenységből áll, és a tevékenység a Hive használatával dolgozza fel az adatokat egy HDInsight-fürtön.
    
    A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az **AzureStorageLinkedService** nevű scriptLinkedService szolgáltatás által megadva), és az **adfgetstarted** tároló **script** mappájában történik.

    A **defines** (meghatározza) szakasz meghatározza a futásidő beállításait, amelyek Hive konfigurációs értékekként (például ${hiveconf:inputtable}, ${hiveconf:partitionedtable}) lesznek átadva a Hive-parancsfájlnak.

    A folyamat **start** (kezdés) és **end** (befejezés) tulajdonságai a folyamat aktív időszakát határozzák meg.

    A tevékenység JSON-fájljában meg van határozva, hogy a Hive-parancsfájl a **linkedServiceName** – **HDInsightOnDemandLinkedService** által meghatározott számítási szolgáltatáson fut.

    > [AZURE.NOTE] A fenti példában használt JSON-tulajdonságokkal kapcsolatos részletekért lásd az [Anatomy of a Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) (A folyamatok működése) című témakört. 

3. Ellenőrizze az alábbiakat: 
    1. Az **input.log** fájl létezik az Azure blob-tároló **adfgetstarted** tárolójának **inputdata** mappájában.
    2. A **partitionweblogs.hql** fájl létezik az Azure blob-tároló **adfgetstarted** tárolójának **script** mappájában. Ha nem látja ezeket a fájlokat, hajtsa végre [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című cikkben előfeltételként felsorolt lépéseket. 
    3. Ellenőrizze, hogy a folyamat JSON-fájljában lecserélte-e a **storageaccountname** kifejezést a tárfiókja nevére. 
2. A folyamat üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére. Mivel a **start** (kezdés) és az **end** (befejezés) időpontok múltbeli értékekre vannak beállítva, és az **isPaused** tulajdonság értéke false (hamis), a folyamat (a folyamatban foglalt tevékenység) az üzembe helyezés után azonnal fut. 
4. Győződjön meg arról, hogy a folyamat megjelenik a fanézetben.

    ![A fanézet a folyamattal](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Gratulálunk, sikeresen létrehozta első folyamatát!

## Folyamat figyelése

6. A Data Factory Editor paneljeinek a bezárásához és a Data Factory panelre való visszatéréshez kattintson az **X**, majd a **Diagram** elemre.
  
    ![Diagram csempe](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. A diagramnézet áttekintést nyújt az oktatóanyagban használt folyamatokról és adatkészletekről.
    
    ![Diagramnézet](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. A folyamat összes tevékenységének megtekintéséhez kattintson a jobb gombbal a folyamatra a diagramban, majd kattintson az Open pipeline (Folyamat megnyitása) elemre. 

    ![Folyamat megnyitása menü](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. Győződjön meg arról, hogy a HDInsightHive tevékenység megjelenik a folyamatban. 
  
    ![Folyamat megnyitása nézet](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Az előző nézethez való visszatéréshez az oldal tetején lévő navigációs menüben kattintson a **Data factory** elemre. 
10. A **diagramnézetben** kattintson duplán az **AzureBlobInput** adatkészletre. Győződjön meg arról, hogy a szelet **Ready** (Kész) állapotú. Eltarthat néhány percig, amíg a szelet Ready (Kész) állapotúként jelenik meg. Ha ez azután sem történik meg, hogy vár néhány percet, ellenőrizze, hogy a megfelelő tárolóba (adfgetstarted) és mappába (inputdata) helyezte-e a bemeneti fájlt (input.log).

    ![Kész állapotú bemeneti szelet](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. Kattintson az **X** elemre az **AzureBlobInput** panel bezárásához. 
12. A **diagramnézetben** kattintson duplán az **AzureBlobOutput** adatkészletre. Látni fogja, hogy a szelet feldolgozás alatt áll.

    ![Adatkészlet](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. A feldolgozás befejeztével a szelet **Ready** (Kész) állapotú lesz.
    >[AZURE.IMPORTANT] Az igény szerinti HDInsight-fürt létrehozása általában eltart egy ideig (körülbelül 20 percig).  

    ![Adatkészlet](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png) 
    
10. Ha a szelet **Ready** (Kész) állapotú, a kimeneti adatok **adfgetstarted** Blob Storage-tárolójában ellenőrizze a **partitioneddata** mappát.  
 
    ![kimeneti adatok](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

További részletekért tekintse meg a [Monitor and manage pipelines using Azure portal blades](data-factory-monitor-manage-pipelines.md) (Folyamatok figyelése és felügyelete az Azure Portal paneleinek használatával) című cikket. 

> [AZURE.IMPORTANT] A szelet sikeres feldolgozásakor a rendszer törli a bemeneti fájlt. Ezért ha újra le szeretné futtatni a szeletet, vagy újra el szeretné végezni az oktatóanyagban foglaltakat, töltse fel a bemeneti fájlt (input.log) az adfgetstarted tároló inputdata mappájába.

## Összefoglalás 
Az oktatóanyag során létrehozott egy Azure data factoryt, amely egy HDInsight Hadoop-fürtön futtatott Hive-parancsfájllal dolgozza fel az adatokat. Az Azure Portal Data Factory Editor eszközét használta a következő lépések végrehajtásához:  

1.  Létrehozott egy Azure **data factoryt**.
2.  Létrehozott két **társított szolgáltatást**:
    1.  Az **Azure Storage** társított szolgáltatást, amely a bemeneti és kimeneti adatokat tároló Azure blob-tárolót társítja a data factoryval.
    2.  Az **Azure HDInsight** igény szerinti társított szolgáltatást, amely egy igény szerinti HDInsight Hadoop-fürtöt társít a data factoryval. Az Azure Data Factory létrehoz egy HDInsight Hadoop-fürtöt, amely igény szerint dolgozza fel a bemeneti adatokat és állítja elő a kimeneti adatokat. 
3.  Létrehozott két **adatkészletet**, amelyek leírják a bemeneti és kimeneti adatokat a folyamat HDInsight Hive-tevékenysége számára. 
4.  Létrehozott egy **folyamatot** egy **HDInsight Hive**-tevékenységgel. 

## Következő lépések
Az oktatóanyag során létrehozott egy folyamatot egy adatátalakítási tevékenységgel (HDInsight-tevékenység), amely Hive-parancsfájlt futtat egy igény szerinti HDInsight-fürtön. Ha tudni szeretné, hogyan használhatja a Másolás tevékenységet az adatok Azure-blobból Azure SQL Database adatbázisba történő másolásához, tekintse meg a következő cikket: [Tutorial: Copy data from an Azure blob to Azure SQL](./data-factory-get-started.md) (Oktatóanyag: adatok másolása Azure-blobból Azure SQL Database adatbázisba).

## Lásd még:
| Témakör | Leírás |
| :---- | :---- |
| [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) | Ez a cikk felsorolja az Azure Data Factory által támogatott adatátalakítási tevékenységeket (mint például a jelen oktatóanyagban használt HDInsight Hive-átalakítás). | 
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) | Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok](data-factory-create-pipelines.md) | Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) | Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban.
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) | Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. 

  




<!--HONumber=jun16_HO2-->


