---
title: Idősorozat-előrejelzési modell automatikus betanítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Azure Machine Learning segítségével egy idősorozat-előrejelzési regressziós modell automatikus gépi tanulás használatával.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d4e36c0d3838af85768453496a51ecd295c22b93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081845"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Idősorozat-előrejelzési modell automatikus betanítása
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan taníthatja be az idősorozat-előrejelzési regressziós modellt az Azure Machine Learning automatikus gépi tanuláshasználatával. Az előrejelzési modell konfigurálása hasonló a szabványos regressziós modell automatikus gépi tanulás sal történő beállításához, de bizonyos konfigurációs lehetőségek és előfeldolgozási lépések léteznek az idősorozat-adatokkal való munkához. A következő példák bemutatják, hogyan kell:

* Adatok előkészítése az idősorozat-modellezéshez
* Adott idősorozat-paraméterek konfigurálása objektumban [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)
* Előrejelzések futtatása idősorozat-adatokkal

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Az automatizált ml segítségével kombinálhatja a technikákat és a megközelítéseket, és ajánlott, kiváló minőségű idősorozat-előrejelzést kaphat. Az automatizált idősorozat-kísérleteket többváltozós regressziós problémaként kezeli a rendszer. A múltbeli idősorok értékei "elfordulnak", hogy a regresszor további dimenzióivá váljanak más előrejelzőkkel együtt.

Ez a megközelítés, ellentétben a klasszikus idősorozat módszerek, van egy előnye, természetesen magában foglalja a több kontextuális változók és azok kapcsolatát egymással a képzés során. A valós előrejelzési alkalmazásokban több tényező is befolyásolhatja az előrejelzést. Például az eladások előrejelzésekor a korábbi trendek, az árfolyam és az ár kölcsönhatásai együttesen hajtják az értékesítési eredményt. További előny, hogy a regressziós modellek minden közelmúltbeli újítása azonnal az előrejelzésre vonatkozik.

[Beállíthatja,](#config) hogy milyen messze a jövőben az előrejelzés ki kell terjesztenie (az előrejelzési horizont), valamint a késések és így tovább. Az automatikus ml egyetlen, de gyakran belsőleg elágazó modellt tanul az adatkészlet és az előrejelzési horizontok összes eleméhez. Így több adat áll rendelkezésre a modellparamétereinek becsléséhez, és lehetővé válik a láthatatlan adatsorok általánosítása.

A betanítási adatokból kinyert funkciók kritikus szerepet játszanak. Az automatizált ml pedig szabványos előfeldolgozási lépéseket hajt végre, és további idősorozat-funkciókat hoz létre a szezonális hatások rögzítéséhez és a prediktív pontosság maximalizálásához.

## <a name="time-series-and-deep-learning-models"></a>Idősorozatok és Deep Learning modellek


Az automatikus ml az ajánlási rendszer részeként natív idősorozatokat és mélytanulási modelleket is biztosít a felhasználóknak. Ezek a tanulók a következők:
+ Próféta
+ Auto-ARIMA
+ ElőrejelzésTCN

Az automatizált ML mélytanulása lehetővé teszi az egyváltozós és többváltozós idősorozat-adatok előrejelzését.

A mélytanulási modellek három belső képességgel rendelkeznek:
1. A bemenetektől a kimenetekig tetszőleges hozzárendelésekből tanulhatnak
1. Több bemenetet és kimenetet támogatnak
1. Ezek automatikusan kinyerhetik a bemeneti adatok mintáit, amelyek hosszú szekvenciákra terjednek ki

Adott nagyobb adatok, deep learning modellek, például a Microsoft ForecastTCN, javíthatja a pontszámok az eredményül kapott modell. 

Natív idősorozat tanulók is biztosított részeként automatizált ML. A Próféta olyan idősorozatokkal működik a legjobban, amelyeknek erős szezonális hatásaik és több évadnyi történelmi adatuk van. A Prophet pontos & gyors, robusztus és kiugró, hiányzó adatok és drámai változások az idősorozatodban. 

Az autoregresszív integrált mozgóátlag (ARIMA) az idősorok előrejelzésének népszerű statisztikai módszere. Ezt az előrejelzési technikát általában rövid távú előrejelzési forgatókönyvekben használják, ahol az adatok olyan trendek bizonyítékait mutatják, mint például a ciklusok, amelyek kiszámíthatatlanok és nehezen modellezhetők vagy előre jelezhetők. Az Auto-ARIMA az adatokat helyhez kötött adatokká alakítja, hogy konzisztens, megbízható eredményeket kapjon.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásáról az [Azure Machine Learning-munkaterület létrehozása című témakörben található.](how-to-manage-workspace.md)
* Ez a cikk egy automatizált gépi tanulási kísérlet beállításának alapvető ismereteit feltételezi. Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) vagy az [útmutatót](how-to-configure-auto-train.md) az alapvető automatizált gépi tanulási kísérlet tervezési minták megtekintéséhez.

## <a name="preparing-data"></a> Az adatok előkészítése

A legfontosabb különbség egy előrejelzési regressziós feladat típusa és regressziós feladat típusa az automatizált gépi tanulás, amely magában egy szolgáltatás az adatok, amely egy érvényes idősorozat. A normál idősorozatok jól meghatározott és konzisztens gyakorisággal rendelkeznek, és folyamatos időtartam minden mintavételi pontján értékkel rendelkeznek. Vegye figyelembe a fájl `sample.csv`következő pillanatképét .

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

Ez az adatkészlet egy egyszerű példa a napi értékesítési adatok egy vállalat, amely két különböző üzletek, A és B. Emellett van egy funkció, `week_of_year` amely lehetővé teszi a modell számára, hogy észlelje a heti szezonalitás. A `day_datetime` mező egy tiszta idősort jelöl napi `sales_quantity` gyakorisággal, és a mező az előrejelzések futtatásának céloszlopa. Olvassa be az adatokat egy Pandas `to_datetime` adatkeretbe, majd `datetime` használja a függvényt annak biztosítására, hogy az idősorozat egy típus legyen.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Ebben az esetben az adatok már növekvő sorrendben `day_datetime`vannak az időmező szerint. A kísérlet beállításakor azonban győződjön meg arról, hogy a kívánt időoszlop növekvő sorrendbe van rendezve egy érvényes idősorozat létrehozásához. Tegyük fel, hogy az adatok 1000 rekordot tartalmaznak, és az adatok at determinisztikus felosztással hoznak létre betanítási és tesztelési adatkészletek létrehozásához. Azonosítsa a címkeoszlop nevét, és állítsa a feliratra. Ebben a példában a `sales_quantity`címke lesz . Ezután válassza el `test_data` a `test_target` feliratmezőt a készlet létrehozásához.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> A jövőbeli értékek előrejelzésére szolgáló modell betanításakor győződjön meg arról, hogy a betanításhoz használt összes funkció használható a tervezett horizonton történő előrejelzések futtatásakor. Például egy igény-előrejelzés létrehozásakor, beleértve az aktuális tőzsdei árfolyam jellemzőt is, jelentősen növelheti a betanítás pontosságát. Ha azonban hosszú horizonton kíván előre jelezni, előfordulhat, hogy nem tudja pontosan megjósolni a jövőbeli idősorozat-pontoknak megfelelő jövőbeli készletértékeket, és a modell pontossága szenvedhet.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Kísérlet konfigurálása és futtatása

Az előrejelzési feladatokhoz az automatizált gépi tanulás az idősorozat-adatokra jellemző előfeldolgozási és becslési lépéseket használja. A következő előfeldolgozási lépések lesznek végrehajtva:

* Az idősorozat-minta gyakoriságának észlelése (például óránkénti, napi, heti) és új rekordok létrehozása a hiányzó időpontokhoz, hogy az adatsor folyamatos legyen.
* Impute hiányzó értékek a cél (keresztül előre kitöltés) és a funkció oszlopok (a medián oszlop értékek)
* Szemcsealapú funkciók létrehozása a rögzített hatások engedélyezéséhez a különböző sorozatok között
* Időalapú funkciók létrehozása a szezonális minták tanulásának elősegítésére
* Kategorikus változók kódolása numerikus mennyiségekre

Az [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) objektum határozza meg az automatikus gépi tanulási feladathoz szükséges beállításokat és adatokat. A regressziós problémához hasonlóan szabványos betanítási paramétereket is definiál, például feladattípust, iterációk számát, betanítási adatokat és keresztellenőrzések számát. Az előrejelzési feladatokhoz további paramétereket kell beállítani, amelyek hatással vannak a kísérletre. Az alábbi táblázat ismerteti az egyes paramétereket és azok használatát.

| Paraméter&nbsp;neve | Leírás | Kötelező |
|-------|-------|-------|
|`time_column_name`|Az idősorok létrehozásához és gyakoriságának meghatározásához használt bemeneti adatok dátumidő oszlopának megadására szolgál.|✓|
|`grain_column_names`|A bemeneti adatokban az egyes adatsorok csoportjait meghatározó név(ek). Ha a szemcse nincs definiálva, az adatkészlet egy idősorozatnak minősül.||
|`max_horizon`|A kívánt maximális előrejelzési horizontot határozza meg az idősorozat-gyakoriság egységeiben. Az egységek a betanítási adatok időintervallumán alapulnak, például havonta, hetente, amelyet az előrejelzőnek előre kell jeleznie.|✓|
|`target_lags`|A célértékektől az adatok gyakorisága alapján késleltetni a sorok száma. A lag listaként vagy egy egész számként jelenik meg. Késés akkor kell használni, ha a kapcsolat a független változók és a függő változók nem egyezik meg, vagy alapértelmezés szerint korrelál. Például, amikor megpróbálja előre jelezni a kereslet egy termék, a kereslet minden hónapban függhet az ár az egyes áruk 3 hónappal korábban. Ebben a példában érdemes lehet a cél (igény) negatívan 3 hónappal, hogy a modell a megfelelő kapcsolat betanítása.||
|`target_rolling_window_size`|*n* az előre jelzett értékek létrehozásához használt előzményidőszakok, <= betanítási halmaz mérete. Ha nincs megadva, *n* a teljes betanítási készlet mérete. Adja meg ezt a paramétert, ha csak egy bizonyos mennyiségű előzményt szeretne figyelembe venni a modell betanításakor.||
|`enable_dnn`|Engedélyezze az előrejelzési DNN-eket.||

További információt a [referenciadokumentációban](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) talál.

Az idősorozat-beállításokat szótárobjektumként hozza létre. Állítsa `time_column_name` be `day_datetime` a mezőt az adatkészletben. Adja `grain_column_names` meg a paramétert annak érdekében, hogy **két külön idősorozat-csoport** jön létre az adatokhoz; egyet az A és B üzlethez. `max_horizon` Állítsa be az előrejelzési ablakot 10 időszakra a lehetőséggel, `target_rolling_window_size`és adjon `target_lags` meg egyetlen késést a célértékeken két, a paraméterrel előrébb lévő időszakra. Javasoljuk, hogy `max_horizon`állítsa `target_rolling_window_size` `target_lags` be , és az "automatikus", amely automatikusan felismeri ezeket az értékeket az Ön számára. Az alábbi példában az "automatikus" beállításokat használták ezekhez a paraméterekhez. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozási lépései (funkciónormalizálás, hiányzó adatok kezelése, szöveg átalakítása numerikussá stb.) az alapul szolgáló modell részévé válnak. Ha a modell t használja az előrejelzések, ugyanazokat az előzetes feldolgozási lépéseket alkalmazott betanítása során automatikusan alkalmazza a bemeneti adatokat.

A fenti `grain_column_names` kódrészlet meghatározásával az AutoML két külön idősorozat-csoportot hoz létre, más néven több idősort. Ha nincs megadva szemcse, az AutoML azt feltételezi, hogy az adatkészlet egyetlen idősorozat. Ha többet szeretne megtudni az egyes idősorozatokról, olvassa el az [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Most hozzon `AutoMLConfig` létre egy `forecasting` szabványos objektumot, adja meg a feladat típusát, és küldje el a kísérletet. A modell befejezése után olvassa be a legjobb futtatási ismétlést.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Tekintse meg az [előrejelzési minta notebook részletes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) kód példák at speciális előrejelzési konfiguráció, beleértve:

* [ünnep felismerése és featurization](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [gördülő eredetű keresztérvényesítés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurálható késések](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [gördülőablak összesítő szolgáltatásai](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>DNN-előrejelzési kísérlet engedélyezése

> [!NOTE]
> Az automatikus gépi tanulás ban az előrejelzés DNN-támogatása előzetes verzióban érhető el, és nem támogatott a helyi futtatások esetében.

Annak érdekében, hogy a DNN-ek az `enable_dnn` előrejelzés, be kell állítania a paramétert az AutoMLConfig true. 

Azt javasoljuk, hogy egy AML számítási fürt GPU-skus és legalább két csomópont ot a számítási cél. Annak érdekében, hogy elegendő idő legyen a DNN-betanítás befejezéséhez, javasoljuk, hogy a kísérlet időidejét legalább néhány órára határozza meg.
A GPU-kat tartalmazó AML számítási és virtuális gépméretekkel kapcsolatos további információkért tekintse meg az [AML compute dokumentációt](how-to-set-up-training-targets.md#amlcompute) és a [GPU-ra optimalizált virtuálisgép-méretek dokumentációját.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)

Tekintse meg az [italgyártás-előrejelzési jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) egy részletes kódpéldául a DNN-ek kihasználásához.

### <a name="view-feature-engineering-summary"></a>Szolgáltatástechnikai összegzés megtekintése

Az automatikus gépi tanulás idősorozat-feladattípusaihoz megtekintheti a szolgáltatástervezési folyamat részleteit. A következő kód az egyes nyers funkciókat és a következő attribútumokat mutatja:

* Nyers szolgáltatás neve
* A nyers funkcióból kialakított mesterséges funkciók száma
* Típus észlelve
* A szolgáltatás eldobásának e
* A nyers szolgáltatás szolgáltatásátalakításának listája

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Előrejelzés a legjobb modellel

Használja a legjobb modell iteráció a tesztadatkészlet értékeinek előrejelzéséhez.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Másik lehetőségként használhatja `forecast()` a `predict()`függvényhelyett . A következő példában `y_pred` először az `NaN`összes értéket a helyére cseréli. Az előrejelzés eredete ebben az esetben a betanítási adatok végén `predict()`lesz, ahogy az a használata során általában lenne. Ha azonban csak a második felét `y_pred` `NaN`cseréli le a programra, a függvény az első félidőben változatlanul hagyja a numerikus értékeket, de az `NaN` értékeket a második felében előre jelezi. A függvény az előre jelzett értékeket és az igazított jellemzőket is visszaadja.

A függvényben `forecast_destination` lévő paraméter `forecast()` segítségével egy megadott dátumig is előre jelezheti az értékeket.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Számítsa ki az RMSE-t `actual_labels` (négyzetes négyzetes négyzetes hibát) a tényleges értékek és az előre jelzett értékek között a alkalmazásban. `predict_labels`

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Most, hogy a modell általános pontossága meg van határozva, a legreálisabb következő lépés az ismeretlen jövőbeli értékek előrejelzésére a modell használata. Adja meg az adathalmazt a `test_data` tesztkészlettel megegyező formátumban, de a jövőbeli dátumidővel, és az eredményül kapott előrejelzési készlet az egyes idősorozat-lépcsők előre jelzett értékei. Tegyük fel, hogy az adatkészlet utolsó idősorozatrekordjai 2018.12.31.Tegyük fel, hogy az adatkészlet utolsó idősorozatrekordjai 2018.12/31/2018. A következő napra vonatkozó igény előrejelzéséhez (vagy annyi időszakhoz, `max_horizon`amennyit előre kell jeleznie, <= ) hozzon létre egy idősorozat-rekordot minden egyes üzlethez 2019.01.01.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Ismételje meg a szükséges lépéseket a jövőbeli adatok `best_run.predict(test_data)` adatkeretbe való betöltéséhez, majd futtassa a jövőbeli értékek előrejelzéséhez.

> [!NOTE]
> Az értéknél nagyobb időszakokra nem lehet `max_horizon`előre jelezni az értékeket. A modellt újra be kell tanítani egy nagyobb horizonttal, hogy a jövőbeli értékeket a jelenlegi horizonton túl is előre lehessen jelezni.

## <a name="next-steps"></a>További lépések

* Az [oktatóanyagból](tutorial-auto-train-models.md) megtudhatja, hogyan hozhat létre kísérleteket az automatizált gépi tanulással.
* Tekintse meg az [Azure Machine Learning SDK Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) referencia dokumentáció.
