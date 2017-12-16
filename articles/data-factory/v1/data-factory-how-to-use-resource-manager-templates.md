---
title: "Adat-előállítóban használata Resource Manager sablonok |} Microsoft Docs"
description: "Megtudhatja, hogyan történő létrehozásáról és használatáról az Azure Resource Manager-sablonok létrehozása a Data Factory entitásokat."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: e0f4a284a46ba56ba4e3229a72e99efef0cf9dc2
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Azure Data Factory entitásokat hozhatnak létre a sablonok segítségével
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. 

## <a name="overview"></a>Áttekintés
Során azt tapasztalhatja, saját maga az adat-integráció igényeinek Azure Data Factory használatával újból felhasználja a ugyanilyen mintájú különböző környezetek vagy végrehajtási ugyanahhoz a megoldáshoz belül többször is ugyanezt a feladatot. A sablonok révén megvalósítása és kezelése forgatókönyvekben egyszerű módon. Sablonok az Azure Data Factory ideálisak újrahasznosításának és ismétlődési forgatókönyveit.

Vegye figyelembe a helyzet, amikor egy szervezet rendelkezik a 10 gyártási tőzsdei árfolyamjelző keresztül történik. A naplók az egyes gépek önálló helyszíni SQL Server-adatbázisban tárolódnak. A vállalat szeretne a felhőben az alkalmi használatával egyetlen adatraktár létrehozásához. Azt is szeretné, ha a ugyanez a logika, de a fejlesztői, tesztelési és éles környezetek különböző konfigurációkkal rendelkező.

Ebben az esetben egy feladatot meg kell ismételni belül ugyanabba a környezetbe, de eltérő értékű között az egyes gyár 10 adat-előállítók. Gyakorlatilag **ismétlődési** található. Templating lehetővé teszi, hogy a elvonása, az általános folyamat (Ez azt jelenti, hogy ugyanazokat a tevékenységeket rendelkező minden adat-előállító adatcsatornák), de minden olyan üzem külön paraméterfájl használ.

Továbbá, mivel a szervezet által telepítendő ezek 10 adat-előállítók többször különböző környezetek között, sablonok segítségével **újrahasznosításának** külön paraméterfájlt fejlesztésekhez felügyelniük teszteléséhez és éles környezetben.

## <a name="templating-with-azure-resource-manager"></a>Az Azure Resource Manager eszközzel templating
[Az Azure Resource Manager-sablonok](../../azure-resource-manager/resource-group-overview.md#template-deployment) nagyszerű módját az Azure Data Factory templating eléréséhez. Resource Manager-sablonok JSON-fájl határozza meg az infrastruktúra és az Azure-megoldás konfigurációját. Mivel az Azure Resource Manager-sablonok működik a legtöbb vagy az összes Azure-szolgáltatásokkal, széles körben használat könnyedén kezelheti a Azure eszközök összes erőforrást. Lásd: [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md) további információt a Resource Manager-sablonok általában.

## <a name="tutorials"></a>Oktatóanyagok
Az alábbi oktatóanyagok adat-előállító entitások létrehozása a Resource Manager-sablonok használatával lépésenkénti útmutatót lásd:

* [Oktatóanyag: Adatok másolása az Azure Resource Manager-sablon használatával folyamat létrehozása](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Oktatóanyag: Hozzon létre egy folyamatot adatfeldolgozásra történő Azure Resource Manager-sablon](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory sablonok a Githubon
Tekintse meg a következő Azure gyors üzembe helyezési sablonokat a Githubon:

* [Adatok másolása az Azure SQL Database az Azure Blob Storage tartalmazó Data factory létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Hozzon létre egy adat-előállító Hive tevékenység on Azure HDInsight-fürt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Adatok másolása Salesforce Azure-blobot tartalmazó Data factory létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Hozzon létre egy adat-előállító tevékenységek láncában: adatokat másol egy FTP-kiszolgáló Azure BLOB, hívja meg az adatok átalakítására igény szerinti HDInsight-fürtök a hive parancsfájl, és másolja át az eredményt az Azure SQL-adatbázisba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Ahhoz, hogy az Azure Data Factory sablonokat nyugodtan [Azure gyors üzembe helyezési](https://azure.microsoft.com/documentation/templates/). Tekintse meg a [hozzájárulás útmutató](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) sablonok, amelyek megoszthatók az ebben a tárházban keresztül fejlesztése során.

A következő szakaszok részletesen bemutatják a Resource Manager-sablon adat-előállító erőforrások meghatározása.

## <a name="defining-data-factory-resources-in-templates"></a>Adat-előállító erőforrások meghatározása a sablonokban
A legfelső szintű sablon meghatározásához egy adat-előállítót a következő:

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
"Változó" a dataFactoryName típusúként van definiálva:

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

Lásd: [társított Társzolgáltatás](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [összekapcsolt szolgáltatások számítási](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) kívánja telepíteni az adott társított szolgáltatás JSON tulajdonságainak vonatkozó további információért. A "dependsOn" paraméternek megfelelő adat-előállító nevét adja meg. Az Azure Storage összekapcsolt szolgáltatás meghatározásának példa az alábbi JSON-definícióban:

### <a name="define-datasets"></a>Adja meg az adatkészletek

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
Tekintse meg [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a JSON-tulajdonságok az adott adathalmaztípushoz vonatkozó további információért kívánja telepíteni. Megjegyzés: a "dependsOn" paraméter határozza meg a megfelelő adat-előállító és a tároló neve társított szolgáltatás. Az Azure blob-tároló adatkészlet típusú meghatározásának példa az alábbi JSON-definícióban:

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

### <a name="define-pipelines"></a>Folyamatok megadása

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

Tekintse meg [folyamatok meghatározása](data-factory-create-pipelines.md#pipeline-json) JSON tulajdonságainak meghatározása a megadott feldolgozási sorban lévő és a tevékenységek kívánja telepíteni vonatkozó további információért. Jegyezze fel az "dependsOn" paraméter határozza meg a data factory nevét, és a szolgáltatások és adathalmazokat bármely megfelelő-e kapcsolva. Egy folyamatot, amely adatainak másolása az Azure Blob Storage-ból az Azure SQL Database-példa az az alábbi JSON kódrészletet:

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
## <a name="parameterizing-data-factory-template"></a>Adat-előállító sablon paraméterezése
Gyakorlati tanácsok a paraméterezése, lásd: [ajánlott eljárások Azure Resource Manager-sablonok létrehozásához](../../azure-resource-manager/resource-manager-template-best-practices.md). Általában a paraméter használatával kell tartani, különösen akkor, ha a változók helyett használhatók. Csak adja meg a paramétereket a következő esetekben:

* Beállítások eltérőek lehetnek a környezet által (Példa: fejlesztői, tesztelési és éles)
* Titkos kulcsokat (például a jelszavak)

Ha a titkos kulcsok lekéréses kell [Azure Key Vault](../../key-vault/key-vault-get-started.md) Azure Data Factory entitások-sablonokkal való telepítésekor adja meg a **kulcstároló** és **titkos neve** látható módon a Példa:

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
> A meglévő adat-előállítók sablonok exportálása jelenleg még nem támogatott, de napjainkban működik.
>
>
