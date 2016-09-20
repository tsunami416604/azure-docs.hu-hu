<properties
    pageTitle="Az első data factory létrehozása (Resource Manager-sablon) | Microsoft Azure"
    description="Az oktatóanyag során létre fog hozni egy minta Azure Data Factory-folyamatot egy Azure Resource Manager-sablon használatával."
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
    ms.date="08/01/2016"
    ms.author="spelluru"/>

# Oktatóanyag: Az első Azure data factory létrehozása Azure Resource Manager-sablon használatával
> [AZURE.SELECTOR]
- [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md)
- [A Data Factory Editor használata](data-factory-build-your-first-pipeline-using-editor.md)
- [A PowerShell használata](data-factory-build-your-first-pipeline-using-powershell.md)
- [A Visual Studio használata](data-factory-build-your-first-pipeline-using-vs.md)
- [A Resource Manager-sablon használata](data-factory-build-your-first-pipeline-using-arm.md)
- [A REST API használata](data-factory-build-your-first-pipeline-using-rest-api.md)


Ez az oktatóanyag bemutatja, hogyan hozhatja létre első Azure data factoryját egy Azure Resource Manager-sablon használatával. 


## Előfeltételek
Az oktatóanyag áttekintése című témakörben felsorolt előfeltételek mellett a következőket kell telepítenie:

- Mielőtt továbblépne, mindenképpen olvassa el [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című cikket, és hajtsa végre az előfeltételként felsorolt lépéseket. 
- **Telepítse az Azure PowerShellt**. Kövesse a [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) című cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre.
- Ez a cikk nem nyújt fogalmi áttekintést az Azure Data Factory szolgáltatásról. A szolgáltatás részletes áttekintéséhez olvassa el az [Azure Data Factoryt bemutató](data-factory-introduction.md) cikket. 
- Az Azure Resource Manager-sablonokkal kapcsolatban az [Authoring Azure Resource Manager Templates](../resource-group-authoring-templates.md) (Azure Resource Manager-sablonok készítése) című cikkben tájékozódhat bővebben. 

> [AZURE.IMPORTANT]
> Az ebben a cikkben található útmutatások követéséhez mindenképpen hajtsa végre [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című cikkben előfeltételként felsorolt lépéseket. 

## Resource Manager-sablon létrehozása

Hozzon létre egy **ADFTutorialARM.json** nevű JSON-fájlt a **C:\ADFGetStarted** mappában az alábbi tartalommal: 

A sablon az alábbi Data Factory-entitások létrehozását teszi lehetővé.

1. A **TutorialDataFactoryARM** nevű **data factory**. A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Például a másolási tevékenység, amely adatokat másol a forrásadattárból a céladattárba, és a HDInsight Hive tevékenység, amely Hive-parancsfájlt futtat a bemeneti adatok kimeneti adatokká történő átalakításához. 
2. Két **társított szolgáltatás**: a **StorageLinkedService** és a **HDInsightOnDemandLinkedService**. Ezek a társított szolgáltatások a data factoryjához társítják az Azure Storage-fiókját és egy igény szerinti Azure HDInsight-fürtöt. Ebben a példában az Azure Storage-fiók a bemeneti és a kimeneti adatokat tárolja a folyamathoz. a HDInsight társított szolgáltatás pedig a folyamat tevékenységében meghatározott Hive-parancsfájlt futtatja. Meg kell határoznia, hogy mely adattárat/számítási szolgáltatásokat használja a forgatókönyvben, és társított szolgáltatások létrehozásával a data factoryhoz kell társítania őket. 
3. Két (bemeneti/kimeneti) **adatkészlet**: **AzureBlobInput** és **AzureBlobOutput**. Ezek az adatkészletek képviselik a Hive-feldolgozás bemeneti és kimeneti adatait. Ezek az adatkészletek az oktatóanyag során korábban létrehozott **StorageLinkedService** szolgáltatásra hivatkoznak. A társított szolgáltatás egy Azure Storage-fiókra mutat, az adatkészletek pedig meghatározzák a bemeneti és kimeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet.   

Kattintson a **Using Data Factory Editor** (A Data Factory Editor használata) fülre, és arra a cikkre válthat, amely részletezi az ebben a sablonban használt JSON-tulajdonságokat.

> [AZURE.IMPORTANT] Módosítsa a **storageAccountName** és a **storageAccountKey** változó értékét. Módosítsa a **dataFactoryName** változót is, mert a névnek egyedinek kell lennie.


    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "TutorialDataFactoryARM",
            "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
            "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
            "apiVersion": "2015-10-01",
            "storageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDataset": "AzureBlobInput",
            "blobOutputDataset": "AzureBlobOutput",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "linkedservices",
                        "name": "[variables('storageLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "AzureStorage",
                            "typeProperties": {
                                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "linkedservices",
                        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "HDInsightOnDemand",
                            "typeProperties": {
                                "clusterSize": 4,
                                "version":  "3.2",
                                "timeToLive": "00:05:00",
                                "osType": "windows",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobInputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
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
                        },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobOutputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "published": false,
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
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
                        },
                        {
                            "dependsOn": [
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
                            ],
                            "type": "datapipelines",
                            "name": "[variables('dataFactoryName')]",
                            "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "description": "My first Azure Data Factory pipeline",
                                "activities": [
                                    {
                                        "type": "HDInsightHive",
                                        "typeProperties": {
                                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                                            "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                                            "defines": {
                                                "inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                                                "partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
                ]
            }
        ]
    }

Kattintson a **Using Data Factory Editor** (A Data Factory Editor használata) fülre, és arra a cikkre válthat, amely részletezi az ebben a sablonban használt JSON-tulajdonságokat.

Vegye figyelembe a következőket: 

- A Data Factory létrehoz egy **Windows-alapú** HDInsight-fürtöt a fenti JSON-fájllal. A szolgáltatás **Linux-alapú** HDInsight-fürtöt is képes létrehozni. További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás). 
- Igény szerinti HDInsight-fürt helyett **saját HDInsight-fürtöt** is használhat. További információ: [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) (HDInsight társított szolgáltatás).
- A HDInsight-fürt létrehoz egy **alapértelmezett tárolót ** a JSON-fájlban megadott blob-tárolóban (**linkedServiceName**). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer mindig létrehoz egy HDInsight-fürt, amikor fel kell dolgozni egy szeletet, kivéve, ha van meglévő élő fürt (**timeToLive**), majd a feldolgozás végén a rendszer törli a fürtöt.

    Ahogy egyre több szelet lesz feldolgozva, egyre több tároló jelenik meg az Azure Blob Storage-tárban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: „adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”. Az Azure Blob Storage-tárból olyan eszközökkel törölheti a tárolókat, mint például a [Microsoft Storage Explorer](http://storageexplorer.com/).

További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás).

> [AZURE.NOTE] Az Azure data factory létrehozására szolgáló Resource Manager-sablonra a [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json) webhelyén találhat egy másik példát.  

## Data factory létrehozása

1. Indítsa el az **Azure PowerShellt**, és futtassa az alábbi parancsot. 
    - Futtassa a **Login-AzureRmAccount** parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.  
    - Futtassa a következő parancsot annak az előfizetésnek a kiválasztásához, amelyikben létre szeretné hozni a data factoryt.
            Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
1. Futtassa a következő parancsot a Data Factory-entitásoknak az 1. lépésben létrehozott Resource Manager-sablonnal történő telepítéséhez. 

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Folyamat figyelése
 
1.  Miután bejelentkezett az [Azure Portalra](https://portal.azure.com/), kattintson a **Tallózás** elemre, és válassza az **Adat-előállítók** lehetőséget.
        ![Browse (Tallózás)->Data factories](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.  A **Data Factories** panelen kattintson a létrehozott data factoryre (**TutorialFactoryARM**).   
2.  A data factoryhoz tartozó **Data Factory** panelen kattintson a **Diagram** elemre.
        ![Diagram csempe](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.  A **diagramnézet** áttekintést nyújt az oktatóanyagban használt folyamatokról és adatkészletekről.
    
    ![Diagramnézet](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. A diagramnézetben kattintson duplán az **AzureBlobOutput** adatkészletre. Látni fogja, hogy a szelet feldolgozás alatt áll.

    ![Adatkészlet](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. A feldolgozás befejeztével a szelet **Ready** (Kész) állapotú lesz. Az igény szerinti HDInsight-fürt létrehozása általában eltart egy ideig (körülbelül 20 percig). 

    ![Adatkészlet](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png) 
10. Ha a szelet **Ready** (Kész) állapotú, a kimeneti adatok **adfgetstarted** Blob Storage-tárolójában ellenőrizze a **partitioneddata** mappát.  

A [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) (Adatkészletek és folyamatok figyelése) című cikkben útmutatást találhat arról, hogy hogyan használhatja az Azure Portal paneleit az oktatóanyagban létrehozott folyamatok és adatkészletek figyeléséhez.

Az adatfolyamatok figyeléséhez a Monitor and Manage Appot is használhatja. Az alkalmazás használatával kapcsolatos információkért tekintse meg a [Monitor and manage Azure Data Factory pipelines using Monitoring App](data-factory-monitor-manage-app.md) (Azure Data Factory-folyamatok figyelése és felügyelete a Monitoring App használatával) című cikket. 

> [AZURE.IMPORTANT] A szelet sikeres feldolgozásakor a rendszer törli a bemeneti fájlt. Ezért ha újra le szeretné futtatni a szeletet, vagy újra el szeretné végezni az oktatóanyagban foglaltakat, töltse fel a bemeneti fájlt (input.log) az adfgetstarted tároló inputdata mappájába.

## Resource Manager-sablon átjáró létrehozásához
Az itt látható, mintául szolgáló Resource Manager-sablonnal egy háttérben lévő logikai átjáró hozható létre. Telepítenie kell egy átjárót a helyszíni számítógépen vagy az Azure IaaS virtuális gépen, és egy kulcs használatával regisztrálnia kell az átjárót a Data Factory szolgáltatásban. További információkért lásd: [Move data between on-premises and cloud](data-factory-move-data-between-onprem-and-cloud.md) (Adatok áthelyezése a helyszíni rendszer és a felhő között).

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

Ez a sablon létrehozza a GatewayUsingArmDF nevű data factoryt a GatewayUsingARM nevű átjáróval. 

## Lásd még:
| Témakör | Leírás |
| :---- | :---- |
| [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) | Ez a cikk felsorolja az Azure Data Factory által támogatott adatátalakítási tevékenységeket (mint például a jelen oktatóanyagban használt HDInsight Hive-átalakítás). |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) | Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok](data-factory-create-pipelines.md) | Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) | Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban.
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) | Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. 

  




<!--HONumber=sep16_HO1-->


