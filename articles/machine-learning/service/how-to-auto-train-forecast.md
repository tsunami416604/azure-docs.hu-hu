---
title: Idősorozat-előrejelzési modell automatikus betanítása
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a Azure Machine Learning szolgáltatás egy idősorozat-előrejelzési regressziós modell automatikus gépi tanulással történő betanításához.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 34902aa23339b62920f918ae19b410a99e226a0e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358812"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Idősorozat-előrejelzési modell automatikus betanítása

Ebből a cikkből megtudhatja, hogyan végezheti el az idősorozat-előrejelzési regressziós modell betanítását a Azure Machine Learning szolgáltatásban található automatikus gépi tanulás használatával. Az előrejelzési modell konfigurálása hasonló ahhoz, hogy szabványos regressziós modellt állítson be automatizált gépi tanulással, de bizonyos konfigurációs beállítások és előfeldolgozási lépések léteznek az idősorozat-információkkal való munkavégzéshez. Az alábbi példák a következőket mutatják be:

* Az idősorozat-modellezéssel kapcsolatos adatelőkészítés
* Adott idősorozat-paraméterek konfigurálása egy [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) objektumban
* Előrejelzések futtatása idősoros adattal

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Az automatikus ML-vel kombinálhatja a technikákat és a megközelítéseket, és egy ajánlott, magas színvonalú idősorozat-előrejelzést is igénybe vehet. Az automatikus idősorozat-kísérletet többváltozós regressziós problémaként kezeli a rendszer. A korábbi idősorozat-értékek "Pivotal", hogy további dimenziókat regressor a többi előrejelzővel együtt. 

Ez a klasszikus idősorozat-módszerekkel ellentétben az egyik előnye, hogy természetesen több kontextusos változót is magában foglal, és a képzés során egymáshoz fűződő kapcsolataikat. A valós idejű előrejelzési alkalmazásokban több tényező is befolyásolhatja az előrejelzést. Például az értékesítések előrejelzése, a múltbeli trendek, az árfolyam és az ár interakciója együttesen hajtja végre az értékesítés eredményét. További előny, hogy a regressziós modellek legújabb innovációi azonnal érvénybe kerülnek az előrejelzések alapján.

Megadhatja [, hogy a](#config) jövőben milyen mértékben kell kiterjesztenie az előrejelzést (az előrejelzési horizontot), valamint a késéseket és egyebeket. Az automatizált ML egyetlen, de gyakran belsőleg elágazó modellt tanul az adatkészlet összes eleméhez és előrejelzési horizontokhoz. Így több adat érhető el a modell paramétereinek becsléséhez és az általánosításhoz, hogy a láthatatlan adatsorozatok elérhetővé válnak. 

A betanítási adatokból kinyert funkciók kritikus szerepet játszanak. Az automatizált ML standard előfeldolgozási lépéseket hajt végre, és további idősorozat-funkciókat hoz létre az idényjellegű hatások rögzítése és a prediktív pontosság maximalizálása érdekében. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkaterületén. A munkaterület létrehozásával kapcsolatban tekintse meg a [Azure Machine learning szolgáltatás munkaterületének létrehozása](setup-create-workspace.md)című témakört.
* Ez a cikk azt feltételezi, hogy alapszintű ismerettel rendelkezik egy automatizált gépi tanulási kísérlet beállításával. Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) , vagy az [útmutató](how-to-configure-auto-train.md) segítségével tekintse meg az alapszintű gépi tanulási kísérlet tervezési mintáit.

## <a name="preparing-data"></a>Az adatelőkészítés

Az előrejelzési regressziós feladattípusok és a regressziós feladattípusok közötti legfontosabb különbség az automatizált Machine Learningen belül, beleértve az adatok egy érvényes idősorozatot jelölő funkcióját. A rendszeres idősorozatok jól definiált és konzisztens gyakorisággal rendelkeznek, és minden mintavételi ponton egy értékkel rendelkeznek, amely folyamatos időtartományban van. Vegye figyelembe a fájl `sample.csv`következő pillanatképét.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Ez az adathalmaz egy egyszerű példa arra, hogy egy vállalat napi értékesítési adatforgalma két különböző üzlettel rendelkezik, a és a B. Emellett a funkció `week_of_year` lehetővé teszi, hogy a modell képes legyen a heti szezonális felderíteni. A mező `day_datetime` a napi gyakoriságú tiszta idősorozatot jelöli, a `sales_quantity` mező pedig az előrejelzések futtatásának cél oszlopa. Olvassa el az adatait egy Panda dataframe, majd használja `to_datetime` a függvényt az idősorozat `datetime` típusának biztosításához.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Ebben az esetben az idő mező `day_datetime`alapján az adatgyűjtés már növekvő sorrendbe van rendezve. A kísérlet beállításakor azonban győződjön meg arról, hogy a kívánt idő oszlop növekvő sorrendben van rendezve egy érvényes idősorozat létrehozásához. Tegyük fel, hogy az adatok 1 000-es rekordokat tartalmaznak, és az adatok alapján determinisztikus az adatkészletek képzéséhez és teszteléséhez. Ezután válassza el a cél `sales_quantity` mezőt az előrejelzési vonat és a tesztelési készletek létrehozásához.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> A jövőbeli értékek előrejelzésére szolgáló modell betanításakor győződjön meg arról, hogy a képzésben használt összes funkció használható a kívánt horizonthoz tartozó előrejelzések futtatásakor. Például, ha egy igény-előrejelzést hoz létre, beleértve a jelenlegi tőzsdei árfolyam szolgáltatását is, jelentősen növelheti a képzés pontosságát. Ha azonban hosszú horizontot szeretne előre jelezni, akkor előfordulhat, hogy nem tudja pontosan megjósolni a jövőbeli idősorozat-pontoknak megfelelő készletek értékeit, és a modell pontossága romolhat.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Kísérlet konfigurálása és futtatása

Az előrejelzési feladatokhoz az automatizált gépi tanulás az idősorozat-adatokra vonatkozó előzetes feldolgozási és becslési lépéseket használja. A rendszer a következő előfeldolgozási lépéseket hajtja végre:

* Az idősorozat mintavételi gyakoriságának (például óránkénti, napi, heti) észlelése, és új rekordok létrehozása a hiányzó időpontokhoz, hogy a sorozat folyamatos legyen.
* Hiányzó értékek bevonása a célhelyből (továbbítási kitöltéssel) és a funkció oszlopai (medián Column Values használatával)
* Gabona-alapú szolgáltatások létrehozása a rögzített hatások engedélyezéséhez a különböző adatsorozatok között
* Időalapú szolgáltatások létrehozása a szezonális minták tanulásának segítésére
* Kategorikus változók kódolása numerikus mennyiségre

Az `AutoMLConfig` objektum meghatározza az automatizált gépi tanulási feladatokhoz szükséges beállításokat és adatmennyiséget. A regressziós problémákhoz hasonlóan szabványos betanítási paramétereket is definiálhat, például a feladattípust, az ismétlések számát, a betanítási adatok számát és az eltérő érvényességi értéket. Az előrejelzési feladatokhoz további paramétereket kell megadni, amelyek hatással vannak a kísérletre. Az alábbi táblázat az egyes paramétereket és azok használatát ismerteti.

| Param | Leírás | Kötelező |
|-------|-------|-------|
|`time_column_name`|A dátum-és idősorozatok létrehozásához használt bemeneti adatok datetime oszlopának megadására szolgál.|✓|
|`grain_column_names`|Az egyes adatsorozat-csoportokat meghatározó nevek a bemeneti adatokban. Ha a gabona nincs meghatározva, a rendszer az adathalmazt egy idősorozatra feltételezi.||
|`max_horizon`|Meghatározza a maximálisan kívánatos előrejelzési horizontot a Time-sorozat gyakoriságának egységében. Az egységek a betanítási adatokat tartalmazó időintervallumon alapulnak, például havonta, heti rendszerességgel, amelyet az előrejelzésnek meg kell jósolnia.|✓|
|`target_lags`|*n* időszakok – a modell betanítása előtt továbbítson késési célként megadott értékeket.||
|`target_rolling_window_size`|*n* korábbi időszakok, amelyeket az előre jelzett értékek előállítására használhat, < = betanítási készlet mérete. Ha nincs megadva, az *n* a teljes betanítási készlet mérete.||

Hozzon létre egy idősorozat-beállításokat szótár objektumként. Állítsa az `time_column_name` `day_datetime` értékét a mezőre az adatkészletben. Adja meg `grain_column_names` a paramétert annak biztosításához, hogy **két különálló idősorozat-csoport** legyen létrehozva az adathalmazhoz, egy pedig az a és a `max_horizon` B tárolóhoz. Végül állítsa a 50-re a teljes tesztelési csoport előrejelzéséhez. Állítsa be az előrejelzési ablakot 10 időszakra `target_rolling_window_size`, és a megjelenő értékeket a `target_lags` paraméterrel megelőzően 2 időszakra késlelteti.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

Most hozzon létre `AutoMLConfig` egy standard objektumot, adja `forecasting` meg a feladattípust, és küldje el a kísérletet. A modell befejeződése után kérje le a legjobb futtatási iterációt.

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
> Az önellenőrzési (CV) eljárás esetében az idősoros adatok megsértik a Canonical K-fold – több ellenőrzési stratégia alapszintű statisztikai feltételezéseit, így az automatizált gépi tanulás egy gördülő eredetű ellenőrzési eljárást valósít meg a létrehozáshoz az idősoros adatsorozatok több érvényesítéssel is rendelkeznek. Az eljárás használatához a `n_cross_validations` paramétert az `AutoMLConfig` objektumban kell megadni. Megkerülheti az `X_valid` érvényesítést, és a és `y_valid` a paraméterekkel saját ellenőrzési készleteket is használhat.

### <a name="view-feature-engineering-summary"></a>Szolgáltatások mérnöki összefoglalásának megtekintése

Az automatikus gépi tanulásban az idősoros feladattípusok esetében a funkciók mérnöki folyamatának adatait tekintheti meg. A következő kód az összes nyers funkciót megjeleníti, valamint a következő attribútumokat:

* Nyers szolgáltatás neve
* A nyers szolgáltatásból kialakult mérnöki funkciók száma
* Típus észlelhető
* Azt, hogy a szolgáltatás el lett-e dobva
* A nyers szolgáltatáshoz tartozó szolgáltatás-átalakítások listája

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Előrejelzés a legjobb modellel

Használja a legjobb modell iterációt a tesztelési adatkészletre vonatkozó előrejelzési értékekhez.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Azt is megteheti, `forecast()` hogy a függvényt használja a `predict()`helyett, amely lehetővé teszi a specifikációk használatát a jóslatok elindításához. A következő példában először az összes értéket `y_pred` `NaN`kell lecserélnie a alkalmazásban. Az előrejelzés forrása ebben az esetben a betanítási adatgyűjtési időszak végén lesz, mivel általában a használatakor `predict()`lenne. Ha azonban csak a második felét `y_pred` `NaN`cserélte le, a függvény a numerikus értékeket az első fél változatlanul hagyja, de a második félidőben is `NaN` megbecsüli az értékeket. A függvény az előre jelzett értékeket és az igazított funkciókat is visszaadja.

A `forecast_destination` `forecast()` függvény paraméterét is használhatja az értékek előrejelzésére a megadott dátumig.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Kiszámítja a gyökátlagos (a legfelső szintű négyzetes hibát) a `y_test` tényleges értékek és az előre jelzett `y_pred`értékek között.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Most, hogy a modell teljes pontossága meg lett határozva, a legreálisabb következő lépés a modell használata az ismeretlen jövőbeli értékek előrejelzésére. Egyszerűen adjon meg egy adatkészletet a tesztelési készlettel `X_test` megegyező formátumban, de a jövőbeli dátum és idő értékekkel, és az eredményül kapott előrejelzési készlet az egyes idősorozat-lépések előre jelzett értékei. Tegyük fel, hogy az adatkészletben az utolsó idősorozat rekord a 12/31/2018-es értékre van állítva. Ha a következő napra (vagy az előrejelzéshez szükséges számos időszakra, < = `max_horizon`) szeretne előrejelzést készíteni, hozzon létre egy egyidejű adatsorozat-rekordot a 01/01/2019-es tárolóhoz.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Ismételje meg a szükséges lépéseket a jövőbeli adatok egy dataframe való betöltéséhez `best_run.predict(X_test)` , majd futtassa a parancsot a jövőbeli értékek előrejelzéséhez.

> [!NOTE]
> Nem lehet előre jelezni az értéknél nagyobb `max_horizon`időszakok számát. A modellt újra be kell tanítani, hogy az aktuális horizonton túli jövőbeli értékek előrejelzése nagyobb horizonton történjen.

## <a name="next-steps"></a>További lépések

* Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) , amelyből megtudhatja, hogyan hozhat létre kísérleteket automatizált gépi tanulással.
* Tekintse meg a [Azure Machine learning SDK for Python](https://aka.ms/aml-sdk) dokumentációját.
