---
title: 'Írás: adat-előkészítési Python SDK'
titleSuffix: Azure Machine Learning service
description: Ismerje meg az adatok Azure Machine Learning Data Prep SDK-val. Írhat egy adatfolyamot, és fájlokat a (helyi rendszer, az Azure Blob Storage és Azure Data Lake Storage) a támogatott helyek bármelyikét bármikor adatai.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6753be5613b10b64936cddaafbb9859aad837b02
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358637"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>Az adat írása és konfigurálása a Azure Machine Learning adatelőkészítési SDK-val

Ebből a cikkből megtudhatja, hogyan írhat különböző módszereket az adatíráshoz a [Azure Machine learning adatelőkészítési PYTHON SDK](https://aka.ms/data-prep-sdk) -val, és hogyan konfigurálhatja a kísérletezéshez szükséges információkat a Pythonhoz készült [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-val.  A kimeneti adatokat a adatfolyam bármely pontján lehet írni. Az írásokat a rendszer az eredményül kapott adatfolyamként adja hozzá, és ezeket a lépéseket minden alkalommal futtatja, amikor az adatfolyam fut. Adatok írása párhuzamos írások engedélyezéséhez több partíció-fájlt.

> [!Important]
> Ha új megoldást hoz létre, próbálja meg a [Azure Machine learning](how-to-explore-prepare-data.md) adatkészleteket (előzetes verzió) az adatok, a pillanatkép-adatok átalakításához és a verziószámmal ellátott adatkészlet-definíciók tárolásához. Az adatkészletek az adat-előkészítő SDK következő verziója, amely kibővített funkciókat kínál az adatkészletek AI-megoldásokban való kezeléséhez.
> Ha a `azureml-dataprep` csomag használatával hoz létre adatfolyam az átalakításokkal, ahelyett, hogy a csomagot egy `azureml-datasets` adatkészlet létrehozásához használja, nem fogja tudni használni a pillanatképeket vagy a verziószámú adatkészleteket később.

Mivel ebben az esetben nincs korlátozás hány írása lépéseket egy folyamatban vannak, könnyen adhat hozzá további írási lépéseket a köztes eredményeket hibaelhárítási vagy egyéb folyamatokhoz.

Minden alkalommal, amikor egy írási lépés futtatásakor az adatok az adatfolyam egy teljes lekéréses történik. Például írási három lépésben az adatfolyam olvasása és feldolgozása az adatkészlet minden rekord három alkalommal.

## <a name="supported-data-types-and-location"></a>Támogatott adattípusok és helye

A következő fájlformátumokat támogatottak.
-   Tagolt fájlokat (CSV, TSV, stb.)
-   Parquet-fájlokat

A Azure Machine Learning adat-előkészítő Python SDK használatával az alábbiakra írhat adatbevitelt:
+ a helyi fájlrendszerben
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>A Spark szempontok

Az adatfolyam Spark való futtatáskor kell írni egy üres mappát. Megpróbálja futtatni az írási hiba egy létező mappa eredményez. Győződjön meg arról, hogy a célmappában üres, vagy használjon másik célhelyet az egyes futtatások, vagy az írás sikertelen lesz.

## <a name="monitoring-write-operations"></a>Írási műveletek figyelése

Az Ön kényelme érdekében sikeres nevű sentinel fájl jön létre egy írási befejeződése után. A jelenléte segítségével azonosíthatja a egy köztes írási nem kell megvárnia a teljes folyamat befejezéséhez befejezése után.

## <a name="example-write-code"></a>Írási kódpéldákat

Ebben a példában először be kell töltenie az adatfolyamatot a `auto_read_file()`használatával. Ezeket az adatokat különböző formátumokban újból felhasználja.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Példa a kimenetre:

| | 1\. oszlop | Column2 | Column3 | 4\. oszlopig | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | None | NO | NO | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | None | NO | NO | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | None | NO | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | None | NO | NO |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | None | NO | NO | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>Tagolt fájl például

A következő kód a [`write_to_csv()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-csv-directory-path--datadestination--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) függvényt használja az adat tagolt fájlba írásához.

```python
# Create a new data flow using `write_to_csv`
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Példa a kimenetre:

| | 1\. oszlop | Column2 | Column3 | 4\. oszlopig | Column5 | Column6 | Column7 | Column8 | Column9 |
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

| | 1\. oszlop | Column2 | Column3 | 4\. oszlopig | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | NO | NO | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | NO | NO | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | NO | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | NO | NO |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | NO | NO | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Parquet fájl például

A verzióhoz hasonlóan [`write_to_parquet()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-parquet-file-path--typing-union--datadestination--nonetype----none--directory-path--typing-union--datadestination--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) a függvény egy új adatfolyamot ad vissza, amely egy írási parketta-lépés, amely az adatfolyam futásakor lesz végrehajtva. `write_to_csv()`

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

|   | 1\. oszlop | Column2 | Column3 | 4\. oszlopig | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | NO | NO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | NO | NO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | NO| NO| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Az automatizált gépi tanulással kapcsolatos képzések konfigurálása

Adja át az újonnan írt adatfájlját [`AutoMLConfig`](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) egy objektumba az automatikus gépi tanulási képzés előkészítéséhez. 

A következő mintakód bemutatja, hogyan alakíthatja át a adatfolyam egy Panda dataframe, majd kioszthatja azokat képzési és tesztelési adatkészletekben az automatizált gépi tanulás betanításához.

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

Ha nem igényel semmilyen köztes adatelőkészítési lépést, például az előző példában, közvetlenül `AutoMLConfig`a adatfolyam adhatja át.

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
* Tekintse meg [](https://aka.ms/data-prep-sdk) az SDK áttekintését tervezési mintákhoz és használati példákhoz 
* A regressziós modellre [](tutorial-auto-train-models.md) vonatkozó automatizált gépi tanulásról szóló oktatóanyagban talál példát
