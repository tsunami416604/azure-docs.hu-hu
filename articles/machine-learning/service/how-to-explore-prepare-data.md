---
title: Fedezze fel és alakíthat át adatokat (adatkészlet osztály)
titleSuffix: Azure Machine Learning service
description: Adatfeltárás segítségével az összefoglaló statisztikák és adatok törlését, átalakítási és funkciófejlesztési adatok előkészítése
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: e29ef2616a43223ec582575ca6363f78b26e5f22
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753059"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Ismerje meg, és előkészíti az adatokat az adatkészlet osztályhoz (előzetes verzió)

Ismerje meg, és az azureml-adatkészletek csomaggal, az adatok előkészítése az [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). A [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) osztály (előzetes verzió) segítségével megvizsgálhatja és készítse elő adatait funkciók megadásával: mintavétel, összefoglaló statisztikák és intelligens átalakításokat. Adatátalakítási lépéseket mentett [az adatkészlet-definíciók](how-to-manage-dataset-definitions.md) az képes a más sémák több nagy fájlok nagy mértékben skálázható módon.

> [!Important]
> Néhány adatkészlet osztályok (előzetes verzió) függőségekkel rendelkezik a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomag (elérhetővé tétel GA). Miközben átalakítási függvényeket közvetlenül a GA'ed elvégezhető [Data Prep funkciók](how-to-transform-data.md), azt javasoljuk, hogy az adatkészlet csomag burkolókat ebben a cikkben ismertetett, ha egy új megoldást épít. Az Azure Machine Learning-adatkészletek (előzetes verzió) lehetővé teszi nemcsak az adatátalakítás, hanem [-pillanatkép adatainak](how-to-create-dataset-snapshots.md) és tárolása [rendszerverzióval ellátott adatkészlet-definíciókban](how-to-manage-dataset-definitions.md). Az adatkészletek az adatkészleteket az AI-megoldások kezelésével kapcsolatos bővített funkciókat kínál a Data Prep SDK következő verziójában.

## <a name="prerequisites"></a>Előfeltételek

Ismerje meg, és az adatok előkészítéséhez, lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Az Azure Machine Learning szolgáltatás munkaterületén. Lásd: [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Az Azure Machine Learning SDK Pythonhoz készült (1.0.21 verzió vagy újabb), amely tartalmazza az azureml-adatkészletek csomagot. Telepíteni, vagy frissítsen az SDK legújabb verzióját, lásd: [telepítése vagy frissítése az SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Az Azure Machine Learning Adatelőkészítéshez SDK-t. Telepítse vagy frissítse a legújabb verzióra, lásd: [telepíteni vagy frissíteni a Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* A példák és kövesse a mintafájlok letöltése: [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) és [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Mintavételezés

Az adatok kezdeti megismerheti a data-architektúra és a tartalom mintát venni. Jelenleg a [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) adatkészlet osztályból származó módszer támogatja a felső N, véletlenszerű egyszerű és Stratified mintavételezési stratégia.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Felső N-minta

Felső N mintavételezése esetén, az adatkészlet az első n rekordok a minta. Ez azért hasznos, ha csak kívánt képet kapjon a milyen az adatok rekordok megjelenítése például vagy mezők Mik az adatok megtekintéséhez.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||azonosító|Eset száma|Dátum|Letiltás|IUCR|Elsődleges típusa|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|MEGTÉVESZTŐ ELJÁRÁS|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD ELENTÉS MENTÉSE|890|LOPÁS|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTÓI ELENTÉS MENTÉSE|1154|MEGTÉVESZTŐ ELJÁRÁS|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S VALÓ MENTÉSE|1120|MEGTÉVESZTŐ ELJÁRÁS|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE ELENTÉS MENTÉSE|890|LOPÁS|...

### <a name="simple-random-sample"></a>Egyszerű véletlenszerűen vett minta

Egyszerű véletlenszerű mintavételezése esetén, az adatokkal való feltöltés minden tagja van, egy ütemezésére ugyanannyi esély választja, a minta részeként. Az a `simple_random` mintavételezési stratégia, a rekordokat az adatkészletből származó ki van jelölve, a megadott valószínűség alapján, és adja vissza a módosított adatkészlet. A kezdőérték paraméter nem kötelező.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||azonosító|Eset száma|Dátum|Letiltás|IUCR|Elsődleges típusa|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD ELENTÉS MENTÉSE|890|LOPÁS|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTÓI ELENTÉS MENTÉSE|1154|MEGTÉVESZTŐ ELJÁRÁS|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE ELENTÉS MENTÉSE|890|LOPÁS|...

### <a name="stratified-sample"></a>Rétegzett minta

Rétegzett minták győződjön meg arról, hogy bizonyos csoportok egy sokaság megjelenjen a mintát. Az a `stratified` mintavételezési stratégia, a lakosság strata és alcsoportokra, feltárt Hasonlóságok alapján van osztva, és a rekordok minden egyes strata, a strata súlyok által jelzett megfelelően a rendszer véletlenszerűen választja ki a `fractions` paraméter.

A következő példában azt minden rekord csoportból a megadott oszlopok alapján, és tartalmazza az említett rekord, a strata X súly információi alapján `fractions`. Ha egy strata nincs megadva, vagy a rekord nem csoportosíthatók, a minta alapértelmezett súlyozási érték 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||azonosító|Eset száma|Dátum|Letiltás|IUCR|Elsődleges típusa|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD ELENTÉS MENTÉSE|890|LOPÁS|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE ELENTÉS MENTÉSE|890|LOPÁS|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN ELENTÉS MENTÉSE|810|LOPÁS|...

## <a name="explore-with-summary-statistics"></a>Ismerje meg az összefoglaló statisztikák

 Rendellenességek észlelése, a hiányzó értékeket, vagy hiba történt a száma a [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) metódust. Ez a függvény beolvassa a profil, és az adatokat, ami viszonzásul segíti az összefoglaló statisztikák határozza meg a szükséges előkészítés adatműveletekre kell alkalmazni.

```Python
dataset.get_profile()
```

||Típus|Min|Max|Darabszám|Hiányzó száma|Nem hiányzó száma|Hiányzó százalék|Hibák száma|Üres száma|0,1 % ki osztóérték|1 % ki osztóérték|5 %-os ki osztóérték|25 %-os ki osztóérték|50 %-os ki osztóérték|75 %-os ki osztóérték|95 %-os ki osztóérték|99 %-os ki osztóérték|99,9 %-os ki osztóérték|középérték|Szórás|Variancia|Döntés|Értékek
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
azonosító|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Eset száma|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Dátum|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Letiltás|FieldType.STRING|004XX S KILBOURN ELENTÉS MENTÉSE|113XX S VALÓ MENTÉSE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Elsődleges típusa|FieldType.STRING|MEGTÉVESZTŐ ELJÁRÁS|LOPÁS|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Leírás|FieldType.STRING|HAMIS ELLENŐRZÉSE|500 USD KERESZTÜL|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Leírás helye|FieldType.STRING||SCHOOL, PUBLIC, BUILDING|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Letartóztatás|FieldType.BOOLEAN|False (Hamis)|False (Hamis)|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Hazai|FieldType.BOOLEAN|False (Hamis)|False (Hamis)|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Körzet|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Közösségi terület|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
Az FBI kód|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X koordinátáját|FieldType.INTEGER|1.16309e + 06|1.18336e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e + 06|1.16309e + 06|1.16309e + 06|1.16401e + 06|1.16678e + 06|1.17921e + 06|1.18336e + 06|1.18336e + 06|1.18336e + 06|1.17108e + 06|10793.5|1.165e + 08|0.335126|-2.33333
Y koordinátája|FieldType.INTEGER|1.8315e + 06|1.908e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e + 06|1.8315e + 06|1.8315e + 06|1.83614e + 06|1.85005e + 06|1.89352e + 06|1.908e + 06|1.908e + 06|1.908e + 06|1.86319e + 06|39905.2|1.59243e + 09|0.293465|-2.33333
Év|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Frissítés dátuma|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Szélesség|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Hosszúság|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location egység|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Hiányzó imputálására

Adatkészletek esetében a null értékeket, a NaN és értékek nincsenek tartalmat tartalmazó minősülnek hiányzó értékeket. Ezek hatással van a gépi tanulási modellek teljesítményét, vagy érvénytelen következtetéseket vezethet. Imputálási egy általánosan használt megközelítése oldja meg a hiányzó értékeket, és akkor hasznos, ha hiányzik a magas százalékos érték a rekordokat, amelyek egyszerűen csak azt követően törölhetők.

Az előző szakaszban létrehozott adatkészlet-profilból, láthatjuk, hogy `Latitude` és `Longitude` oszlopok rendelkezzenek a magas százalékos érték hiányzik. Ebben a példában azt az átlag kiszámítása és imputálására hiányzó ezen két oszlop értékeit.

Először kérje le a legújabb definíció a az adatkészlet [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) le az adatokat a formázásra és [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), ezért azt csak a cím kívánt oszlopok megtekintésére.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||azonosító|Letartóztatás| Szélesség|Hosszúság|
-|---------|-----|---------|----------|
|0|10498554|False (Hamis)|41.692834|-87.604319|
|1|10516598|False (Hamis)| 41.744107 |-87.664494|
|2|10519196|False (Hamis)| NaN|NaN|

Ezután ellenőrizze a `MEAN` használatával szélességi oszlop értékét a [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) függvény. Ez a függvény elfogad egy tömb, az oszlopok a `group_by_columns` paraméterrel adja meg az összesítési szinten. A `summary_columns` paraméterben az `SummaryColumnsValue` függvénynek, amely megadja az aktuális oszlop nevét, az új számított mező neve és a `SummaryFunction` végrehajtásához.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Letartóztatás|Latitude_MEAN|
--|-----|--------|
|0|False (Hamis)|41.780049|

Az értékek imputálására ellenőrizni fogjuk, ha az [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) egy állandó kifejezés, amelyek biztosítják az vagy egy számított oszlopokat további `MIN`, `MAX`, `MEAN` érték, vagy egy `CUSTOM` értéket. Amikor `group_by_columns` van megadva, hiányzó imputálni csoport szerint kell `MIN`, `MAX`, és `MEAN` csoportonként számítja ki.

A [ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) -column_id karakterláncot fogad el és a egy `ReplaceValueFunction` határozza meg a impute típusát. A hiányzó hosszúság érték imputálására a-87 külső ismeretanyag alapján.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Imputálására lépéseket összefűzhetők be egy `ImputeMissingValuesBuilder` objektumba a [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) függvény osztály [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). A `impute_columns` paraméterben tömbjét `ImputeColumnArguments` objektumokat.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Hívja a [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) impute lépéseket tárolja a funkciót, és alkalmazza őket egy adatfolyam objektum használatával [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Ahogyan az alábbi táblázatban kimeneti, a hiányzó földrajzi szélesség volt imputált a `MEAN` értékét `Arrest==False` -87 a csoportot, és a hiányzó hosszúsági volt imputált.

||azonosító|Letartóztatás|Szélesség|Hosszúság
-|---------|-----|---------|----------
0|10498554|False (Hamis)|41.692834|-87.604319
1|10516598|False (Hamis)|41.744107|-87.664494
2|10519196|False (Hamis)|41.780049|-87.000000

Frissítse az adatkészlet-definícióban, [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) , hogy az elvégzett Adatátalakítási lépéseket.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||azonosító|Letartóztatás|Szélesség|Hosszúság
-|---------|-----|---------|----------
0|10498554|False (Hamis)|41.692834|-87.604319
1|10516598|False (Hamis)|41.744107|-87.664494
2|10519196|False (Hamis)|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Előfeltétel-szabályok létrehozása

Gyakran az adatok együttműködünk tisztítása során, és adatok előkészítése üzemi van szükség az összesített adatok egy részét. Ennek eredményeképpen egyes Előfeltételek a tisztítás részeként létrehozunk előfordulhat, hogy kapcsolja hamisnak bizonyult. Az adatkészlet, amely folyamatosan frissíti, például egy oszlopot, amely eredetileg csak szereplő számok egy adott tartományon belül tartalmazhatja az értékeket későbbi végrehajtások szélesebb köre. Ezek a hibák gyakran hibás folyamatok vagy a hibás adatokat eredményez.

Adatkészletek támogatása az adatokat, amelyek értékeli ki, a folyamat végrehajtja a helyességi feltételek létrehozása. Ezek a helyességi feltételek tudassa velünk győződjön meg arról, hogy az előfeltételek az adatok továbbra is a pontos és, ha nem, ennek megfelelően hibáinak a kezelése.

Például, ha korlátozni szeretné `Latitude` és `Longitude` értékek az adatkészlet, meghatározott numerikus tartományokra, a [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) módszer biztosítja, hogy ez a helyzet, mindig.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Típus|Min|Max|Darabszám|Hiányzó száma|Nem hiányzó száma|Hiányzó százalék|Hibák száma|Üres száma|0,1 % ki osztóérték|1 % ki osztóérték|5 %-os ki osztóérték|25 %-os ki osztóérték|50 %-os ki osztóérték|75 %-os ki osztóérték|95 %-os ki osztóérték|99 %-os ki osztóérték|99,9 %-os ki osztóérték|középérték|Szórás|Variancia|Döntés|Értékek
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
azonosító|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Letartóztatás|FieldType.BOOLEAN|False (Hamis)|False (Hamis)|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Szélesség|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
Hosszúság|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

A profil, azt láthatja, hogy a `Error Count` számára a `Longitude` oszlop értéke 3. Az alábbi kód szűri az adatkészlet, lekéri a hiba és látja, hogy milyen érték hatására a helyességi feltétel alkalmazása sikertelen lesz. Itt módosíthatja a kódot és az adatok megtisztításához megfelelően.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Oszlopok a Származtatás példa

A speciális eszközök adatkészletekhez egyik oszlopait a kívánt eredmények példáit származtassa lehetővé teszi. Ez lehetővé teszi, adjon meg például az SDK-t, hogy azt generálhat kódot az importálni kívánt átalakítások eléréséhez.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||azonosító|Eset száma|Dátum|Letiltás|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD ELENTÉS MENTÉSE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTÓI ELENTÉS MENTÉSE|...

Tegyük fel, hogy kell alakítania a dátum és idő formátumot a "2016-04-04 ESTE 10 – 12 AM". Az a [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) argumentum, a kívánt kimenetet a példákban a `example_data` paraméter a következő formátumban: *(eredeti kimeneti, a kívánt kimeneti)* .

A következő kódot a kívánt kimeneti két példákat tartalmaz ("2016-04-04 23:56:00", "2016-04-04 10 PM-12 AM") és ("2016-04-15 17:00:00", "2016-04-15 du. 4 – 18: 00")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Az alábbi táblázatban figyelje meg, hogy egy olyan új oszlop Date_Time_Range szereplő rekordok a megadott formátumban.

||azonosító|Dátum|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10 PM – 12 AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10 ÓRA ÉS DÉL

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Az intelligens csoportosításokat

Amikor adatokat gyűjtse össze a különböző forrásokból származó, helyesírás, kis-és nagybetűk vagy ugyanazokat az entitásokat rövidítéseit léphetnek fel. Automatikusan szabványosíthatja, és ezek variantní hodnoty egyeztetni az SDK-t az intelligens csoportosítási és szöveges fürtözés, funkciókat.

Ha például az oszlop `inspections.business.city` az városa neve "San Francisco" több formáját tartalmazza.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.Business.address|inspections.Business.City|...|
-|-----|-------------------------|------------|--|---
0|16162|Gyors-N-Ezee indiai élelmiszerek|3861 24th St|SF|...
1|67565|Thai király nudli Cafe|1541 TARAVAL St|A SAN FRANCISCÓBAN|...
2|67565|Thai király nudli Cafe|1541 TARAVAL St|A SAN FRANCISCÓBAN|...
3|68701|Grindz|832 clement St|SF|...
4|69186|Premier szintű élelmiszer- & események, Inc.|1255 22nd St|S.F.|...

Használjuk a [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) metódus hozzáadása "San Francisco" automatikusan észlelt kanonikus alakja tartalmazó oszlop. Az argumentumok `source_column` és `new_column_name` szükség. Is van lehetősége:

* Egy olyan új oszlop létrehozása `similarity_score_column_name`, azt mutatjuk be, hogy a hasonló elemek pontszám eredeti és kanonikus értékeket minden egyes párjai közötti.

* Adjon meg egy `similarity_threshold`, vagyis az értékek csoportosítja a minimális hasonlóság pontszámának.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.Business.address|inspections.Business.City|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Gyors-N-Ezee indiai élelmiszerek|3861 24th St|SF|A San Franciscóban|0.814806|...
1|67565|Thai király nudli Cafe|1541 TARAVAL St|A SAN FRANCISCÓBAN|A San Franciscóban|1.000000|...
2|67565|Thai király nudli Cafe|1541 TARAVAL St|A SAN FRANCISCÓBAN|A San Franciscóban|1.000000|...
3|68701|Grindz|832 clement St|SF|A San Franciscóban|0.814806|...
4|69186|Premier szintű élelmiszer- & események, Inc.|1255 22nd St|S.F.|A San Franciscóban|0.814806|...

Az eredményül kapott adatkészlet-definícióban a másik változata, "A San Franciscóban" képviselő az adatok lettek normalizált ugyanazt a karakterláncot, "A San Franciscóban".

Az intelligens csoportosítási lépést, amelyet a legújabb, az adatkészlet-definícióban az `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>További lépések

* [Az adatkészlet-definíciók életciklusának kezelése](how-to-manage-dataset-definitions.md).

* Tekintse meg a automatizált machine learning [oktatóanyag](tutorial-auto-train-models.md) regressziós modell példát.

* Lásd: az SDK [áttekintése](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) tervezési minták és használati példák.

* Példa adatkészleteket használó, tekintse meg a [notebookok minta](https://aka.ms/dataset-tutorial).
