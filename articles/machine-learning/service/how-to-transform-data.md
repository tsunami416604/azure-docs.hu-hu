---
title: 'Transforms: data prep Python SDK'
titleSuffix: Azure Machine Learning service
description: Ismerje meg a átalakítása és az Azure Machine Learning Data Prep SDK adattisztítást. Átalakítás módszerekkel oszlopok hozzáadása, szűrje ki a nem kívánt sorokra vagy oszlopokra és imputálására a hiányzó értékeket.
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
ms.openlocfilehash: c2f11b08f5d8e9bb3be7acfa8ba62100bdc55d99
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889800"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Adatok átalakítása a az Azure Machine Learning Data Prep SDK segítségével

Ebben a cikkben megismerheti az Azure Machine Learning Data Prep SDK használata az adatok különböző módszert. Az SDK-t kínál, amelyek megkönnyítik az egyszerű oszlopok hozzáadása, szűrje ki a nem kívánt sorokra vagy oszlopokra és imputálására hiányzó funkciók. Az SDK dokumentációja, olvassa el a [áttekintése](https://aka.ms/data-prep-sdk).

Ebben az útmutatóban példát a következő feladatokhoz:

- Az autorefresh tulajdonság oszlop hozzáadása
- [Hiányzó imputálására](#impute-missing-values)
- [Oszlopok származtatása példa](#derive-column-by-example)
- [Szűrés](#filtering)
- [Egyéni Python-átalakítások](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Az autorefresh tulajdonság oszlop hozzáadása

Az Azure Machine Learning Data Prep SDK tartalmaz `substring` kifejezések alapján számítja ki egy létező oszlopok közötti értéket, és ezután helyezze ezt az értéket egy olyan új oszlop. Ebben a példában betölteni az adatokat, és oszlopok hozzáadása a bemeneti adatokat próbál.

```python
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


Használja a `substring(start, length)` előtagot kinyerése a kis számú oszlopot, és a karakterlánc egy új oszlop kifejezése `Case Category`. Átadja a `substring_expression` változót a `expression` paraméter hoz létre egy új számított oszlopot, amely végrehajtja a kifejezés minden egyes rekordján.

```python
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



Használja a `substring(start)` kifejezés csak annyi kinyerése a kis számú oszlopot, és a egy olyan új oszlop létrehozása. Konvertálása a numerikus adatokat típus használatával a `to_number()` függvényt, és adja át a karakterlánc oszlopnév paraméterként.

```python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Hiányzó imputálására

Az SDK-t is imputálására megadott oszlopokat a hiányzó értékeket. Ebben a példában betölteni a szélességi és hosszúsági értékeket, és ismételje meg a bemeneti adatokat a hiányzó értékeket imputálására.

```python
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(3)
```

||ID (Azonosító)|Letartóztatás|Szélesség|Hosszúság|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|

A harmadik rekord hiányzik a szélességi és hosszúsági értékeket. Ezeket a hiányzó értékeket imputálására, használhatja [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) további rögzített kifejezés. Azt is imputálására az vagy egy számított oszlopokat `MIN`, `MAX`, `MEAN` érték, vagy egy `CUSTOM` értéket. Amikor `group_by_columns` van megadva, hiányzó imputálni csoport szerint kell `MIN`, `MAX`, és `MEAN` csoportonként számítja ki.

Ellenőrizze a `MEAN` használatával szélességi oszlop értékét a [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) függvény. Ez a függvény elfogad egy tömb, az oszlopok a `group_by_columns` paraméterrel adja meg az összesítési szinten. A `summary_columns` paramétert fogad el egy `SummaryColumnsValue` hívja. A függvény hívásához szükséges, adja meg az aktuális oszlop nevét, az új számított mező neve és a `SummaryFunction` végrehajtásához.

```python
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

A `MEAN` földrajzi szélesség értéke pontosságú úgy tűnik, használja a `ImputeColumnArguments` függvény imputálására azt. Ez a függvény elfogad egy `column_id` karakterláncot, és a egy `ReplaceValueFunction` határozza meg a impute típusát. A hiányzó hosszúság érték imputálására a 42 külső ismeretanyag alapján.

Imputálására lépéseket összefűzhetők be egy `ImputeMissingValuesBuilder` objektumot, a jelentéskészítő funkció segítségével `impute_missing_values()`. A `impute_columns` paraméterben tömbjét `ImputeColumnArguments` objektumokat. Hívja a `learn()` impute lépéseket tárolására függvényt, és a egy adatfolyam objektum segítségével alkalmazhatja `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])

impute_builder.learn()
df_imputed = impute_builder.to_dataflow()
df_imputed.head(3)
```

||ID (Azonosító)|Letartóztatás|Szélesség|Hosszúság|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|

Ahogyan az a fenti eredményt, a hiányzó szélességi a volt imputált a `MEAN` értékét `Arrest=='false'` csoport. A hiányzó hosszúsági a 42 volt imputált.

```python
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Oszlopok származtatása példa alapján

A speciális eszközök az Azure Machine Learning Data Prep SDK az egyik oszlopait a kívánt eredmények példáit származtassa lehetővé teszi. Ez lehetővé teszi, adjon meg egy példa az SDK-t, hogy azt generálhat kódot a kívánt átalakítási eléréséhez.

```python
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dataflow.head(4)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|AZ FM-15|22|50|10|
|1|1/1/2015 1:00|AZ FM-12|22|50|10|
|2|1/1/2015 1:54|AZ FM-15|22|50|10|
|3|1/1/2015 2:54|AZ FM-15|22|50|11|

Tegyük fel, hogy szeretne-e a fájl egy adatkészlet formátumú dátumot és időt amelyeknél csatlakozzon "2018. március 10. |} 2 AM - 4 AM ".

```python
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5) 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|2015 január 1-12 AM - 2 AM|
|1|1/1/2015 1:00|2015 január 1-12 AM - 2 AM|
|2|1/1/2015 1:54|2015 január 1-12 AM - 2 AM|
|3|1/1/2015 2:54|2015 január 1-2 AM - 4 AM|
|4|1/1/2015 3:54|2015 január 1-2 AM - 4 AM|

A fenti kód először létrehoz egy jelentéskészítő származtatott oszlop. Forrásoszlopok megfontolandó tömbjét adnia (`DATE`), és fel kell venni az új oszlop nevét. Az első példában látható adja át a második sor (index: 1), és a származtatott oszlopot meg kell adni egy várt értékkel.

Végezetül hívja `builder.preview(skip=30, count=5)` és a származtatott oszlop mellett a forrás oszlopban láthatja. A formátumot a megfelelő úgy tűnik, de csak értékeket látható ugyanazon dátumra "2015. január 1.".

Most adja át a kívánt sorok számát `skip` a felső sort megtekintéséhez kicsit lentebb.

> [!NOTE]
> A preview() függvény kihagyja a sorokat, de nem szám újra a kimeneti index. Az alábbi példában az a tábla indexének 0 30 index az adatfolyamban felel meg.

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|2015. január 1-én ESTE 10 – 12 óra|
|1|1/1/2015 23:54|2015. január 1-én ESTE 10 – 12 óra|
|2|1/1/2015 23:59|2015. január 1-én ESTE 10 – 12 óra|
|3|1/2/2015 0:54|2015. február 1-én 12 AM - 2 AM|
|4|1/2/2015 1:00|2015. február 1-én 12 AM - 2 AM|

Itt láthatja a létrehozott program problémáját. A származtatása program elemezni a dátum szerint "Nap/hó/év", azaz nem választható ebben az esetben kizárólag a fent megadott egy példa alapján, választotta. A probléma megoldásához, egy adott rekord index célként, és adjon meg egy másik példa használatával a `add_example()` működnek a `builder` változó.

```python
builder.add_example(source_data=df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|2015. január 1-én ESTE 10 – 12 óra|
|1|1/1/2015 23:54|2015. január 1-én ESTE 10 – 12 óra|
|2|1/1/2015 23:59|2015. január 1-én ESTE 10 – 12 óra|
|3|1/2/2015 0:54|Jan 2, 2015 12 AM - 2 AM|
|4|1/2/2015 1:00|Jan 2, 2015 12 AM - 2 AM|

Sorok megfelelően kezeli a most "1/2/2015',"2015 január 2"de ha túl indexazonosító származtatott oszlop 76, azt látja, hogy értékeket a végén semmit nem származtatott oszlop.

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|2015 január 3-6: 00 - 8 -kor|
|1|1/3/2015 7:54|2015 január 3-6: 00 - 8 -kor|
|2|1/29/2015 6:54|None|
|3|1/29/2015 7:00|None|
|4|1/29/2015 7:54|None|

```python
builder.add_example(source_data=df.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```
||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|2015 január 3-6: 00 - 8 -kor|
|1|1/3/2015 7:54|2015 január 3-6: 00 - 8 -kor|
|2|1/29/2015 6:54|2015. január 29-én 6: 00 - 8 -kor|
|3|1/29/2015 7:00|2015. január 29-én 6: 00 - 8 -kor|
|4|1/29/2015 7:54|2015. január 29-én 6: 00 - 8 -kor|

 A hívás aktuális példa származtatási listájának megtekintéséhez `list_examples()` builder objektumon.

```python
examples = builder.list_examples()
```

| |DATE|Példa|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|2015 január 1-12 AM - 2 AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12 AM - 2 AM|-2|
|2|1/29/2015 20:54|2015. január 29-én du. 8 – 22: 00|-3|


Bizonyos esetekben ha törli a hibás, példák formájában adható át vagy `example_row` származó a pandas DataFrame, vagy `example_id` értéket. Például, ha futtatja `builder.delete_example(example_id=-1)`, az első átalakítási példa törli.


Hívás `to_dataflow()` a szerkesztő a ad vissza egy adatfolyamot hozzáadni kívánt származtatott oszlopokat.

```python
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Szűrés

Az SDK-csomagja tartalmazza a módszerek [ `Dataflow.drop_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) és [ `Dataflow.filter()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py) lehetővé teszi, hogy szűrje ki a sorok vagy oszlopok.

### <a name="initial-setup"></a>Kezdeti beállítás

```python
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|None|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|N|1|0|0|0|0|1|.00|0|0|21,25|
|2|2013-08-01-09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01-09:48:00|2013-08-01-09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01-10:38:35|2013-08-01-10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Oszlopok szűrése

Az oszlopok szűréséhez használja a `Dataflow.drop_columns()`. Ez a módszer felvesz egy oszlop eldobása, vagy egy összetettebb argumentum nevű [ `ColumnSelector` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py).

#### <a name="filtering-columns-with-list-of-strings"></a>Oszlopok a karakterláncok listájának szűrése

Ebben a példában `drop_columns` karakterláncok listáját használja. Mindegyik sztring pontosan egyeznie kell az eldobni kívánt oszlop.

```python
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|None|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|0|0|0|0|1|.00|0|0|21,25|

#### <a name="filtering-columns-with-regex"></a>A regex oszlopok szűrése

Másik megoldásként használhatja a `ColumnSelector` kifejezés oszlopokat, amelyek megfelelnek egy regex kifejezés. Ebben a példában az oszlopok, amelyek megfelelnek a kifejezésnek drop `Column*|.*longitude|.*latitude`.

```python
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|None|
|1|2013-08-01-08:14:37|2013-08-01-09:09:06|1|.00|0|0|21,25|

## <a name="filtering-rows"></a>Sorok szűrése

Sorok szűrése, használja `DataFlow.filter()`. Ez a módszer egy Azure Machine Learning Data Prep SDK kifejezést argumentumként, és adja vissza egy új adatfolyamot a sorokat, amelyek a kifejezés értéke TRUE. Kifejezések használata kifejezés sikerei beépített (`col`, `f_not`, `f_and`, `f_or`) és a normál operátorok (>, <>, =, < =, ==,! =).

### <a name="filtering-rows-with-simple-expressions"></a>Az egyszerű kifejezések sorok szűrése

Kifejezésszerkesztő `col`, adja meg az oszlop nevét egy karakterlánc-argumentum `col('column_name')`. Ez a kifejezés használata az egyik az alábbi szabványos operátorok >, <>, =, < =, ==,! = hozhat létre például egy kifejezést `col('Tip_amount') > 0`. Végül adja át a beépített kifejezés be a `Dataflow.filter` függvény.

Ebben a példában `dataflow.filter(col('Tip_amount') > 0)` adja vissza egy új adatfolyamot a sorokat, amelyben értékét `Tip_amount` 0-nál nagyobb.

> [!NOTE] 
> `Tip_amount` numerikus, amely lehetővé teszi számunkra, hogy a többi numerikus értéket összehasonlítja egy kifejezés összeállítása először alakul.

```python
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01-19:33:28|2013-08-01-19:35:21|5|.00|0,08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0,30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Az összetett kifejezések sorok szűrése

Összetett kifejezések szűrni, kombinálja a kifejezés sikerei legalább egy egyszerű kifejezést `f_not`, `f_and`, vagy `f_or`.

Ebben a példában `Dataflow.filter()` egy új adatfolyamot a sorokat adja vissza, `'Passenger_count'` kevesebb mint 5 és `'Tolls_amount'` 0-nál nagyobb.

```python
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|

Akkor is egynél több Kifejezésszerkesztő hozhat létre egy beágyazott kifejezés kombinálása sorok szűrése.

> [!NOTE]
> `lpep_pickup_datetime` és `Lpep_dropoff_datetime` dátum és idő, amely lehetővé teszi számunkra, hogy a többi datetime értéket összehasonlítja egy kifejezés összeállítása először konvertálja.

```python
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
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06 + 00:00|2013-08-13 06:30:28 + 00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20 + 00:00|2013-08-23 12:50:28 + 00:00|2.0|8.22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>Egyéni Python-átalakítások

Mindig lesz forgatókönyvek végzett átalakítás a legegyszerűbb lehetőség a saját parancsfájl van írásához. Az SDK-t is használhat egyéni Python-szkriptek három bővítmény pontokat biztosít.

- Új parancsfájl oszlop
- Új parancsfájl-szűrő
- Partíció átalakítása

A bővítmények mindegyike a vertikális és horizontális felskálázás modul használata támogatott. A bővítmény pontok használata egyik legfőbb előnye az, hogy nem kell lekérni az összes adat adatkeretek létrehozásához. Az egyéni Python-kód csak fog futni, más átalakítást, ipari méretekben, például partíció, és általában párhuzamosan.

### <a name="initial-data-preparation"></a>Kezdeti adatok előkészítése

Első lépésként bizonyos adatok betöltése az Azure-Blobból.

```python
import azureml.dataprep as dprep
col = dprep.col

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale megye|10171002158| |
|1|ALABAMA|1|101710|Hale megye|10171002162| |

Trim le az adatkészlet, és végezze el néhány alapszintű átalakítások, például oszlopok eltávolítása, értékeket cserélje le, és típusok alakítása.

```python
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale megye|1.017100e + 10|None|
|1|ALABAMA|Hale megye|1.017100e + 10|None|

Keresse meg a következő szűrő használatával null értékeket.

```python
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale megye|1.017100e + 10|None|
|1|ALABAMA|Hale megye|1.017100e + 10|None|

### <a name="transform-partition"></a>Partíció átalakítása

Használat [ `transform_partition()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) cserélje le az összes null értéket 0-val. Ez a kód partíciónként, nem pedig a teljes adatkészlet egyszerre futni fog. Ez azt jelenti, hogy egy nagyméretű adathalmazon, ez a kód párhuzamosan is futtathatók, a modul feldolgozza az adatokat, a partíció szerint partíció.

A Python-szkriptet meg kell határoznia a hívott függvény `transform()` , amely két argumentumot, `df` és `index`. A `df` argumentum lesz, amely tartalmazza az adatokat a partíció pandas dataframe és a `index` argumentum értéke egy egyedi azonosítót a partíció. Az átalakító függvény a teljes körűen szerkeszthetik az átadott adathalmaz, de dataframe kell visszaadnia. Importálja a Python-szkriptet könyvtárra sem a környezetben, ahol az adatfolyamot fut léteznie kell.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale megye|1.017100e + 10|0.0|
|1|ALABAMA|Hale megye|1.017100e + 10|0.0|

### <a name="new-script-column"></a>Új parancsfájl oszlop

Egy Python-szkriptet is használhatja, a megyét, és az állapot nevét tartalmazó új oszlop létrehozása, és így kihasználhatja az állapot nevét. Ehhez használja a [ `new_script_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) az adatfolyam metódust.

A Python-szkriptet meg kell határoznia a hívott függvény `newvalue()` , amely egy egyetlen argumentumot `row`. A `row` argumentum értéke egy dict (`key`: oszlopnév, `val`: jelenlegi érték) és a rendszer átad a függvény az adatkészlet minden egyes sorára. Ez a funkció az új oszlopban használandó értéket kell visszaadnia. Importálja a Python-szkriptet könyvtárra sem a környezetben, ahol az adatfolyamot fut léteznie kell.

```python
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
df.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale megye|Hale megyét, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|Hale megye|Hale megyét, Alabama|1.017100e + 10|0.0|

### <a name="new-script-filter"></a>Új parancsfájl-szűrő

Egy Python kifejezés használatával [ `new_script_filter()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) az adatkészlet csak ahol "Hale" nem szerepel az új sorok szűrése `county_state` oszlop. A kifejezés eredménye `True` szeretnénk a sort, ha és `False` eldobni a sor.

```python
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
df.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson megye|Jefferson megyét, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|Jefferson megye|Jefferson megyét, Alabama|1.019200e + 10|0.0|

## <a name="next-steps"></a>További lépések

* Lásd: az SDK [áttekintése](https://aka.ms/data-prep-sdk) tervezési minták és használati példák
* Tekintse meg az Azure Machine Learning Data Prep SDK [oktatóanyag](tutorial-data-prep.md) példa egy adott forgatókönyv megoldása
