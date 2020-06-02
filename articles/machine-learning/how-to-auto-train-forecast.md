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
ms.date: 03/09/2020
ms.openlocfilehash: bfb53893031300926944ca97a760aec199f699c0
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266440"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Idősorozat-előrejelzési modell automatikus betanítása
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan konfigurálhat és betaníthat egy idősorozat-előrejelző regressziós modellt a [Azure Machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)-ban található automatikus gépi tanulás használatával. 

Az alacsony kódú felhasználói élményért tekintse meg a következő [oktatóanyagot:](tutorial-automated-ml-forecast.md) az automatikus gépi tanulással kapcsolatos előrejelzési igények az automatikus gépi tanulással a [Azure Machine learning Studióban](https://ml.azure.com/).

Az előrejelzési modell konfigurálása hasonló ahhoz, hogy szabványos regressziós modellt állítson be automatizált gépi tanulással, de bizonyos konfigurációs beállítások és előfeldolgozási lépések léteznek az idősorozat-információkkal való munkavégzéshez. 

Beállíthatja például, hogy a jövőben az előrejelzésnek [milyen mértékben](#config) kell kiterjesztenie (az előrejelzési horizonton), valamint a késéseket és egyebeket. Az automatizált ML egyetlen, de gyakran belsőleg elágazó modellt tanul az adatkészlet összes eleméhez és előrejelzési horizontokhoz. Így több adat érhető el a modell paramétereinek becsléséhez és az általánosításhoz, hogy a láthatatlan adatsorozatok elérhetővé válnak.

Az alábbi példák a következőket mutatják be:

* Az idősorozat-modellezéssel kapcsolatos adatelőkészítés
* Adott idősorozat-paraméterek konfigurálása egy [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objektumban
* Előrejelzések futtatása idősoros adattal

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

A klasszikus idősorozat-módszerekkel ellentétben az automatikus ML-ben a korábbi idősorozat-értékek "csuklós", hogy további dimenziókat regressor a többi előrejelzővel együtt. Ez a megközelítés több kontextusbeli változót is magában foglal, és a képzés során egymáshoz fűződő kapcsolatukat. Mivel több tényező is befolyásolhatja az előrejelzést, ez a módszer jól illeszkedik a valós előrejelzési forgatókönyvekhez. Például az értékesítések előrejelzése, a múltbeli trendek, az árfolyam és az ár interakciója együttesen hajtja végre az értékesítés eredményét. 

A betanítási adatokból kinyert funkciók kritikus szerepet játszanak. Az automatizált ML standard előfeldolgozási lépéseket hajt végre, és további idősorozat-funkciókat hoz létre az idényjellegű hatások rögzítése és a prediktív pontosság maximalizálása érdekében.

## <a name="time-series-and-deep-learning-models"></a>Idősorozatok és mély tanulási modellek

Az automatizált ML mély tanulási funkciói lehetővé teszik az előrejelzési univariate és a többváltozós idősorozatok adatsorait.

A Deep learning-modellek három belső képességgel rendelkeznek:
1. Tanulhatnak a bemenetekről a kimenetekre való tetszőleges leképezésekről
1. Több bemenetet és kimenetet támogatnak
1. Képesek automatikusan kinyerni a hosszú sorokra kiterjedő bemeneti adatok mintáit

A nagyobb adatmennyiségek, a mély tanulási modellek, például a Microsoft ForecastTCN javíthatják az eredményül kapott modell pontszámait. Ismerje meg, hogyan [konfigurálhatja a kísérletet a Deep learning szolgáltatáshoz](#configure-a-dnn-enable-forecasting-experiment).

Az automatikus ML lehetővé teszi, hogy a felhasználók natív idősorozatú és mély tanulási modelleket is biztosítanak a javaslatrendszer részeként. 

Modellek| Leírás | Előnyök
----|----|---
Próféta (előzetes verzió)|A próféta a legjobb idősorozattal működik, amely erős szezonális hatásokat és több időszakot is tartalmaz. A modell kihasználása érdekében telepítse helyileg a használatával `pip install fbprophet` . | Pontos & gyors, robusztus a kiugró értékek, a hiányzó adatmennyiségek és az idősorozat drámai változásai.
Automatikus ARIMA (előzetes verzió)|A AutoRegressive integrált mozgóátlag (ARIMA) a legjobbat hajtja végre, ha az adatok állomáson vannak. Ez azt jelenti, hogy a statisztikai tulajdonságok, például a középérték és a variancia állandó a teljes készleten. Ha például egy érme tükrözését hajtja végre, akkor a fejek beszerzésének valószínűsége 50%, függetlenül attól, hogy a mai, a holnapi vagy a jövő évi tükrözést szeretné-e megtekinteni.| Kiválóan használható a univariate sorozatokhoz, mivel a korábbi értékeket a jövőbeli értékek előrejelzésére használjuk.
ForecastTCN (előzetes verzió)| A ForecastTCN egy olyan neurális hálózati modell, amely a legigényesebb előrejelzési feladatok kezelésére, a nem lineáris helyi és globális trendek rögzítésére szolgál az adatokban, valamint az idősorozatok közötti kapcsolatokat.|Képes az adathalmazok összetett trendjeinek kihasználása és a nagy adatkészletek rugalmas méretezésére.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásával kapcsolatban tekintse meg [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md)című témakört.
* Ez a cikk azt feltételezi, hogy alapszintű ismerettel rendelkezik egy automatizált gépi tanulási kísérlet beállításával. Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) , vagy az [útmutató](how-to-configure-auto-train.md) segítségével tekintse meg az alapszintű gépi tanulási kísérlet tervezési mintáit.

## <a name="preparing-data"></a> Az adatok előkészítése

Az előrejelzési regressziós feladattípusok és a regressziós feladattípusok közötti legfontosabb különbség az automatizált Machine Learningen belül, beleértve az adatok egy érvényes idősorozatot jelölő funkcióját. A rendszeres idősorozatok jól definiált és konzisztens gyakorisággal rendelkeznek, és minden mintavételi ponton egy értékkel rendelkeznek, amely folyamatos időtartományban van. Vegye figyelembe a fájl következő pillanatképét `sample.csv` .

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

Ez az adathalmaz egy egyszerű példa arra, hogy egy vállalat napi értékesítési adatforgalma két különböző üzlettel rendelkezik, A és A B. Emellett a funkció `week_of_year` lehetővé teszi, hogy a modell képes legyen a heti szezonális felderíteni. A mező a `day_datetime` napi gyakoriságú tiszta idősorozatot jelöli, a mező `sales_quantity` pedig az előrejelzések futtatásának cél oszlopa. Olvassa el az adatait egy Panda dataframe, majd használja a `to_datetime` függvényt az idősorozat `datetime` típusának biztosításához.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Ebben az esetben az adatmező már növekvő sorrendbe rendezi az adathalmazt `day_datetime` . A kísérlet beállításakor azonban győződjön meg arról, hogy a kívánt idő oszlop növekvő sorrendben van rendezve egy érvényes idősorozat létrehozásához. Tegyük fel, hogy az adatok 1 000-es rekordokat tartalmaznak, és az adatok alapján determinisztikus az adatkészletek képzéséhez és teszteléséhez. Azonosítsa a címke oszlop nevét, és állítsa a címkére. Ebben a példában a címke a következő lesz: `sales_quantity` . Ezután válassza el a Label (címke) mezőt a `test_data` készlet létrehozásához `test_target` .

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> A jövőbeli értékek előrejelzésére szolgáló modell betanításakor győződjön meg arról, hogy a képzésben használt összes funkció használható a kívánt horizonthoz tartozó előrejelzések futtatásakor. Például, ha egy igény-előrejelzést hoz létre, beleértve a jelenlegi tőzsdei árfolyam szolgáltatását is, jelentősen növelheti a képzés pontosságát. Ha azonban hosszú horizontot szeretne előre jelezni, akkor előfordulhat, hogy nem tudja pontosan megjósolni a jövőbeli idősorozat-pontoknak megfelelő készletek értékeit, és a modell pontossága romolhat.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Betanítási és érvényesítési adatkészletek
A konstruktorban külön betanítási és érvényesítési készletek is megadhatók `AutoMLConfig` .

### <a name="rolling-origin-cross-validation"></a>Gördülő forrás – több ellenőrzés
Az idősorozat-előrejelzés működés közbeni eredetének ellenőrzéséhez (ROCV) az idősorozatok átmeneti konzisztens módon való felosztására szolgálnak. A ROCV a sorozatot az oktatási és érvényesítési adatpontok alapján osztja el. A forrás időbeli kidobása a kereszt-érvényesítési betöltést eredményezi.  

![helyettesítő szöveg](./media/how-to-auto-train-forecast/ROCV.svg)

Ez a stratégia megőrzi az idősorozat-adatok integritását, és megszünteti az adatok szivárgásának kockázatát. A ROCV automatikusan az előrejelzési feladatok elvégzéséhez használja a betanítási és érvényesítési adatok összeadásával, valamint a több érvényesítési folyamatnak a használatával történő beállításával `n_cross_validations` . Tudjon meg többet arról, hogy az automatikus ML hogyan alkalmazza a kereszt-ellenőrzést, hogy [megakadályozza a túlzottan illeszkedő modelleket](concept-manage-ml-pitfalls.md#prevent-over-fitting).

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
További információ a [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Kísérlet konfigurálása és futtatása

Az előrejelzési feladatokhoz az automatizált gépi tanulás az idősorozat-adatokra vonatkozó előzetes feldolgozási és becslési lépéseket használja. A rendszer a következő előfeldolgozási lépéseket hajtja végre:

* Az idősorozat mintavételi gyakoriságának (például óránkénti, napi, heti) észlelése, és új rekordok létrehozása a hiányzó időpontokhoz, hogy a sorozat folyamatos legyen.
* Hiányzó értékek bevonása a célhelyből (továbbítási kitöltéssel) és a funkció oszlopai (medián Column Values használatával)
* Gabona-alapú szolgáltatások létrehozása a rögzített hatások engedélyezéséhez a különböző adatsorozatok között
* Időalapú szolgáltatások létrehozása a szezonális minták tanulásának segítésére
* Kategorikus változók kódolása numerikus mennyiségre

Az [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) objektum meghatározza az automatizált gépi tanulási feladatokhoz szükséges beállításokat és adatmennyiséget. A regressziós problémákhoz hasonlóan szabványos betanítási paramétereket is definiálhat, például a feladattípust, az ismétlések számát, a betanítási adatok számát és az eltérő érvényességi értéket. Az előrejelzési feladatokhoz további paramétereket kell megadni, amelyek hatással vannak a kísérletre. Az alábbi táblázat az egyes paramétereket és azok használatát ismerteti.

| Paraméter &nbsp; neve | Leírás | Kötelező |
|-------|-------|-------|
|`time_column_name`|A dátum-és idősorozatok létrehozásához használt bemeneti adatok datetime oszlopának megadására szolgál.|✓|
|`grain_column_names`|Az egyes adatsorozat-csoportokat meghatározó nevek a bemeneti adatokban. Ha a gabona nincs meghatározva, a rendszer az adathalmazt egy idősorozatra feltételezi.||
|`max_horizon`|Meghatározza a maximálisan kívánatos előrejelzési horizontot a Time-sorozat gyakoriságának egységében. Az egységek a betanítási adatokat tartalmazó időintervallumon alapulnak, például havi, heti rendszerességgel, amelyet az előrejelzésnek meg kell jósolnia.|✓|
|`target_lags`|A megcélzott értékeket az adatok gyakorisága alapján késleltető sorok száma. A lag listaként vagy egyetlen egész számként jelenik meg. A késést akkor kell használni, ha a független változók és a függő változó közötti kapcsolat alapértelmezés szerint nem felel meg egymásnak. Ha például egy termék iránti keresletre próbál előrejelzést kérni, a havi igény bármelyik hónapra az adott árucikkek előző 3 hónapjának árával függ. Ebben a példában előfordulhat, hogy 3 hónap elteltével negatívan szeretné megtekinteni a célt (keresletet), hogy a modell a megfelelő kapcsolaton legyen betanítva.||
|`target_rolling_window_size`|*n* korábbi időszakok, amelyeket az előre jelzett értékek előállítására használhat, <= betanítási készlet mérete. Ha nincs megadva, az *n* a teljes betanítási készlet mérete. Akkor válassza ezt a paramétert, ha csak bizonyos mennyiségű előzményt szeretne figyelembe venni a modell betanításakor.||
|`enable_dnn`|Előrejelzési DNN engedélyezése.||

További információt a [dokumentációban](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) talál.

Hozzon létre egy idősorozat-beállításokat szótár objektumként. Állítsa az értékét a `time_column_name` `day_datetime` mezőre az adatkészletben. Adja meg a `grain_column_names` paramétert annak biztosításához, hogy **két különálló idősorozat-csoport** legyen létrehozva az adathalmazhoz, egy pedig az a és a B tárolóhoz. végül állítsa a `max_horizon` 50-re a teljes tesztelési csoport előrejelzéséhez. Állítsa be az előrejelzési ablakot 10 időszakra a értékkel `target_rolling_window_size` , és egyetlen késést határozzon meg a megcélzott értékeken a paraméterrel megjelenő két időszakra vonatkozóan `target_lags` . Javasoljuk, hogy állítsa be `max_horizon` , `target_rolling_window_size` és `target_lags` az "Auto" értékre, amely automatikusan felismeri ezeket az értékeket. Az alábbi példában az "Auto" beállításokat használták a paraméterekhez. 

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
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

A `grain_column_names` fenti kódrészletben a AutoML két külön idősorozat-csoportot hoz létre, más néven több idősorozatot. Ha nincs megadva gabona, a AutoML azt feltételezi, hogy az adatkészlet egy egyidejű adatsorozat. Az egyidejű adatsorozatokkal kapcsolatos további tudnivalókért tekintse meg a [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

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

Tekintse meg az [előrejelzési minta jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) a speciális előrejelzési konfigurációk részletes kódjával kapcsolatban, beleértve a következőket:

* [üdülés észlelése és featurization](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [gördülő-eredetű kereszt-ellenőrzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurálható késések](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [a gördülő ablak összesített funkciói](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>DNN-előrejelzési kísérlet engedélyezése

> [!NOTE]
> Az automatikus Machine Learning DNN támogatása előzetes verzióban érhető el, és helyi futtatások esetén nem támogatott.

Az előrejelzési DNN kihasználása érdekében a paramétert True értékre kell állítania `enable_dnn` a AutoMLConfig. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
További információ [a AutoMLConfig](#configure-and-run-experiment).

Azt is megteheti, hogy kijelöli a `Enable deep learning` lehetőséget a Studióban.
![helyettesítő szöveg](./media/how-to-auto-train-forecast/enable_dnn.png)

Javasoljuk, hogy használjon olyan pénzmosás-számítási fürtöt, amely GPU SKU-ket és legalább két csomópontot használ számítási célként. Ahhoz, hogy elegendő idő legyen a DNN képzés befejezésére, javasoljuk, hogy állítsa be a kísérlet időtúllépését legalább néhány órára.
A GPU-k által használt és a virtuális gépek méretével kapcsolatos további információkért lásd a [pénzmosás-alapú számítási dokumentációt](how-to-set-up-training-targets.md#amlcompute) és a GPU-ra [optimalizált virtuálisgép-méretek dokumentációját](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Tekintse meg az [üzemi előrejelzést tartalmazó jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) , amely egy részletes kód, például a DNN kihasználása.

### <a name="target-rolling-window-aggregation"></a>Cél gördülő ablak összesítése
A legjobb információ gyakran a cél legutóbbi értéke lehet. A cél összesítő statisztikáinak létrehozása növelheti az előrejelzések pontosságát. A cél gördülő ablak összesítései lehetővé teszik, hogy az adatértékek folyamatos összesítését adja hozzá szolgáltatásként. Ha engedélyezni szeretné a cél működés közbeni Windows-t, állítsa be a kívánt `target_rolling_window_size` egész számú ablak méretét. 

Erre példa látható az energia-igény előrejelzése során. Három nap alatt hozzáadhat egy gördülő ablakos szolgáltatást, amely a fűtött szóközök termikus változásait is figyelembe veszi. Az alábbi példában ezt a három méretet hoztuk létre `target_rolling_window_size=3` a konstruktor beállításával `AutoMLConfig` . A tábla megjeleníti a funkciók mérnöki felépítését, amely az ablak összesítésének alkalmazása során következik be. A minimális, a maximális és az összeg oszlopok a megadott beállítások alapján három ablakból jönnek létre. Az egyes sorok egy új számított funkcióval rendelkeznek, a 2017-es szeptember 8-án eltelt időbélyeg esetében: a maximális, a minimum és az összeg értékeit a rendszer a következő értékekkel számítja ki, az igény szerinti értékek szerint: szeptember 8, 2017 1:20:00 – 3:20:00. Ez az ablak három műszakban jelenik meg, hogy feltöltse a fennmaradó sorokhoz tartozó adatokat.

![helyettesítő szöveg](./media/how-to-auto-train-forecast/target-roll.svg)

Ezeket a kiegészítő funkciókat további kontextusbeli adatok létrehozásával és használatával segítheti a betanítási modell pontosságát.

Tekintse meg a [cél gördülő ablak összesítési funkcióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)kihasználó Python-kód példáját.

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

A `forecast()` függvényt a helyett kell használni `predict()` , ez lehetővé teszi a specifikációkat, amikor a jóslatok elindulnak. A következő példában először az összes értéket kell lecserélnie a alkalmazásban `y_pred` `NaN` . Az előrejelzés forrása ebben az esetben a betanítási adatgyűjtési időszak végén lesz, mivel általában a használatakor lenne `predict()` . Ha azonban csak a második felét cserélte le `y_pred` `NaN` , a függvény a numerikus értékeket az első fél változatlanul hagyja, de a második félidőben is megbecsüli az `NaN` értékeket. A függvény az előre jelzett értékeket és az igazított funkciókat is visszaadja.

A `forecast_destination` függvény paraméterét is használhatja az `forecast()` értékek előrejelzésére a megadott dátumig.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Kiszámítja a GYÖKÁTLAGOS (a legfelső szintű négyzetes hibát) a `actual_labels` tényleges értékek és az előre jelzett értékek között `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Most, hogy a modell teljes pontossága meg lett határozva, a legreálisabb következő lépés a modell használata az ismeretlen jövőbeli értékek előrejelzésére. Adja meg az adathalmazt a tesztelési csoporttal megegyező formátumban `test_data` , de a jövőbeli dátum/idő értékkel, az eredményül kapott előrejelzési készlet pedig az egyes idősorozat-lépések előre jelzett értékei. Tegyük fel, hogy az adatkészletben az utolsó idősorozat rekord a 12/31/2018-es értékre van állítva. Ha a következő napra (vagy az előrejelzéshez szükséges számos időszakra, <=) szeretne előrejelzést `max_horizon` készíteni, hozzon létre egy egyidejű adatsorozat-rekordot a 01/01/2019-es tárolóhoz.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Ismételje meg a szükséges lépéseket a jövőbeli adatok egy dataframe való betöltéséhez, majd futtassa a parancsot `best_run.predict(test_data)` a jövőbeli értékek előrejelzéséhez.

> [!NOTE]
> Nem lehet előre jelezni az értéknél nagyobb időszakok számát `max_horizon` . A modellt újra be kell tanítani, hogy az aktuális horizonton túli jövőbeli értékek előrejelzése nagyobb horizonton történjen.

## <a name="next-steps"></a>További lépések

* Kövesse az [oktatóanyagot](tutorial-auto-train-models.md) , amelyből megtudhatja, hogyan hozhat létre kísérleteket automatizált gépi tanulással.
* Tekintse meg a [Azure Machine learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) dokumentációját.
