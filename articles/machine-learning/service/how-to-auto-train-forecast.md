---
title: Automatikus – train idősorozat-előrejelzési modell
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használja az Azure Machine Learning szolgáltatás egy idősorozat-előrejelzési regressziós modell használatával automatikus gépi tanulás.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: e1b584d38c4583e37b7c47535c836d1fa7d428f1
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357248"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatikus – train idősorozat-előrejelzési modell

Ebből a cikkből elsajátíthatja egy idősorozat-előrejelzési regressziós modell betanításához automatizált a machine learning szolgáltatásban Azure Machine Learning használatával. Előrejelzési modell konfigurálása állít be egy automatizált machine learning segítségével standard regressziós modell hasonló, de bizonyos konfigurációs beállításokat és előfeldolgozásához lépéseket léteznek idősorozat-adatok használata. Az alábbi példák bemutatják, hogyan való:

* A time series modellezési adatok előkészítése
* Az adott idősorozat-paramétereinek konfigurálása egy [ `AutoMLConfig` ](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) objektum
* Futtassa az előrejelzések idősorozat adatokhoz

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkaterületén. A munkaterület létrehozásához lásd: [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](setup-create-workspace.md).
* Ez a cikk egy automatizált machine learning-kísérlet beállítása alapszintű ismeretét feltételezi. Kövesse a [oktatóanyag](tutorial-auto-train-models.md) vagy [útmutató](how-to-configure-auto-train.md) az alapvető automatikus machine learning-kísérlet tervezési minták megtekintéséhez.

## <a name="preparing-data"></a>Adatok előkészítése

A legfontosabb különbség a között egy előrejelzési regressziós feladattípus és regressziós feladattípus belüli automatikus gépi tanulási közé a következők tartoznak egyik funkciója, amely érvényes az idősor lépésközi jelöli az adatok. Rendszeres idősorozat tartalmaz egy jól definiált és konzisztens gyakoriság és egy értéket egy folyamatos azt az időtartományt a minta pontjára. Fontolja meg egy fájl a következő pillanatkép `sample.csv`.

    week_starting,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/10/2018,A,2300,37
    9/10/2018,B,550,37
    9/17/2018,A,2100,38
    9/17/2018,B,650,38
    9/24/2018,A,2400,39
    9/24/2018,B,700,39
    10/1/2018,A,2450,40
    10/1/2018,B,650,40

Ez az adatkészlet egy vállalat, amely rendelkezik a és B. emellett két különböző áruházakban heti értékesítési adatok egy egyszerű példa, van egy funkciót `week_of_year` , amely lehetővé teszi a modell heti szezonalitás észleléséhez. A mező `week_starting` jelenti egy tiszta idősorozat heti gyakorisággal, és a mező `sales_quantity` a futó előrejelzések a céloszlop van. Az adatok olvasása a Pandas dataframe-be, majd használja a `to_datetime` függvényt, hogy az idősorozat-e, egy `datetime` típusa.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["week_starting"] = pd.to_datetime(data["week_starting"])
```

Ebben az esetben az adatok lett rendezve az idő mező szerint növekvő `week_starting`. Ugyanakkor kísérlet beállításakor biztosítják a kívánt időre oszlop növekvő sorrendben hozhat létre egy érvényes idősorozat van rendezve. Feltételezik, hogy az adatok 1000 rekordot tartalmaz, és adja meg a determinisztikus felosztása a képzés és az adatkészletek az adatokat. A célmező majd külön `sales_quantity` hozhat létre, az előrejelzési tanítási és tesztelési állítja be.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Az értékeket későbbi előrejelzési modell betanításakor ügyeljen rá, hogy a használt képzési szolgáltatásai használhatók az importálni kívánt horizon adatokat futtatásakor. Például egy kereslet-előrejelzési létrehozásakor, beleértve az aktuális tőzsdei árfolyam funkció nagy mértékben megnövelheti képzési pontosságát. Azonban ha azt tervezi, a hosszú horizon előrejelzést, nem lehet előre pontosan a jövőbeli idősorozat-pontok megfelelő jövőbeli tőzsdei értékeket, és modellpontosságból volt tapasztalható.

## <a name="configure-experiment"></a>Kísérlet konfigurálása

Automatizált gépi tanulási feladatok előrejelzés, használja az idősorozat-adatok adott előzetes feldolgozása és költségbecslési lépéseket. A következő előfeldolgozási lépéseket fogja végrehajtani:

* Idősorozat-mintavétel gyakorisága (például óránként, naponta, hetente), és új rekordok hiányzó idő pontot, hogy az a sorozat folyamatos létrehozása észleli.
* A cél (keresztül előre ki) és a szolgáltatás oszlopok (középérték oszlop értékeinek használatával) a hiányzó értékeket imputálására
* Időfelbontási szint-alapú funkciók engedélyezéséhez rögzített hatások különböző adatsorozatainak létrehozása
* Időalapú funkciók elsajátításához szezonális minták létrehozása
* Numerikus mennyiségek kategorikus változókat kódolása

A `AutoMLConfig` objektuma határozza meg a beállításokat és a egy automatizált machine learning feladatot a szükséges adatokat. Regressziós problémaként hasonlóan megadhat standard képzési paraméterek tevékenység típusa, a betanítási adatok, az ismétlések száma és a kereszt-ellenőrzés száma. Az előrejelzési feladatok, nincsenek további paramétereket kell megadni, amelyek befolyásolják a kísérletet. A következő táblázat az egyes paraméterek és azok használatát ismerteti.

| Param | Leírás | Szükséges |
|-------|-------|-------|
|`time_column_name`|Itt adhatja meg a dátum/idő oszlop a bemeneti adatok az idősor és annak gyakoriságát adatcsatornához.|✓|
|`grain_column_names`|Az egyes adatsorozat-csoportok meghatározása a bemeneti adatok neve. Időfelbontási szint nincs definiálva, ha az adatkészlet adatforrásmérete egy idősorozat.||
|`max_horizon`|Maximálisan keresett előrejelzési horizon idősorozat-gyakoriság egysége.|✓|

Hozzon létre egy szótárobjektum az idősorozat-beállításokat. Állítsa be a `time_column_name` , a `week_starting` mezőt az adatkészlet. Adja meg a `grain_column_names` annak érdekében, hogy a paraméter **két különálló idősorozat-csoportok** jönnek létre az adatok; az egyik az áruházban A, b végül beállítása a `max_horizon` 50 annak érdekében, hogy előre jelezni a teljes vizsgálat beállítása.

```python
time_series_settings = {
    "time_column_name": "week_starting",
    "grain_column_names": ["store"],
    "max_horizon": 50
}
```

Most hozzon létre egy standard `AutoMLConfig` objektumot, adja meg a `forecasting` típusú feladat, és a kísérlet elküldése. A modell befejezését követően beolvasni a legjobb Futtatás iteráció.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> A kereszt-ellenőrzés (CV) eljárás idősorozat-adatok is megsértik a canonical K lépésből kereszt-ellenőrzési stratégia az alapvető statisztikai feltételezéseket, automatizált machine learning megvalósítja a működés közbeni forrás érvényesítési eljárás létrehozása kereszt-ellenőrzési modellrész idősorozat-adatok. Ez az eljárás használatához adja meg a `n_cross_validations` paramétert a `AutoMLConfig` objektum. Érvényesítési és használja a saját érvényesítési beállítja a lehetséges kikerülni a `X_valid` és `y_valid` paramétereket.

## <a name="forecasting-with-best-model"></a>A legjobb modellt előrejelzés

A legjobb modellt az iteráció használatával előre jelzett értékek a tesztelési adatkészletnél.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Gyökátlagos kiszámítása (root mean-készlet négyzet hiba) között a `y_test` tényleges értékek és az előre jelzett értékek `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Most, hogy a teljes modellpontosságból meghatározása, a legtöbb valósághű következő lépés az, hogy a modell használatával előrejelzést ismeretlen jövőbeli értékek. Egyszerűen adja meg a teszt beállított ugyanebben a formátumban adatkészlet `X_test` , de a jövőbeli időpontok és az eredményül kapott előrejelzési beállítása az előre jelzett értékek mindegyik idősorozat-lépéshez. Tegyük fel, a hét indítása volt az adatkészlet utolsó idősorozat-rekordjai 12/31/2018. A következő hét iránti kereslet előrejelzése (vagy az előrejelzéshez az igény szerint annyi időt, < = `max_horizon`), hozzon létre egy sorozat rekord időt az egyes üzletek a hét indítása 01/07/2019.

    week_starting,store,week_of_year
    01/07/2019,A,2
    01/07/2019,A,2

Ismételje meg a szükséges lépéseket, majd futtassa a jövőbeli adatok betöltése az dataframe `best_run.predict(X_test)` előre jelezni a jövőbeli értékeket.

> [!NOTE]
> Nagyobb, mint időszakok száma, az értékek nem becsülhető a `max_horizon`. A modell az előre jelezni a jövőbeli érték meghaladja a jelenlegi közeljövőre egy nagyobb horizon újra betanított kell lennie.

## <a name="next-steps"></a>További lépések

* Kövesse a [oktatóanyag](tutorial-auto-train-models.md) automatikus machine learning-kísérletek létrehozása.
* Nézet a [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) referenciák dokumentációiba.
