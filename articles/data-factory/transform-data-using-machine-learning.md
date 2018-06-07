---
title: Hozzon létre Azure Data Factory használatával prediktív adatok folyamatok |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre prediktív adatcsatorna Azure Machine Learning - kötegelt végrehajtási tevékenység az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: ffa65e542b83bccf1629674a0a4860e236401605
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621523"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Hozzon létre prediktív folyamatok Azure Machine Learning és az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [2. verzió – Előzetes verzió](transform-data-using-machine-learning.md)

[Az Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) lehetővé teszi létrehozása, tesztelése és telepítése a prediktív elemzési megoldások. Magas szintű szempontból elkészült a három lépést:

1. **Hozzon létre egy tanítási kísérletet**. Ebben a lépésben az Azure ML Studio úgy teheti meg. Az ML studio olyan együttműködési Látványelem-fejlesztési környezet, amelyekkel betanítása és tesztelése egy prediktív elemzési modell betanítási adatok használatával.
2. **Alakítsa át egy prediktív kísérletté**. Miután a modell még betanítva meglévő adatokkal, és készen áll az új adatok pontozása céljából, előkészítése, és a kísérlet pontozó egyszerűsítésére.
3. **A webszolgáltatás üzembe**. A pontozási kísérlet közzéteheti Azure webszolgáltatásként. Adatokat küldeni a modell a webes szolgáltatás végpontját keresztül, és eredmény előrejelzéseket fogadni a modellből.  

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Machine Learning kötegelt végrehajtási tevékenység a V1](v1/data-factory-azure-ml-batch-execution-activity.md).


### <a name="data-factory-and-machine-learning-together"></a>Adat-előállító és a gépi tanulás együtt
Az Azure Data Factory lehetővé teszi, hogy könnyen létrehozhat egy közzétett [Azure Machine Learning] [azure-gépi tanulásra] webszolgáltatás prediktív elemzési használó folyamatok. Használja a **kötegelt végrehajtási tevékenység** egy Azure Data Factory-folyamat a hívhat meg az Azure gépi tanulás webszolgáltatás számára a előrejelzéseket készítsen a kötegben lévő adatokat. 

Az Azure ml kísérletek pontozási prediktív modelleket idővel kell kell retrained új bemeneti adatkészletek használata. A Data Factory-folyamat az Azure ML modellje is működik, az alábbi lépések végrehajtásával:

1. Tegye közzé a tanítási kísérletet (nem prediktív kísérletté) webszolgáltatásként. Az előző példában a webszolgáltatásként teszi közzé a prediktív kísérletté hasonló módon teheti meg ebben a lépésben az Azure ML Studio.
2. Az Azure ML kötegelt végrehajtási tevékenység segítségével meghívni a webszolgáltatás a tanítási kísérletet. Alapvetően használhatja az Azure ML kötegelt végrehajtási tevékenység képzési webszolgáltatás és a pontozási webszolgáltatás meghívására.

Miután elkészült, az átképezési, frissítése a pontozási webszolgáltatás (prediktív kísérletté webszolgáltatásként kitett) újonnan betanított modell használatával a **Azure ML Update Erőforrástevékenység**. Lásd: [modellek használata az Update-Erőforrástevékenység frissítése](update-machine-learning-models.md) cikkben alább.

## <a name="azure-machine-learning-linked-service"></a>Az Azure Machine Learning társított szolgáltatás

Létrehozhat egy **Azure Machine Learning** társított az Azure Machine Learning webszolgáltatás összekapcsolása egy Azure data factory szolgáltatásnak. A társított szolgáltatás Azure Machine Learning kötegelt végrehajtási tevékenység által használt és [Update-Erőforrástevékenység](update-machine-learning-models.md). 


```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "apiVersion" : "2017-09-01-preview",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) a JSON-definícióból tulajdonságok leírása a cikkben találhat. 

Az Azure Machine Learning klasszikus webszolgáltatások, mind az új webszolgáltatás-a prediktív kísérletté támogatja. Lehetősége van a Data Factory használatához a megfelelőt. Az Azure Machine Learning társított szolgáltatás létrehozásához szükséges információkat, keresse fel https://services.azureml.net, ahol a Web Services-(új) és a klasszikus Web Services találhatók. Kattintson a webszolgáltatás szeretné elérni, és kattintson a **felhasználás** lap. Másolás **elsődleges kulcs** a **apiKey** tulajdonság, és **kötegelt kérelmekben** a **mlEndpoint** tulajdonság. 

![Az Azure Machine Learning-webszolgáltatásokat](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Az Azure Machine Learning kötegelt végrehajtási tevékenység

A következő JSON-részlet Azure Machine Learning kötegelt végrehajtási tevékenység határozza meg. A tevékenység definíciójának a korábban létrehozott társított Azure Machine Learning szolgáltatás hivatkozás van. 

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



| Tulajdonság          | Leírás                              | Szükséges |
| :---------------- | :--------------------------------------- | :------- |
| név              | A tevékenység a folyamat neve.     | Igen      |
| leírás       | A tevékenység mit leíró szöveg.  | Nem       |
| type              | Data Lake Analytics U-SQL-tevékenység, a tevékenység típusa nem **AzureMLBatchExecution**. | Igen      |
| linkedServiceName | Az Azure gépi tanulás összekapcsolt szolgáltatások társított szolgáltatás. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk. | Igen      |
| webServiceInputs  | Kulcs, érték párok, leképezési Azure Machine Learning webszolgáltatás bemenetei nevét. A közzétett Azure Machine Learning webszolgáltatás definiált bemeneti paraméterek kell egyeznie. Érték: a bemeneti Blob helyének megadása egy Azure Storage társított szolgáltatások és a fájl elérési útja tulajdonságok párt. | Nem       |
| webServiceOutputs | Kulcs, érték párok, leképezési Azure Machine Learning Web Service kimenetek nevét. A kimeneti paraméterek a közzétett Azure Machine Learning webszolgáltatás kell egyeznie. Egy Azure Storage társított szolgáltatások és a fájl elérési útja tulajdonságok pár adja meg a kimeneti Blob-helyét. | Nem       |
| globalParameters  | Kulcs, az Azure ML kötegelt végrehajtási szolgáltatás végpont átadandó érték párok. Kulcsoknak egyezniük kell a közzétett Azure ML web service definiált webszolgáltatás-paramétereket nevét. Érték lett átadva az Azure ML kötegelt végrehajtási kérelem GlobalParameters tulajdonságában | Nem       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>1. forgatókönyv: Kísérletek Web service bemenetek/kimenetek adataira az Azure Blob Storage használata

Ebben a forgatókönyvben az Azure Machine Learning Web service lehetővé teszi az adatok segítségével az Azure blob Storage-fájlból való előrejelzéseket és az előrejelzés eredményt a blob Storage tárolóban. A következő JSON határozza meg a Data Factory-folyamathoz egy AzureMLBatchExecution tevékenységet. A bemeneti és kimeneti adatok Azure Blog LinkedName és FilePath párból hivatkozik. Minta bemenetekhez és kimenetekhez a társított szolgáltatás nem egyezik, az egyes Data Factory a bemenetek/kimenetek különböző összekapcsolt szolgáltatások segítségével vegye fel a megfelelő fájlokat és az Azure ML Web Service küldése. 

> [!IMPORTANT]
> Az Azure ML kísérlet, a webszolgáltatás bemenetét és a kimeneti portok, valamint a globális paraméterek alapértelmezett neve lehet ("input1", "input2"), amely testre szabható. WebServiceInputs, webServiceOutputs és globalParameters beállítások használt neveket pontosan egyeznie kell a kísérleti nevét. A minta-kérések forgalma a a várt leképezés ellenőrzése az Azure ML végpont kötegelt végrehajtási súgó lapon tekintheti meg.
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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>2. forgatókönyv: Kísérleteket, tekintse meg a különböző tárolók adatainak olvasási/írási modulok használata
Azure ML kísérletek létrehozásakor egy másik gyakori forgatókönyv, hogy adatokat importálhat és a kimeneti adatok modulok használja. Az adatok importálása modul használatával adatok betöltése az a kísérlet, és a kimeneti adatok modul az adatok mentése a kísérletekből. Adatok importálása és a kimeneti adatok modullal kapcsolatos részletekért lásd: [és adatokat importálhat](https://msdn.microsoft.com/library/azure/dn905997.aspx) és [kimeneti adatok](https://msdn.microsoft.com/library/azure/dn905984.aspx) témakörök MSDN könyvtárában.     

Az adatok importálása és a kimeneti adatok modulok használata esetén ajánlott minden egyes tulajdonsága ezek a modulok a webszolgáltatási paraméter használandó. Ezek a paraméterek lehetővé teszik a adja meg a beállításokat futásidőben. Például az adatok importálása modul, amely használja az Azure SQL Database segítségével létrehozhat egy kísérlet: XXX.database.windows.net. A webszolgáltatás telepítése után szeretné-e a fogyasztók webszolgáltatás adjon meg egy másik Azure SQL Server nevű `YYY.database.windows.net`. A webszolgáltatási paraméter segítségével engedélyezze ezt az értéket be kell állítani.

> [!NOTE]
> Webes szolgáltatás bemeneti és kimeneti eltérnek a webszolgáltatás-paramétereket. Az első esetben láthatta, hogyan egy bemeneti és kimeneti adható meg az Azure gépi tanulás webszolgáltatás. Ebben a forgatókönyvben a paraméternek az adott webszolgáltatás, amely megfelel az adatok/kimeneti adatokat importálhat modulok tulajdonságai át.
>
> 

Vizsgáljuk meg egy olyan helyzetben használhat webszolgáltatás-paramétereket. Egy telepített Azure Machine Learning webszolgáltatás, amelyet használ egy olvasó modul adatokat olvasni az adatforrásokat, Azure Machine Learning által támogatott egyik rendelkezik (például: Azure SQL Database). A kötegelt végrehajtás hajtja végre, az eredmények írt író modul (az Azure SQL Database) használatával.  Nincs a webszolgáltatás bemenetei és kimenetei a kísérletek vannak definiálva. Ebben az esetben ajánlott úgy beállítani, hogy az írási és olvasási szerepkörökhöz modulok vonatkozó webszolgáltatás-paramétereket. Ez a konfiguráció lehetővé teszi, hogy az olvasási/írási modulok kell konfigurálni a AzureMLBatchExecution tevékenység használatakor. Azt adja meg a webszolgáltatás-paramétereket a **globalParameters** a tevékenység JSON szakasz az alábbiak szerint.

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
> A webszolgáltatás-paramétereket kis-és nagybetűket, ezért figyeljen oda arra, hogy a nevét adja meg, ha a tevékenység JSON egyeznek azokkal a webszolgáltatás által elérhetővé tett tárolókra.
>

Miután elkészült, az átképezési, frissítése a pontozási webszolgáltatás (prediktív kísérletté webszolgáltatásként kitett) újonnan betanított modell használatával a **Azure ML Update Erőforrástevékenység**. Lásd: [modellek használata az Update-Erőforrástevékenység frissítése](update-machine-learning-models.md) cikkben alább.



## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [A Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
