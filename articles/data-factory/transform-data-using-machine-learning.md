---
title: Azure Data Factory segítségével prediktív adatcsatornák létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy prediktív folyamatot az Azure Machine Learning - kötegelt végrehajtási tevékenység az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 494aa435f2b3e682ae330baa61d4778f3d91d789
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025742"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Az Azure Machine Learning és az Azure Data Factory prediktív adatcsatornák létrehozása
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuális verzió](transform-data-using-machine-learning.md)

[Az Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) lehetővé teszi, hogy hozhat létre, tesztelheti és üzembe helyezése a prediktív elemzési megoldások. Egy magas szintű szempontjából ez történik, három lépésben:

1. **Betanítási kísérlet létrehozása**. Az Azure Machine Learning Studio használatával teheti meg ezt a lépést. A Machine Learning studio egy által biztosított együttműködési környezettel vizuális fejlesztői környezet, amellyel taníthat vagy tesztelhet egy prediktív elemzési modellt használ a betanítási adatok.
2. **Alakítsa át egy prediktív kísérletet**. A modell rendelkezik betanítva a adatokkal, és készen áll az új adatok pontozása céljából, előkészítése, és egyszerűsíthetők a pontozó kísérlet során.
3. **Helyezze üzembe webszolgáltatásként, amely**. A pontozó kísérlet során egy Azure-webszolgáltatásként teheti közzé. Adatokat küldeni a modell használatával a webes szolgáltatás végpontját, és az eredmény előrejelzéseket kap a modellből.  

### <a name="data-factory-and-machine-learning-together"></a>A Data Factory és a gépi tanulás együtt
Az Azure Data Factory lehetővé teszi, hogy könnyedén hozhat létre, melyek egy közzétett [az Azure Machine Learning] [azure-gépi tanulási] webes szolgáltatást prediktív elemzési folyamatok. Használatával a **kötegelt végrehajtási tevékenység** az Azure Data Factory-folyamatot, az Azure Machine Learning webszolgáltatás, hogy előrejelzéseket végezzen a batch szolgáltatásban az adatok hívhatók meg. 

Az idő múlásával a prediktív modelleket a kísérletek pontozási Azure ML új bemeneti adatkészletek használatával lehet retrained kell. A Data Factory-folyamatot az Azure gépi Tanulási modellek ténytábláknál a következő lépések végrehajtásával:

1. Tegye közzé a tanítási kísérlet (nem prediktív kísérletté) webszolgáltatásként. Ebben a lépésben az Azure gépi tanulás stúdióban végezhet el, ahogy tette elérhetővé a prediktív kísérletté webszolgáltatásként, amely az előző forgatókönyvben.
2. Az Azure ML kötegelt végrehajtási tevékenység használatával meghívni a webszolgáltatás a betanítási kísérlet. Alapvetően használhatja az Azure ML kötegelt végrehajtási tevékenység képzési webszolgáltatás és a pontozási webszolgáltatás meghívandó.

Miután elkészült, az átképezési, a pontozási webszolgáltatás (prediktív kísérletté webszolgáltatásként közzétéve) az újonnan betanított modell használatával módosítsa a **Azure ML Update Erőforrástevékenység**. Lásd: [frissítési modellek használata az Update-Erőforrástevékenység](update-machine-learning-models.md) részleteivel.

## <a name="azure-machine-learning-linked-service"></a>Az Azure Machine Learning-beli társított szolgáltatás

Létrehoz egy **Azure Machine Learning** társított szolgáltatást az Azure Machine Learning Web Service az Azure data factoryt. Az Azure Machine Learning kötegelt végrehajtási tevékenység által használt társított szolgáltatás és [Update-Erőforrástevékenység](update-machine-learning-models.md). 


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

Lásd: [társított szolgáltatások számítása](compute-linked-services.md) a JSON-definíciót a tulajdonságok leírását a cikk. 

Az Azure Machine Learning klasszikus webszolgáltatásoknál és a prediktív kísérletet új webszolgáltatásokat is támogatja. A Data Factory használata a megfelelőt választhatja. Az Azure Machine Learning társított szolgáltatás létrehozásához szükséges adatokat, keresse fel https://services.azureml.net, hol a Web Services-(új) és a klasszikus webszolgáltatások listáját. Kattintson a Web Service hozzáférést, és kattintson a kívánt **felhasználás** lapot. Másolás **elsődleges kulcs** a **apikey tulajdonsággal végzett tesztelése** tulajdonságot, és **kérések** a **mlEndpoint** tulajdonság. 

![Az Azure Machine Learning-webszolgáltatások](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Az Azure Machine Learning Batch Execution-tevékenység

A következő JSON-kódrészlet definiál egy Azure Machine Learning Batch Execution-tevékenység. A tevékenység meghatározásában a korábban létrehozott társított Azure Machine Learning szolgáltatás vonatkozó hivatkozás van. 

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
| név              | A folyamat a tevékenység neve     | Igen      |
| leírás       | A tevékenység leírása leíró szöveg.  | Nem       |
| type              | Data Lake Analytics U-SQL-tevékenység, a tevékenység típusa van **AzureMLBatchExecution**. | Igen      |
| linkedServiceName | Társított szolgáltatások, az Azure Machine Learning társított szolgáltatást. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk. | Igen      |
| webServiceInputs  | Kulcs érték párok, leképezése az Azure Machine Learning Web Service bemenetek nevei. Kulcsot meg kell egyeznie a közzétett Azure Machine Learning Web Service a definiált bemeneti paraméterek. A bemeneti Blob helyének megadása egy Azure Storage társított szolgáltatásokat és a fájl elérési útja tulajdonságok pár értéke. | Nem       |
| webServiceOutputs | Kulcs érték párok, leképezése az Azure Machine Learning Web Service kimenetek nevei. A közzétett Azure Machine Learning Web Service a kimeneti paraméterek kell egyeznie. Egy Azure Storage társított szolgáltatást, és a fájl elérési útja tulajdonságok pár adja meg a kimeneti Blob-helyeken. | Nem       |
| globalParameters  | Kulcs érték párok történő átadása az Azure ML kötegelt végrehajtási szolgáltatás végpontját. Kulcsok meg kell egyeznie a közzétett Azure ML web service-ben definiált webszolgáltatás-paraméterek nevei. Az Azure ML kötegelt végrehajtási kérelem GlobalParameters tulajdonságában érték lett átadva | Nem       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>1. forgatókönyv: A kísérletek webes szolgáltatás bemenete/kimenete, amely az adatoknak az Azure Blob Storage használatával

Ebben a forgatókönyvben az Azure Machine Learning Web service-adatok egy Azure blob Storage-fájlból előrejelzéseket tesz, és a blob Storage-Előrejelzési eredményeket tárolja. A következő JSON-kódrészlet meghatározza egy Data Factory-folyamatot egy olyan AzureMLBatchExecution tevékenységgel. A bemeneti és kimeneti adatokat az Azure Blog Storage olyan LinkedName és FilePath virtuálisgép-pár hivatkozik. A minta a társított szolgáltatás a bemenetek és kimenetek különböző, különböző társított szolgáltatásokat, az egyes a bemenetekben/kimenetekben. a Data Factory segítségével a megfelelő fájlok felvétele és küldeni az Azure ML Web Service. 

> [!IMPORTANT]
> Az Azure Machine Learning-kísérlet, webes szolgáltatás bemeneti és kimeneti portok és a globális paraméterek alapértelmezett neve lehet ("input1", "input2"), amelyek testre szabhatók. A nevek webServiceInputs webServiceOutputs és globalParameters beállításokat használhat a kísérletek a nevek pontosan egyeznie kell. A kérelem hasznosadat-minta a kötegelt végrehajtási súgóoldalán az Azure ML-végpontokra, ellenőrizze a várt leképezést a tekintheti meg.
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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>2. forgatókönyv: Író vagy olvasó modulok használata az adatoknak a különböző tárolók kísérletek
Egy másik gyakori forgatókönyv, amikor az Azure Machine Learning-kísérletek létrehozása az adatok importálása és a kimeneti adatokat modult használja. Adatok betöltése egy kísérletet az szolgál az adatok importálása modullal, és a kimeneti adatokat modul az adatok mentése a kísérletekből. Adatok importálása és a kimeneti adatokat modullal kapcsolatos részletekért lásd: [adatok importálása](https://msdn.microsoft.com/library/azure/dn905997.aspx) és [kimeneti adatok](https://msdn.microsoft.com/library/azure/dn905984.aspx) témakörök az MSDN könyvtárában.     

Az adatok importálása és a kimeneti adatokat modulok használata esetén hasznos lehet egy webes szolgáltatás paraméter használata minden egyes tulajdonsága ezeket a modulokat. Webes paraméterek lehetővé teszik az értékek konfigurálása során. Például létrehozhat egy kísérlet használja az Azure SQL Database-adatok importálása modullal: XXX.database.windows.net. Miután a web service telepítve lett, a web service, adjon meg egy másik Azure SQL Server nevű felhasználói számára engedélyezni szeretné `YYY.database.windows.net`. A Web service paramétert használhatja, hogy ezt az értéket kell konfigurálni.

> [!NOTE]
> Webes szolgáltatás bemeneti és kimeneti eltérnek a webszolgáltatás-paraméterek. Az első esetben láthatta, hogyan egy bemeneti és kimeneti adható meg az Azure Machine Learning webszolgáltatás. Ebben a forgatókönyvben adja át a paramétereket egy webszolgáltatás, amelyek megfelelnek az adatok/kimeneti adatok importálása modulok tulajdonságait.
>
> 

Tekintsük át egy webszolgáltatás-paraméterek a forgatókönyvet. Egy telepített Azure Machine Learning webszolgáltatás, amelyet egy olvasó modul használ adatokat olvasni az Azure Machine Learning által támogatott adatforrások egyik rendelkezik (például: Az Azure SQL Database). A kötegelt végrehajtás hajtja végre, miután az eredmények használatával írt író modul (az Azure SQL Database).  Nincs webes szolgáltatás bemeneteit és kimeneteit a kísérletek vannak definiálva. Ebben az esetben javasoljuk, hogy konfigurálja a megfelelő webszolgáltatás-paraméterek az írási és olvasási szerepkörökhöz modulok. Ez a konfiguráció lehetővé teszi, hogy az író vagy olvasó modulok konfigurálását, ha a AzureMLBatchExecution tevékenységgel. Azt adja meg a webszolgáltatás-paraméterek a **globalParameters** a következő szakasz a tevékenység JSON.

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
> A webszolgáltatás-paraméterek kis-és nagybetűket, ezért győződjön meg arról, hogy a nevét adja meg, ha a tevékenység a JSON egyeznek azokkal a webszolgáltatás által elérhetővé tett.
>

Miután elkészült, az átképezési, a pontozási webszolgáltatás (prediktív kísérletté webszolgáltatásként közzétéve) az újonnan betanított modell használatával módosítsa a **Azure ML Update Erőforrástevékenység**. Lásd: [frissítési modellek használata az Update-Erőforrástevékenység](update-machine-learning-models.md) részleteivel.



## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
