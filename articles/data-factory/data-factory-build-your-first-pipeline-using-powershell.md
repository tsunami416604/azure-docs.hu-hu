<properties
    pageTitle="Az első data factory létrehozása (PowerShell) | Microsoft Azure"
    description="Az oktatóanyag során létre fog hozni egy minta Azure Data Factory-folyamatot az Azure PowerShell használatával."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/16/2016"
    ms.author="spelluru"/>

# Az első Azure data factory létrehozása az Azure PowerShell használatával
> [AZURE.SELECTOR]
- [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md)
- [A Data Factory Editor használata](data-factory-build-your-first-pipeline-using-editor.md)
- [A PowerShell használata](data-factory-build-your-first-pipeline-using-powershell.md)
- [A Visual Studio használata](data-factory-build-your-first-pipeline-using-vs.md)
- [A Resource Manager-sablon használata](data-factory-build-your-first-pipeline-using-arm.md)


Ez az oktatóanyag bemutatja, hogyan hozhatja létre első Azure data factoryját az Azure PowerShell használatával. 


## Előfeltételek
Az oktatóanyag áttekintése című témakörben felsorolt előfeltételek mellett a következőket kell telepítenie:

- Mielőtt továbblépne, **mindenképpen** olvassa el [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című cikket, és hajtsa végre az előfeltételként felsorolt lépéseket.
- **Azure PowerShell**. Kövesse a [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) című cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre.
- (elhagyható) Ez a cikk nem tárgyalja az összes Data Factory-parancsmagot. A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentációért tekintse meg a [Data Factory Cmdlet Reference](https://msdn.microsoft.com/library/dn820234.aspx) (Data Factory-parancsmagok referenciája) című cikket. 

Ha az **1.0-s verziónál régebbi** Azure PowerShellt használ, az [itt](https://msdn.microsoft.com/library/azure/dn820234.aspx) ismertetett parancsmagokat kell használnia. A Data Factory-parancsmagok használata előtt emellett futtatnia kell az alábbi parancsokat: 
 
1. Indítsa el az Azure PowerShellt, és futtassa a következő parancsokat. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.
    1. Futtassa az **Add-AzureAccount** parancsot, és adja meg a felhasználónevet és jelszót, amelyekkel az Azure Portalra jelentkezik be.
    2. Futtassa a **Get-AzureSubscription** parancsot a fiókhoz tartozó előfizetések megtekintéséhez.
    3. Futtassa a **Select-AzureSubscription** parancsot a kívánt előfizetés kiválasztásához. Ennek az előfizetésnek egyeznie kell az Azure Portalon használt előfizetéssel.
4. Váltson AzureResourceManager módba, mivel az Azure Data Factory-parancsmagok ebben a módban érhetők el: **Switch-AzureMode AzureResourceManager**.

A részletekért tekintse meg a [Deprecation of Switch AzureMode in Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell) (A Switch AzureMode elévülése az Azure PowerShellben) című cikket. 


## Data factory létrehozása

Ebben a lépésben az Azure PowerShell használatával létrehozza a **FirstDataFactoryPSH** nevű Azure data factoryt. A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Például a másolási tevékenység, amely adatokat másol a forrásadattárból a céladattárba, és a HDInsight Hive tevékenység, amely Hive-parancsfájlt futtat a bemeneti adatok kimeneti adatokká történő átalakításához. Ebben a lépésben létrehozzuk a data factoryt. 

1. Indítsa el az Azure PowerShellt, és futtassa az alábbi parancsot. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.
    - Futtassa a **Login-AzureRmAccount** parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.  
    - Futtassa a **Get-AzureRmSubscription** parancsot a fiókhoz elérhető összes előfizetés megtekintéséhez.
    - Futtassa a **Select-AzureRmSubscription <Name of the subscription>** parancsot a használni kívánt előfizetés kiválasztásához. Ennek az előfizetésnek egyeznie kell az Azure Portalon használt előfizetéssel.
3. Hozzon létre egy Azure-erőforráscsoportot **ADFTutorialResourceGroup** néven a következő parancs futtatásával.

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Az oktatóanyag különböző lépései során feltételezzük, hogy az ADFTutorialResourceGroup elnevezésű erőforráscsoportot használja. Ha másik erőforráscsoportot használ, akkor az oktatóanyagban azt használja az ADFTutorialResourceGroup helyett.
4. Futtassa a **New-AzureRmDataFactory** parancsmagot a **FirstDataFactoryPSH** nevű data factory létrehozásához.  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"


Vegye figyelembe a következőket:
 
- Az Azure Data Factory nevének globálisan egyedinek kell lennie. Ha a **Data factory name “FirstDataFactoryPSH” is not available** (A „FirstDataFactoryPSH” data factory-név nem érhető el) hibaüzenetet kapja, módosítsa a nevet (például: azÖnneveFirstDataFactoryPSH). Használja ezt az ADFTutorialFactoryPSH helyett az oktatóanyag lépéseinek végrehajtása során. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
- Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.
- A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.
- Ha a „**This subscription is not registered to use namespace Microsoft.DataFactory**” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel: 

    - Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Az alábbi parancs futtatásával ellenőrizheti a Data Factory-szolgáltató regisztrálását. 
    
            Get-AzureRmResourceProvider
    - Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel automatikusan regisztrálja a szolgáltatót.

A folyamat létrehozása előtt először létre kell hoznia néhány Data Factory-entitást. Először társított szolgáltatásokat kell létrehoznia, amelyek adattárakat/számítási szolgáltatásokat társítanak az adattárhoz, majd bemeneti és kimeneti adatkészleteket kell meghatároznia, amelyek a társított adattárakban lévő adatokat képviselik, végül létrehozhatja a folyamatot egy olyan tevékenységgel, amely ezeket az adatkészleteket használja. 

## Társított szolgáltatások létrehozása 
Ebben a lépésben társítani fogja az Azure Storage-fiókját és egy igény szerinti Azure HDInsight-fürtöt a data factoryjához. Ebben a példában az Azure Storage-fiók a bemeneti és a kimeneti adatokat tárolja a folyamathoz, a HDInsight társított szolgáltatás pedig a folyamat tevékenységében meghatározott Hive-parancsfájlt futtatja. Meg kell határoznia, hogy mely adattárat/számítási szolgáltatásokat használja a forgatókönyvben, és társított szolgáltatások létrehozásával a data factoryhoz kell társítania őket.

### Azure Storage társított szolgáltatás létrehozása
Ebben a lépésben társítani fogja az Azure Storage-fiókját a data factoryjához. A jelen oktatóanyag esetében ugyanazt az Azure Storage-fiókot fogja használni a bemeneti/kimeneti adatok és a HQL-parancsfájl tárolásához.

1. Hozzon létre egy StorageLinkedService.json nevű JSON-fájlt a C:\ADFGetStarted mappában az alábbi tartalommal. Ha még nem létezik, hozza létre az ADFGetStarted mappát.

        {
            "name": "StorageLinkedService",
            "properties": {
                "type": "AzureStorage",
                "description": "",
                "typeProperties": {
                    "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        }

    Az **account name** kifejezést cserélje az Azure Storage-fiókja nevére, az **account key** kifejezést pedig az Azure Storage-fiók kulcsára. A tárelérési kulcs lekérésével kapcsolatos információk: [Tárelérési kulcsok megtekintése, másolása és újragenerálása](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2. Az Azure PowerShellben váltson az ADFGetStarted mappára.
3. Társított szolgáltatásokat a **New-AzureRmDataFactoryLinkedService** parancsmag használatával hozhat létre. Ehhez, valamint az oktatóanyagban használt többi Data Factory-parancsmaghoz is meg kell adnia értékeket a *ResourceGroupName* és a *DataFactoryName* paraméterek számára. Vagy a **Get-AzureRmDataFactory** használatával lekérhet egy **DataFactory**-objektumot, és továbbíthatja azt anélkül, hogy minden egyes alkalommal meg kellene adnia a *ResourceGroupName* és a *DataFactoryName* értékeket a parancsmag futtatásakor. Futtassa az alábbi parancsot a **Get-AzureRmDataFactory** parancsmag kimenetének hozzárendeléséhez a **$df** változóhoz.

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. Most futtassa a **New-AzureRmDataFactoryLinkedService** parancsmagot a **StorageLinkedService** társított szolgáltatás létrehozásához.

        New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

    Ha nem futtatta volna a **Get-AzureRmDataFactory** parancsmagot, és nem rendelte volna hozzá a kimenetet a **$df** változóhoz, meg kellene adnia a *ResourceGroupName* és a *DataFactoryName* paraméterek értékeit az alábbiak szerint.

        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

    Ha az oktatóanyag közepén bezárta az Azure PowerShellt, a következő indításakor futtatnia kell a **Get-AzureRmDataFactory** parancsmagot az oktatóanyag befejezéséhez.

### Azure HDInsight társított szolgáltatás létrehozása
Ebben a lépésben egy igény szerinti HDInsight-fürtöt fog társítani data factoryjához. A HDInsight-fürtöt a rendszer automatikusan létrehozza a futásidő során, majd törli a feldolgozás befejezését követően, miután egy adott ideig tétlen volt. Igény szerinti HDInsight-fürt helyett saját HDInsight-fürtöt is használhat. További információ: [Compute Linked Services](data-factory-compute-linked-services.md) (Számítási társított szolgáltatás).  

1. Hozzon létre egy **HDInsightOnDemandLinkedService**.json nevű JSON-fájlt a **C:\ADFGetStarted** mappában az alábbi tartalommal.

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "StorageLinkedService"
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
2. Futtassa a **New-AzureRmDataFactoryLinkedService** parancsmagot aHDInsightOnDemandLinkedService nevű társított szolgáltatás létrehozásához.

        New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## Adatkészletek létrehozása
Ebben a lépésben adatkészleteket fog létrehozni, amelyek a Hive-feldolgozás bemeneti és kimeneti adatait képviselik. Ezek az adatkészletek az oktatóanyag során korábban létrehozott **StorageLinkedService** szolgáltatásra hivatkoznak. A társított szolgáltatás egy Azure Storage-fiókra mutat, az adatkészletek pedig meghatározzák a bemeneti és kimeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet.   

### Bemeneti adatkészlet létrehozása
1. Hozzon létre egy **InputTable.json** nevű JSON-fájlt a **C:\ADFGetStarted** mappában az alábbi tartalommal:

        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "StorageLinkedService",
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

    A fenti JSON-kódrészlet meghatározza az **AzureBlobInput** nevű adatkészletet, amely a bemeneti adatokat jelöli a folyamat egyik tevékenységéhez. Emellett azt is meghatározza, hogy a bemeneti adatok az **adfgetstarted** nevű blobtárolóban és az **inputdata** nevű mappában találhatók.

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

  	| Tulajdonság | Leírás |
  	| :------- | :---------- |
  	| type | A tulajdonság beállítása AzureBlob, mert az adatok az Azure Blob Storage-tárban találhatók. |  
  	| linkedServiceName | A korábban létrehozott StorageLinkedService szolgáltatásra hivatkozik. |
  	| fileName | Ez a tulajdonság nem kötelező. Ha kihagyja, az összes fájl ki lesz választva a folderPath útvonalról. Ebben az esetben csak az input.log fájl lesz feldolgozva. |
  	| type | A naplófájlok szövegformátumúak, ezért a TextFormat típust használjuk. | 
  	| columnDelimiter | A naplófájlokban vesszővel (,) vannak elválasztva az oszlopok. |
  	| frequency/interval | A frequency (gyakoriság) beállítása Month (Hónap), az interval (időköz) beállítása pedig 1, ami azt jelenti, hogy a bemeneti szeletek havonta érhetők el. | 
  	| external | Ez a tulajdonság true (igaz) értékre van állítva, ha a bemeneti adatokat nem a Data Factory szolgáltatás hozta létre. | 

2. A Data Factory-adatkészlet létrehozásához futtassa az alábbi parancsot az Azure PowerShellben.

        New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### Kimeneti adatkészlet létrehozása
Most a kimeneti adatkészletet fogja létrehozni, amely az Azure Blob Storage-tárban tárolt kimeneti adatokat jelöli.

1. Hozzon létre egy **OutputTable.json** nevű JSON-fájlt a **C:\ADFGetStarted** mappában az alábbi tartalommal:

        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
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

    A fenti JSON-kódrészlet meghatározza az **AzureBlobOutput** nevű adatkészletet, amely a kimeneti adatokat jelöli a folyamat egyik tevékenységéhez. Emellett azt is meghatározza, hogy az eredmények tárolása az **adfgetstarted** nevű blobtárolóban és a **partitioneddata** nevű mappában történik. Az **availability** (rendelkezésre állás) szakasz meghatározza, hogy a kimeneti adatkészlet előállítása havonta történik.

2. A Data Factory-adatkészlet létrehozásához futtassa az alábbi parancsot az Azure PowerShellben.

        New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Folyamat létrehozása
Ebben a lépésben létrehozza a **HDInsightHive** tevékenységgel rendelkező első folyamatát. Vegye figyelembe, hogy a bemeneti szelet havonta érhető el (frequency: Month, interval: 1), a kimeneti szelet előállítása havonta történik, és a tevékenység scheduler (ütemező) tulajdonsága szintén a hónap értékre van beállítva (lásd alább). A kimeneti adatkészlet és a tevékenységütemező beállításainak egyezniük kell. Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. Az alábbi JSON-fájlban használt tulajdonságok magyarázata a szakasz végén található. 


1. Hozzon létre egy MyFirstPipelinePSH.json nevű JSON-fájlt a C:\ADFGetStarted mappában az alábbi tartalommal:

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
                            "scriptLinkedService": "StorageLinkedService",
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
    
    A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az **StorageLinkedService** nevű scriptLinkedService szolgáltatás által megadva), és az **adfgetstarted** tároló **script** mappájában történik.

    A **defines** (meghatározza) szakasz meghatározza a futásidő beállításait, amelyek Hive konfigurációs értékekként (például ${hiveconf:inputtable}, ${hiveconf:partitionedtable}) lesznek átadva a Hive-parancsfájlnak.

    A folyamat **start** (kezdés) és **end** (befejezés) tulajdonságai a folyamat aktív időszakát határozzák meg.

    A tevékenység JSON-fájljában meg van határozva, hogy a Hive-parancsfájl a **linkedServiceName** – **HDInsightOnDemandLinkedService** által meghatározott számítási szolgáltatáson fut.

    > A fenti példában használt JSON-tulajdonságokkal kapcsolatos részletekért lásd az [Anatomy of a Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) (A folyamatok működése) című témakört. 
2.  Győződjön meg arról, hogy az **input.log** fájl megjelenik az Azure Blob Storage **adfgetstarted/inputdata** mappájában, és futtassa az alábbi parancsot a folyamat üzembe helyezéséhez. Mivel a **start** (kezdés) és az **end** (befejezés) időpontok múltbeli értékekre vannak beállítva, és az **isPaused** tulajdonság értéke false (hamis), a folyamat (a folyamatban foglalt tevékenység) az üzembe helyezés után azonnal fut. 

        New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Gratulálunk, sikeresen létrehozta első folyamatát az Azure PowerShell használatával!

## Folyamat figyelése
Ebben a lépésben az Azure PowerShell használatával figyeli az Azure data factory eseményeit.

1. Futtassa a **Get-AzureRmDataFactory** parancsot, és rendelje hozzá a kimenetet egy **$df** változóhoz.

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. A **Get-AzureRmDataFactorySlice** parancs futtatásával hívja le az összes szelet részleteit a folyamat **EmpSQLTable** nevű kimeneti táblájában.  

        Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

    Megfigyelheti, hogy a StartDateTime itt megadott értéke megegyezik a folyamat JSON-fájljában megadott kezdési idővel. Az alábbihoz hasonló kimenetnek kell megjelennie.

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : FirstDataFactoryPSH
        DatasetName       : AzureBlobOutput
        Start             : 2/1/2014 12:00:00 AM
        End               : 3/1/2014 12:00:00 AM
        RetryCount        : 0
        State             : InProgress
        SubState          :
        LatencyStatus     :
        LongRetryCount    : 0


3. A **Get-AzureRmDataFactoryRun** parancs futtatásával lekérheti az adott szeletre vonatkozó tevékenységfuttatások részleteit.

        Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

    Az alábbihoz hasonló kimenetnek kell megjelennie.
        
        Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
        ResourceGroupName   : ADFTutorialResourceGroup
        DataFactoryName     : FirstDataFactoryPSH
        DatasetName         : AzureBlobOutput
        ProcessingStartTime : 12/18/2015 4:50:33 AM
        ProcessingEndTime   : 12/31/9999 11:59:59 PM
        PercentComplete     : 0
        DataSliceStart      : 2/1/2014 12:00:00 AM
        DataSliceEnd        : 3/1/2014 12:00:00 AM
        Status              : AllocatingResources
        Timestamp           : 12/18/2015 4:50:33 AM
        RetryAttempt        : 0
        Properties          : {}
        ErrorMessage        :
        ActivityName        : RunSampleHiveActivity
        PipelineName        : MyFirstPipeline
        Type                : Script

    Futtassa a parancsmagot, amíg a szelet **Ready** (Kész) vagy **Failed** (Sikertelen) állapotú nem lesz. Ha a szelet Ready (Kész) állapotú, a kimeneti adatok blobtárolójának **adfgetstarted** tárolójában ellenőrizze a **partitioneddata** mappát.  Vegye figyelembe, hogy az igény szerinti HDInsight-fürt létrehozása általában eltart egy ideig.

    ![kimeneti adatok](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)


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
Az oktatóanyag során létrehozott egy folyamatot egy adatátalakítási tevékenységgel (HDInsight-tevékenység), amely Hive-parancsfájlt futtat egy igény szerinti Azure HDInsight-fürtön. Ha tudni szeretné, hogyan használhatja a Másolás tevékenységet az adatok Azure-blobból Azure SQL Database adatbázisba történő másolásához, tekintse meg a következő cikket: [Tutorial: Copy data from an Azure blob to Azure SQL](./data-factory-get-started.md) (Oktatóanyag: adatok másolása Azure-blobból Azure SQL Database adatbázisba).

## Lásd még:
| Témakör | Leírás |
| :---- | :---- |
| [Data Factory Cmdlet Reference (Data Factory-parancsmagok referenciája)](https://msdn.microsoft.com/library/azure/dn820234.aspx) |  A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentáció. |
| [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) | Ez a cikk felsorolja az Azure Data Factory által támogatott adatátalakítási tevékenységeket (mint például a jelen oktatóanyagban használt HDInsight Hive-átalakítás). |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) | Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok](data-factory-create-pipelines.md) | Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) | Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban.
| [Monitor and manage pipelines using Azure portal blades (Folyamatok figyelése és felügyelete az Azure Portal paneljeinek használatával)](data-factory-monitor-manage-pipelines.md) | Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat az Azure Portal paneljeinek használatával. |
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) | Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. 




<!--HONumber=jun16_HO2-->


