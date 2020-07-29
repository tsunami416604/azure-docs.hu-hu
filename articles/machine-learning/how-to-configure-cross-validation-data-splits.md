---
title: Több ellenőrzés és adatfelosztás konfigurálása automatizált gépi tanulási kísérletekben
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan konfigurálhat több ellenőrzési és adatkészlet-felosztást az automatizált gépi tanulási kísérleteknél
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 900d5cd435a913c0859c862d176fd30130e0a079
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321496"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Az adatmegosztások és a több érvényesítés konfigurálása az automatizált gépi tanulásban

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a betanítási/érvényesítési adatosztásokat, és hogyan hozhatja meg az automatikus gépi tanulás, a AutoML és a kísérletek különböző beállításait.

Ha Azure Machine Learning, ha a AutoML használatával több ML modellt hoz létre, minden gyermeknek a modell minőségi metrikáinak kiszámításával ellenőriznie kell a kapcsolódó modellt, például a pontosságot vagy a AUC súlyozást. Ezek a metrikák az egyes modellekhez tartozó előrejelzések összehasonlításával vannak kiszámítva, és az érvényesítési adatokban szereplő korábbi észrevételekből származó valódi címkékkel vannak ellátva. 

A AutoML kísérletek automatikusan elvégzik a modell érvényesítését. Az alábbi szakaszok azt ismertetik, hogyan lehet tovább testreszabni az érvényesítési beállításokat a [Azure Machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)-val. 

Az alacsony kódú vagy a kód nélküli felhasználói élményért lásd: [az automatizált gépi tanulási kísérletek létrehozása Azure Machine learning Studióban](how-to-use-automated-ml-for-ml-models.md). 

> [!NOTE]
> A Studio jelenleg támogatja a betanítási/érvényesítési adatfelosztást és az ellenőrzési lehetőségeket, de nem támogatja az egyes adatfájlok megadását az ellenőrzési készlethez. 

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez szükséges,

* Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásával kapcsolatban tekintse meg [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md)című témakört.

* A Azure Machine Learning SDK-val való automatikus gépi tanulási kísérlet beállításának ismerete. Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) , vagy az [útmutató](how-to-configure-auto-train.md) segítségével tekintse meg az alapszintű gépi tanulási kísérlet tervezési mintáit.

* Az átellenőrzési és a betanítási/ellenőrzési adatmennyiség megismertetése ML-fogalmakkal. A magas szintű magyarázathoz

    * [A betanítási, ellenőrzési és tesztelési készletek ismertetése Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [A több érvényesítés ismertetése](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default--data-splits-and-cross-validation"></a>Alapértelmezett adatmegosztások és több ellenőrzés

A kísérlet és a betanítási beállítások megadásához használja a [AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) objektumot. A következő kódrészletben figyelje meg, hogy csak a szükséges paraméterek vannak definiálva, azaz a vagy a paraméterei `n_cross_validation` `validation_ data` **nem** szerepelnek benne.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Ha nem ad meg explicit módon a vagy a `validation_data` `n_cross_validation` paramétert, a AutoML az egyetlen megadott adatkészlet sorainak számától függően alkalmazza az alapértelmezett technikákat `training_data` :

|Betanítási &nbsp; &nbsp; adatméret| Érvényesítési módszer |
|---|-----|
|**Nagyobb, &nbsp; mint &nbsp; 20 000 &nbsp; sor**| A betanítási/érvényesítési adatfelosztást alkalmazza a rendszer. Az alapértelmezett érték a kezdeti betanítási adatkészlet 10%-át adja meg az érvényesítési készletként. Ezt az ellenőrzési készletet pedig a metrikák számításához használja a rendszer.
|**Kisebb, &nbsp; mint &nbsp; 20 000 &nbsp; sor**| A rendszer több ellenőrzési módszert alkalmaz. A kidobások alapértelmezett száma a sorok számától függ. <br> **Ha az adatkészlet kevesebb mint 1 000 sor, a**rendszer 10 karámot használ. <br> **Ha a sorok 1 000 és 20 000 között vannak**, akkor három hajtogatás van használatban.

## <a name="provide-validation-data"></a>Érvényesítési információ megadása

Ebben az esetben akár egyetlen adatfájllal is elindíthat, és kioszthatja azokat képzési és ellenőrzési készletekben, vagy megadhat egy külön adatfájlt az érvényesítési csoport számára. Mindkét `validation_data` esetben az objektumban lévő paraméter `AutoMLConfig` hozzárendeli az érvényesítési csoportként használandó összes adattípust. Ez a paraméter csak [Azure Machine learning adatkészlet](how-to-create-register-datasets.md) vagy pandák dataframe formájában fogad adatkészleteket.   

A következő mintakód kifejezetten meghatározza, hogy a megadott adatmennyiség mely részét `dataset` használja a betanításhoz és az érvényesítéshez.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Ellenőrzési készlet méretének megadása

Ebben az esetben csak egyetlen adatkészlet van megadva a kísérlethez. Ez a paraméter nincs `validation_data` megadva, **not** és a megadott adatkészlet hozzá van rendelve a `training_data` paraméterhez.  Az `AutoMLConfig` objektumban beállíthatja, hogy a `validation_size` paraméter a betanítási adatai egy részét kitartsa az érvényesítéshez. Ez azt jelenti, hogy az érvényesítési készletet a megadott kezdeti AutoML osztja fel a rendszer `training_data` . Ennek az értéknek 0,0 és 1,0 közöttinek kell lennie (például a 0,2 azt jelenti, hogy az adatellenőrzések 20%-át az érvényesítési adatként tároljuk).

Tekintse meg a következő kódrészletet:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>A Kereszttanúsítványok számának beállítása

Több ellenőrzés végrehajtásához adja meg a `n_cross_validations` paramétert, és állítsa egy értékre. Ezzel a paraméterrel állítható be, hogy hány több műveletet hajtson végre a rendszer az azonos számú hajtogatás alapján.

A következő kódban öt összehajtogatható a kereszt-ellenőrzés. Ebből kifolyólag öt különböző képzés, az egyes képzések 4/5-es, valamint minden egyes, a 1/5-es és az összes érvényesítési Holdout az egyes időpontokban.

Ennek eredményeképpen a metrikák kiszámítása az 5 ellenőrzési metrika átlagával történik.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Egyéni, kereszt-ellenőrzési bedobások meghatározása

Megadhatja a saját kereszt-ellenőrzési (CV) adatait is. Ez egy fejlettebb forgatókönyvnek tekintendő, mert meghatározza, hogy mely oszlopok legyenek felosztva és használhatók az érvényesítéshez.  Vegyen fel egyéni KtgE felosztott oszlopokat a betanítási adatokba, és adja meg, hogy mely oszlopok vannak feltöltve a paraméterben szereplő oszlopnevek alapján `cv_split_column_names` . Minden oszlop egy több-ellenőrzéses felosztást jelöl, és 1 vagy 0 egész számmal van kitöltve – ahol 1 azt jelzi, hogy a sort kell használni a betanításhoz, és 0 azt jelzi, hogy a sort kell használni az ellenőrzéshez.

A következő kódrészlet a bank marketing-adattartalmait tartalmazza, és két, "cV1" és "CV2" típusú KtgE felosztott oszlopot tartalmaz.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> A `cv_split_column_names` és a `training_data` használata `label_column_name` esetén frissítse a Azure Machine learning Python SDK-1.6.0 vagy újabb verzióját. Az előző SDK-verziók esetében tekintse meg a használatát `cv_splits_indices` , de vegye figyelembe, hogy a `X` és az `y` adatkészletek csak a bemenettel együtt használatosak. 

## <a name="next-steps"></a>További lépések

* [A kiegyensúlyozatlan és a túlterheléses adatkezelés megakadályozása](concept-manage-ml-pitfalls.md).
* [Oktatóanyag: automatikus gépi tanulás használata a taxi viteldíjak előrejelzéséhez – az Adatfelosztás szakasz](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Az [idősorozat-előrejelzési modell automatikus betanítása](how-to-auto-train-forecast.md).
