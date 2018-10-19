---
title: Az automatikus machine learning-kísérlet – az Azure Machine Learning konfigurálása
description: Automatizált gépi tanulási algoritmus választja ki az Ön számára, és készen áll a központi telepítési modell generál. Ismerje meg a beállításokat, amelyek segítségével automatizált machine learning-példakísérleteket konfigurálása.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1aeb1315cfafbcdf3507a6e49d71e1f1e69b537c
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430187"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>Az automatikus machine learning-kísérlet konfigurálása

Automatizált a machine learning (automatikus ML) olyan algoritmusai és hiperparaméterek kiválasztja az Ön számára, és készen áll a központi telepítési modell generál. A modell további testre is letölthető. Többféle módon automatikus gépi Tanulási kísérletek konfigurálásához használhatja. Ebben az útmutatóban, megtudhatja, hogyan különböző konfigurációs beállításainak megadásához.

Egy automatizált Machine Learning Példák megtekintése: [oktatóanyag: egy automatizált gépi tanulással osztályozási modell betanításához](tutorial-auto-train-models.md) vagy [és a felhőben automatizált machine learning-modellek betanításához](how-to-auto-train-remote.md).

Az automatikus machine learningben elérhető konfigurációs lehetőségek:

* Válassza ki a kísérlet típusát, például a besorolás, a regresszió 
* Az adatforrás, formátumokat és adatok újbóli lekérésére
* Válassza ki a számítási célnak (helyi vagy távoli)
* Automatikus gépi Tanulási kísérlet beállításai
* Automatizált gépi Tanulási kísérlet futtatása
* Modell metrikák böngészése
* Regisztráljon és a modell üzembe helyezése

## <a name="select-your-experiment-type"></a>A kísérlet típusának kiválasztása
Mielőtt elkezdené a kísérlethez, meg kell határoznia a megoldandó, machine learning probléma típusú. Automatizált ML támogatja a felügyelt tanítás két kategóriába: besorolási és regressziós. Gépi tanulás támogatja a következő algoritmusokat automatizált az automation és a beállítási folyamat során. Felhasználójaként van, nem szükséges, hogy adja meg az algoritmus.
Besorolás | Regresszió
--|--
sklearn.linear_model. LogisticRegression | sklearn.linear_model. ElasticNet
sklearn.linear_model. SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes. BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes. MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model. LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model. SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm. LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm. LGBMClassifier |


## <a name="data-source-and-format"></a>Adatforrás és a formátum
Automatizált ML található adatok támogatja a helyi asztali számítógépeken, vagy a felhőben Azure Blob Storage-ban. Az adatok olvashatók be a scikit-ismerje meg a támogatott formátumok. A (szolgáltatások) X (1) Numpy tömbök adatok és az y (célváltozó vagy más néven címke) vagy (2) Pandas dataframe olvashat. 

Példák:

1.  Numpy tömbök

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  Pandas dataframe

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Kísérlet futtatása távoli számítási adatlehívást

Ha egy távoli számítási használatával futtathatja a kísérletet, az adatok betöltése közben kell csomagolni, külön python-szkriptet a `get_data()`. Ez a szkript távoli számítási van futtassa, ahol az automatizált gépi Tanulási kísérlet futtatása. `get_data` szükségtelenné teszi az adatok lehívása törzsének a hálózaton keresztül. Nélkül `get_data`, a kísérlet sikertelen lesz, amikor távoli számítási futtatja.

Íme egy példa `get_data`:

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
from sklearn.preprocessing import LabelEncoder 
def get_data(): # Burning man 2016 data 
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42) 
    return { "X" : df, "y" : y }
```

Az a `AutoMLConfig` objektumot, akkor adja meg a `data_script` paraméter, és adja meg az elérési útját a `get_data` hasonló, az alábbi parancsfájlt:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` parancsfájl lépjen vissza a következő:
Kulcs | Típus |    Kölcsönösen kizárják egymást az | Leírás
---|---|---|---
X | Pandas Dataframe vagy Numpy tömbje | data_train, a címkét, az oszlopok |  Megkezdheti az összes funkció
Y | Pandas Dataframe vagy Numpy tömbje |   label   | Megkezdheti az adatok. A besorolás kell lennie az egész számok tömbje.
X_valid | Pandas Dataframe vagy Numpy tömbje   | data_train, felirat | _Nem kötelező_ érvényesítése az összes szolgáltatás. Ha nincs megadva, az X train között van felosztva, és ellenőrzése
y_valid |   Pandas Dataframe vagy Numpy tömbje | data_train, felirat | _Nem kötelező_ a felirat adatainak ellenőrzése a. Ha nincs megadva, az y train között van felosztva, és ellenőrzése
sample_weight | Pandas Dataframe vagy Numpy tömbje |   data_train, a címkét, az oszlopok| _Nem kötelező_ súlyértéket minden mintához. Akkor használja, ha szeretné rendelni az adatpontokhoz különböző súlya 
sample_weight_valid | Pandas Dataframe vagy Numpy tömbje | data_train, a címkét, az oszlopok |    _Nem kötelező_ súlyértéket minden érvényesítési mintához. Ha nincs megadva, a sample_weight train között van felosztva, és ellenőrzés
data_train |    Pandas Dataframe |  X, y, X_valid, y_valid |    Minden adat (funkciók és címke) rendelkező betanítása
label | sztring  | X, y, X_valid, y_valid |  A data_train melyik oszlop felel meg a címke
Oszlopok | karakterláncok tömbje  ||  _Nem kötelező_ oszlopok engedélyezett funkciók használata
cv_splits_indices   | Egész számok tömbje ||  _Nem kötelező_ indexek kell felosztani az adatokat az érvényesítési közötti listája

## <a name="train-and-validation-data"></a>Adatok train és érvényesítése

Külön vonat és érvényesítési beállítása get_data() vagy közvetlenül is megadhat a `AutoMLConfig` metódust.

## <a name="cross-validation-split-options"></a>Keresztellenőrzési vágási lehetőségek

### <a name="k-folds-cross-validation"></a>K – Modellrész keresztellenőrzés

Használat `n_cross_validations` ellenőrzések közötti számát adja meg a beállítást. A tanítási adathalmazt lesz véletlenszerűen felosztva `n_cross_validations` modellrész egyenlő méretű. Során minden egyes keresztellenőrzési round egyet a kevesebb modellrészt használható ellenőrzés céljából a többi modellrész tanított modell. Ez a folyamat ismétlődik a `n_cross_validations` kerekít mindaddig, amíg minden modellrészek érvényesítési beállított egyszer használatos. Végül, az átlagos pontszámmodell összes `n_cross_validations` kerekít lesz jelentve, és a megfelelő modellt fog retrained a a egészében betanítási adatok készlete.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Monte Carlo keresztellenőrzés (más néven) Ismétlődő véletlenszerű alárendelt mintavételi)

Használjon `validation_size` százalékos aránya az érvényesítés, és használja a használandó képzési adatkészlet megadásához `n_cross_validations` közötti ellenőrzések száma. Egyes során keresztellenőrzés round, egy részhalmazát méretű `validation_size` ellenőrzés céljából a többi adat tanított modell a rendszer véletlenszerűen választ. Végül, az átlagos pontszámmodell összes `n_cross_validations` kerekít lesz jelentve, és a megfelelő modellt fog retrained a a egészében betanítási adatok készlete.

### <a name="custom-validation-dataset"></a>Egyéni ellenőrzési adatkészlet

Egyéni ellenőrzési adatkészletet használja, ha véletlenszerű split nem fogadható el (általában time series és imbalanced adatokat). Ennek a saját érvényesítési adatkészletet is megadhat. A modellben értékelik az érvényesítési élelmiszer-megadott véletlenszerű adatkészlet helyett.

## <a name="compute-to-run-experiment"></a>Számítási kísérlet futtatása

Ezután határozza meg, hol kell betanítani a modellt. Egy automatizált Machine Learning betanítási kísérlet futtat számítási célt tulajdonosa, és kezelheti. 

Támogatott számítási lehetőségek közül választhat:
1.  A helyi gépen, például a helyi számítógépen vagy laptopon – általában amikor rendelkezik kisméretű adatkészlet, és továbbra is a feltárás fázisban.
2.  Egy távoli gépen a felhőben – [Azure adatelemzési virtuális gép](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) Linux – operációs rendszert futtató rendelkezik egy nagy méretű adathalmazt, és a egy nagy méretű géphez, amely elérhető az Azure Cloud vertikálisan. 
3.  Az Azure Batch AI-fürt – A felügyelt fürt, amely beállíthat horizontális felskálázása és gépi Tanulási automatikus ismétlések párhuzamosan is futtatni. 

<a name='configure-experiment'/>
## <a name="configure-your-experiment-settings"></a>A kísérlet beállításainak konfigurálása

Vannak, amelyek segítségével konfigurálhatja az automatikus gépi Tanulási kísérlet több belül. Ezeket a paramétereket állítja be hárítható el egy `AutoMLConfig` objektum.

Néhány példa:

1.  Besorolási kísérlet az elsődleges metrika és a maximális száma az iteráció és a Befejezés után 50 ismétlési és 2 keresztellenőrzési modellrész kísérletet 12 000 másodperc idő szerint súlyozott AUC használatával.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  Alább egy például 100 közelítő befejezéséhez regressziós kísérlet meg, a minden egyes ismétléskor tartó legfeljebb 600 másodperc az 5-érvényesítési adatbázisközi modellrész.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

Ez a táblázat felsorolja a paraméterek beállításai is futtathatja a kísérletet és alapértelmezett értékeik érhető el.

Tulajdonság |  Leírás | Alapértelmezett érték
--|--|--
`task`  |Adja meg a machine learning probléma típusát. Megengedett értékek: <li>osztályozás</li><li>Regresszió</li>    | None |
`primary_metric` |Ez a metrika szeretné optimalizálni a modell épületben. Például a primary_metric pontossága ad meg, ha automatikus ML keres egy modell található a legnagyobb pontosságú. Csak egy primary_metric kísérletenként adható meg. Megengedett értékek: <br/>**Besorolási**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Regresszió**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | Besorolási: pontossága <br/>A regresszió: spearman_correlation <br/> |
`exit_score` |  A primary_metric célérték adhatja meg. Miután egy modell található, amely megfelel a primary_metric cél, gépi tanulás automatikus le fog állni, léptetés, és az a kísérlet befejeződik. Ha ez az érték nincs beállítva (alapértelmezett), automatikus gépi Tanulási kísérlet ismétléseinek megadott tevékenységsort futtatásához továbbra is. Egy dupla értéket vesz fel. Ha soha nem eléri a cél, majd automatikus Machine Learning továbbra is addig ismétlések megadott ismétlések száma.|   None
`iterations` |Az ismétlések maximális számát. Minden egyes ismétléskor megegyezik a betanítási feladat, amely egy folyamat eredményez. Folyamat az adatok előfeldolgozása és a modell. Egy jó minőségű modellt használja 250 vagy több | 100
`Concurrent_iterations`|    Párhuzamosan futtatni az ismétlések maximális száma. Ez a beállítás csak távoli számítási működik.|    1
`max_cores_per_iteration`   | Azt jelzi, hogy hány magunk a számítási célnak a használni kívánt betanításához egy folyamatot. Ha az algoritmus kihasználhatják a több mag, majd ez növeli a Többmagos gépen a teljesítményt. Beállíthatja a gépen elérhető összes mag használandó 1 értéket ad.|  1
`max_time_sec` |    Korlátozza egy adott iterációhoz szükséges idő (másodperc). Ha egy iterációját meghaladja a megadott mennyiséget, a iteráció beolvasása megszakítva. Ha nincs megadva, a rendszer az iteráció továbbra is fut, amíg befejeződik. |   None
`n_cross_validations`   |A keresztellenőrzés felosztásainak száma| None
`validation_size`   |Állítsa be az összes képzési minta százalékában érvényesítési mérete.|  None
`preprocess` | Igaz/hamis <br/>Igaz lehetővé teszi, hogy a bemeneti adatok az előfeldolgozási végrehajtásához kísérletezhet. Következő része a előfeldolgozása<li>Hiányzó adatok: Biztosítják a hiányzó adatokat-numerikus az átlag, a legtöbb elemként szöveg </li><li>Kategorikus értékek: Adattípusa szám és egyedi száma értékek e kevesebb mint 5 %-os, az egyik gyakori kódolási alakíthatók át egymásba </li><li>Stb. Ellenőrizze a teljes listát [a GitHub-adattár](https://aka.ms/aml-notebooks)</li><br/>Megjegyzés: Ha az adatok ritka nem használhat előfeldolgozása = true |  False (Hamis) | 
`blacklist_algos`   | Automatizált gépi Tanulási kísérlet megkísérli számos különböző algoritmus rendelkezik. Konfigurálja a gépi tanulás automatikus bizonyos algoritmusok kizárása a kísérletet. Akkor hasznos, ha vegye figyelembe, hogy aránytól nem működnek jól az adatkészlet. Kivéve a algoritmusok is takaríthat meg számítási erőforrásokat és képzési időt.<br/>Besorolási megengedett értékek<br/><li>Logisztikai regresszió</li><li>SGD osztályozó</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>Extra fák</li><li>Színátmenet kiemelése</li><li>lgbm_classifier</li><br/>Engedélyezett értékek regresszió<br/><li>Nettó rugalmas</li><li>Színátmenet kiemelési regressor erre</li><li>DT regressor erre</li><li>kNN regressor erre</li><li>Szabadkézi lars</li><li>SGD regressor erre</li><li>RF regressor erre</li><li>Extra fák regressor erre</li>|   None
`verbosity` |Folyamatban van a részletes és a kritikus folyamatban információval azt szabályozza, a naplózási szint a legkevésbé.<br/>Engedélyezett értékek a következők:<br/><li>logging.INFO</li><li>a naplózás. FIGYELMEZTETÉS</li><li>a naplózás. HIBA TÖRTÉNT</li><li>a naplózás. KRITIKUS</li>  | logging.INFO</li> 
`X` | Megkezdheti az összes funkció |  None
`y` |   Megkezdheti az adatok. A besorolás kell lennie az egész számok tömbje.|  None
`X_valid`|_Nem kötelező_ érvényesítése az összes szolgáltatás. Ha nincs megadva, az X train között van felosztva, és ellenőrzése |   None
`y_valid`   |_Nem kötelező_ a felirat adatainak ellenőrzése a. Ha nincs megadva, az y train között van felosztva, és ellenőrzése    | None
`sample_weight` |   _Nem kötelező_ súlyértéket minden mintához. Akkor használja, ha szeretné rendelni az adatpontokhoz különböző súlya |   None
`sample_weight_valid`   |   _Nem kötelező_ súlyértéket minden érvényesítési mintához. Ha nincs megadva, a sample_weight train között van felosztva, és ellenőrzés   | None
`run_configuration` |   RunConfiguration objektum.  Távoli Futtatás használja. |None
`data_script`  |    A get_data metódus tartalmazó fájl elérési útja.  Távoli Futtatás szükséges.   |None


## <a name="run-experiment"></a>Kísérlet futtatása

Ezután azt is kezdeményezhető a kísérlet futtatásához és a egy modell létrehozása a számunkra. Adja át a `AutoMLConfig` , a `submit` metódus a modell generálásához.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Függőségek először telepíteni egy új adatelemző virtuális GÉPET.  Mielőtt látható a kimenetre akár 10 percig is eltarthat.
>Beállítás `show_output` kimenetet a konzolon látható igaz eredményez.


## <a name="explore-model-metrics"></a>Modell metrikák böngészése
Megtekintheti az eredményeket a widget vagy beágyazott, ha Ön történő használatát. A részletek "Nyomon követésére és modellek kiértékelése". (Győződjön meg arról, AML-tartalmak automatizált gépi Tanulási releváns információkat tartalmaz)

A következő metrikák minden egyes ismétléskor lesznek mentve.
* AUC_macro
* AUC_micro
* AUC_weighted
* accuracy
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hogyan és hol érdemes a modell üzembe helyezése](how-to-deploy-and-where.md).

Tudjon meg többet [ML automatikus osztályozási modell betanításához hogyan](tutorial-auto-train-models.md) vagy [betanításához egy távoli erőforrás automatikus gépi tanulás használatával hogyan](how-to-auto-train-remote.md). 