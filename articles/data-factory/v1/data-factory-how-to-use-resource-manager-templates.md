---
title: Resource Manager-sablonok használata a Data Factoryban |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és használhat az Azure Resource Manager-sablonok létrehozása a Data Factory-entitásokat.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 9fb63ae141665dbeb64ee7046427098d4482aa55
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111322"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>A sablonok segítségével az Azure Data Factory-entitások létrehozása
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

## <a name="overview"></a>Áttekintés
Közben az Azure Data Factory használatával az adatok integrációs igényeinek, előfordulhat, hogy észre magát újbóli felhasználása ugyanazt a mintát között a különböző környezetek vagy végrehajtási megoldásban belül többször is ugyanazt a feladatot. A sablonok segítségével, megvalósítása és kezelése az ezekben a forgatókönyvekben a egyszerű módon. Sablonok az Azure Data Factoryban ideálisak forgatókönyvekről, újrahasznosíthatóság, és az ismétlődés.

Fontolja meg a helyzet, amelyben egy szervezet rendelkezik világszerte 10 gyártási üzemek. Minden üzem naplóit külön helyszíni SQL Server-adatbázis vannak tárolva. A vállalat szeretne hozhat létre egy egyetlen data warehouse a felhőben, az alkalmi elemzésekhez. Hogy ezek a ugyanazt a logikát, de az eltérő konfigurációk fejlesztési, tesztelési és éles környezetek esetében.

Ebben az esetben egy feladat meg kell ismételni ugyanabban a környezetben található, de eltérő értékek az egyes lapkagyártó műhelyben a 10 adat-előállítók között. Gyakorlatilag **ismétlési** megtalálható. Sablonalapú lehetővé teszi, hogy a általános folyamat (azaz ugyanazok a tevékenységek kellene az egyes adat-előállítók folyamatokat) az absztrakció, de az egyes lapkagyártó műhelyben külön paraméterfájlt használja.

Továbbá, ahogy a szervezet szeretné, üzembe helyezés ezek 10 adat-előállítók többször több különböző környezetekben, sablonok ezzel **újrahasznosíthatóság** felügyelniük különböző paraméterfájlok fejlesztéséhez, teszteléséhez és éles környezetben.

## <a name="templating-with-azure-resource-manager"></a>Az Azure Resource Manager-sablonalapú
[Az Azure Resource Manager-sablonok](../../azure-resource-manager/resource-group-overview.md#template-deployment) praktikus módot nyújtanak az Azure Data Factoryban sablonalapú eléréséhez. Resource Manager-sablonok meghatározása infrastruktúráját és konfigurációját az Azure-megoldás révén egy JSON-fájlt. Az Azure Resource Manager-sablonok működik a legtöbb vagy az összes Azure-szolgáltatásokkal, mert azt széles körben használható egyszerű kezelését az Azure-eszközök az összes erőforrást. Lásd: [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md) tudhat meg többet a Resource Manager-sablonok általában.

## <a name="tutorials"></a>Oktatóanyagok
Részletes útmutatás a Data Factory-entitások létrehozása a Resource Manager-sablonok használatával a következő oktatóanyagokban talál:

* [Oktatóanyag: Hozzon létre egy folyamatot az adatok másolása az Azure Resource Manager-sablon használatával](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Oktatóanyag: Adatok feldolgozása folyamat létrehozása az Azure Resource Manager-sablon használatával](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory sablonok a Githubon
Tekintse meg az alábbi Azure gyors üzembe helyezés sablonok a Githubon:

* [Hozzon létre egy Data factoryben az adatok Azure Blob Storage-ból az Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Adat-előállító létrehozása az Azure HDInsight-fürtön a Hive-tevékenység](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Hozzon létre egy Data factoryben az adatok a Salesforce-ból az Azure-Blobok](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Hozzon létre egy adat-előállítót, amely tevékenységek: adatokat másol egy FTP-kiszolgáló Azure-Blobok, hív meg egy igény szerinti HDInsight-fürtön az adatok átalakítása a hive-parancsprogram, és másolja át az eredmény az Azure SQL Database-be](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Nyugodtan, az Azure Data Factory sablonok megosztásához [Azure gyors üzembe helyezési](https://azure.microsoft.com/documentation/templates/). Tekintse meg a [közreműködői útmutató](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) , amelyek megoszthatók a tárház keresztül sablonok fejlesztése során.

A következő szakaszok a Resource Manager-sablon meghatározása a Data Factory-erőforrásokkal kapcsolatos adatokat.

## <a name="defining-data-factory-resources-in-templates"></a>Data Factory-erőforrások meghatározása a sablonokban
A data factory meghatározásához a legfelső szintű sablon a következő:

```JSON
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
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Data Factory definiálása
A data factoryt a Resource Manager-sablonban definiálhatja az alábbi minta szerint:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
A datafactoryname értékeket a "változók" határozza meg:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Adja meg a társított szolgáltatások

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Lásd: [Storage társított szolgáltatás](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [számítási társított szolgáltatások](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) kívánja telepíteni az adott társított szolgáltatás JSON-tulajdonságokkal kapcsolatos részletekért. A "dependsOn" paraméternek megfelelő adat-előállító nevét adja meg. Az Azure Storage társított szolgáltatás meghatározása példa a következő JSON-definíciójában:

### <a name="define-datasets"></a>Adatkészletet határoz meg

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Tekintse meg [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) az adott adatkészlet esetén a JSON-tulajdonságokkal kapcsolatos részletekért kívánja telepíteni. Megjegyzés: a "dependsOn" paraméter adja meg a megfelelő adat-előállító és a storage társított szolgáltatást. Az adatkészlet típusa Azure blob Storage meghatározása példa a következő JSON-definíciójában:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>A folyamatok meghatározása

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Tekintse meg [folyamatok meghatározása](data-factory-create-pipelines.md#pipeline-json) definiálása az adott folyamatok és tevékenységek kívánja telepíteni a JSON-tulajdonságokkal kapcsolatos részletekért. Megjegyzés: a "dependsOn" paraméter adja meg az adat-előállító nevét, és bármely megfelelő társított szolgáltatásokat és adathalmazokat. Egy folyamatot, amely adatokat másol az Azure Blob Storage az Azure SQL Database egy példát a következő JSON-kódrészletben látható:

```JSON
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
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>A Data Factory sablon paraméterezése
Ajánlott eljárások a paraméterezése, lásd: [ajánlott eljárások az Azure Resource Manager-sablonok létrehozására](../../azure-resource-manager/resource-manager-template-best-practices.md). Általánosságban véve a paraméter használatával kerülendő, különösen akkor, ha a változók helyett használhatók. Csak adja meg a paraméterek a következő esetekben:

* Beállítások régiónként eltérő környezetben (Példa: a fejlesztési, tesztelési és éles)
* Titkos kulcsokat (például jelszavak)

Ha a titkos kódok lekérése kell [Azure Key Vault](../../key-vault/key-vault-overview.md) sablonok használata az Azure Data Factory-entitások üzembe helyezésekor, adja meg a **a key vault** és **název tajného kódu** , ahogyan a Példa:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> A meglévő adat-előállítók sablonok exportálása jelenleg még nem támogatott, amíg van a működését.
>
>
