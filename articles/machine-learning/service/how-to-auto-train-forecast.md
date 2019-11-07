---
title: Idősorozat-előrejelzési modell automatikus betanítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható a Azure Machine Learning egy idősorozat-előrejelzési regressziós modell automatikus gépi tanulással történő betanításához.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 276e741a9462c19a3cba9ad1f9ac44e2da7ef1d3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580707"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Idősorozat-előrejelzési modell automatikus betanítása
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan végezheti el az idősorozat-előrejelzési regressziós modell betanítását a Azure Machine Learning automatikus gépi tanulásával. Az előrejelzési modell konfigurálása hasonló ahhoz, hogy szabványos regressziós modellt állítson be automatizált gépi tanulással, de bizonyos konfigurációs beállítások és előfeldolgozási lépések léteznek az idősorozat-információkkal való munkavégzéshez. Az alábbi példák a következőket mutatják be:

* Az idősorozat-modellezéssel kapcsolatos adatelőkészítés
* Adott idősorozat-paraméterek konfigurálása egy [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) objektumban
* Előrejelzések futtatása idősoros adattal

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Az automatikus ML-vel kombinálhatja a technikákat és a megközelítéseket, és egy ajánlott, magas színvonalú idősorozat-előrejelzést is igénybe vehet. Az automatikus idősorozat-kísérletet többváltozós regressziós problémaként kezeli a rendszer. A korábbi idősorozat-értékek "Pivotal", hogy további dimenziókat regressor a többi előrejelzővel együtt.

Ez a klasszikus idősorozat-módszerekkel ellentétben az egyik előnye, hogy természetesen több kontextusos változót is magában foglal, és a képzés során egymáshoz fűződő kapcsolataikat. A valós idejű előrejelzési alkalmazásokban több tényező is befolyásolhatja az előrejelzést. Például az értékesítések előrejelzése, a múltbeli trendek, az árfolyam és az ár interakciója együttesen hajtja végre az értékesítés eredményét. További előny, hogy a regressziós modellek legújabb innovációi azonnal érvénybe kerülnek az előrejelzések alapján.

[Megadhatja, hogy a](#config) jövőben milyen mértékben kell kiterjesztenie az előrejelzést (az előrejelzési horizontot), valamint a késéseket és egyebeket. Az automatizált ML egyetlen, de gyakran belsőleg elágazó modellt tanul az adatkészlet összes eleméhez és előrejelzési horizontokhoz. Így több adat érhető el a modell paramétereinek becsléséhez és az általánosításhoz, hogy a láthatatlan adatsorozatok elérhetővé válnak.

A betanítási adatokból kinyert funkciók kritikus szerepet játszanak. Az automatizált ML standard előfeldolgozási lépéseket hajt végre, és további idősorozat-funkciókat hoz létre az idényjellegű hatások rögzítése és a prediktív pontosság maximalizálása érdekében.

## <a name="time-series-and-deep-learning-models"></a>Idősorozatok és mély tanulási modellek


Az automatikus ML lehetővé teszi, hogy a felhasználók natív idősorozatú és mély tanulási modelleket is biztosítanak a javaslatrendszer részeként. Ezek a tanulók a következők:
+ Próféta
+ Automatikus ARIMA
+ ForecastTCN

Az automatizált ML mély tanulási funkciói lehetővé teszik az előrejelzési univariate és a többváltozós idősorozatok adatsorait.

A Deep learning-modellek három belső capbailities rendelkeznek:
1. Tanulhatnak a bemenetekről a kimenetekre való tetszőleges leképezésekről
1. Több bemenetet és kimenetet támogatnak
1. Képesek automatikusan kinyerni a hosszú sorokra kiterjedő bemeneti adatok mintáit

A nagyobb adatmennyiségek, a Deep learning-modellek, például a Microsofts ForecastTCN növelhetik az eredményül kapott modell pontszámait. 

A natív idősorozat-tanulók az automatizált ML részeként is elérhetők. A próféta a legjobb idősorozattal működik, amely erős szezonális hatásokat és több időszakot is tartalmaz. A próféta pontos & gyors, robusztus kiugró, hiányzó adatokat és drámai változásokat tartalmaz az idősorozatban. 

A AutoRegressive integrált mozgóátlag (ARIMA) egy népszerű statisztikai módszer az idősorozat-előrejelzéshez. Ezt az előrejelzési technikát gyakran használják rövid távú előrejelzési forgatókönyvekben, ahol az adatok olyan trendeket mutatnak, mint például a ciklusok, amelyek kiszámíthatatlanok és megnehezítik a modellezést vagy az előrejelzést. Az automatikus ARIMA úgy alakítja át az adatait, hogy az konzisztens és megbízható eredményeket kapjon.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásával kapcsolatban tekintse meg [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md)című témakört.
* Ez a cikk azt feltételezi, hogy alapszintű ismerettel rendelkezik egy automatizált gépi tanulási kísérlet beállításával. Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) , vagy az [útmutató](how-to-configure-auto-train.md) segítségével tekintse meg az alapszintű gépi tanulási kísérlet tervezési mintáit.

## <a name="preparing-data"></a>Az adatelőkészítés

Az előrejelzési regressziós feladattípusok és a regressziós feladattípusok közötti legfontosabb különbség az automatizált Machine Learningen belül, beleértve az adatok egy érvényes idősorozatot jelölő funkcióját. A rendszeres idősorozatok jól definiált és konzisztens gyakorisággal rendelkeznek, és minden mintavételi ponton egy értékkel rendelkeznek, amely folyamatos időtartományban van. Vegye figyelembe egy fájl következő pillanatképét `sample.csv`.

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

Ez az adathalmaz egy egyszerű példa arra, hogy egy vállalat napi értékesítési adatforgalma két különböző üzlettel rendelkezik, A és A B. Emellett van egy olyan szolgáltatás is a `week_of_year` számára, amely lehetővé teszi a modell számára, hogy észlelje a heti szezonális munkavégzést. A mező `day_datetime` a napi gyakoriságú tiszta idősorozatot jelöli, és a mező `sales_quantity` a cél oszlop az előrejelzések futtatásához. Olvassa el az adatait egy Panda dataframe, majd az `to_datetime` függvény használatával győződjön meg arról, hogy az idősorozat `datetime` típusú.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Ebben az esetben az adatmezők már növekvő sorrendbe vannak rendezve `day_datetime`. A kísérlet beállításakor azonban győződjön meg arról, hogy a kívánt idő oszlop növekvő sorrendben van rendezve egy érvényes idősorozat létrehozásához. Tegyük fel, hogy az adatok 1 000-es rekordokat tartalmaznak, és az adatok alapján determinisztikus az adatkészletek képzéséhez és teszteléséhez. Azonosítsa a címke oszlop nevét, és állítsa a címkére. Ebben a példában a címke `sales_quantity`lesz. Ezután válassza el `test_data` a Label (címke) mezőt a `test_target` készlet létrehozásához.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
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

A `AutoMLConfig` objektum az automatizált gépi tanulási feladatokhoz szükséges beállításokat és adatmennyiséget határozza meg. A regressziós problémákhoz hasonlóan szabványos betanítási paramétereket is definiálhat, például a feladattípust, az ismétlések számát, a betanítási adatok számát és az eltérő érvényességi értéket. Az előrejelzési feladatokhoz további paramétereket kell megadni, amelyek hatással vannak a kísérletre. Az alábbi táblázat az egyes paramétereket és azok használatát ismerteti.

| Param | Leírás | Kötelező |
|-------|-------|-------|
|`time_column_name`|A dátum-és idősorozatok létrehozásához használt bemeneti adatok datetime oszlopának megadására szolgál.|✓|
|`grain_column_names`|Az egyes adatsorozat-csoportokat meghatározó nevek a bemeneti adatokban. Ha a gabona nincs meghatározva, a rendszer az adathalmazt egy idősorozatra feltételezi.||
|`max_horizon`|Meghatározza a maximálisan kívánatos előrejelzési horizontot a Time-sorozat gyakoriságának egységében. Az egységek a betanítási adatokat tartalmazó időintervallumon alapulnak, például havonta, heti rendszerességgel, amelyet az előrejelzésnek meg kell jósolnia.|✓|
|`target_lags`|A megcélzott értékeket az adatok gyakorisága alapján késleltető sorok száma. Ez listaként vagy egyetlen egész számként jelenik meg. A késést akkor kell használni, ha a független változók és a függő változó közötti kapcsolat alapértelmezés szerint nem felel meg egymásnak. Ha például egy termék iránti keresletre próbál előrejelzést kérni, a havi igény bármelyik hónapra az adott árucikkek előző 3 hónapjának árával függ. Ebben a példában előfordulhat, hogy 3 hónap elteltével negatívan szeretné megtekinteni a célt (keresletet), hogy a modell a megfelelő kapcsolaton legyen betanítva.||
|`target_rolling_window_size`|*n* korábbi időszakok, amelyeket az előre jelzett értékek előállítására használhat, < = betanítási készlet mérete. Ha nincs megadva, az *n* a teljes betanítási készlet mérete. Akkor válassza ezt a paramétert, ha csak bizonyos mennyiségű előzményt szeretne figyelembe venni a modell betanításakor.||
|`enable_dnn`|Előrejelzési DNN engedélyezése.||

További információt a [dokumentációban](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) talál.

Hozzon létre egy idősorozat-beállításokat szótár objektumként. Állítsa a `time_column_name` az adathalmaz `day_datetime` mezőjére. Adja meg a `grain_column_names` paramétert annak biztosításához, hogy **két különálló idősorozat-csoport** legyen létrehozva az adatsorokhoz; az A és B tároló egyike. Végül állítsa a `max_horizon` 50-re, hogy a teljes tesztelési készletet előre meg lehessen jósolni. Állítsa be az előrejelzési időszakot 10, `target_rolling_window_size`értékkel rendelkező időszakra, és az `target_lags` paraméterrel előrébb álló két időszakra vonatkozóan egyetlen késést határozzon meg.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

A fenti kódrészletben szereplő `grain_column_names` definiálásával a AutoML két különálló idősorozat-csoportot hoz létre, más néven több idősorozatot. Ha nincs megadva gabona, a AutoML azt feltételezi, hogy az adatkészlet egy egyidejű adatsorozat. Ha többet szeretne megtudni az egyidejű adatsorozatokról, tekintse meg a [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Most hozzon létre egy standard `AutoMLConfig` objektumot, adja meg a `forecasting` feladattípust, és küldje el a kísérletet. A modell befejeződése után kérje le a legjobb futtatási iterációt.

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

Tekintse meg az [energia igényét bemutató notebookot](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) a speciális előrejelzési konfigurációk részletes kódjával kapcsolatban, beleértve a következőket:

* üdülés észlelése és featurization
* gördülő-eredetű kereszt-ellenőrzés
* konfigurálható késések
* a gördülő ablak összesített funkciói

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>DNN-előrejelzési kísérlet engedélyezése

> [!NOTE]
> Az automatikus Machine Learning DNN támogatása előzetes verzióban érhető el.

Az előrejelzési DNN kihasználása érdekében `enable_dnn` a AutoMLConfig paramétert True értékre kell állítani a. 

A DNN használatához javasoljuk, hogy használjon olyan pénzmosás-számítási fürtöt, amely GPU SKU-ket és legalább 2 csomópontot használ számítási célként. További információkért tekintse meg a [pénzmosás-számítás dokumentációját](how-to-set-up-training-targets.md#amlcompute) . A GPU-ket tartalmazó virtuálisgép-méretekkel kapcsolatos további információkért lásd a GPU-ra [optimalizált virtuális gépek méretét](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) .

Ahhoz, hogy elegendő idő legyen a DNN képzés befejezésére, javasoljuk, hogy a kísérlet időtúllépését legalább néhány óráig állítsa be.

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
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Azt is megteheti, hogy `predict()`helyett a `forecast()` függvényt használja, amely lehetővé teszi a specifikációk megkezdését. Az alábbi példában a `y_pred` az összes értéket lecseréli a `NaN`. Ebben az esetben az előrejelzési forrás a betanítási adatgyűjtési időszak végén fog megjelenni, mivel ez általában a `predict()`használatakor lenne. Ha azonban csak a `y_pred` második felét cserélte le `NaN`, akkor a függvény a numerikus értékeket az első fél változatlanul hagyja, de a második felében előre megbecsüli a `NaN` értékeket. A függvény az előre jelzett értékeket és az igazított funkciókat is visszaadja.

A `forecast()` függvény `forecast_destination` paraméterét is használhatja az értékek előrejelzésére a megadott dátumig.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Kiszámítja a GYÖKÁTLAGOS (a legfelső szintű négyzetes hibát) a `actual_labels` tényleges értékei és a `predict_labels`előrejelzett értékei között.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_lables, predict_labels))
rmse
```

Most, hogy a modell teljes pontossága meg lett határozva, a legreálisabb következő lépés a modell használata az ismeretlen jövőbeli értékek előrejelzésére. Egyszerűen adjon meg egy adatkészletet a tesztelési készlet `test_data`, de a jövőbeli datetime értékekkel megegyező formátumban, és az eredményül kapott előrejelzési készlet az egyes idősorozat-lépések előre jelzett értékei. Tegyük fel, hogy az adatkészletben az utolsó idősorozat rekord a 12/31/2018-es értékre van állítva. Ha a következő napra (vagy az előrejelzéshez szükséges számos időszakra vonatkozóan szeretne előrejelzést készíteni, < = `max_horizon`), hozzon létre egy egyidejű adatsorozat-rekordot a 01/01/2019-es tárolóhoz.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Ismételje meg a szükséges lépéseket a jövőbeli adatok dataframe való betöltéséhez, majd `best_run.predict(test_data)` futtatásához a jövőbeli értékek előrejelzéséhez.

> [!NOTE]
> Nem lehet előre jelezni a `max_horizon`nál nagyobb időszakok számának értékét. A modellt újra be kell tanítani, hogy az aktuális horizonton túli jövőbeli értékek előrejelzése nagyobb horizonton történjen.

## <a name="next-steps"></a>További lépések

* Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) , amelyből megtudhatja, hogyan hozhat létre kísérleteket automatizált gépi tanulással.
* Tekintse meg a [Azure Machine learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) dokumentációját.
