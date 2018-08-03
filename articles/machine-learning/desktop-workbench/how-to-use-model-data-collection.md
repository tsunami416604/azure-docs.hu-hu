---
title: A modell adatok gyűjtési szolgáltatás használata az Azure Machine Learning Workbenchben |} A Microsoft Docs
description: Ez a cikk ismerteti a modell adatok gyűjtési szolgáltatás használata az Azure Machine Learning Workbenchben
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 5c1a884ebe6216c4e8099f2ada2182ccff68b63e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449784"
---
# <a name="collect-model-data-by-using-data-collection"></a>Az adatgyűjtés modelladatok gyűjtése

A modell adatok gyűjtési szolgáltatás az Azure Machine Learning segítségével archiválja modellbemenetek és előrejelzések webszolgáltatásból.

## <a name="install-the-data-collection-package"></a>Az adatgyűjtési csomag telepítése
A gyűjtemény könyvtára natív módon telepítheti a Linux és Windows.

### <a name="windows"></a>Windows
Telepítse az adatokat gyűjtő moduljának Windows, a következő paranccsal:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Linuxon először telepítse a libxml ++ könyvtárban. Futtassa az alábbi parancsot, amely a sudo kell kiadni:

    sudo apt-get install libxml++2.6-2v5

Ezután futtassa a következő parancsot:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Környezeti változók beállítása

A modelladatok gyűjtésének két környezeti változó függ. AML_MODEL_DC_STORAGE_ENABLED értékre kell állítani **igaz** (csak kisbetűkkel) és AML_MODEL_DC_STORAGE kell állítani az Azure Storage-fiókhoz tartozó kapcsolati karakterláncot, az adatokat tárolni szeretné.

Ezekhez a környezeti változókhoz van beállítva, a web service-fürtön az Azure-ban való futtatásakor. Helyi futtatás során kell állítani őket saját maga. Ha használja a Docker, a docker, futtassa a parancsot az – e paraméter használatával adja át a környezeti változókat.

## <a name="collect-data"></a>Adatok gyűjtése

A modelladatgyűjtés használata, hajtsa végre a következő módosításokat a pontozófájl:

1. Adja hozzá a következő kódot a fájl elejéhez:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

1. Adja hozzá a következő kódsort a `init()` függvény:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1. Adja hozzá a következő kódsort a `run(input_df)` függvény:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Győződjön meg arról, hogy a változók `input_df` és `pred` (előrejelzés értéket `model.predict()`) hívása előtt inicializálja a `collect()` függvény rajtuk.

1. Használja a `az ml service create realtime` parancsot a `--collect-model-data true` kapcsolót, hogy a valós idejű webszolgáltatás létrehozására. Ebben a lépésben gondoskodik arról, hogy a modell adatait gyűjti a program fut a szolgáltatás.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
1. Ha tesztelni szeretné az adatgyűjtést, futtassa a `az ml service run realtime` parancsot:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Az összegyűjtött adatok megtekintése
Az összegyűjtött adatok megtekintése a blob storage-ban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **minden szolgáltatás**.
1. A Keresés mezőbe írja be a **tárfiókok** és az Enter billentyűt.
1. Az a **tárfiókok** keresés panelen válassza ki a **tárfiók** erőforrás. Annak megállapításához, a storage-fiókot, használja az alábbi lépéseket:

    a. Ugrás az Azure Machine Learning Workbench, válassza ki a projektet, amelyen dolgozik, és nyisson meg egy parancssort a **fájl** menü.
    
    b. Adja meg `az ml env show -v` , és ellenőrizze a *storage_account* értéket. Ez az Ön Storage-fiókjának neve

1. Válassza ki **tárolók** az erőforrás panel menüjének, és a tároló nevű **modeldata**. Tekintse meg az adatok propagálódjanak a storage-fiókba, szükség van az első webszolgáltatás-kérés után akár 10 percet várnia. Az adatok a következő tárolóútvonallal vannak a blobokba továbbítva:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Az Azure-blobokból tudják használni a többféle módon, Microsoft szoftvereket és nyílt forráskódú eszközök használatával. Néhány példa:
- Az Azure Machine Learning Workbench: A .csv-fájlt az Azure Machine Learning Workbenchben való felvételével nyissa meg a .csv-fájlt adatforrásként.
- Excel: Nyissa meg a napi csv-fájlokat a táblázatként.
- [Power bi-ban](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): hozzon létre diagramokat a .csv nyerhető a blobok lekért adatokkal.
- [A Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): adatkeretek hozzon létre egy .csv-adatok nagy része.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive-](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): .csv-adatok betöltése, a Hive táblát, és végezzen SQL-lekérdezéseket közvetlenül a blobon.

