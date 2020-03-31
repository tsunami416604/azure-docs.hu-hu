---
title: Erőforrás-kezelő sablonok használata az adatgyárban
description: Ismerje meg, hogyan hozhat létre és használhat Azure Resource Manager-sablonokat a Data Factory-entitások létrehozásához.
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
ms.openlocfilehash: 930a3f0cf629c99fc03a84c701bcf7b2807c77c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972866"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Sablonok használata Azure Data Factory-entitások létrehozásához
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

## <a name="overview"></a>Áttekintés
Az Azure Data Factory adatintegrációs igényeinek használata közben előfordulhat, hogy ugyanazt a mintát használja fel különböző környezetekben, vagy ugyanazt a feladatot ugyanazt a megoldást ismétlődően valósítja meg. A sablonok segítségével egyszerűen megvalósíthatja és kezelheti ezeket a forgatókönyveket. Az Azure Data Factory sablonjai ideálisak olyan forgatókönyvekhez, amelyek újrafelhasználhatóságot és ismétlést foglalnak magukban.

Tekintsük azt a helyzetet, amikor egy szervezetnek világszerte 10 gyártóüzeme van. Az egyes üzemek naplói egy külön helyszíni SQL Server-adatbázisban tárolódnak. A vállalat egyetlen adatraktárt szeretne építeni a felhőben az ad hoc elemzések hez. Azt is szeretné, hogy ugyanazt a logikát, de a különböző konfigurációk fejlesztési, tesztelési és éles környezetekben.

Ebben az esetben egy feladatot meg kell ismételni ugyanabban a környezetben, de különböző értékekkel a 10 adatgyárban minden gyártóüzemhez. Valójában **ismétlődés** van jelen. A Templating lehetővé teszi ennek az általános folyamatnak az absztrakcióját (azaz az egyes adat-előállítókban azonos tevékenységekkel rendelkező folyamatokat), de minden egyes gyártóüzemhez külön paraméterfájlt használ.

Továbbá, mivel a szervezet szeretné telepíteni ezt a 10 adatgyárak többször különböző környezetekben, sablonok használhatják ezt az **újrafelhasználhatóság** használatával külön paraméterfájlok fejlesztési, tesztelési és éles környezetekben.

## <a name="templating-with-azure-resource-manager"></a>Templating az Azure Resource Managerrel
[Az Azure Resource Manager-sablonok](../../azure-resource-manager/templates/overview.md) nagyszerű megoldást jelentenek az Azure Data Factory ban történő templating elérésére. A Resource Manager-sablonok egy JSON-fájlon keresztül határozzák meg az Azure-megoldás infrastruktúráját és konfigurációját. Mivel az Azure Resource Manager-sablonok az összes vagy a legtöbb Azure-szolgáltatással működnek, széles körben használható az Azure-eszközök összes erőforrásának egyszerű kezelésére. Az Erőforrás-kezelő sablonjairól az [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/templates/template-syntax.md) című témakörben olvashat bővebben.

## <a name="tutorials"></a>Oktatóanyagok
Az alábbi oktatóanyagokból részletesen bemutatja, hogy a Data Factory-entitásokat erőforrás-kezelősablonok használatával hozhassa létre:

* [Oktatóanyag: Hozzon létre egy folyamatot az adatok másolásához az Azure Resource Manager sablon használatával](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Oktatóanyag: Hozzon létre egy folyamatot az adatok feldolgozásához az Azure Resource Manager sablon használatával](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory-sablonok a GitHubon
Tekintse meg az alábbi Azure-gyorsindítási sablonokat a GitHubon:

* [Adatgyár létrehozása az Azure Blob Storage adatainak az Azure SQL Database-be történő másolásához](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Adatgyár létrehozása Hive-tevékenységgel az Azure HDInsight-fürtön](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Adatgyár létrehozása adatok másolásához a Salesforce-ból az Azure Blobs-ba](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Tevékenységeket láncokat tartalmazó adatfeldolgozó létrehozása: adatok másolása FTP-kiszolgálóról Az Azure Blobs ba, egy hive-parancsfájl meghívása egy igény szerinti HDInsight-fürtön az adatok átalakításához, valamint az eredmény az Azure SQL Database-be másolása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Az [Azure Quick start](https://azure.microsoft.com/documentation/templates/)szolgáltatásban megoszthatja Azure Data Factory-sablonjait. Tekintse meg a [hozzájárulási útmutatót,](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) miközben sablonokat fejleszt, amelyek ezen a tárházon keresztül oszthatók meg.

A következő szakaszok a Data Factory-erőforrások Erőforrás-kezelő sablonban történő meghatározásával kapcsolatos részleteket tartalmaznak.

## <a name="defining-data-factory-resources-in-templates"></a>Adatfeldolgozó erőforrások definiálása sablonokban
Az adat-előállító meghatározásának legfelső szintű sablonja a következő:

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
A dataFactoryName a "változók" a következőképpen van definiálva:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Csatolt szolgáltatások definiálása

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

A telepíteni kívánt kapcsolt szolgáltatás JSON-tulajdonságainak részleteiért lásd a [Storage Linked Service](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy a [Compute Linked Services](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) című témakört. A "dependsOn" paraméter megadja a megfelelő adat-előállító nevét. Egy példa egy összekapcsolt szolgáltatás definiálására az Azure Storage-hoz a következő JSON-definícióban látható:

### <a name="define-datasets"></a>Adatkészletek definiálása

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
A telepíteni kívánt adatkészlettípus JSON-tulajdonságaival kapcsolatos részleteket a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) című dokumentumban találja. Vegye figyelembe, hogy a "dependsOn" paraméter megadja a megfelelő adat-előállító és tárolókapcsolt szolgáltatás nevét. Az Azure blob storage adatkészlettípusának definiálására példa a következő JSON-definíció látható:

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

### <a name="define-pipelines"></a>Folyamatok definiálása

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

Tekintse meg a [folyamatok definiálása](data-factory-create-pipelines.md#pipeline-json) a JSON-tulajdonságok meghatározásához az adott folyamat és a telepíteni kívánt tevékenységek meghatározásához. Vegye figyelembe, hogy a "dependsOn" paraméter megadja az adat-előállító nevét, valamint a megfelelő csatolt szolgáltatásokat vagy adatkészleteket. Egy példa egy folyamat, amely adatokat másol az Azure Blob Storage-ból az Azure SQL Database-be a következő JSON-kódrészletben látható:

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
## <a name="parameterizing-data-factory-template"></a>Adatgyársablon paraméterezése
A paraméterezéssel kapcsolatos gyakorlati tanácsok az [Azure Resource Manager-sablonok létrehozásának ajánlott módszerei című témakörben olvashat.](../../azure-resource-manager/resource-manager-template-best-practices.md) Általánosságban elmondható, hogy a paraméterhasználatot minimalizálni kell, különösen akkor, ha változók használhatók helyette. Csak a következő esetekben adjon meg paramétereket:

* A beállítások környezettől függően változnak (például: fejlesztés, tesztelés és éles környezet)
* Titkos kulcsok (például jelszavak)

Ha az Azure Data Data Factory-entitások sablonok használatával történő üzembe helyezésekor titkokat kell leállítania az [Azure Key Vaultból,](../../key-vault/key-vault-overview.md) adja meg a **key vaultot** és a **titkos nevet** az alábbi példában látható módon:

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
> Bár a meglévő adatgyárak sablonjainak exportálása jelenleg még nem támogatott, a munkálatok folyamatban vannak.
>
>
