---
title: "Oktatóanyag: Folyamatok létrehozása a Resource Manager-sablon használatával | Microsoft Docs"
description: "Az oktatóanyag során létrehoz egy másolási tevékenységgel rendelkező Azure Data Factory-folyamatot az Azure Resource Manager-sablon használatával."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: ebc5dbf790ca6012cfe9a7ea9ccee9fdacb46ffd
ms.openlocfilehash: 9ad4378ab27433858d14237fe451b16690711f3a


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása az Azure Resource Manager-sablon használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és figyelhet meg Azure Data Factoryt az Azure Resource Manager-sablonokkal. Az adat-előállító folyamata adatokat másol az Azure Blob Storage-tárból az Azure SQL Database-be.

## <a name="prerequisites"></a>Előfeltételek
* Tekintse meg [Az oktatóanyag áttekintése és előfeltételei](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikket, és hajtsa végre az **előfeltételként** felsorolt lépéseket.
* Kövesse a [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell telepítése és konfigurálása) című cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre. Ebben az oktatóanyagban Data Factory-entitásokat fogunk üzembe helyezni a PowerShell-lel. 
* (Nem kötelező) Az Azure Resource Manager-sablonokkal kapcsolatban az [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md) című cikkben tájékozódhat bővebben.

## <a name="in-this-tutorial"></a>Az oktatóanyag tartalma
Ebben az oktatóanyagban az alábbi Data Factory-entitásokkal rendelkező adat-előállítót hozza létre:

| Entitás | Leírás |
| --- | --- |
| Azure Storage társított szolgáltatás |Társítja az Azure Storage-fiókot az adat-előállítóhoz. Az Azure Storage az oktatóanyagban alkalmazott másolási tevékenység forrásadattára, az Azure SQL Database pedig a fogadó adattára. Meghatározza a másolási tevékenység bemeneti adatait tartalmazó tárfiókot. |
| Azure SQL Database társított szolgáltatás |Társítja az Azure SQL Database-t az adat-előállítóhoz. Meghatározza a másolási tevékenység kimeneti adatait tartalmazó Azure SQL Database-t. |
| Azure blobbemeneti adatkészlet |Az Azure Storage társított szolgáltatásra vonatkozik. A társított szolgáltatás egy Azure Storage-fiókra hivatkozik, az Azure-blob adatkészlet pedig meghatározza a bemeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet. |
| Az Azure SQL kimeneti adatkészlete |Az Azure SQL társított szolgáltatásra vonatkozik. Az Azure SQL társított szolgáltatás egy Azure SQL-kiszolgálóra hivatkozik, az Azure SQL-adatkészlet pedig meghatározza a kimeneti adatokat tartalmazó tábla nevét. |
| Adatfolyamat |A folyamat egyetlen másolás típusú tevékenységgel rendelkezik, amely Azure Blob-adatkészletet bemenetként, az Azure SQL-adatkészletet pedig kimenetként használja. A másolási tevékenység adatokat másol egy Azure-blobból az Azure SQL Database egyik táblájába. |

A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Kétféle típusú tevékenység létezik: az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) és az [adatátalakítási tevékenységek](data-factory-data-transformation-activities.md). Az oktatóanyag során létrehoz egy egyetlen tevékenységgel (másolási tevékenységgel) rendelkező folyamatot.

![Az Azure Blob másolása az Azure SQL Database-be](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

A következő szakasz a Data Factory-entitások meghatározására szolgáló teljes Resource Manager-sablont ismerteti, így gyorsan végighaladhat az oktatóanyagon és tesztelheti a sablont. Az egyes Data Factory-entitások meghatározásának megértéséhez tekintse meg a [Data Factory-entitások a sablonban](#data-factory-entities-in-the-template) szakaszt.

## <a name="data-factory-json-template"></a>Data Factory JSON-sablon
Az adat-előállító meghatározásához szükséges legfelső szintű Resource Manager-sablon a következő: 

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
Hozzon létre egy JSON-fájlt **ADFCopyTutorialARM.json** néven a **C:\ADFGetStarted** mappában az alábbi tartalommal:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
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
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
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
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Day",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Day",
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
              "[variables('azureSqlLinkedServiceName')]",
              "[variables('blobInputDatasetName')]",
              "[variables('sqlOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "activities": [
                {
                  "name": "CopyFromAzureBlobToAzureSQL",
                  "description": "Copy data frm Azure blob to Azure SQL",
                  "type": "Copy",
                  "inputs": [
                    {
                      "name": "[variables('blobInputDatasetName')]"
                    }
                  ],
                  "outputs": [
                    {
                      "name": "[variables('sqlOutputDatasetName')]"
                    }
                  ],
                  "typeProperties": {
                    "source": {
                      "type": "BlobSource"
                    },
                    "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                  },
                  "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                  }
                }
              ],
              "start": "2016-10-02T00:00:00Z",
              "end": "2016-10-03T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>Paramétereket tartalmazó JSON-file
Hozzon létre egy **ADFCopyTutorialARM-Parameters.json** elnevezésű JSON-fájlt, amely paramétereket tartalmaz az Azure Resource Manager-sablon számára. 

> [!IMPORTANT]
> Adja meg az Azure Storage-fiók nevét és kulcsát a **storageAccountName** és **storageAccountKey** paraméterek értékeinek.  
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the Azure SQL server>" },
        "databaseName": { "value": "<Name of the Azure SQL database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
    }
}
```

> [!IMPORTANT]
> Készíthet különálló JSON-paraméterfájlokat a fejlesztési, tesztelési és az éles környezetek számára, amelyeket ugyanazzal a Data Factory JSON-sablonnal használhat. Ezekben a környezetekben automatizálhatja a Data Factory-entitások üzembe helyezését egy PowerShell-szkript használatával.  
> 
> 

## <a name="create-data-factory"></a>Data factory létrehozása
1. Indítsa el az **Azure PowerShellt**, és futtassa az alábbi parancsot:
   * Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
    ```PowerShell
    Login-AzureRmAccount    
    ```  
   * Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.
    ```PowerShell
    Get-AzureRmSubscription
    ```   
   * Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. 
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```    
2. Futtassa a következő parancsot a Data Factory-entitásoknak az 1. lépésben létrehozott Resource Manager-sablonnal történő telepítéséhez.

    ```PowerShell   
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Folyamat figyelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókkal.
2. A bal oldali menüben kattintson az **Adat-előállítók** lehetőségre (vagy) kattintson a **További szolgáltatások** **INTELLIGENCIA ÉS ELEMZÉS** kategóriájában található **Adat-előállítók** lehetőségre.
   
    ![Adat-előállítók menü](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. Az **Adat-előállítók** oldalon keressen rá az adat-előállítóra. 
   
    ![Adat-előállító keresése](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Kattintson az Azure Data Factoryre. Megjelenik az adat-előállító kezdőlapja.
   
    ![Az adat-előállító kezdőlapja](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. Kattintson az **Ábra** csempére az adat-előállító diagramnézetének megjelenítéséhez.
   
    ![Az adat-előállító diagramnézete](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. A diagramnézetben kattintson duplán a **SQLOutputDataset** adatkészletre. Ekkor megjelenik a szelet állapota. Amikor a másolási művelet befejeződik, állítsa az állapotot **Kész** értékre.
   
    ![Kész állapotú kimeneti szelet](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. Ha a szelet **Kész** állapotban van, ellenőrizze, hogy az adatok az Azure SQL Database **emp** táblájába vannak-e másolva.

A [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) (Adatkészletek és folyamatok figyelése) című cikkben útmutatást találhat arról, hogy hogyan használhatja az Azure Portal paneleit az oktatóanyagban létrehozott folyamatok és adatkészletek figyeléséhez.

Az adatfolyamatok figyeléséhez a Monitor and Manage Appot is használhatja. Az alkalmazás használatával kapcsolatos információkért tekintse meg a [Monitor and manage Azure Data Factory pipelines using Monitoring App](data-factory-monitor-manage-app.md) (Azure Data Factory-folyamatok figyelése és felügyelete a Monitoring App használatával) című cikket.

## <a name="data-factory-entities-in-the-template"></a>Data Factory-entitások a sablonban
### <a name="define-data-factory"></a>Adat-előállító definiálása
Az adat-előállítót a Resource Manager-sablonban definiálhatja az alábbi minta szerint:  

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
"dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
```

Ez az erőforráscsoport-azonosítón alapuló egyedi karakterlánc.  

### <a name="defining-data-factory-entities"></a>Data Factory-entitások definiálása
Az alábbi Data Factory-entitások a JSON-sablonban vannak definiálva: 

1. [Azure Storage társított szolgáltatás](#azure-storage-linked-service)
2. [Azure SQL Database társított szolgáltatás](#azure-sql-database-linked-service)
3. [Azure Blob-adatkészlet](#azure-blob-dataset)
4. [Azure SQL-adatkészlet](#azure-sql-dataset)
5. [Másolási tevékenységgel rendelkező adatfolyamat](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
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

A connectionString a storageAccountName és storageAccountKey paramétereket használja. A paraméterek értékei a konfigurációs fájlok használatával adhatók át. A definíció változókat is használ: azureStorageLinkedServiceName és dataFactoryName, amelyek a sablonban vannak definiálva. 

#### <a name="azure-sql-database-linked-service"></a>Azure SQL Database társított szolgáltatás
Ebben a szakaszban megadhatja az Azure SQL-kiszolgáló nevét, az adatbázis nevét, a felhasználónevet és a felhasználói jelszót. Az Azure SQL társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure SQL társított szolgáltatás](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) című szakaszt.  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlDatabase",
          "description": "Azure SQL linked service",
          "typeProperties": {
            "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
          }
    }
}
```

A connectionString az sqlServerName, databaseName, sqlServerUserName és sqlServerPassword paramétereket használja, amelyeknek az értékei a konfigurációs fájlok használatával adhatók át. A definíció a sablon következő változóit is használja: azureSqlLinkedServiceName, dataFactoryName.

#### <a name="azure-blob-dataset"></a>Azure Blob-adatkészlet
Megadhatja a bemeneti adatokat tartalmazó blobtároló, mappa és fájl nevét. Az Azure Blob-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Blob-adatkészlet tulajdonságai](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) című szakaszt. 

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
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Day",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Azure SQL-adatkészlet
Megadhatja az Azure SQL Database-ben található tábla nevét, amely az Azure Blob Storage-tárból másolt adatokat tartalmazza. Az Azure SQL-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure SQL-adatkészlet tulajdonságai](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) című szakaszt. 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Day",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>Adatfolyamat
Definiálhat egy folyamatot, amely adatokat másol az Azure Blob-adatkészletből az Azure SQL-adatkészletbe. A példában található folyamat definiálásához használt JSON-elemek leírásához tekintse meg [A folyamat JSON-fájlja](data-factory-create-pipelines.md#pipeline-json) című szakaszt. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('azureSqlLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "activities": [
        {
              "name": "CopyFromAzureBlobToAzureSQL",
              "description": "Copy data frm Azure blob to Azure SQL",
              "type": "Copy",
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('sqlOutputDatasetName')]"
            }
              ],
              "typeProperties": {
                "source": {
                      "type": "BlobSource"
                },
                "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                }
              },
              "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
              }
        }
          ],
          "start": "2016-10-02T00:00:00Z",
          "end": "2016-10-03T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>A sablon ismételt felhasználása
Az oktatóanyagban létrehozott egy sablont a Data Factory-entitások definiálásához, illetve egy másikat a paraméterek értékeinek átadásához. A folyamat adatokat másol az Azure Storage-fiókokból a paramétereken keresztül megadott Azure SQL Database-be. Ha ugyanazt a sablont szeretné használni a Data Factory-entitások különböző környezetekben történő üzembe helyezéséhez, hozzon létre egy paraméterfájlt az egyes környezetekhez, és használja azt az adott környezetben történő üzembe helyezéskor.     

Példa:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

Megfigyelheti, hogy az első parancs a fejlesztőkörnyezet, a második a tesztkörnyezet, a harmadik pedig az éles környezet paraméterfájlját használja.  

Emellett ismétlődő feladatok elvégzéséhez is újból felhasználhatja a sablont. Ilyen eset például, ha több olyan, egy vagy több folyamattal rendelkező adat-előállítót is létre kell hoznia, amelyek ugyanazt a logikát alkalmazzák, de az egyes adat-előállítók különböző Azure-tárfiókokat és Azure SQL Database-fiókokat használnak. Ebben a forgatókönyvben ugyanazt a sablont használja ugyanabban a környezetben (fejlesztői, teszt vagy éles) különböző paraméterfájlokkal az adat-előállítók létrehozásához.   




<!--HONumber=Dec16_HO4-->


