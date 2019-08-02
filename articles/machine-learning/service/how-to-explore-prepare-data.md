---
title: Az adathalmazok megismerése és átalakítása (adatkészlet osztálya)
titleSuffix: Azure Machine Learning service
description: Adatok feltárása az összegző statisztikák használatával és az adatok előkészítése az adatok tisztításával, átalakításával és funkcióinak fejlesztésével
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: f680a1cb15edf0141897c74da3b7c7afa01acae0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699118"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Az adatkészletek osztályának megismerése és előkészítése (előzetes verzió)

Megtudhatja, hogyan tekintheti át és készítheti elő az adatokat a azureml-adatkészletek csomaggal a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-ban. Az [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) osztály (előzetes verzió) lehetővé teszi az adatok feltárását és előkészítését olyan funkciókkal, mint például a mintavételezés, az összegző statisztika és az intelligens átalakítások. Az átalakítási lépések az [adatkészlet](how-to-manage-dataset-definitions.md) -definíciókban lesznek mentve, és a különböző sémák több nagyméretű fájljának nagy mértékben méretezhető módon történő kezelésére képes.

> [!Important]
> Egyes adatkészlet-osztályok (előzetes verzió) függőségei vannak a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomaggal (GA). Az átalakítási függvények közvetlenül a GA'ed [adat](how-to-transform-data.md)-előkészítési funkciókkal is elvégezhető, ezért javasoljuk, hogy az ebben a cikkben ismertetett adatkészlet-csomag burkolóit új megoldás létrehozásakor. Azure Machine Learning adatkészletek (előzetes verzió) lehetővé teszik, hogy ne csak az adatokat alakítsa át, hanem a [Pillanatkép-adatokat](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) is, és tárolja a verziószámmal ellátott [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)-definíciókat. Az adatkészletek az adat-előkészítő SDK következő verziója, amely kibővített funkciókat kínál az adatkészletek AI-megoldásokban való kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az adatai feltárásához és előkészítéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Az Azure Machine Learning szolgáltatás munkaterületén. Lásd: [Azure Machine learning szolgáltatás munkaterületének létrehozása](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* A Pythonhoz készült Azure Machine Learning SDK (1.0.21 vagy újabb verzió), amely tartalmazza a azureml-adatkészletek csomagot. Az SDK legújabb verziójának telepítéséhez vagy frissítéséhez lásd: [az SDK telepítése vagy frissítése](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* A Azure Machine Learning adat-előkészítési SDK. A legújabb verzió telepítéséhez vagy frissítéséhez lásd: [az adat-előkészítési SDK telepítése vagy frissítése](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Töltse le a következő példákkal ellátott mintát: [Crime. csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) és [City. JSON](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Mintavételezés

Az adatarchitektúra és a tartalom kezdeti megismeréséhez vegyen fel egy mintát az adataiból. Jelenleg az [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) adatkészlet osztály metódusa támogatja a legfontosabb N, egyszerű véletlenszerű és rétegzett mintavételi stratégiákat.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Legfontosabb N minta

A legfontosabb N mintavételezésnél az adatkészlet első n rekordja a minta. Ez akkor hasznos, ha csak azt szeretné megtudni, hogy az adatrekordok hogyan néznek ki, vagy hogy milyen mezők találhatók az adatokban.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Eset száma|Dátum|Letiltás|IUCR|Elsődleges típusa|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|MEGTÉVESZTŐ GYAKORLAT|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|LOPÁS|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|MEGTÉVESZTŐ GYAKORLAT|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|MEGTÉVESZTŐ GYAKORLAT|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|LOPÁS|...

### <a name="simple-random-sample"></a>Egyszerű véletlenszerű minta

Az egyszerű véletlenszerű mintavételezés során az adatpopuláció minden tagja egyenlő eséllyel van kiválasztva a minta részeként. `simple_random` A minta stratégiában az adatkészlet rekordjai a megadott valószínűség alapján vannak kiválasztva, és egy módosított adatkészletet adnak vissza. A Seed paraméter megadása nem kötelező.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Eset száma|Dátum|Letiltás|IUCR|Elsődleges típusa|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|LOPÁS|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|MEGTÉVESZTŐ GYAKORLAT|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|LOPÁS|...

### <a name="stratified-sample"></a>Rétegzett minta

A rétegzett minták biztosítják, hogy a mintában a populáció egyes csoportjai is megjelenjenek. A minta stratégiában a populáció rétegekbe, vagy alcsoportokra oszlik, hasonlóságok alapján, a rekordok pedig véletlenszerűen vannak kiválasztva az egyes rétegekből, a `fractions` paraméter által jelzett rétegek súlyozása alapján. `stratified`

A következő példában az egyes rekordokat a megadott oszlopok szerint csoportosítjuk, és belefoglaljuk az említett rekordot a rétegek X súlyozási `fractions`információi alapján. Ha nincs megadva réteg, vagy a rekord nem csoportosítható, a minta alapértelmezett súlyozása 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|Eset száma|Dátum|Letiltás|IUCR|Elsődleges típusa|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|LOPÁS|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|LOPÁS|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|LOPÁS|...

## <a name="explore-with-summary-statistics"></a>Ismerkedés az összegző statisztikával

 A metódussal észlelheti a rendellenességeket, a hiányzó [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) értékeket vagy a hibák számát. Ez a függvény beolvassa az adatok profilját és összegző statisztikáit, ami pedig segít meghatározni a szükséges adat-előkészítési műveleteket.

```Python
dataset.get_profile()
```

||Type|Min|Max|Darabszám|Hiányzó száma|Nem hiányzó száma|Hiányzó százalék|Hibák száma|Üres száma|0,1 % ki osztóérték|1 % ki osztóérték|5 %-os ki osztóérték|25 %-os ki osztóérték|50 %-os ki osztóérték|75 %-os ki osztóérték|95 %-os ki osztóérték|99 %-os ki osztóérték|99,9 %-os ki osztóérték|középérték|Szórás|Variancia|Döntés|Értékek
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|– 0,495701|-1.02814
Eset száma|FieldType.STRING|HZ239907|HZ278872|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Date|FieldType. DATE|2016-04-04 23:56:00 + 00:00|2016-04-15 17:00:00 + 00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Letiltás|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType. INTEGER|810|1154|10,0|0.0|10,0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058,5|137,285|18847,2|– 0,785501|– 1,3543
Elsődleges típusa|FieldType.STRING|MEGTÉVESZTŐ GYAKORLAT|LOPÁS|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Leírás|FieldType.STRING|HAMIS VIZSGÁLAT|TÖBB MINT $500|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Leírás helye|FieldType.STRING||ISKOLA, NYILVÁNOS, ÉPÜLET|10,0|0.0|10,0|0.0|0.0|1.0||||||||||||||
Letartóztatás|FieldType. BOOLEAN|False (Hamis)|False (Hamis)|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Hazai|FieldType. BOOLEAN|False (Hamis)|False (Hamis)|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Szívverés|FieldType. INTEGER|531|2433|10,0|0.0|10,0|0.0|0.0|0.0|531|531|531|614|1318,5|1911|2433|2433|2433|1371.1|692,094|478994|0,105418|– 1,60684
Kerület|FieldType. INTEGER|5|24|10,0|0.0|10,0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6,94822|48,2778|0,0930109|– 1,62325
Ward|FieldType. INTEGER|1|48|10,0|0.0|10,0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16,2635|264,5|0,173723|– 1,51271
Közösségi terület|FieldType. INTEGER|4|77|10,0|0.0|10,0|0.0|0.0|0.0|4|8.5|4|24|37,5|71|77|77|77|41,2|26.6366|709,511|0,112157|– 1,73379
Az FBI kód|FieldType. INTEGER|6|11|10,0|0.0|10,0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2,36643|5.6|– 0,702685|– 1,59582
X koordinátáját|FieldType. INTEGER|1.16309 e + 06|1.18336 e + 06|10,0|7.0|3.0|0,7|0.0|0.0|1.16309 e + 06|1.16309 e + 06|1.16309 e + 06|1.16401 e + 06|1.16678 e + 06|1.17921 e + 06|1.18336 e + 06|1.18336 e + 06|1.18336 e + 06|1.17108 e + 06|10793,5|1.165 e + 08|0,335126|– 2,33333
Y koordinátája|FieldType. INTEGER|1.8315 e + 06|1.908 e + 06|10,0|7.0|3.0|0,7|0.0|0.0|1.8315 e + 06|1.8315 e + 06|1.8315 e + 06|1.83614 e + 06|1.85005 e + 06|1.89352 e + 06|1.908 e + 06|1.908 e + 06|1.908 e + 06|1.86319 e + 06|39905,2|1.59243 e + 09|0.293465|– 2,33333
Év|FieldType. INTEGER|2016|2016|10,0|0.0|10,0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Frissítés dátuma|FieldType. DATE|2016-05-11 15:48:00 + 00:00|2016-05-27 15:45:00 + 00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Szélesség|FieldType.DECIMAL|41.6928|41,9032|10,0|7.0|3.0|0,7|0.0|0.0|41.6928|41.6928|41.6928|41,7057|41.7441|41,8634|41,9032|41,9032|41,9032|41,78|0,109695|0,012033|0,292478|– 2,33333
Hosszúság|FieldType.DECIMAL|– 87,6764|-87.6043|10,0|7.0|3.0|0,7|0.0|0.0|– 87,6764|– 87,6764|– 87,6764|– 87,6734|– 87,6645|-87.6194|-87.6043|-87.6043|-87.6043|– 87,6484|0,0386264|0,001492|0,344429|– 2,33333
Location|FieldType.STRING||(41,903206037,-87,676361925)|10,0|0.0|10,0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Hiányzó imputálására

Az adatkészletekben a null értékek, a NaN és a nem tartalmazó értékek nem minősülnek hiányzó értéknek. Ezek befolyásolhatják a gépi tanulási modellek teljesítményét, vagy érvénytelen következtetésekhez vezethetnek. A imputálási az egyik gyakori módszer a hiányzó értékek kezelésére, és akkor hasznos, ha a hiányzó értékű rekordok nagy hányadát szeretné egyszerűen törölni.

Az előző szakaszban létrehozott adatkészlet-profilból azt láthatjuk, hogy `Latitude` `Longitude` az oszlopok nagy százaléka hiányzó értékekkel rendelkezik. Ebben a példában a két oszlop esetében kiszámítjuk az átlagot és az imputált hiányzó értékeket.

Először is szerezze be az adatkészlet [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) legújabb definícióját, és bontsa ki az adatokat az-val [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), így csak azokat az oszlopokat tekintjük meg, amelyekkel foglalkozni szeretnénk.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|Letartóztatás| Szélesség|Hosszúság|
-|---------|-----|---------|----------|
|0|10498554|False (Hamis)|41.692834|-87.604319|
|1|10516598|False (Hamis)| 41,744107 |-87.664494|
|2|10519196|False (Hamis)| NaN|NaN|

Ezután a [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) függvény használatával `MEAN` vizsgálja meg a szélesség oszlop értékét. Ez a függvény elfogad egy tömb, az oszlopok a `group_by_columns` paraméterrel adja meg az összesítési szinten. A `summary_columns` paraméter elfogadja a `SummaryColumnsValue` függvényt, amely megadja az aktuális oszlopnevet, az új számított mező nevét és a `SummaryFunction` végrehajtandó műveletet.

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

Az imputált értékek ellenőrzése után [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) a használatával megtudhat egy rögzített kifejezést, amely az oszlopokat kiszámított `MIN`, `MAX` `MEAN` értékkel vagy `CUSTOM` értékkel adja át. Amikor `group_by_columns` van megadva, hiányzó imputálni csoport szerint kell `MIN`, `MAX`, és `MEAN` csoportonként számítja ki.

Az [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) elfogad egy column_id karakterláncot, a `ReplaceValueFunction` és a értéket az imputált típus megadásához. A hiányzó hosszúsági értékhez a-87-as számú, külső ismeret alapján kell imputáltnak lennie.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Az imputált lépések összevonhatók egy `ImputeMissingValuesBuilder` objektumba az [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) osztály függvény [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)használatával. A `impute_columns` paraméterben tömbjét `ImputeColumnArguments` objektumokat.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Hívja meg [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) a függvényt az imputált lépések tárolásához, és alkalmazza őket egy adatfolyam-objektumra a használatával. [`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Ahogy az a következő kimeneti táblázatban is látható, a hiányzó szélesség a `MEAN` `Arrest==False` csoport értékével lett bejelentve, a hiányzó hosszúság pedig-87.

||id|Letartóztatás|Szélesség|Hosszúság
-|---------|-----|---------|----------
0|10498554|False (Hamis)|41.692834|-87.604319
1|10516598|False (Hamis)|41,744107|-87.664494
2|10519196|False (Hamis)|41.780049|-87.000000

Frissítse az adatkészlet definícióját a [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) rel, hogy megőrizze a végrehajtott átalakítás lépéseit.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Letartóztatás|Szélesség|Hosszúság
-|---------|-----|---------|----------
0|10498554|False (Hamis)|41.692834|-87.604319
1|10516598|False (Hamis)|41,744107|-87.664494
2|10519196|False (Hamis)|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Érvényesítési szabályok létrehozása

Gyakran előfordul, hogy az adattisztítás és az adatelőkészítés során használt adatmennyiség csak az éles környezetben szükséges teljes adatmennyiség részhalmaza. Ennek eredményeképpen előfordulhat, hogy a takarítás részeként végrehajtott feltételezések némelyike hamis lehet. Egy folyamatosan frissülő adatkészletben például egy olyan oszlop, amely eredetileg csak egy adott tartományba tartozó számokat tartalmazott, a későbbi végrehajtásokban számos értéket tartalmazhat. Ezek a hibák gyakran hibás folyamatokat vagy hibás adatátvitelt eredményeznek.

Az adatkészletek támogatják az olyan adatérvényesítések létrehozását, amelyek a folyamat végrehajtásakor lesznek kiértékelve. Ezek az állítások lehetővé teszik, hogy az adatfeltételezések továbbra is pontosak legyenek, és ha nem, ennek megfelelően kezelje a hibákat.

Ha például korlátozni `Latitude` kívánja az adatkészletet és `Longitude` az értékeket egy adott numerikus tartományra, [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) a metódus biztosítja, hogy ez mindig igaz legyen.

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

||Type|Min|Max|Darabszám|Hiányzó száma|Nem hiányzó száma|Hiányzó százalék|Hibák száma|Üres száma|0,1 % ki osztóérték|1 % ki osztóérték|5 %-os ki osztóérték|25 %-os ki osztóérték|50 %-os ki osztóérték|75 %-os ki osztóérték|95 %-os ki osztóérték|99 %-os ki osztóérték|99,9 %-os ki osztóérték|középérték|Szórás|Variancia|Döntés|Értékek
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|– 0,495701|-1.02814
Letartóztatás|FieldType. BOOLEAN|False (Hamis)|False (Hamis)|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Szélesség|FieldType.DECIMAL|41.6928|41,9032|10,0|0.0|10,0|0.0|0.0|0.0|41.6928|41,7185|41.6928|41,78|41,78|41,78|41,9032|41,9032|41,9032|41,78|0,0517107|0,002674|0,837593|1.05
Hosszúság|FieldType. INTEGER|-87|-87|10,0|0.0|10,0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

A profilban láthatja, hogy `Error Count` `Longitude` az oszlop értéke 3. A következő kód szűri az adatkészletet, lekéri a hibát, és megtekinti, hogy az állítás milyen értéket eredményez. Itt állíthatja be a kódot, és megfelelő módon megtisztíthatja adatait.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Oszlopok származtatása példa alapján

Az adatkészletek fejlettebb eszközeinek egyike az oszlopok származtatása a kívánt eredmények példáinak használatával. Így például megadhatja az SDK-t, így programkódot hozhat létre a kívánt átalakítások eléréséhez.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Eset száma|Dátum|Letiltás|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

Tegyük fel, hogy át kell alakítania a dátum-és időformátumot "2016-04-04 10 – 12AM" értékre. Az [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) argumentumban adja meg a kívánt kimenetre vonatkozó példákat `example_data` a paraméterben a következő formátumban: *(eredeti kimenet, kívánt kimenet)* .

A következő kód két példát mutat be a kívánt kimenetre: ("2016-04-04 23:56:00", "2016-04-04 10 – 12AM") és ("2016-04-15 17:00:00", "2016-04-15 16:00-18:00")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Figyelje meg, hogy az alábbi táblázatban egy új oszlop található, amely a megadott formátumú rekordokat tartalmazza Date_Time_Range.

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10 – 12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10:00 – 12:00

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Fuzzy csoportok

Ha különböző forrásokból gyűjt adatokat, akkor előfordulhat, hogy az azonos entitások helyesírási, nagybetűs vagy rövidítései eltérő változatban találkozhatnak. Automatikusan szabványosíthatja és összehangolhatja ezeket a változatokat az SDK-k fuzzy csoportosításával vagy a szöveges fürtözéssel.

Az oszlop `inspections.business.city` például a "San Francisco" város nevének számos formáját tartalmazza.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||ellenőrzések. Business. business_id|inspections.business_name|ellenőrzések. Business.-címek|ellenőrzések. Business. City|...|
-|-----|-------------------------|------------|--|---
0|16162|Gyors N-eZee indiai élelmiszerek|3861 24th St|SF|...
1|67565|Thai Noodles Cafe királya|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|Thai Noodles Cafe királya|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 Clement St|SF|...
4|69186|Premier Catering & events, Inc.|1255 22nd St|S.F.|...

Vegyünk fel egy [`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) olyan oszlopot, amely a "San Francisco" automatikusan észlelt kanonikus formáját használja. Az `source_column` argumentumok `new_column_name` és a szükségesek. A következő lehetőségek is rendelkezésre állnak:

* Hozzon létre egy új `similarity_score_column_name`oszlopot, amely a hasonlósági pontszámot mutatja az eredeti és a kanonikus értékek minden párja között.

* Adja meg a értéket ,amelyacsoportbacsoportosíthatóértékekminimálishasonlóságipontszáma.`similarity_threshold`

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||ellenőrzések. Business. business_id|inspections.business_name|ellenőrzések. Business.-címek|ellenőrzések. Business. City|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Gyors N-eZee indiai élelmiszerek|3861 24th St|SF|San Francisco|0,814806|...
1|67565|Thai Noodles Cafe királya|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1,000000|...
2|67565|Thai Noodles Cafe királya|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1,000000|...
3|68701|Grindz|832 Clement St|SF|San Francisco|0,814806|...
4|69186|Premier Catering & events, Inc.|1255 22nd St|S.F.|San Francisco|0,814806|...

Az eredményül kapott adatkészlet definíciójában az adatban szereplő "San Francisco" jelölő különböző variációi a "San Francisco" karakterláncra lettek normalizálva.

Mentse ezt a zavaros csoportosítási lépést a legújabb adatkészlet-definícióba `update_definition()`a következővel:.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>További lépések

* A regressziós modellre [](tutorial-auto-train-models.md) vonatkozó automatizált gépi tanulásról szóló oktatóanyagban talál példát.

* Tekintse meg [](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) az SDK áttekintését a tervezési mintákhoz és a használati példákhoz.

* Az adatkészletek használatára vonatkozó példát a [minta notebookok](https://aka.ms/dataset-tutorial)című témakörben talál.
