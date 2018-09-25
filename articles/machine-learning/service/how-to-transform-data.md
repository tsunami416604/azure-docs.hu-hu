---
title: Adatok átalakítása a segítségével az Azure Machine Learning Data Prep SDK – Python
description: Ismerje meg a átalakítása és az Azure Machine Learning Data Prep SDK adattisztítást. Átalakítás módszerekkel oszlopok hozzáadása, szűrje ki a nem kívánt sorokra vagy oszlopokra és imputálására a hiányzó értékeket.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 388957fc5dac5cdab5bee34a4431eaa524e76a48
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999902"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Adatok átalakítása a az Azure Machine Learning Data Prep SDK segítségével

A [az Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) megtisztíthatja az adatait különböző átalakítás módszert kínál. Ezek a metódusok révén egyszerűen oszlopok hozzáadása, szűrje ki a nem kívánt sorokra vagy oszlopokra és imputálására a hiányzó értékeket.

Jelenleg nincsenek módszer a következő feladatokhoz:
- [Az autorefresh tulajdonság oszlop hozzáadása](#add-column-using-expression)
- [Hiányzó imputálására](#impute-missing-values)
- [Oszlopok származtatása példa](#derive-column-by-example)
- [Szűrés](#filtering)
- [Egyéni Python-átalakítások](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Az autorefresh tulajdonság oszlop hozzáadása

Az Azure Machine Learning Data Prep SDK tartalmaz `substring` kifejezések alapján számítja ki egy létező oszlopok közötti értéket, és ezután helyezze ezt az értéket egy olyan új oszlop. Ebben a példában azt fogja betölteni az adatokat, majd próbálja meg oszlopok hozzáadása a bemeneti adatokat.

```
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID (Azonosító)|Eset száma|Dátum|Letiltás|IUCR|Elsődleges típusa|Leírás|Leírás helye|Letartóztatás|Hazai|...|Ward|Közösségi terület|Az FBI kód|X koordinátáját|Y koordinátája|Év|Frissítés dátuma|Szélesség|Hosszúság|Hely|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015. 11:50:00 PM|050XX N NEWLAND ELENTÉS MENTÉSE|0820|LOPÁS|500 USD MAJD A|UTCA.|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |DU. 12:42:46|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|07/05/2015. 11:30:00 ÓRAKOR|011XX W MORSE ELENTÉS MENTÉSE|0460|AKKUMULÁTOR|EGYSZERŰ|UTCA.|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 DU. 12:42:46|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|07/05/2015. 11:20:00 ÓRAKOR|121XX S ELŐTÉR ELENTÉS MENTÉSE|0486|AKKUMULÁTOR|EGYSZERŰ BELFÖLDI AKKUMULÁTOR|UTCA.|false|true|...|9|53|08B|||2015|07/12/2015 DU. 12:42:46|



Használja a `substring(start, length)` előtagot kinyerése a kis számú oszlopot, és az adatokat egy olyan új oszlop a kifejezésben: eset kategória.

```
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID (Azonosító)|Eset száma|Kis kategória|Dátum|Letiltás|IUCR|Elsődleges típusa|Leírás|Leírás helye|Letartóztatás|...|Ward|Közösségi terület|Az FBI kód|X koordinátáját|Y koordinátája|Év|Frissítés dátuma|Szélesség|Hosszúság|Hely|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015. 11:50:00 PM|050XX N NEWLAND ELENTÉS MENTÉSE|0820|LOPÁS|500 USD MAJD A|UTCA.|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |DU. 12:42:46|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|HY|07/05/2015. 11:30:00 ÓRAKOR|011XX W MORSE ELENTÉS MENTÉSE|0460|AKKUMULÁTOR|EGYSZERŰ|UTCA.|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 DU. 12:42:46|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|HY|07/05/2015. 11:20:00 ÓRAKOR|121XX S ELŐTÉR ELENTÉS MENTÉSE|0486|AKKUMULÁTOR|EGYSZERŰ BELFÖLDI AKKUMULÁTOR|UTCA.|false|true|...|9|53|08B|||2015|07/12/2015 DU. 12:42:46|



Használja a `substring(start)` csak annyi kinyerése a kis számú oszlopban, majd azt egy numerikus adattípusúra konvertálása és helyezni, egy olyan új oszlop kifejezése: eset azonosítója.
```
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
case_id.head(3)
```

||ID (Azonosító)|Eset száma|Eset azonosítója|Dátum|Letiltás|IUCR|Elsődleges típusa|Leírás|Leírás helye|Letartóztatás|...|Ward|Közösségi terület|Az FBI kód|X koordinátáját|Y koordinátája|Év|Frissítés dátuma|Szélesség|Hosszúság|Hely|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|329907.0|07/05/2015. 11:50:00 PM|050XX N NEWLAND ELENTÉS MENTÉSE|0820|LOPÁS|500 USD MAJD A|UTCA.|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |DU. 12:42:46|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|329265.0|07/05/2015. 11:30:00 ÓRAKOR|011XX W MORSE ELENTÉS MENTÉSE|0460|AKKUMULÁTOR|EGYSZERŰ|UTCA.|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 DU. 12:42:46|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|329253.0|07/05/2015. 11:20:00 ÓRAKOR|121XX S ELŐTÉR ELENTÉS MENTÉSE|0486|AKKUMULÁTOR|EGYSZERŰ BELFÖLDI AKKUMULÁTOR|UTCA.|false|true|...|9|53|08B|||2015|07/12/2015 DU. 12:42:46|

## <a name="impute-missing-values"></a>Hiányzó imputálására

Az Azure Machine Learning Data Prep SDK is imputálására megadott oszlopokat a hiányzó értékeket. Ebben a példában fogja betölteni a szélességi és hosszúsági értékeket, és ismételje meg a bemeneti adatokat a hiányzó értékeket imputálására.

```
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(5)
```

||ID (Azonosító)|Letartóztatás|Szélesség|Hosszúság|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

A harmadik rekord hiányzik a szélességi és hosszúsági értékeket. Ezeket a hiányzó értékeket imputálására, használhatja a `ImputeMissingValuesBuilder` további rögzített programot. Azt is imputálására az vagy egy számított oszlopokat `MIN`, `MAX`, vagy `MEAN` érték vagy egy `CUSTOM` értéket. Amikor `group_by_columns` van megadva, hiányzó imputálni csoport szerint kell `MIN`, `MAX`, és `MEAN` csoportonként számítja ki.

Először is gyorsan ellenőrizheti a `MEAN` szélességi oszlop értéke.
```
df_mean = df.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
df_mean = df_mean.filter(dprep.col('Arrest') == 'false')
df_mean.head(1)
```

||Letartóztatás|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

A `MEAN` földrajzi szélesség értékét minden rendben, így szélességi imputálására használhatja. A hiányzó hosszúság érték azt fogja imputálására a külső ismeretanyag alapján 42.


```
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
# call learn() to learn a fixed program to impute missing values
impute_builder.learn()
# call to_dataflow() to get a data flow with impute step added
df_imputed = impute_builder.to_dataflow()

# check impute result
df_imputed.head(5)
```

||ID (Azonosító)|Letartóztatás|Szélesség|Hosszúság|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

Ahogyan az a fenti eredményt, a hiányzó szélességi a volt imputált a `MEAN` értékét `Arrest=='false'` csoport. A hiányzó hosszúsági a 42 volt imputált.
```
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Oszlopok származtatása példa alapján
A speciális eszközök az Azure Machine Learning Data Prep SDK az egyik oszlopait a kívánt eredmények példáit származtassa lehetővé teszi. Ez lehetővé teszi, adjon meg egy példa az SDK-t, hogy azt generálhat kódot tervezett származtatását eléréséhez.

```
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
df = dataflow.head(10)
df
```
||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|AZ FM-15|22|50|10|
|1|1/1/2015 1:00|AZ FM-12|22|50|10|
|2|1/1/2015 1:54|AZ FM-15|22|50|10|
|3|1/1/2015 2:54|AZ FM-15|22|50|11|
|4|1/1/2015 3:54|AZ FM-15|24|46|13|
|5|1/1/2015 4:00|AZ FM-12|24|46|13|
|6|1/1/2015 4:54|AZ FM-15|22|52|15|
|7|1/1/2015 5:54|AZ FM-15|23|48|17|
|8|1/1/2015 6:54|AZ FM-15|23|50|14|
|9|1/1/2015 7:00|AZ FM-12|23|50|14|

Amint látható, ez a fájl viszonylag egyszerű. Azonban azt feltételezik, hogy szeretne-e a fájl egy adatkészlet formátumú dátumot és időt amelyeknél csatlakozzon "2018. március 10. |} 2 AM - 4 AM ".

Az adatok a formázásához szükséges alakíthatja át.

```
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview() 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|2015 január 1-12 AM - 2 AM|
|1|1/1/2015 1:00|2015 január 1-12 AM - 2 AM|
|2|1/1/2015 1:54|2015 január 1-12 AM - 2 AM|
|3|1/1/2015 2:54|2015 január 1-2 AM - 4 AM|
|4|1/1/2015 3:54|2015 január 1-2 AM - 4 AM|
|5|1/1/2015 4:00|2015 január 1-4 AM – 6: 00|
|6|1/1/2015 4:54|2015 január 1-4 AM – 6: 00|
|7|1/1/2015 5:54|2015 január 1-4 AM – 6: 00|
|8|1/1/2015 6:54|2015. január 1. 6: 00 - 8 -kor|
|9|1/1/2015 7:00|2015. január 1. 6: 00 - 8 -kor|

A fenti kód először létrehoz egy jelentéskészítő származtatott oszlop. Forrásoszlopok megfontolandó tömbjét megadott (`DATE`) és a egy a hozzáadandó új oszlop neve.

Az első példában látható a második sor (index: 1) az átadott és a egy várt értékkel rendelte a származtatott oszlop.

Végül nevű `builder.preview()` és a származtatott oszlop mellett a forrás oszlopban láthatja. A formátum finom úgy tűnik, de ugyanarra az időpontra "2015. január 1." csak látható értékeket.

Most adja át a kívánt sorok számát `skip` a felső sort megtekintéséhez kicsit lentebb.

```
preview_df = builder.preview(skip=30)
preview_df
```

||DATE|date_timerange|
|-----|-----|-----|
|30|11/1/2015 22:54|2015. január 1-én ESTE 10 – 12 óra|
|31|11/1/2015 23:54|2015. január 1-én ESTE 10 – 12 óra|
|32|11/1/2015 23:59|2015. január 1-én ESTE 10 – 12 óra|
|33|11/2/2015 0:54|2015. február 1-én 12 AM - 2 AM|
|34|11/2/2015 1:00|2015. február 1-én 12 AM - 2 AM|
|35|11/2/2015 1:54|2015. február 1-én 12 AM - 2 AM|
|36|11/2/2015 2:54|2015. február 1-2 AM - 4 AM|
|37|11/2/2015 3:54|2015. február 1-2 AM - 4 AM|
|38|11/2/2015 4:00|2015. február 1-4 AM – 6: 00|
|39|11/2/2015 4:54|2015. február 1-4 AM – 6: 00|

Itt láthatja a létrehozott program probléma: a származtatása program választott kizárólag a fent megadott egy példa alapján, elemezni a dátum szerint "Nap/hó/év", azaz nem választható ebben az esetben.

A probléma megoldásához, meg kell adnia egy másik példát.

```
builder.add_example(source_data=preview_df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
preview_df = builder.preview(skip=30, count=10)
preview_df
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|2015. január 1-én ESTE 10 – 12 óra|
|31|1/1/2015 23:54|2015. január 1-én ESTE 10 – 12 óra|
|32|1/1/2015 23:59|2015. január 1-én ESTE 10 – 12 óra|
|33|1/2/2015 0:54|Jan 2, 2015 12 AM - 2 AM|
|34|1/2/2015 1:00|Jan 2, 2015 12 AM - 2 AM|
|35|1/2/2015 1:54|Jan 2, 2015 12 AM - 2 AM|
|36|1/2/2015 2:54|2015. január 2 2 AM - 4 AM|
|37|1/2/2015 3:54|2015. január 2 2 AM - 4 AM|
|38|1/2/2015 4:00|2015. január 2 4 AM – 6: 00|
|39|1/2/2015 4:54|2015. január 2 4 AM – 6: 00|


Most, sorok megfelelően kezeli a "1/2/2015'"Jan 2, 2015", de ha, tekintse meg a származtatott oszlop további, meg is látja, hogy értékeket a végén semmit nem származtatott oszlop. Javításához, meg kell adnia egy másik példa 66 sorhoz.

```
builder.add_example(source_data=preview_df.iloc[66], example_value='Jan 29, 2015 8PM-10PM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|2015. január 1-én ESTE 10 – 12 óra|
|1|1/1/2015 23:54|2015. január 1-én ESTE 10 – 12 óra|
|2|1/1/2015 23:59|2015. január 1-én ESTE 10 – 12 óra|
|3|1/2/2015 0:54|Jan 2, 2015 12 AM - 2 AM|
|4|1/2/2015 1:00|Jan 2, 2015 12 AM - 2 AM|
|5|1/2/2015 1:54|Jan 2, 2015 12 AM - 2 AM|
|6|1/2/2015 2:54|2015. január 2 2 AM - 4 AM|
|7|1/2/2015 3:54|2015. január 2 2 AM - 4 AM|
|8|1/2/2015 4:00|2015. január 2 4 AM – 6: 00|
|9|1/2/2015 4:54|2015. január 2 4 AM – 6: 00|

Minden rendben, de azt láthatja, hogy éppen nem pontosan mit szerettünk volna. Kell külön dátumát és idejét ' |} "létrehozni a megfelelő formátumban.

Egy másik példa, amely javításához is hozzáadhat. Ezúttal az előzetes verzióról egy sort ad helyett értékét az oszlop nevét tartalmazó hozhatnak létre a `source_data` paraméter.

```
builder.add_example(source_data={'DATE': '11/11/2015 0:54'}, example_value='Nov 11, 2015 | 12AM-2AM')
builder.preview(count=10)
```
||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|None|
|1|1/1/2015 23:54|None|
|2|1/1/2015 23:59|None|
|3|1/2/2015 0:54|None|
|4|1/2/2015 1:00|None|
|5|1/2/2015 1:54|None|
|6|1/2/2015 2:54|None|
|7|1/2/2015 3:54|None|
|8|1/2/2015 4:00|None|
|9|1/2/2015 4:54|None|

Mivel most csak származtatott oszlopban szereplő értéket tartalmazó sorok felel meg pontosan a azt a megadott példák amelyekre egyértelműen kellett negatív hatást.

Nézzük meg a példák:
```
examples = builder.list_examples()
examples
```

| |DATE|Példa|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|2015 január 1-12 AM - 2 AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12 AM - 2 AM|-2|
|2|1/29/2015 20:54|2015. január 29-én du. 8 – 22: 00|-3|
|3|11/11/2015 0:54|2015. november 11-én \| 12 AM - 2 AM|-4|

Láthatja, hogy inkonzisztens példák biztosítunk. A probléma megoldásához, cserélje le az első három példák helyes méretprofilokat kell (beleértve a "|}" dátum és idő között).

Azt is érhet el, hogy hibás példák törlése (vagy ad `example_row` a pandas DataFrame vagy ad `example_id` érték) és a felvétel új módosított példák vissza.

```
builder.delete_example(example_id=-1)
builder.delete_example(example_row=examples.iloc[1])
builder.delete_example(example_row=examples.iloc[2])
builder.add_example(examples.iloc[0], 'Jan 1, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[1], 'Jan 2, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[2], 'Jan 29, 2015 | 8PM-10PM')
builder.preview()
```

| | DATE | date_timerange |
| -------- | -------- | -------- |
| 0 | 1/1/2015 0:54 | 2015. január 1-én \| 12 AM - 2 AM |
| 1 | 1/1/2015 1:00 | 2015. január 1-én \| 12 AM - 2 AM |
| 2 | 1/1/2015 1:54 | 2015. január 1-én \| 12 AM - 2 AM |
| 3 | 1/1/2015 2:54 | 2015. január 1-én \| 2 AM - 4 AM |
| 4 | 1/1/2015 3:54 | 2015. január 1-én \| 2 AM - 4 AM |
| 5 | 1/1/2015 4:00 | 2015. január 1-én \| 4 AM – 6: 00|
| 6 | 1/1/2015 4:54 | 2015. január 1-én \| 4 AM – 6: 00|
| 7 | 1/1/2015 5:54 | 2015. január 1-én \| 4 AM – 6: 00|
| 8 | 1/1/2015 6:54 | 2015. január 1-én \| 6: 00 - 8 -kor|
| 9 | 1/1/2015 7:00 | 2015. január 1-én \| 6: 00 - 8 -kor|

Most az adatok helyesnek tűnik, és végül meghívhatjuk `to_dataflow()` a szerkesztő a amely adja vissza egy adatfolyamot hozzáadni kívánt származtatott oszlopokat.

```
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
df
```

## <a name="filtering"></a>Szűrés

Az SDK-csomagja tartalmazza a módszerek `Dataflow.drop_columns` és `Dataflow.filter` lehetővé teszi, hogy szűrje ki a sorok vagy oszlopok.

### <a name="initial-setup"></a>Kezdeti telepítés
```
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|None|None|None|None|None|None|None|None|None|None|None|None|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|N|1|0|0|0|0|1|.00|0|0|21,25|
|2|2013-08-01-09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01-09:48:00|2013-08-01-09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01-10:38:35|2013-08-01-10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Oszlopok szűrése

Az oszlopok szűréséhez használja a `Dataflow.drop_columns`. Ez a módszer felvesz egy oszlop eldobása, vagy egy összetettebb argumentum nevű `ColumnSelector`.

#### <a name="filtering-columns-with-list-of-strings"></a>Oszlopok a karakterláncok listájának szűrése

Ebben a példában `drop_columns` karakterláncok listáját használja. Mindegyik sztring pontosan egyeznie kell az eldobni kívánt oszlop.

``` 
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|None|None|None|None|None|None|None|None|None|None|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|0|0|0|0|1|.00|0|0|21,25|
|2|2013-08-01-09:13:00|2013-08-01 11:38:00|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01-09:48:00|2013-08-01-09:49:00|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01-10:38:35|2013-08-01-10:38:51|0|0|0|0|1|.00|0|0|3.25|

#### <a name="filtering-columns-with-regex"></a>A regex oszlopok szűrése
Másik lehetőségként használhatja a `ColumnSelector` kifejezés oszlopokat, amelyek megfelelnek egy regex kifejezés. Ebben a példában azt dobja el az oszlopok, amelyek megfelelnek a kifejezésnek `Column*|.*longitude|.*latitude`.

```
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|None|None|None|None|None|None|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|1|.00|0|0|21,25|
|2|2013-08-01-09:13:00|2013-08-01 11:38:00|2|.00|0|0|75|
|3|2013-08-01-09:48:00|2013-08-01-09:49:00|1|.00|0|1|2.1|
|4|2013-08-01-10:38:35|2013-08-01-10:38:51|1|.00|0|0|3.25|

## <a name="filtering-rows"></a>Sorok szűrése

Sorok szűrése, használja `DataFlow.filter`. Ez a módszer egy Azure Machine Learning Data Prep SDK kifejezést argumentumként, és adja vissza egy új adatfolyamot a sorokat, amelyek a kifejezés értéke TRUE. Kifejezések használata kifejezés sikerei beépített (`col`, `f_not`, `f_and`, `f_or`) és a normál operátorok (>, <>, =, < =, ==,! =).

### <a name="filtering-rows-with-simple-expressions"></a>Az egyszerű kifejezések sorok szűrése

Kifejezésszerkesztő `col`, adja meg az oszlop nevét egy karakterlánc-argumentum `col('column_name')` és egyet az alábbi szabványos operátorok együtt >, <>, =, < =, ==,! =, hozhat létre például egy kifejezést `col('Tip_amount') > 0`. Végül adja át a beépített kifejezés be a `Dataflow.filter` függvény.

Ebben a példában `dataflow.filter(col('Tip_amount') > 0)` adja vissza egy új adatfolyamot a sorokat, amelyben értékét `Tip_amount` 0-nál nagyobb.

> [!NOTE] 
> `Tip_amount` numerikus, amely lehetővé teszi számunkra, hogy a többi numerikus értéket összehasonlítja egy kifejezés összeállítása először alakul.

```
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01-19:33:28|2013-08-01-19:35:21|5|.00|0,08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0,30|0|3.8|
|2|2013-08-05 14:11:42|2013-08-05 14:12:47|1|.00|1.05|0|4.55|
|3|2013-08-05 14:15:56|2013-08-05 14:18:04|5|.00|2.22|0|5.72|
|4|2013-08-05 14:42:14|2013-08-05 14:42:38|1|.00|0.88|0|4.38|

### <a name="filtering-rows-with-complex-expressions"></a>Az összetett kifejezések sorok szűrése

Összetett kifejezések szűrni, kombinálja a kifejezés sikerei legalább egy egyszerű kifejezést `f_not`, `f_and`, vagy `f_or`.

Ebben a példában `Dataflow.filter` egy új adatfolyamot a sorokat adja vissza, `'Passenger_count'` kevesebb mint 5 és `'Tolls_amount'` 0-nál nagyobb.

```
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|
|2|2013-08-12 19:48:12|2013-08-12 20:03:42|1.0|5.50|1,00|10.66|30.66|
|3|2013-08-13 06:11:06|2013-08-13 06:30:28|1.0|9.57|7.47|5.33|44.8|
|4|2013-08-16 20:33:50|2013-08-16 20:48:50|1.0|5.63|3.00|5.33|27.83|

Akkor is egynél több Kifejezésszerkesztő hozhat létre egy beágyazott kifejezés kombinálása sorok szűrése.

> [!NOTE]
> `lpep_pickup_datetime` és `Lpep_dropoff_datetime` dátum és idő, amely lehetővé teszi számunkra, hogy a többi datetime értéket összehasonlítja egy kifejezés összeállítása először konvertálja.

```
dataflow = dataflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dataflow = dataflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dataflow = dataflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06 + 00:00|2013-08-13 06:30:28 + 00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20 + 00:00|2013-08-23 12:50:28 + 00:00|2.0|8.22|8.08|5.33|40.41|
|2|2013-08-25 09:12:52 + 00:00|2013-08-25 09:34:34 + 00:00|1.0|8.80|8.33|5.33|41.66|
|3|2013-08-25 16:46:51 + 00:00|2013-08-25 17:13:55 + 00:00|2.0|9.66|7.37|5.33|44.20|
|4|2013-08-25 17:42:11 + 00:00|2013-08-25 18:02:57 + 00:00|1.0|9.60|6.87|5.33|41.20|

## <a name="custom-python-transforms"></a>Egyéni Python-átalakítások 

Lesz forgatókönyvek esetén a legegyszerűbb dolog, hogy tegye néhány Python-kód írása. Az SDK biztosít három bővítmény pontokat is használhat.

- Új parancsfájl oszlop
- Új parancsfájl-szűrő
- Partíció átalakítása

A bővítmények mindegyike a vertikális és horizontális felskálázás modul használata támogatott. A bővítmény pontok használata egyik legfőbb előnye az, hogy nem kell lekérni az összes adat adatkeretek létrehozásához. Az egyéni Python-kód csak fog futni, más átalakítást, ipari méretekben, például partíció, és általában párhuzamosan.

### <a name="initial-data-preparation"></a>Kezdeti adatok előkészítése

Első lépésként bizonyos adatok betöltése az Azure-Blobból.

```
import azureml.dataprep as dprep
col = dprep.col

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(5)
```
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale megye|10171002158| |
|1|ALABAMA|1|101710|Hale megye|10171002162| |
|2|ALABAMA|1|101710|Hale megye|10171002156| |
|3|ALABAMA|1|101710|Hale megye|10171000588|2|
|4|ALABAMA|1|101710|Hale megye|10171000589| |

Trim le az adatkészletet, és végezze el néhány alapszintű átalakításokat.

```
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(5)
```
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale megye|1.017100e + 10|None|
|1|ALABAMA|Hale megye|1.017100e + 10|None|
|2|ALABAMA|Hale megye|1.017100e + 10|None|
|3|ALABAMA|Hale megye|1.017100e + 10|2|
|4|ALABAMA|Hale megye|1.017100e + 10|None|

Keresse meg a szűrők használatával null értékeket. Először keresse meg az egyes, ezért most adja meg a hiányzó értékeket.

```
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale megye|1.017100e + 10|None|
|1|ALABAMA|Hale megye|1.017100e + 10|None|
|2|ALABAMA|Hale megye|1.017100e + 10|None|
|3|ALABAMA|Hale megye|1.017100e + 10|None|
|4|ALABAMA|Hale megye|1.017100e + 10|None|

### <a name="transform-partition"></a>Partíció átalakítása

Hasznos pandas függvény használatával az összes null értékeket cserélje le a 0. Ez a kód partíció található, nem az összes, az adatkészlet egyszerre futnak. Azt jelenti, hogy Nagy adatkészleteken, ez a kód párhuzamosan is futtathatók, a modul feldolgozza az adatokat, a partíció szerint partíció.

```
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
h = df.head(5)
h
```
||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale megye|1.017100e + 10|0.0|
|1|ALABAMA|Hale megye|1.017100e + 10|0.0|
|2|ALABAMA|Hale megye|1.017100e + 10|0.0|
|3|ALABAMA|Hale megye|1.017100e + 10|2.0|
|4|ALABAMA|Hale megye|1.017100e + 10|0.0|

### <a name="new-script-column"></a>Új parancsfájl oszlop

Használhatja a Python-kód, a megyét, és az állapot nevét tartalmazó új oszlop létrehozása, és így kihasználhatja az állapot nevét. Ehhez használja a `new_script_column()` az adatfolyam metódust.

```
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
h = df.head(5)
h
```
||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale megye|Hale megyét, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|Hale megye|Hale megyét, Alabama|1.017100e + 10|0.0|
|2|ALABAMA|Hale megye|Hale megyét, Alabama|1.017100e + 10|0.0|
|3|ALABAMA|Hale megye|Hale megyét, Alabama|1.017100e + 10|2.0|
|4|ALABAMA|Hale megye|Hale megyét, Alabama|1.017100e + 10|0.0|
### <a name="new-script-filter"></a>Új parancsfájl-szűrő

Most, hozhat létre egy Python-kifejezést a adatkészletet, csak ha "Hale" nem szerepel az új sorok szűrése `county_state` oszlop. A kifejezés eredménye `True` szeretnénk a sort, ha és `False` eldobni a sor.

```
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
h = df.head(5)
h
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson megye|Jefferson megyét, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|Jefferson megye|Jefferson megyét, Alabama|1.019200e + 10|0.0|
|2|ALABAMA|Jefferson megye|Jefferson megyét, Alabama|1.019200e + 10|0.0|
|3|ALABAMA|Jefferson megye|Jefferson megyét, Alabama|1.019200e + 10|0.0|
|4|ALABAMA|Jefferson megye|Jefferson megyét, Alabama|1.019200e + 10|0.0|
