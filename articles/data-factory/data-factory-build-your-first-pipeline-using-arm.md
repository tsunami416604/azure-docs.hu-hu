---
title: "Az első data factory létrehozása (Resource Manager-sablon) | Microsoft Docs"
description: "Ebben az oktatóprogramban egy egyszerű minta Azure Data Factory-folyamatot fog létrehozni egy Azure Resource Manager-sablon segítségével."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eb9e70b9-a13a-4a27-8256-2759496be470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 7303b51a4a107e63e4c6514f7bf8f33a3ba00e39
ms.contentlocale: hu-hu
ms.lasthandoff: 07/10/2017


---
# Oktatóanyag: Az első Azure data factory létrehozása Azure Resource Manager-sablon használatával
<a id="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template" class="xliff"></a>
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
> 

Ez a cikk bemutatja, hogyan hozhatja létre első Azure data factoryját egy Azure Resource Manager-sablonnal. Ha ezt az oktatóanyagot más eszközök/SDK-k használatával szeretné elvégezni, válassza ki az egyik lehetőséget a legördülő listából.

A jelen oktatóanyagban szereplő folyamat egyetlen tevékenységet tartalmaz: ez a **HDInsight Hive-tevékenység**. A tevékenység egy hive-szkriptet futtat egy Azure HDInsight fürtön, amely a bemeneti adatokat átalakítja a kimeneti adatok előállításához. A folyamat úgy van ütemezve, hogy havonta egyszer fusson a megadott kezdő és befejező időpontok közt. 

> [!NOTE]
> Az oktatóanyagban található adatfolyamat átalakítja a bemeneti adatokat, hogy ezzel kimeneti adatokat hozzon létre. Az adatok Azure Data Factory használatával történő másolásának útmutatásáért olvassa el [az adatok Blob Storage-ból SQL Database-be történő másolását ismertető oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Az oktatóanyagban szereplő folyamat csak egyetlen tevékenységtípussal rendelkezik: HDInsightHive. Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További tudnivalókért lásd: [Ütemezés és végrehajtás a Data Factoryban](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## Előfeltételek
<a id="prerequisites" class="xliff"></a>
* Olvassa el [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című részt, és hajtsa végre az **előfeltételként** felsorolt lépéseket.
* Kövesse a [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre.
* Az Azure Resource Manager-sablonokkal kapcsolatban az [Authoring Azure Resource Manager Templates](../azure-resource-manager/resource-group-authoring-templates.md) (Azure Resource Manager-sablonok készítése) című cikkben tájékozódhat bővebben. 

## Az oktatóanyag tartalma
<a id="in-this-tutorial" class="xliff"></a>
| Entitás | Leírás |
| --- | --- |
| Azure Storage társított szolgáltatás |Társítja az Azure Storage-fiókot a data factoryhoz. Ebben a példában az Azure Storage-fiók a bemeneti és a kimeneti adatokat tárolja a folyamathoz. |
| HDInsight igény szerinti társított szolgáltatás |Egy igény szerinti HDInsight-fürtöt társít a data factoryhoz. A rendszer automatikusan létrehozza a fürtöt az adatok feldolgozásához, majd törli a feldolgozás befejezése után. |
| Azure Blob bemeneti adatkészlet |Az Azure Storage társított szolgáltatásra vonatkozik. A társított szolgáltatás egy Azure Storage-fiókra hivatkozik, az Azure-blob adatkészlet pedig meghatározza a bemeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet. |
| Azure Blob kimeneti adatkészlet |Az Azure Storage társított szolgáltatásra vonatkozik. A társított szolgáltatás egy Azure Storage-fiókra hivatkozik, az Azure-blob adatkészlet pedig meghatározza a kimeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet. |
| Adatfolyamat |A folyamat egyetlen HDInsightHive típusú tevékenységgel rendelkezik, amely felhasználja a beérkező adatkészletet, és elkészíti a kimenőt. |

A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Kétféle típusú tevékenység létezik: az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) és az [adatátalakítási tevékenységek](data-factory-data-transformation-activities.md). Az oktatóanyag segítségével egyetlen tevékenységgel (Hive-tevékenységgel) rendelkező folyamatot hozhat létre.

A következő szakasz a Data Factory-entitások meghatározására szolgáló teljes Resource Manager-sablont ismerteti, így gyorsan végighaladhat az oktatóanyagon és tesztelheti a sablont. Az egyes Data Factory-entitások meghatározásának megértéséhez tekintse meg a [Data Factory-entitások a sablonban](#data-factory-entities-in-the-template) szakaszt.

## Data Factory JSON-sablon
<a id="data-factory-json-template" class="xliff"></a>
A data factory meghatározásához szükséges legfelső szintű Resource Manager-sablon a következő: 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Hozzon létre egy **ADFTutorialARM.json** nevű JSON-fájlt a **C:\ADFGetStarted** mappában az alábbi tartalommal:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "HDInsightOnDemand",
                  "typeProperties": {
                    "clusterSize": 1,
                    "timeToLive": "00:05:00",
                    "osType": "windows",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                  }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
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
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
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
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2016-10-01T00:00:00Z",
                  "end": "2016-10-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> Az Azure data factory létrehozására szolgáló Resource Manager-sablonra a következő helyen találhat egy másik példát: [Oktatóanyag: Folyamat létrehozása másolási tevékenységgel és Azure Resource Manager-sablon használatával](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  
> 
> 

## Paramétereket tartalmazó JSON-file
<a id="parameters-json" class="xliff"></a>
Hozzon létre egy **ADFTutorialARM-Parameters.json** elnevezésű JSON-fájlt, amely paramétereket tartalmaz az Azure Resource Manager-sablon számára.  

> [!IMPORTANT]
> Adja meg az Azure Storage-fiók nevét és kulcsát a **storageAccountName** és **storageAccountKey** paraméterek értékeinek ebben a paraméterfájlban. 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> Készíthet különálló JSON-paraméterfájlokat a fejlesztési, tesztelési és az éles környezetek számára, amelyeket ugyanazzal a Data Factory JSON-sablonnal használhat. Ezekben a környezetekben automatizálhatja a Data Factory-entitások üzembe helyezését egy PowerShell-szkript használatával. 
> 
> 

## Data factory létrehozása
<a id="create-data-factory" class="xliff"></a>
1. Indítsa el az **Azure PowerShellt**, és futtassa az alábbi parancsot: 
   * Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
    ```PowerShell
    Login-AzureRmAccount
    ```  
   * Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.
    ```PowerShell
    Get-AzureRmSubscription
    ``` 
   * Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Ennek az előfizetésnek egyeznie kell az Azure Portalon használt előfizetéssel.
    ```
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```   
2. Futtassa a következő parancsot a Data Factory-entitásoknak az 1. lépésben létrehozott Resource Manager-sablonnal történő telepítéséhez. 

    ```PowerShell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## Folyamat figyelése
<a id="monitor-pipeline" class="xliff"></a>
1. Miután bejelentkezett az [Azure Portalra](https://portal.azure.com/), kattintson a **Tallózás** elemre, és válassza az **Adat-előállítók** lehetőséget.
     ![Tallózás->Data Factories](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. A **Data Factories** panelen kattintson a létrehozott data factoryre (**TutorialFactoryARM**).    
3. A data factoryhoz tartozó **Data Factory** panelen kattintson a **Diagram** elemre.

     ![Diagram csempe](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. A **diagramnézet** áttekintést nyújt az oktatóanyagban használt folyamatokról és adatkészletekről.
   
   ![Diagramnézet](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. A diagramnézetben kattintson duplán az **AzureBlobOutput** adatkészletre. Látni fogja, hogy a szelet feldolgozás alatt áll.
   
    ![Adatkészlet](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. A feldolgozás befejeztével a szelet **Ready** (Kész) állapotúra vált. Az igény szerinti HDInsight-fürt létrehozása általában eltart egy ideig (körülbelül 20 percig). Ezért a folyamat várhatóan **körülbelül 30 perc** alatt dolgozza fel a szeletet.
   
    ![Adathalmaz](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. Ha a szelet **Ready** (Kész) állapotú, a kimeneti adatok **adfgetstarted** Blob Storage-tárolójában ellenőrizze a **partitioneddata** mappát.  

A [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) (Adatkészletek és folyamatok figyelése) című cikkben útmutatást találhat arról, hogy hogyan használhatja az Azure Portal paneleit az oktatóanyagban létrehozott folyamatok és adatkészletek figyeléséhez.

Az adatfolyamatok figyeléséhez a Monitor and Manage Appot is használhatja. Az alkalmazás használatával kapcsolatos információkért tekintse meg a [Monitor and manage Azure Data Factory pipelines using Monitoring App](data-factory-monitor-manage-app.md) (Azure Data Factory-folyamatok figyelése és felügyelete a Monitoring App használatával) című cikket. 

> [!IMPORTANT]
> A szelet sikeres feldolgozásakor a rendszer törli a bemeneti fájlt. Ezért ha újra le szeretné futtatni a szeletet, vagy újra el szeretné végezni az oktatóanyagban foglaltakat, töltse fel a bemeneti fájlt (input.log) az adfgetstarted tároló inputdata mappájába.
> 
> 

## Data Factory-entitások a sablonban
<a id="data-factory-entities-in-the-template" class="xliff"></a>
### Data Factory definiálása
<a id="define-data-factory" class="xliff"></a>
A data factoryt a Resource Manager-sablonban definiálhatja az alábbi minta szerint:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```
A dataFactoryName az alábbi módon van definiálva: 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
Ez az erőforráscsoport-azonosítón alapuló egyedi karakterlánc.  

### Data Factory-entitások definiálása
<a id="defining-data-factory-entities" class="xliff"></a>
Az alábbi Data Factory-entitások a JSON-sablonban vannak definiálva: 

* [Azure Storage társított szolgáltatás](#azure-storage-linked-service)
* [HDInsight igény szerinti társított szolgáltatás](#hdinsight-on-demand-linked-service)
* [Azure blobbemeneti adatkészlet](#azure-blob-input-dataset)
* [Azure blobkimeneti adatkészlet](#azure-blob-output-dataset)
* [Másolási tevékenységgel rendelkező adatfolyamat](#data-pipeline)

#### Azure Storage társított szolgáltatás
<a id="azure-storage-linked-service" class="xliff"></a>
Ebben a szakaszban megadhatja az Azure-tárfiók nevét és kulcsát. Az Azure Storage társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Storage társított szolgáltatás](data-factory-azure-blob-connector.md#azure-storage-linked-service) című szakaszt. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
A **connectionString** a storageAccountName és storageAccountKey paramétereket használja. A paraméterek értékei a konfigurációs fájlok használatával adhatók át. A definíció változókat is használ: azureStroageLinkedService és dataFactoryName, amelyek a sablonban vannak definiálva. 

#### HDInsight igény szerinti társított szolgáltatás
<a id="hdinsight-on-demand-linked-service" class="xliff"></a>
A HDInsight igény szerinti társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg a [Számítási társított szolgáltatás](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) című cikket.  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "windows",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```
Vegye figyelembe a következő szempontokat: 

* A Data Factory létrehoz egy **Windows-alapú** HDInsight-fürtöt a fenti JSON-fájllal. A szolgáltatás **Linux-alapú** HDInsight-fürtöt is képes létrehozni. További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás). 
* Igény szerinti HDInsight-fürt helyett **saját HDInsight-fürtöt** is használhat. További információ: [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) (HDInsight társított szolgáltatás).
* A HDInsight-fürt létrehoz egy **alapértelmezett tárolót** a JSON-fájlban megadott blob-tárolóban (**linkedServiceName**). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer mindig létrehoz egy HDInsight-fürt, amikor fel kell dolgozni egy szeletet, kivéve, ha van meglévő élő fürt (**timeToLive**), majd a feldolgozás végén a rendszer törli a fürtöt.
  
    Ahogy egyre több szelet lesz feldolgozva, egyre több tároló jelenik meg az Azure Blob Storage-tárban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: „adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”. Az Azure Blob Storage-tárból olyan eszközökkel törölheti a tárolókat, mint például a [Microsoft Storage Explorer](http://storageexplorer.com/).

További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás).

#### Azure blobbemeneti adatkészlet
<a id="azure-blob-input-dataset" class="xliff"></a>
Megadhatja a bemeneti adatokat tartalmazó blobtároló, mappa és fájl nevét. Az Azure Blob-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Blob-adatkészlet tulajdonságai](data-factory-azure-blob-connector.md#dataset-properties) című szakaszt. 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
Ez a definíció az alábbi, a paramétersablonban definiált paramétereket használja: blobContainer, inputBlobFolder és inputBlobName. 

#### Azure Blob kimeneti adatkészlet
<a id="azure-blob-output-dataset" class="xliff"></a>
Megadhatja a kimeneti adatokat tartalmazó blobtároló és mappa nevét. Az Azure Blob-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Blob-adatkészlet tulajdonságai](data-factory-azure-blob-connector.md#dataset-properties) című szakaszt.  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
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

Ez a definíció az alábbi, a paramétersablonban definiált paramétereket használja: blobContainer és outputBlobFolder. 

#### Adatfolyamat
<a id="data-pipeline" class="xliff"></a>
Definiálhat egy folyamatot, amely átalakítja az adatokat a Hive-parancsfájl egy igény szerinti Azure HDInsight-fürtön való futtatásával. A példában található folyamat definiálásához használt JSON-elemek leírásához tekintse meg [A folyamat JSON-fájlja](data-factory-create-pipelines.md#pipeline-json) című szakaszt. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
        {
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                    "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                    "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
            },
            "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
            ],
            "outputs": [
            {
                "name": "[variables('blobOutputDatasetName')]"
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
            "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
        }
        ],
        "start": "2016-10-01T00:00:00Z",
        "end": "2016-10-02T00:00:00Z",
        "isPaused": false
    }
}
```

## A sablon ismételt felhasználása
<a id="reuse-the-template" class="xliff"></a>
Az oktatóanyagban létrehozott egy sablont a Data Factory-entitások definiálásához, illetve egy másikat a paraméterek értékeinek átadásához. Ha ugyanazt a sablont szeretné használni a Data Factory-entitások különböző környezetekben történő üzembe helyezéséhez, hozzon létre egy paraméterfájlt az egyes környezetekhez, és használja azt az adott környezetben történő üzembe helyezéskor.     

Példa:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Megfigyelheti, hogy az első parancs a fejlesztőkörnyezet, a második a tesztkörnyezet, a harmadik pedig az éles környezet paraméterfájlját használja.  

Emellett ismétlődő feladatok elvégzéséhez is újból felhasználhatja a sablont. Ilyen eset például, ha több olyan, egy vagy több folyamattal rendelkező adat-előállítót is létre kell hoznia, amelyek ugyanazt a logikát alkalmazzák, de az egyes adat-előállítók különböző Azure-tárfiókokat és Azure SQL Database-fiókokat használnak. Ebben a forgatókönyvben ugyanazt a sablont használja ugyanabban a környezetben (fejlesztői, teszt vagy éles) különböző paraméterfájlokkal a data factoryk létrehozásához. 

## Resource Manager-sablon átjáró létrehozásához
<a id="resource-manager-template-for-creating-a-gateway" class="xliff"></a>
Az itt látható, mintául szolgáló Resource Manager-sablonnal egy háttérben lévő logikai átjáró hozható létre. Telepítsen egy átjárót a helyszíni számítógépre vagy az Azure IaaS virtuális gépre, és regisztrálja az átjárót egy kulccsal a Data Factory szolgáltatásban. További információkért lásd: [Move data between on-premises and cloud](data-factory-move-data-between-onprem-and-cloud.md) (Adatok áthelyezése a helyszíni rendszer és a felhő között).

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
Ez a sablon létrehozza a GatewayUsingArmDF nevű data factoryt a GatewayUsingARM nevű átjáróval. 

## Lásd még:
<a id="see-also" class="xliff"></a>
| Témakör | Leírás |
|:--- |:--- |
| [Folyamatok](data-factory-create-pipelines.md) |Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) |Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban. |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) |Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) |Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. |


