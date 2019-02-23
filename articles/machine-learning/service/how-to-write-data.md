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
ms.openlocfilehash: 1853ff4141a7af3260ef9575bb2457819ab2d4a9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735016"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Az Azure Machine Learning Data Prep SDK használata az adatok írása

Ebből a cikkből megtudhatja, különböző módszerekkel használata az adatok írása a [Azure Machine Learning Data Prep Python SDK](https://aka.ms/data-prep-sdk). Kimeneti adatok csak írható egy adatfolyam bármely pontján, és írási műveletek hozzá szeretné adni az eredményül kapott adatokat a folyamat lépéseit, és minden alkalommal, amikor az adatok folyamat futtatása. Adatok írása párhuzamos írások engedélyezéséhez több partíció-fájlt.

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

## <a name="next-steps"></a>További lépések
* Lásd: az SDK [áttekintése](https://aka.ms/data-prep-sdk) tervezési minták és használati példák 
* Tekintse meg a automatizált machine learning [oktatóanyag](tutorial-auto-train-models.md) regressziós modell példát