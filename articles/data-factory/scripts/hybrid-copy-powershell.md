---
title: 'PowerShell-parancsfájlt: Adatok másolása a helyszínről az Azure Data Factory használatával |} A Microsoft Docs'
description: Ez a PowerShell-szkript adatokat másol egy helyszíni SQL Server-adatbázisból egy másikra egy Azure Blob Storage.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 1218408bee067d6edb274fdcbf4fae62b8245a01
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160666"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Az adatok másolása a helyszínről az Azure data factory-folyamatok létrehozása a PowerShell használatával

A PowerShell-példaszkript létrehoz egy folyamatot az Azure Data Factoryben, amely adatokat másol egy helyszíni SQL Server-adatbázisból egy Azure Blob Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- **Egy SQL Server**. Egy helyszíni SQL Server-adatbázist használ egy **forrás** adatokat tárolni ebben a példában.
- **Azure Storage-fiók** Az Azure blob storage-ot használ egy **cél/fogadó** adatokat tárolni ebben a példában. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../../storage/common/storage-quickstart-create-account.md) ismertető cikket.
- **Saját üzemeltetésű integrációs modul**. Töltse le az MSI-fájlt a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717) , majd futtassa a gépen egy saját üzemeltetésű integrációs modul telepítése.  

### <a name="create-sample-database-in-sql-server"></a>Hozzon létre mintaadatbázist az SQL Server
1. A helyszíni SQL Server-adatbázis, hozzon létre egy táblázatot nevű **emp** a következő SQL-parancsfájl használatával: 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. A táblába néhány Mintaadat beszúrásához:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a szkript létrehoz, amelyek meghatározzák a Data Factory-entitásokat (társított szolgáltatás, adatkészlet és folyamatot) JSON-fájlokat a c:\ mappába a merevlemezen.

```powershell
$resourceGroupName = "<Resource group name>"
$dataFactoryName = "<Data factory name>" # must be globally unique
$storageAccountName = "<Az.Storage account name>"
$storageAccountKey = "<Az.Storage account key>"
$sqlServerName = "<SQL server name>"
$sqlDatabaseName = "SQL Server database name"
$sqlTableName = "emp" # create the emp table if it does not already exist in your database with ID, FirstName, and LastName columns of type String. 
$sqlUserName = "<SQL Authentication - user name>"
$sqlPassword = "<SQL Authentication - user password>"
$blobFolderPath = "<Azure blob container name>/<Azure blob folder name>"
$integrationRuntimeName = "<Self-hosted integration runtime name"
$pipelineName = "SqlServerToBlobPipeline"
$dataFactoryRegion = "China East"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryRegion

# create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Name $dataFactoryName -Location $dataFactoryRegion

# create a self-hosted integration runtime
Set-AzDataFactoryIntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName

# get the authorization key from the created integration runtime in the cloud
Get-AzDataFactoryIntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json

# IMPORTANT: Install self-hosted integration runtime on your machine and use one of the keys to register the IR installed on your machine with the cloud service

# create an Az.Storage linked service

## JSON definition of the linked service. 
$storageLinkedServiceDefinition = @"
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$storageLinkedServiceDefinition | Out-File c:\AzureStorageLinkedService.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File c:\AzureStorageLinkedService.json

# create an on-premises SQL Server linked service

## JSON definition of the linked service. 
$sqlServerLinkedServiceDefinition = @"
{
   "properties": {
     "type": "SqlServer",
     "typeProperties": {
         "connectionString": {
             "type": "SecureString",
            "value": "Server=$sqlServerName;Database=$sqlDatabaseName;User ID=$sqlUserName;Password=$sqlPassword;Timeout=60"
         }
     },
     "connectVia": {
       "type": "integrationRuntimeReference",
       "referenceName": "$integrationRuntimeName"
     }
 },
 "name": "SqlServerLinkedService"
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$sqlServerLinkedServiceDefinition | Out-File c:\SqlServerLinkedService.json

## Encrypt SQL Server credentials 
New-AzDataFactoryLinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File "c:\SqlServerLinkedService.json" > c:\EncryptedSqlServerLinkedService.json

# Create a SQL Server linked service
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName "EncryptedSqlServerLinkedService" -File "c:\EncryptedSqlServerLinkedService.json"


# Create a source dataset for source SQL Server Database

## JSON definition of the dataset
$sourceSqlServerDatasetDefiniton = @"
{
   "properties": {
        "type": "SqlServerTable",
        "typeProperties": {
            "tableName": "$sqlTableName"
        },
        "structure": [
             {
                "name": "ID",
                "type": "String"
            },
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
        ],
        "linkedServiceName": {
            "referenceName": "EncryptedSqlServerLinkedService",
            "type": "LinkedServiceReference"
        }
    },
    "name": "SqlServerDataset"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sourceSqlServerDatasetDefiniton | Out-File c:\SqlServerDataset.json

# Create an Azure Blob dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File "c:\SqlServerDataset.json"

# Create a dataset for sink Azure Blob Storage

## JSON definition of the dataset
$sinkBlobDatasetDefiniton = @"
{
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "$blobFolderPath",
            "format": {
                "type": "TextFormat"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    },
    "name": "AzureBlobDataset"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sinkBlobDatasetDefiniton | Out-File c:\AzureBlobDataset.json

## Create the Azure Blob dataset
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File "c:\AzureBlobDataset.json"


# Create a pipeline in the data factory

## JSON definition of the pipeline
$pipelineDefinition = @"
{
   "name": "$pipelineName",
    "properties": {
        "activities": [       
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type":"BlobSink"
                    }
                },
                "name": "CopySqlServerToAzureBlobActivity",
                "inputs": [
                    {
                        "referenceName": "SqlServerDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "AzureBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ]
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipelineDefinition | Out-File c:\SqlServerToBlobPipeline.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$pipelineName" -File "c:\SqlServerToBlobPipeline.json"


# start the pipeline run
$runId = Invoke-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName

# Check the pipeline run status until it finishes the copy operation
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline $pipelineName run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}

# Get the activity run details 
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName `
        -PipelineRunId $runId `
        -RunStartedAfter (Get-Date).AddMinutes(-10) `
        -RunStartedBefore (Get-Date).AddMinutes(10) `
        -ErrorAction Stop

    $result

    if ($result.Status -eq "Succeeded") {`
        $result.Output -join "`r`n"`
    }`
    else {`
        $result.Error -join "`r`n"`
    }

# To remove the data factory from the resource gorup
# Remove-AzDataFactory -Name $dataFactoryName -ResourceGroupName $resourceGroupName
# 
# To remove the whole resource group
# Remove-AzResourceGroup  -Name $resourceGroupName
```


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő parancsot használhatja, ha az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás eltávolítása:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Az erőforráscsoport az adat-előállító eltávolításához futtassa a következő parancsot: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | A társított szolgáltatás hitelesítő adatai titkosítja, és létrehoz egy új társított szolgáltatás definíciójában a titkosított hitelesítő adataival. 
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Az adat-előállító létrehoz egy társított szolgáltatást. A társított szolgáltatás hivatkozik egy adattárat vagy számítási, adat-előállító. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Az adat-előállító egy adatkészletet hoz létre. Adatkészlet egy folyamat egyik tevékenységének bemeneti/kimeneti jelöli. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Létrehoz egy folyamatot az adat-előállítóban. Egy folyamatot, amely egy bizonyos műveletet hajt végre egy vagy több tevékenységet tartalmaz. Ez a folyamat egy másolási tevékenység adatokat másol egy helyről egy másik helyre egy Azure Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | A folyamat futtatása hoz létre. Más szóval futtatja a folyamatot. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | A tevékenység (tevékenység-végrehajtásonként), a Futtatás részleteinek beolvasása az adatcsatorna. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell szkriptminták találhatók az [Azure Data Factory PowerShell-minták](../samples-powershell.md).
