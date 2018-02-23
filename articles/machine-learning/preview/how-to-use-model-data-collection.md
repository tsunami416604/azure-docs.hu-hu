---
title: "A szolgáltatással modell adatok gyűjtése az Azure Machine Learning-munkaterület |} Microsoft Docs"
description: "Ez a cikk a modell adatok gyűjtemény szolgáltatás használata az Azure Machine Learning-munkaterület beszél"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 5e6a79a9b716b6797a1af21f0dcb911219b77207
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="collect-model-data-by-using-data-collection"></a>Adatgyűjtés a modell adatainak gyűjtéséről

A modell adatok gyűjtemény funkció az Azure Machine Learning segítségével archiválja a modell a be- és egy webszolgáltatás-bővítmény által.

## <a name="install-the-data-collection-package"></a>Az adatgyűjtési csomag telepítése
A Linux és Windows natív módon telepítheti a gyűjtemény könyvtára.

### <a name="windows"></a>Windows
Telepítse az adatokat gyűjtő moduljának Windows, a következő paranccsal:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Linux először telepítse a libxml ++ könyvtárban. A következő parancsot, amely kell kiadni a sudo:

    sudo apt-get install libxml++2.6-2v5

Ezután futtassa a következő parancsot:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Környezeti változók beállítása

Modell adatgyűjtés két környezeti változók függ. AML_MODEL_DC_STORAGE_ENABLED értékre kell állítani **igaz** (összes, kisbetű) és AML_MODEL_DC_STORAGE értékre kell állítani az Azure-tárfiók kapcsolati karakterlánca hol szeretné tárolni az adatokat.

Ezek a környezeti változók van beállítva, ha a webszolgáltatás fut a fürtön, az Azure-ban. A helyi futtatás során kell saját kezűleg meg őket. Docker használ, ha a parancs futtatása a docker -e paraméterének használatával átadására környezeti változók.

## <a name="collect-data"></a>Adatok gyűjtése

A modell adatgyűjtés használatához a következő módosításokat a pontozási fájlba:

1. A fájl elejéhez adja hozzá a következő kódot:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Adja hozzá a következő sorokat a kód a `init()` függvény:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Adja hozzá a következő sorokat a kód a `run(input_df)` függvény:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Győződjön meg arról, hogy a változók `input_df` és `pred` (előrejelzés értéket `model.predict()`) a rendszer hívása előtt inicializálja a `collect()` függvény rajtuk.

4. Használja a `az ml service create realtime` parancsot a `--collect-model-data true` kapcsolót, hogy hozzon létre egy valós idejű webszolgáltatás-bővítmény. Ez a lépés gondoskodik arról, hogy a modell adatait gyűjti a program a szolgáltatás fut.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Az adatgyűjtés teszteléséhez futtassa a `az ml service run realtime` parancs:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Az összegyűjtött adatok megtekintése
Az összegyűjtött adatok megtekintése a blob Storage tárolóban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás**.
3. Írja be a keresőmezőbe, **tárfiókok** válassza ki az Enter billentyűt.
4. Az a **tárfiókok** keresési panelen válassza a **tárfiók** erőforrás. Annak megállapításához, a tárfiók, tegye a következőket:

    a. Ugrás az Azure Machine Learning-munkaterület, válassza ki a projekt dolgozik, és nyisson meg egy parancssort a **fájl** menü.
    
    b. Adja meg `az ml env show -v` , és ellenőrizze a *storage_account* érték. Ez az Ön Storage-fiókjának neve

5. Válassza ki **tárolók** az erőforrás-panel menüt, és a tároló neve **modeldata**. Indítsa el a tárolási fiók propagálása adatok megjelenítéséhez szükség lehet a webalkalmazás első kérelem után akár 10 percet várnia. Az adatok a következő tárolóútvonallal vannak a blobokba továbbítva:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Adatok az Azure-blobokat képes használni a többféle módon, Microsoft-szoftverek és nyílt forrású eszközök is. Néhány példa:
- Az Azure Machine Learning munkaterület: Nyissa meg a .csv fájlt Azure Machine Learning-munkaterület adatforrásként a .csv fájl hozzáadásával.
- Excel: Munkafüzetként nyissa meg a napi .csv fájlokat.
- [A Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): blobok adatokat .csv lekért adatok készíthetnek diagramokat.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): adatok keret hozzon létre egy .csv adatok nagy része.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): .csv adatok betöltése a struktúrát a tábla, és végezze el az SQL lekérdezések közvetlenül a blob.

