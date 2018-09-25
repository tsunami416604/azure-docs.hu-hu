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
ms.openlocfilehash: da5823473b7f69fa0a6c65d5ea7319bfd2e92720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946765"
---
# <a name="write-data-with-the-azure-machine-learning-data-prep-sdk"></a>Az Azure Machine Learning Data Prep SDK az adatok írása
Adatok folyamat bármely mozzanata adatai írhat. Ezek az írások hozzá szeretné adni az eredményül kapott adatokat a folyamat lépéseit, és minden alkalommal, amikor az adatok folyamat futnak. Mivel ebben az esetben nincs korlátozás hány írása lépések nincsenek folyamatban lévő, könnyebbé vált a hibaelhárítási köztes eredményeket kiírja, vagy más folyamatokban kell észlelnie. Fontos megjegyezni, hogy a Futtatás minden egyes írási lépést eredményez-e az adatok az adatfolyam egy teljes lekéréses. Például írási három lépésben az adatfolyam olvasni és feldolgozni az adatkészletben lévő mindegyik rekordnak három alkalommal.

## <a name="writing-to-files"></a>A fájlok írása
Az a [az Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py), tudjon adatokat írni a (helyi rendszer, az Azure Blob Storage és Azure Data Lake Storage) a támogatott helyek bármelyikét fájlokat. Adatok írása párhuzamos írások engedélyezéséhez több partíció-fájlt. Ha egy írási befejeződött, egy sikeres sentinel-fájlt is jön létre. Ennek segítségével azonosíthatja, amikor egy köztes írási befejeződött, nem kell megvárnia a teljes folyamat befejezéséhez.

Az adatfolyam Spark való futtatáskor kell írni egy üres mappát. annak a létező mappának az írási futtatására tett kísérlet sikertelen lesz. Győződjön meg arról, hogy a célmappában üres, vagy használjon másik célhelyet az egyes futtatások, vagy az írás sikertelen lesz.

Az Azure Machine Learning Data Prep SDK támogatja a következő fájlformátumokat:
-   Tagolt fájlokat (CSV, TSV, stb.)
-   Parquet-fájlokat

Ebben a példában először az adatfolyam adatok betöltését. A Microsoft ezeket az adatokat különböző formátumokban használja fel újra.

```

import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

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

## <a name="delimited-files"></a>Tagolt fájlok
Az alábbi sor egy új adatfolyamot hoz létre egy írási lépést, de a tényleges írási még nem történt. Ha az adatfolyam fut, az írási végrehajtja.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))
```
Most futtassa a adatok folyamatot, amely futtatja az írási művelet.
```
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
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

Az írt adatok a numerikus oszlopok több hibák miatt nem megfelelően volt értelmezni számok tartalmazza. CSV-fájlba írásakor ezeket null értékeket helyettesítse be az "ERROR" karakterlánc alapértelmezés szerint. Hozzáadhat az írási részeként paraméterek hívja, és null értéket jelöl, karakterláncot kell megadni.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
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
## <a name="parquet-files"></a>Parquet-fájlokat

 Hasonlóan a `write_to_csv` függvényt a fenti `write_to_parquet` egy új adatfolyamot a parquet eszközökben egy lépést, amely lesz végrehajtva, amikor az adatok folyamatfuttatási jogosultságot írási adja vissza.

```
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```
 Mi most futtassa az adatfolyam, amely végrehajtja az írási művelet.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```
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
