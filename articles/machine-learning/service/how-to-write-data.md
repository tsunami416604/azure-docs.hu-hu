---
title: Az Azure Machine Learning Data Prep SDK - Python az adatok írása
description: Ismerje meg az adatok Azure Machine Learning Data Prep SDK-val. Írhat egy adatfolyamot, és fájlokat a (helyi rendszer, az Azure Blob Storage és Azure Data Lake Storage) a támogatott helyek bármelyikét bármikor adatai.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 81344d388fbba0db034b8adb06adab6797ec2ce1
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166744"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Az Azure Machine Learning Data Prep SDK használata az adatok írása
Adatok folyamat bármely mozzanata adatai írhat. Ezek az írások hozzá szeretné adni az eredményül kapott adatokat a folyamat lépéseit, és minden alkalommal, amikor az adatok folyamat futnak. Adatok írása párhuzamos írások engedélyezéséhez több partíció-fájlt.

Mivel ebben az esetben nincs korlátozás hány írása lépéseket egy folyamatban vannak, könnyen adhat hozzá további írási lépéseket a köztes eredményeket hibaelhárítási vagy egyéb folyamatokhoz. 

Minden alkalommal, amikor egy írási lépés futtatásakor az adatok az adatfolyam egy teljes lekéréses történik. Például írási három lépésben az adatfolyam olvasni és feldolgozni az adatkészletben lévő mindegyik rekordnak három alkalommal.

## <a name="supported-data-types-and-location"></a>Támogatott adattípusok és helye

A következő fájlformátumokat támogatottak.
-   Tagolt fájlokat (CSV, TSV, stb.)
-   Parquet-fájlokat

Használatával a [Azure Machine Learning Data Prep python SDK](https://aka.ms/data-prep-sdk), írhat adatokat:
+ a helyi fájlrendszerben
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>A Spark szempontok
Az adatfolyam Spark való futtatáskor kell írni egy üres mappát. Megpróbálja futtatni az írási hiba egy létező mappa eredményez. Győződjön meg arról, hogy a célmappában üres, vagy használjon másik célhelyet az egyes futtatások, vagy az írás sikertelen lesz.

## <a name="monitoring-write-operations"></a>Írási műveletek figyelése
Az Ön kényelme érdekében sikeres nevű sentinel fájl jön létre egy írási befejeződése után. A jelenléte segítségével azonosíthatja a egy köztes írási nem kell megvárnia a teljes folyamat befejezéséhez befejezése után.

## <a name="example-write-code"></a>Írási kódpéldákat

Ebben a példában először az adatfolyam adatok betöltését. A Microsoft ezeket az adatokat különböző formátumokban használja fel újra.

```python
import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

Példa a kimenetre:
|   |  1. oszlop |    Column2 | Column3 | 4. oszlopig  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   None|       NEM|     NEM  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   None|       NEM|     NEM  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    None|   NEM| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    None|   NEM| NEM| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    None|   NEM| NEM| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    None|   NEM| NEM| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   None|   NEM| NEM|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    None|   NEM| NEM| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    None|   NEM| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    None|   NEM| SV|     |77000.0|   15500.0|    120.0|

### <a name="delimited-file-example"></a>Tagolt fájl például

Ebben a szakaszban látható egy példa a a `write_to_csv` függvény tagolt fájl írása.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Példa a kimenetre:
|   |  1. oszlop |    Column2 | Column3 | 4. oszlopig  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   HIBA |       NEM|     NEM  |   ENRS    |HIBA    |   HIBA |   HIBA|    
|   1|      10003.0 |   99999.0 |   HIBA |       NEM|     NEM  |   ENSO|       HIBA|        HIBA |HIBA|   
|   2|  10010.0|    99999.0|    HIBA |   NEM| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    HIBA |   NEM| NEM| |   HIBA|    HIBA|    HIBA|
|4| 10014.0|    99999.0|    HIBA |   NEM| NEM| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    HIBA |   NEM| NEM| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   HIBA |   NEM| NEM|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    HIBA |   NEM| NEM| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    HIBA |   NEM| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    HIBA |   NEM| SV|     |77000.0|   15500.0|    120.0|

Több hibák jelennek meg a numerikus oszlopok miatt nem megfelelően volt értelmezni, számokat, a fenti kimenetben láthatja. CSV-fájlba írásakor ezeket null értékeket helyettesítse be az "ERROR" karakterlánc alapértelmezés szerint. 

Hozzáadhat az írási részeként paraméterek hívja, és null értéket jelöl, karakterláncot kell megadni. Példa:

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

A fenti kód a kimenetet eredményez:
|   |  1. oszlop |    Column2 | Column3 | 4. oszlopig  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       NEM|     NEM  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NEM|     NEM  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NEM| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NEM| NEM| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NEM| NEM| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   NEM| NEM| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   NEM| NEM|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NEM| NEM| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NEM| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   NEM| SV|     |77000.0|   15500.0|    120.0|


### <a name="parquet-file-example"></a>Parquet fájl például

Hasonló `write_to_csv`, a `write_to_parquet` függvény egy új adatfolyamot a parquet eszközökben egy lépést, amely hajtja végre, ha az adatfolyam futtatások írási adja vissza.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Ezután futtathatja is az adatok az adatfolyam az írási művelet elindításához.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

A fenti kód a kimenetet eredményez:
|   |  1. oszlop |    Column2 | Column3 | 4. oszlopig  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NEM|     NEM  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NEM|     NEM  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NEM| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NEM| NEM| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NEM| NEM| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   NEM| NEM| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   NEM| NEM|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NEM| NEM| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NEM| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   NEM| SV|     |77000.0|   15500.0|    120.0|
