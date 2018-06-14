---
title: Az első data factory létrehozása (Azure Portal) | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy minta Azure Data Factory-folyamatot az Azure Portal Data Factory Editor eszközével.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: d5b14e9e-e358-45be-943c-5297435d402d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 75139e39c3391a7662e3d02ee8d56463ac9fcc7a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406558"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Oktatóanyag: Az első adat-előállító létrehozása az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás előzetes verzióban elérhető 2-es verzióját használja, tekintse meg az [adat-előállító Data Factory 2-es verziójával való létrehozását ismertető rövid útmutatót](../quickstart-create-data-factory-dot-net.md).

Ez az oktatóanyag bemutatja, hogyan hozhatja létre első adat-előállítóját az[Azure Portal](https://portal.azure.com/) használatával. Ha ezt az oktatóanyagot más eszközök/SDK-k használatával szeretné elvégezni, válassza ki az egyik lehetőséget a legördülő listából. 

A jelen oktatóanyagban szereplő folyamat egyetlen tevékenységet tartalmaz: ez az Azure HDInsight Hive-tevékenység. A tevékenység egy Hive-szkriptet futtat egy HDInsight-fürtön, amely a bemeneti adatok átalakításával állítja elő a kimeneti adatokat. A folyamat úgy van ütemezve, hogy havonta egyszer fusson a megadott kezdő és befejező időpontok közt. 

> [!NOTE]
> Az oktatóanyagban található adatfolyamat átalakítja a bemeneti adatokat, hogy ezzel kimeneti adatokat hozzon létre. Az adatok Data Factory használatával történő másolására vonatkozó útmutatásért lásd [az adatok Azure Blob Storage-ból Azure SQL Database-be történő másolását ismertető oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További tudnivalókért lásd: [Ütemezés és végrehajtás a Data Factoryban](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Előfeltételek
Olvassa el [Az oktatóanyag áttekintését](data-factory-build-your-first-pipeline.md), és kövesse az „Előfeltételek” varázslóban leírt lépéseket.

Ez a cikk nem nyújt fogalmi áttekintést a Data Factory szolgáltatásról. További információ a szolgáltatásról: [Az Azure Data Factory bemutatása](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Data factory létrehozása
A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Ilyen például a másolási tevékenység, amely adatokat másol a forrásból a célként megadott adattárba. Egy másik példa a HDInsight Hive-tevékenység, amely egy Hive-szkript futtatásával átalakítja a bemeneti adatokat és létrehozza a kimeneti adatokat. 

Adat-előállító létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Kattintson az **Új** > **Adatok + analitika** > **Adat-előállító** elemre.

   ![A Create (Létrehozás) panel](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

3. Az **Új adat-előállító** **Név** mezőjében írja be a **GetStartedDF** értéket.

   ![A New data factory (Új data factory) panel](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > Az adat-előállító nevének globálisan egyedinek kell lennie. Ha „A(z) „GetStartedDF” adat-előállító nem érhető el” hibaüzenetet kapja, módosítsa a nevet. Például használja az ÖnNeveGetStartedDF nevet, majd próbálkozzon újra az adat-előállító létrehozásával. További információ az elnevezési szabályokról: [A Data Factory elnevezési szabályai](data-factory-naming-rules.md).
   >
   > Az adat-előállító neve később DNS-névként regisztrálható, és nyilvánosan láthatóvá tehető.
   >
   >
4. Az **Előfizetés** területen válassza ki azt az Azure-előfizetést, ahol létre kívánja hozni az adat-előállítót.

5. Jelöljön ki egy meglévő erőforráscsoportot, vagy hozzon létre egyet. Az oktatóanyag elvégzéséhez hozzon létre egy erőforráscsoportot a következő névvel: **ADFGetStartedRG**.

6. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listában csak a Data Factory szolgáltatás által támogatott régiók jelennek meg.

7. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet.

8. Kattintson a **Létrehozás** gombra.

   > [!IMPORTANT]
   > Data Factory-példányok létrehozásához a [Data Factory közreműködője](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.
   >
   >
9. Az irányítópulton megjelenő csempén a **Adat-előállító üzembe helyezése** állapotleírás látható:    

   ![Adat-előállító üzembe helyezése – állapot](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

10. Az adat-előállító sikeres létrehozása után megjelenik az **Adat-előállító** oldal, amely megjeleníti az adat-előállító tartalmát.     

    ![Adat-előállító panel](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Mielőtt létrehozna egy folyamatot az adat-előállítóban, először létre kell hoznia néhány adatelőállító-entitást. Először hozza létre a társított szolgáltatásokat, amelyek összekapcsolják az adattárakat/számításokat az Ön adattárával. Majd definiálja a bemeneti és kimeneti adatkészleteket, amelyek a kapcsolódó adattárakban lévő bemeneti és kimeneti adatokat képviselik. Végül hozza létre a folyamatot egy tevékenységgel, amely ezeket az adatkészleteket használja.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben az Azure Storage-fiókját és egy igény szerinti HDInsight-fürtöt társít az adat-előállítóhoz. Ebben a példában a tárfiók a folyamat bemeneti és kimeneti adatait tárolja. A HDInsight társított szolgáltatás a mintában szereplő folyamat tevékenységében meghatározott Hive-szkriptet futtatja. Határozza meg, hogy mely [adattárat](data-factory-data-movement-activities.md)/[számítási szolgáltatásokat](data-factory-compute-linked-services.md) használja a forgatókönyvben. Ezután társítsa ezeket a szolgáltatásokat az adat-előállítóhoz társított szolgáltatások létrehozásával.  

### <a name="create-a-storage-linked-service"></a>Storage-beli társított szolgáltatás létrehozása
Ebben a lépésben társítja a tárfiókot az adat-előállítójához. A jelen oktatóanyag esetében ugyanazt a tárfiókot fogja használni a bemeneti/kimeneti adatok és a HQL-szkript tárolásához.

1. A **GetStartedDF** **Data Factory** panelén kattintson a **Fejlesztés és üzembe helyezés** elemre. Ekkor megjelenik a Data Factory Editor.

   ![Az Author and deploy (Fejlesztés és üzembe helyezés) csempe](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

2. Kattintson az **Új adattár** elemre, és válassza az **Azure Storage** lehetőséget.

   ![Új adattár panel](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

3. A szerkesztőben megjelenik a Storage társított szolgáltatás létrehozására szolgáló JSON-szkript.

   ![Storage társított szolgáltatás](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

4. Cserélje le az **account name** kifejezést a tárfiókja nevére. Az **account key** kifejezést cserélje le a tárfiók hozzáférési kulcsára. A tárelérési kulcs lekéréséről [a tárfiók kezelését](../../storage/common/storage-create-storage-account.md#manage-your-storage-account) ismertető szakasz tárelérési kulcsok megtekintéséről, másolásáról és ismételt létrehozásáról szóló részében olvashat.

5. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Üzembe helyezés** elemére.

    ![A Deploy (Üzembe helyezés) gomb](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   A társított szolgáltatás sikeres üzembe helyezése után megjelenik a Draft-1 (Vázlat-1) ablak. Az ablak bal oldalán, fanézetben látható az **AzureStorageLinkedService** szolgáltatás.

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>HDInsight társított szolgáltatás létrehozása
Ebben a lépésben egy igény szerinti HDInsight-fürtöt társít a data factoryhoz. Az HDInsight-fürt automatikusan jön létre futásidőben. A feldolgozás befejezése és adott idejű tétlenség után törlődik.

1. A Data Factory Editorban válassza a **Továbbiak** > **Új számítás** > **Igény szerinti HDInsight-fürt** lehetőséget.

    ![A New compute (Új számítás) elem](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

2. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-kódrészlet megadja az igény szerinti HDInsight-fürt létrehozásához használt tulajdonságokat.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat.

   | Tulajdonság | Leírás |
   |:--- |:--- |
   | clusterSize |Megadja a HDInsight-fürt méretét. |
   | timeToLive | Megadja, hogy a HDInsight-fürt mennyi ideig lehet tétlen, mielőtt törölné a rendszer. |
   | linkedServiceName | Ez megadja a HDInsight által előállított naplók tárolására szolgáló tárfiókot. |

    Vegye figyelembe a következő szempontokat:

     a. A adat-előállító létrehoz egy Linux-alapú HDInsight-fürtöt a JSON-tulajdonságokkal. További információ: [Igény szerinti HDInsight társított szolgáltatás](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. Igény szerinti HDInsight-fürt helyett saját HDInsight-fürtöt is használhat. További információ: [HDInsight társított szolgáltatás](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. Az HDInsight-fürt létrehoz egy alapértelmezett tárolót a JSON-tulajdonságban megadott blobtárolóban (**linkedServiceName**). Az HDInsight a fürt törlésekor nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer a szeletek feldolgozásakor mindig létrehoz egy HDInsight-fürtöt, kivéve, ha van meglévő élő fürt (**timeToLive**). A fürt automatikusan törlődik a feldolgozás befejezésekor.

     Ahogy a rendszer egyre több szeletet dolgoz fel, egyre több tároló jelenik meg a blobtárolóban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: „adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”. A blobtárolóból olyan eszközökkel törölheti a tárolókat, mint például az [Azure Storage Explorer](http://storageexplorer.com/).

     További információ: [Igény szerinti HDInsight társított szolgáltatás](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

3. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Üzembe helyezés** elemére.

    ![Üzembe helyezés lehetőség](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

4. Győződjön meg arról, hogy a bal oldali fanézetben megjelenik az **AzureStorageLinkedService** és a **HDInsightOnDemandLinkedService**.

    ![Fanézet a társított szolgáltatásokkal](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek a Hive-feldolgozás bemeneti és kimeneti adatait képviselik. Ezek az adatkészletek az oktatóanyagban előzőleg létrehozott AzureStorageLinkedService szolgáltatásra hivatkoznak. A társított szolgáltatás egy tárfiókra mutat. Az adatkészletek meghatározzák a bemeneti és kimeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet.   

### <a name="create-the-input-dataset"></a>A bemeneti adatkészlet létrehozása
1. A Data Factory Editorban válassza a **Továbbiak** > **Új adatkészlet** > **Azure Blob-tároló** lehetőséget.

    ![New dataset (Új adatkészlet)](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

2. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-kódrészletben hozza létre az **AzureBlobInput** nevű adatkészletet, amely a folyamat egyik tevékenységének bemeneti adatait képviseli. Emellett határozza meg azt is, hogy a bemeneti adatok az **adfgetstarted** nevű blobtárolóban és az **inputdata** nevű mappában találhatók.

    ```JSON
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
    ```
    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat.

   | Tulajdonság | Leírás |
   |:--- |:--- |
   | type |A tulajdonság beállítása **AzureBlob**, mert az adatok blobtárolóban találhatók. |
   | linkedServiceName |Az előzőleg létrehozott AzureStorageLinkedService szolgáltatásra hivatkozik. |
   | folderPath | A bemeneti blobokat tartalmazó blobtárolót és mappát adja meg. | 
   | fileName |Ez a tulajdonság nem kötelező. Ha kihagyja, az összes fájl ki lesz választva a folderPath útvonalról. Ebben az oktatóanyagban csak az input.log fájl feldolgozása történik meg. |
   | type |A naplófájlok szövegformátumúak, ezért a **TextFormat** típust használjuk. |
   | columnDelimiter |A naplófájlokban az oszlopok vesszővel (`,`) vannak elválasztva. |
   | frequency/interval |A gyakoriság beállítás értéke **Hónap**, az időköz beállítás értéke **1**, tehát a bemeneti szeletek havonta érhetők el. |
   | external | Ez a tulajdonság **igaz** értékre van állítva, ha a bemeneti adatokat nem ez a folyamat hozta létre. Mivel ebben az oktatóanyagban a bemeneti naplófájlt nem ez a folyamat hozta létre, a tulajdonság értéke **igaz**. |

    További információ ezekről a JSON-tulajdonságokról: [Azure Blob-összekötő](data-factory-azure-blob-connector.md#dataset-properties).

3. Az újonnan létrehozott adatkészlet üzembe helyezéséhez kattintson a parancssor **Üzembe helyezés** elemére. Az adatkészlet megjelenik a bal oldali fanézetben.

### <a name="create-the-output-dataset"></a>A kimeneti adatkészlet létrehozása
Most a kimeneti adatkészletet hozza létre, amely a blobtárolóban tárolt kimeneti adatokat képviseli.

1. A Data Factory Editorban válassza a **Továbbiak** > **Új adatkészlet** > **Azure Blob-tároló** lehetőséget.

2. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba. A JSON-kódrészletben hozza létre az **AzureBlobOutput** nevű adatkészletet, és határozza meg a Hive-szkript által előállított adatok szerkezetét. Emellett határozza meg azt is, hogy az eredmények tárolása az **adfgetstarted** nevű blob-tárolóban és a **partitioneddata** nevű mappában történjen. Az **availability** (rendelkezésre állás) szakasz meghatározza, hogy a kimeneti adatkészlet havonta jön létre.

    ```JSON
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
    ```
    A tulajdonságok leírását a „Bemeneti adatkészlet létrehozása” című szakaszban tekintheti meg. Külső adatkészlet esetén nem kell beállítania a külső tulajdonságot, mert az adatkészletet a Data Factory szolgáltatás állítja elő.

3. Az újonnan létrehozott adatkészlet üzembe helyezéséhez kattintson a parancssor **Üzembe helyezés** elemére.

4. Ellenőrizze az adatkészlet létrehozása sikeres volt-e.

    ![Fanézet a társított szolgáltatásokkal](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehozza a HDInsight Hive-tevékenységgel rendelkező első folyamatot. A bementi szelet havonta érhető el (a gyakoriság értéke hónap, az időközé 1). A kimeneti szelet havonta jön létre. A tevékenység ütemező tulajdonsága szintén havi értékre van állítva. A kimeneti adatkészlet és a tevékenységütemező beállításainak egyezniük kell. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. Az alábbi JSON-kódrészletben használt tulajdonságok magyarázata a szakasz végén található.

1. A Data Factory Editorban válassza a **Továbbiak** > **Új folyamat** lehetőséget.

    ![Az Új folyamat lehetőség](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

2. Másolja és illessze be a következő kódrészletet a Draft-1 (Vázlat-1) ablakba.

   > [!IMPORTANT]
   > A JSON-kódrészletben cserélje a **storageaccountname** kifejezést a tárfiókja nevére.
   >
   >

    ```JSON
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
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    A JSON-kódrészletben létrehoz egy folyamatot, amely egyetlen tevékenységből áll, és a tevékenység a Hive használatával dolgozza fel az adatokat egy HDInsight-fürtön.

    A **partitionweblogs.hql** nevű Hive-szkriptfájl tárolása az **AzureStorageLinkedService** nevű scriptLinkedService szolgáltatással megadott tárfiókban történik. A fájl az **adfgetstarted** tároló **script** mappájában található.

    A **defines** (meghatározza) szakasz meghatározza a futásidő beállításait, amelyeket a rendszer Hive konfigurációs értékként ad át a Hive-szkriptnek. Például: ${hiveconf:inputtable}, és ${hiveconf:partitionedtable}.

    A folyamat **start** (kezdés) és **end** (befejezés) tulajdonságai a folyamat aktív időszakát határozzák meg.

    A tevékenység JSON-fájljában meg van határozva, hogy a Hive-szkript a **linkedServiceName**: **HDInsightOnDemandLinkedService** által meghatározott számítási szolgáltatáson fut.

   > [!NOTE]
   > A példában használt JSON-tulajdonságokkal kapcsolatos további információért lásd a [Data Factory folyamatait és tevékenységeit](data-factory-create-pipelines.md) ismertető témakör a folyamat JSON-fájljáról szóló részét.
   >
   >
3. Ellenőrizze az alábbiakat:

   a. Az **input.log** fájl létezik a blobtároló **adfgetstarted** tárolójának **inputdata** mappájában.

   b. A **partitionweblogs.hql** fájl létezik a blobtároló **adfgetstarted** tárolójának **script** mappájában. Ha nem látja ezeket a fájlokat, hajtsa végre [az oktatóanyag áttekintésének](data-factory-build-your-first-pipeline.md) „Előfeltételek” szakaszában felsorolt lépéseket.

   c. A folyamat JSON-fájljában lecserélte a **storageaccountname** kifejezést a tárfiókja nevére.

4. A folyamat üzembe helyezéséhez kattintson a parancssor **Üzembe helyezés** elemére. Mivel a **start** (kezdés) és az **end** (befejezés) időpontok múltbeli értékekre vannak beállítva, és az **isPaused** tulajdonság értéke **false** (hamis), a folyamat (a folyamatban foglalt tevékenység) az üzembe helyezés után azonnal fut.

5. Győződjön meg arról, hogy a folyamat megjelenik a fanézetben.

    ![A fanézet a folyamattal](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>Folyamat monitorozása
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Folyamat monitorozása a diagramnézet használatával
1. A **Data Factory** panelen kattintson a **Diagram** elemre.

    ![Diagram csempe](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

2. A **diagramnézet** áttekintést nyújt az oktatóanyagban használt folyamatokról és adatkészletekről.

    ![Diagramnézet](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

3. A folyamat összes tevékenységének megtekintéséhez kattintson a jobb gombbal a folyamatra a diagramban, majd kattintson a **Feldolgozási sor megnyitása** elemre.

    ![Folyamat megnyitása menü](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

4. Győződjön meg arról, hogy a **Hive-tevékenység** megjelenik a folyamatban.

    ![Folyamat megnyitása nézet](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Az előző nézethez való visszatéréshez a fenti menüben kattintson a **Data Factory** elemre.

5. A **diagramnézetben** kattintson duplán az **AzureBlobInput** adatkészletre. Győződjön meg arról, hogy a szelet állapota **Kész**. Eltarthat néhány percig, amíg a szelet **Kész** állapotúként jelenik meg. Ha ez néhány perces várakozás után sem történik meg, ellenőrizze, hogy a megfelelő tárolóba (**adfgetstarted**) és mappába (**inputdata**) helyezte-e a bemeneti fájlt (**input.log**).

   ![Kész állapotú bemeneti szelet](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

6. Zárja be az **AzureBlobInput** panelt.

7. A **diagramnézetben** kattintson duplán az **AzureBlobOutput** adatkészletre. Megjelenik a feldolgozás alatt álló szelet.

   ![Adatkészlet feldolgozása folyamatban](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

8. A feldolgozás végeztével a szelet **Kész** állapotúra vált.

   ![Kész állapotú adatkészlet](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > Az igény szerinti HDInsight-fürt létrehozása általában körülbelül 20 percet vesz igénybe. A folyamat várhatóan körülbelül 30 perc alatt dolgozza fel a szeletet.
   >
   >

9. Ha a szelet **Kész** állapotú, a kimeneti adatok **adfgetstarted** Blob Storage-tárolójában ellenőrizze a **partitioneddata** mappát.  

   ![Kimeneti adatok](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

10. A szelet részleteinek az **Adatszelet** panelen való megtekintéséhez kattintson a szeletre.

    ![Az adatszeletre vonatkozó információk](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

11. A **Tevékenységfuttatások** listában válasszon egy tevékenységet a további információk megtekintéséhez. (Ebben a forgatókönyvben ez egy Hive-tevékenység.) Az információ a **Tevékenységfuttatás** részletei panelen jelenik meg.   

    ![Tevékenységfuttatás részletei ablak](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   A naplófájlokban láthatja a végrehajtott Hive-lekérdezést és az állapotadatokat. A naplók hasznosak bármilyen hiba esetén a hibaelhárításban.
   További információ: [Folyamatok monitorozása és kezelése az Azure Portal paneljeivel](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> A szelet sikeres feldolgozásakor a rendszer törli a bemeneti fájlt. Ezért ha újra futtatni kívánja a szeletet, vagy újra el szeretné végezni az oktatóanyagban foglaltakat, töltse fel a bemeneti fájlt (**input.log**) az **adfgetstarted** tároló **inputdata** mappájába.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Folyamat monitorozása a Monitor & Manage alkalmazással
A folyamatok monitorozásához a Monitor & Manage alkalmazást is használhatja. Az [Azure Data Factory-folyamatok Monitor & Manage alkalmazással való figyelését és felügyeletét](data-factory-monitor-manage-app.md) ismertető cikkben további információt találhat az alkalmazás használatával kapcsolatban.

1. Kattintson a **Monitor & Manage** csempére a Data Factory kezdőlapján.

    ![Monitor & Manage csempe](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

2. A Monitor & Manage alkalmazásban módosítsa a **kezdési idő** és a **befejezési idő** értékét, hogy megfeleljen a folyamat kezdési és befejezési idejének. Kattintson az **Alkalmaz** gombra.

    ![Monitor & Manage alkalmazás](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

3. Válasszon egy tevékenységablakot az **Activity Windows** (Tevékenységablakok) listában az információk megtekintéséhez.

    ![Tevékenységablakok lista](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Összegzés
Az oktatóanyag során létrehozott egy adat-előállítót, amely egy HDInsight Hadoop-fürtön futtatott Hive-szkripttel dolgozza fel az adatokat. Az Azure Portal Data Factory Editor eszközét használta a következő lépések végrehajtásához:  

* Adat-előállító létrehozása
* Két társított szolgáltatás létrehozása:
   * Egy Storage társított szolgáltatás, amely a bemeneti és kimeneti adatokat tároló Azure blob-tárolót társítja az adat-előállítóval.
   * Egy HDInsight igény szerinti társított szolgáltatás, amely egy igény szerinti HDInsight Hadoop-fürtöt társít az adat-előállítóval. A Data Factory a megfelelő időben létrehoz egy HDInsight Hadoop-fürtöt, amely igény szerint dolgozza fel a bemeneti adatokat és állítja elő a kimeneti adatokat.
* Két adatkészlet létrehozása, amelyek leírják a bemeneti és kimeneti adatokat a folyamat HDInsight Hive-tevékenysége számára.
* Egy HDInsight Hive-tevékenységgel rendelkező folyamat létrehozása.

## <a name="next-steps"></a>További lépések
Az oktatóanyag során létrehozott egy folyamatot egy adatátalakítási tevékenységgel (HDInsight-tevékenység), amely egy Hive-szkriptet futtat egy igény szerinti HDInsight-fürtön. Ha tudni szeretné, hogyan használhatja a Másolás tevékenységet az adatok Azure-blobból Azure SQL Database-adatbázisba történő másolásához, lásd: [Oktatóanyag: Adatok másolása blobtárolóból az SQL Database-be](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Lásd még
| Témakör | Leírás |
|:--- |:--- |
| [Folyamatok](data-factory-create-pipelines.md) |Ennek a cikknek a segítségével megismerheti a Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) |Ennek a cikknek a segítségével megismerheti a Data Factory adatkészleteit. |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) |Ez a cikk ismerteti a Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok monitorozása és felügyelete a Monitor & Manage alkalmazással](data-factory-monitor-manage-app.md) |Ez a cikk ismerteti, hogyan monitorozhatja és kezelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management alkalmazással. |
