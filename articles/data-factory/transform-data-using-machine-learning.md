---
title: Prediktív adatfolyamatok létrehozása
description: Ismerje meg, hogyan hozhat létre prediktív folyamatot az Azure Machine Learning – Batch-végrehajtási tevékenység használatával az Azure Data Factoryban.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: 44371c78a4d02588eef21aae5a4bba3d033763d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029992"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Prediktív folyamatok létrehozása az Azure Machine Learning és az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuális verzió](transform-data-using-machine-learning.md)

[Az Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) lehetővé teszi, hogy prediktív elemzési megoldásokat hozzon létre, teszteljen és telepítsen. Magas szintű szempontból ez három lépésben történik:

1. **Hozzon létre egy betanítási kísérletet.** Ezt a lépést az Azure Machine Learning Studio (klasszikus) használatával. Az Azure Machine Learning Studio (klasszikus) egy együttműködésen alapuló vizuális fejlesztési környezet, amely et egy prediktív elemzési modell betanítására és tesztelésére használhat betanítási adatok használatával.
2. **Alakítsa át prediktív kísérletté.** Miután a modell betanítása a meglévő adatokkal, és készen áll arra, hogy használja az új adatok pontozására, előkészíti és racionalizálja a kísérletet a pontozáshoz.
3. **Telepítse webszolgáltatásként.** Közzéteheti a pontozási kísérlet egy Azure-webszolgáltatás. Adatokat küldhet a modellnek ezen a webszolgáltatás végpontján keresztül, és a modelltől eredmény-előrejelzéseket kaphat.

### <a name="data-factory-and-machine-learning-together"></a>A Data Factory és a Machine Learning együtt
Az Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre folyamatokat, amelyek egy közzétett [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) webszolgáltatást használnak a prediktív elemzéshez. A **batch-végrehajtási tevékenység** egy Azure Data Factory-folyamat használatával meghívhat egy Azure Machine Learning Studio (klasszikus) webszolgáltatás, hogy előrejelzéseket az adatok kötegelt.

Idővel az Azure Machine Learning Studio (klasszikus) pontozási kísérletek prediktív modelljeit újra kell képezni az új bemeneti adatkészletek használatával. A data factory folyamatból az alábbi lépésekkel újrabetaníthat egy modellt:

1. Tegye közzé a betanítási kísérletet (nem prediktív kísérlet) webszolgáltatásként. Ezt a lépést az Azure Machine Learning Studio (klasszikus), ahogy tette, hogy a prediktív kísérlet, mint egy webes szolgáltatás az előző forgatókönyvben.
2. Az Azure Machine Learning Studio (klasszikus) batch-végrehajtási tevékenység használatával hívja meg a webes szolgáltatást a betanítási kísérlethez. Alapvetően használhatja az Azure Machine Learning Studio (klasszikus) batch-végrehajtási tevékenység meghívására egyaránt betanítási webszolgáltatás és pontozási webszolgáltatás.

Miután végzett az átképzés, frissítse a pontozási webszolgáltatás (prediktív kísérlet elérhetővé vált webszolgáltatásként) az újonnan betanított modell segítségével az **Azure Machine Learning Studio (klasszikus) erőforrás-szolgáltatás frissítése.** A részleteket a [Modellek frissítése az Erőforrás-tevékenység frissítése](update-machine-learning-models.md) című cikkben olvashatja.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning csatolt szolgáltatás

Hozzon létre egy **Azure Machine Learning-alapú** szolgáltatást, amely egy Azure Machine Learning webszolgáltatást egy Azure-adat-előállítóhoz kapcsol. A csatolt szolgáltatást az Azure Machine Learning batch-végrehajtási tevékenység és [az erőforrás-tevékenység frissítése](update-machine-learning-models.md)használja.

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

A JSON-definícióban a tulajdonságokkal kapcsolatos leírásokat a [Számítási csatolt szolgáltatások](compute-linked-services.md) ról című cikkben olvashat.

Az Azure Machine Learning támogatja a klasszikus webszolgáltatásokat és az új webszolgáltatásokat a prediktív kísérlethez. Kiválaszthatja a megfelelőt a Data Factory-ból. Az Azure Machine Learning csatolt szolgáltatás létrehozásához szükséges https://services.azureml.netinformációk beszerezéséhez nyissa meg a , az összes (új) webszolgáltatás és klasszikus webszolgáltatások listája. Kattintson a használni kívánt webszolgáltatásra, majd a **Felhasználás** lapra. **Elsődleges kulcs** másolása **apiKey** tulajdonsághoz és **batch kérelmek** **mlEndpoint** tulajdonsághoz.

![Azure Machine Learning webszolgáltatások](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Azure Machine Learning batch-végrehajtási tevékenység

A következő JSON-kódrészlet egy Azure Machine Learning batch-végrehajtási tevékenységet határoz meg. A tevékenységdefiníció hivatkozást tartalmaz az Azure Machine Learning hez kapcsolódó szolgáltatás korábban létrehozott.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Tulajdonság          | Leírás                              | Kötelező |
| :---------------- | :--------------------------------------- | :------- |
| név              | A folyamatban lévő tevékenység neve     | Igen      |
| leírás       | A tevékenység tevékenységét leíró szöveg.  | Nem       |
| type              | A Data Lake Analytics U-SQL tevékenység esetén a tevékenység típusa **Az AzureMLBatchExecution.** | Igen      |
| linkedServiceName | Összekapcsolt szolgáltatások az Azure Machine Learning csatolt szolgáltatáshoz. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat. | Igen      |
| webServiceInputs  | Kulcs, Érték párok, az Azure Machine Learning webszolgáltatás bemeneteinek nevének leképezése. A kulcsnak meg kell egyeznie a közzétett Azure Machine Learning webszolgáltatásban definiált bemeneti paraméterekkel. Az érték egy Azure Storage Linked Services és FilePath tulajdonságpár, amely megadja a bemeneti Blob-helyeket. | Nem       |
| webServiceOutputs | Kulcs, Érték párok, az Azure Machine Learning webszolgáltatás kimeneteinek leképezése. A kulcsnak meg kell egyeznie a közzétett Azure Machine Learning webszolgáltatásban definiált kimeneti paraméterekkel. Az érték egy Azure Storage Linked Services és FilePath tulajdonságpár, amely meghatározza a kimeneti Blob-helyeket. | Nem       |
| globalParameters  | Kulcs, Érték párok át kell adni az Azure Machine Learning Studio (klasszikus) batch-végrehajtási szolgáltatás végpont. A kulcsoknak meg kell egyezniük a közzétett Azure Machine Learning Studio (klasszikus) webszolgáltatásban definiált webszolgáltatás-paraméterek nevével. Az értékek az Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási kérelem GlobalParameters tulajdonságában kerülnek átadásra | Nem       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>1. forgatókönyv: Webszolgáltatás-bemeneteket/-kimeneteket használó kísérletek, amelyek az Azure Blob Storage-ban lévő adatokra hivatkoznak

Ebben a forgatókönyvben az Azure Machine Learning webszolgáltatás előrejelzéseket készít egy fájlból származó adatok használatával egy Azure blob storage-ban, és tárolja az előrejelzési eredményeket a blob storage.In this scenario, the Azure Machine Learning Web service makes predictions using data from a file in an Azure blob storage and stores the prediction results in the blob storage. A következő JSON egy AzureMLBatchExecution tevékenységgel rendelkező Data Factory-folyamatot határoz meg. Az Azure Blog Storage bemeneti és kimeneti adataiegy LinkedName és FilePath-pár használatával történik. A minta csatolt szolgáltatás bemenetek és kimenetek különbözőek, használhatja a különböző csatolt szolgáltatások minden egyes bemenetek/kimenetek data factory, hogy képes legyen felvenni a megfelelő fájlokat, és elküldi az Azure Machine Learning Studio (klasszikus) webszolgáltatás.

> [!IMPORTANT]
> Az Azure Machine Learning Studio (klasszikus) kísérletében a webszolgáltatás bemeneti és kimeneti portjai és a globális paraméterek alapértelmezett nevekkel ("input1", "input2") rendelkeznek, amelyek testre szabhatók. A webServiceInputs, webServiceOutputs és globalParameters beállításokhoz használt neveknek pontosan meg kell egyezniük a kísérletekben szereplő nevekkel. Megtekintheti a mintakérelem hasznos adatait az Azure Machine Learning Studio (klasszikus) végpont batch-végrehajtási súgólapján a várt leképezés ellenőrzéséhez.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>2. forgatókönyv: Olvasó/író modulok at használó kísérletek a különböző tárolókban lévő adatokra való hivatkozáshoz
Egy másik gyakori forgatókönyv az Azure Machine Learning Studio (klasszikus) kísérletek létrehozásakor az Adatok és a kimeneti adatok moduljainak használata. Az Adatok importálása modul segítségével adatokat tölthet be egy kísérletbe, a Kimeneti adatok modul pedig a kísérletek ből származó adatok mentésére szolgál. Az Adatok és kimeneti adatok moduljainak importálásáról az MSDN-kódtár [adatok](https://msdn.microsoft.com/library/azure/dn905997.aspx) és [kimeneti adatok](https://msdn.microsoft.com/library/azure/dn905984.aspx) importálása témakörében olvashat részletesen.

Az Adatok és kimeneti adatok importálása modulok használatakor célszerű webszolgáltatás-paramétert használni ezekhez a modulokhoz. Ezek a webes paraméterek lehetővé teszik az értékek futásközbeni konfigurálását. Létrehozhat például egy kísérletet egy Azure SQL Database: XXX.database.windows.net használó Import Data modullal. A webszolgáltatás telepítése után engedélyezni szeretné, hogy a webszolgáltatás felhasználói adjanak meg `YYY.database.windows.net`egy másik Azure SQL Server nevű . Az érték konfigurálásának engedélyezéséhez webszolgáltatás-paramétert is használhat.

> [!NOTE]
> A webszolgáltatás bemenete és kimenete eltér a webszolgáltatás paramétereitől. Az első forgatókönyvben azt is láthatta, hogy egy azure Machine Learning Studio (klasszikus) webszolgáltatáshoz hogyan adható meg egy bemeneti és kimeneti szolgáltatás. Ebben a forgatókönyvben adja át az adatok importálása/kimeneti adatok moduljainak tulajdonságainak megfelelő webszolgáltatás paramétereit.
>
>

Nézzünk meg egy forgatókönyvet a webszolgáltatás paramétereinek használatára. Van egy üzembe helyezett Azure Machine Learning webszolgáltatás, amely egy olvasó modul thasználja az adatok olvasásához az Azure Machine Learning által támogatott adatforrások (például: Azure SQL Database). A kötegelt végrehajtás végrehajtása után az eredmények egy Writer modul (Azure SQL Database) használatával írják.  A kísérletekben nincswebszolgáltatás-bemenet és kimenet definiálva. Ebben az esetben azt javasoljuk, hogy konfigurálja a megfelelő webszolgáltatás-paramétereket az olvasó és író modulokhoz. Ez a konfiguráció lehetővé teszi, hogy az olvasó/író modulok konfigurálása az AzureMLBatchExecution tevékenység használatakor. A JSON tevékenység **globalParameters** szakaszában a webszolgáltatás paramétereit az alábbiak szerint adhatja meg.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> A webszolgáltatás paraméterei nem felelnek meg a kis- és nagybetűknek, ezért győződjön meg arról, hogy a JSON tevékenységben megadott nevek megegyeznek a webszolgáltatás által elérhetővé tett nevekkel.
>

Miután végzett az átképzés, frissítse a pontozási webszolgáltatás (prediktív kísérlet elérhetővé vált webszolgáltatásként) az újonnan betanított modell segítségével az **Azure Machine Learning Studio (klasszikus) erőforrás-szolgáltatás frissítése.** A részleteket a [Modellek frissítése az Erőforrás-tevékenység frissítése](update-machine-learning-models.md) című cikkben olvashatja.

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat:

* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Sertésaktivitás](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
