---
title: 'Írás: adat-előkészítési Python SDK-t'
titleSuffix: Azure Machine Learning service
description: Ismerje meg az adatok Azure Machine Learning Data Prep SDK-val. Írhat egy adatfolyamot, és fájlokat a (helyi rendszer, az Azure Blob Storage és Azure Data Lake Storage) a támogatott helyek bármelyikét bármikor adatai.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 8bf61e6506e0d109b83fa323439348c3803bd5ce
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730998"
---
# <a name="write-and-configure-data-using-azure-machine-learning"></a>Írhat, és konfigurálja az adatok Azure Machine Learning segítségével

Ebből a cikkből megtudhatja, különböző módszerekkel használata az adatok írása a [Azure Machine Learning Data Prep Python SDK](https://aka.ms/data-prep-sdk) és konfigurálása, hogy az adatok a [Azure Machine Learning SDK for Pythont](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Kimeneti adatok csak írható egy adatfolyam bármely pontján. Írási műveletek bekerülnek a futás az eredményül kapott adatokat a folyamat lépéseit, és ezeket a lépéseket minden alkalommal, amikor az adatok folyamatfuttatási jogosultságot. Adatok írása párhuzamos írások engedélyezéséhez több partíció-fájlt.

Mivel ebben az esetben nincs korlátozás hány írása lépéseket egy folyamatban vannak, könnyen adhat hozzá további írási lépéseket a köztes eredményeket hibaelhárítási vagy egyéb folyamatokhoz.

Minden alkalommal, amikor egy írási lépés futtatásakor az adatok az adatfolyam egy teljes lekéréses történik. Például írási három lépésben az adatfolyam olvasása és feldolgozása az adatkészlet minden rekord három alkalommal.

## <a name="supported-data-types-and-location"></a>Támogatott adattípusok és helye

A következő fájlformátumokat támogatottak.
-   Tagolt fájlokat (CSV, TSV, stb.)
-   Parquet-fájlokat

Az Azure Machine Learning Data Prep Python SDK használatával, az adatokat írhat be:
+ a helyi fájlrendszerben
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>A Spark szempontok

Az adatfolyam Spark való futtatáskor kell írni egy üres mappát. Megpróbálja futtatni az írási hiba egy létező mappa eredményez. Győződjön meg arról, hogy a célmappában üres, vagy használjon másik célhelyet az egyes futtatások, vagy az írás sikertelen lesz.

## <a name="monitoring-write-operations"></a>Írási műveletek figyelése

Az Ön kényelme érdekében sikeres nevű sentinel fájl jön létre egy írási befejeződése után. A jelenléte segítségével azonosíthatja a egy köztes írási nem kell megvárnia a teljes folyamat befejezéséhez befejezése után.

## <a name="example-write-code"></a>Írási kódpéldákat

Ebben a példában először egy folyamatot az adatok betöltését `auto_read_file()`. Ezeket az adatokat különböző formátumokban újból felhasználja.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Példa a kimenetre:
| | 1. oszlop | Column2 | Column3 | 4. oszlopig | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | None | NO | NO | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | None | NO | NO | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | None | NO | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | None | NO | NO |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | None | NO | NO | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>Tagolt fájl például

A következő kódban a [ `write_to_csv()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-csv-directory-path--destinationpath--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) függvény adatokat írni egy tagolt fájlt.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Példa a kimenetre:
| | 1. oszlop | Column2 | Column3 | 4. oszlopig | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | HIBA | NO | NO | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | HIBA | NO | NO | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | HIBA | NO | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | HIBA | NO | NO |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | HIBA | NO | NO | ENSO |    59783.0 | 5350.0 |  500.0|

Az előző kimeneti hibák jelennek meg a numerikus oszlopok miatt számok jelölésének, amelyek nem megfelelően volt értelmezni. CSV-fájlba írásakor null értékeket helyettesítse be az "ERROR" karakterlánc alapértelmezés szerint.

Adjon hozzá paramétereket az írási részeként hívja, és null értéket jelöl, karakterláncot kell megadni.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

A fenti kód a kimenetet eredményez:
| | 1. oszlop | Column2 | Column3 | 4. oszlopig | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | NO | NO | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | NO | NO | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | NO | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | NO | NO |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | NO | NO | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Parquet fájl például

Hasonló `write_to_csv()`, a [ `write_to_parquet()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-parquet-file-path--typing-union--destinationpath--nonetype----none--directory-path--typing-union--destinationpath--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) függvény egy új adatfolyamot a parquet eszközökben egy lépést, amely hajtja végre, ha az adatfolyam futtatások írási adja vissza.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Futtassa az adatok az adatfolyam az írási művelet elindításához.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

A fenti kód a kimenetet eredményez:
|   | 1. oszlop | Column2 | Column3 | 4. oszlopig | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | NO | NO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | NO | NO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | NO| NO| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Az automatikus machine learning betanítási adatok konfigurálása

Az újonnan írt adatok fájlt, egy [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) objektum automatizált machine learning betanítási előkészítésekor. 

Az alábbi példakód bemutatja, hogyan az adatfolyam átalakítása Pandas dataframe, és ezt követően felosztása tanítási és tesztelési adatkészletek automatizált machine learning képzéshez.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Minden olyan köztes adat-előkészítési lépéseket, például az előző példában nincs szükség, ha az adatfolyamot közvetlenül átadható `AutoMLConfig`.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>További lépések
* Lásd: az SDK [áttekintése](https://aka.ms/data-prep-sdk) tervezési minták és használati példák 
* Tekintse meg a automatizált machine learning [oktatóanyag](tutorial-auto-train-models.md) regressziós modell példát