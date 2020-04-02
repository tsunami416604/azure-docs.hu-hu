---
title: 'Oktatóanyag: Az első Azure ML-modell betanítása a Pythonban'
titleSuffix: Azure Machine Learning
description: Ebben az oktatóanyagban megismerheti az Azure Machine Learning alapvető tervezési mintáit, és betaníthat egy egyszerű scikit-learn modellt a cukorbetegség adatkészlet e-mail-adatkészlet alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: c8f259d2d4df46470a042c3f65ac1b8e1f66b1dd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546027"
---
# <a name="tutorial-train-your-first-ml-model"></a>Oktatóanyag: Az első ML-modell betanítása

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az előző oktatóanyagban [létrehozott egy munkaterületet, és egy fejlesztői környezetet választott.](tutorial-1st-experiment-sdk-setup.md) Ebben az oktatóanyagban megismerheti az Azure Machine Learning alapvető tervezési mintáit, és betaníthat egy egyszerű scikit-learn modellt a cukorbetegség adatkészlet e-mail-adatkészlet alapján. Az oktatóanyag befejezése után rendelkezik az SDK gyakorlati tudásával, hogy összetettebb kísérletek és munkafolyamatok kifejlesztésére készüljön.

Eben az oktatóanyagban az alábbi feladatokkal fog megismerkedni:

> [!div class="checklist"]
> * A munkaterület csatlakoztatása és kísérlet létrehozása
> * Adatok betöltése és scikit-learn modellek betanítása
> * Képzési eredmények megtekintése a stúdióban
> * A legjobb modell lekérése

## <a name="prerequisites"></a>Előfeltételek

Az egyetlen előfeltétel az oktatóanyag, a telepítési környezet és a munkaterület első részének [futtatása.](tutorial-1st-experiment-sdk-setup.md)

Ebben a részben az oktatóanyag, futtatja a kódot a minta Jupyter notebook *oktatóanyagok/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* megnyílt az első rész végén. Ez a cikk végigvezeti a jegyzetfüzetben található kódot.

## <a name="open-the-notebook"></a>A jegyzetfüzet megnyitása

1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com/)

1. Nyissa meg a **tutorial-1st-experiment-sdk-train.ipynb** a mappában, ahogy az [első részben](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> **Ne** hozzon létre *új* notebook a Jupyter felületen! A notebook *oktatóanyagok/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* tartalmazza az **összes kódot és adatot, amely ehhez** az oktatóanyaghoz szükséges.

## <a name="connect-workspace-and-create-experiment"></a>Munkaterület csatlakoztatása és kísérlet létrehozása

> [!Important]
> A cikk többi része ugyanazt a tartalmat tartalmazza, mint a jegyzetfüzetben.  
>
> Váltson most a Jupyter-jegyzetfüzetre, ha a kód futtatásakor szeretne olvasni. 
> Ha egy jegyzetfüzetben egyetlen kódcellát szeretne futtatni, kattintson a kódcellára, és nyomja meg a **Shift+Enter billentyűkombinációt.** Vagy futtassa a teljes jegyzetfüzetet a Felső eszköztár **Összes futtatása** parancsával.

Importálja `Workspace` az osztályt, és töltse `config.json` be `from_config().` az előfizetési adatokat a fájlból az Ez alapértelmezés szerint az aktuális könyvtárJSON-fájlját keresi, de megadhat egy elérési utat is, amely a fájlra mutat a használatával. `from_config(path="your/file/path")` A felhőalapú jegyzetfüzet-kiszolgálókon a fájl automatikusan a gyökérkönyvtárba kerül.

Ha a következő kód további hitelesítést kér, egyszerűen illessze be a hivatkozást egy böngészőbe, és adja meg a hitelesítési jogkivonatot.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Most hozzon létre egy kísérletet a munkaterületen. A kísérlet egy másik alapvető felhőalapú erőforrás, amely a kísérletek (egyéni modellfuttatások) gyűjteményét képviseli. Ebben az oktatóanyagban a kísérlet segítségével futtatja, és nyomon követheti a modell képzés az Azure Machine Learning stúdióban. A paraméterek közé tartozik a munkaterület hivatkozása és a kísérlet karakterláncneve.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Adatok betöltése és felkészülés a betanításra

Ebben az oktatóanyagban a cukorbetegség adatkészletet használja, amely olyan funkciókat használ, mint az életkor, a nem és a BMI a cukorbetegség-betegség progressziójának előrejelzéséhez. Töltse be az adatokat az [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) osztályból, és ossza fel betanítási és tesztkészletekre a használatával. `train_test_split()` Ez a függvény elkülöníti az adatokat, így a modell nem látott adatokat használ a betanítást követő teszteléshez.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Modell betanítása

Egy egyszerű scikit-learn modell betanítása könnyen elvégezhető helyileg a kis léptékű képzéshez, de amikor számos iterációt több tucat különböző funkció-permutációval és hiperparaméter-beállítással tanít, könnyen elveszítheti a nyomon követhetővé, hogy milyen modelleket képzett be, és hogyan képezte ki őket. A következő tervezési minta bemutatja, hogyan használhatja ki az SDK-t, hogy könnyedén nyomon követhesse a betanítást a felhőben.

Hozzon létre egy parancsfájlt, amely a gerincmodelleket egy ciklusban, különböző hiperparaméteralfa-értékeken keresztül vonatoztatja.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

A fenti kód a következőket valósítja meg:

1. A `alphas` tömb minden alfa-hiperparaméter-értékéhez egy új futtatás jön létre a kísérleten belül. Az alfa-értéket a rendszer naplózza, hogy különbséget tegyen az egyes futtatások között.
1. Minden futtatáskor a Ridge modell példányosított, képzett, és előrejelzések futtatásához használt. A gyökér-közép-négyzet-hiba a tényleges versus előre jelzett értékeket számítja ki, majd bejelentkezik a futtatásra. Ezen a ponton a futtatás metaadatokat csatolt mind az alfa-érték, mind az rmse pontosságához.
1. Ezután az egyes futtatások modellszerializálódik, és feltölti a futtatásra. Ez lehetővé teszi, hogy töltse le a modell fájlt a futás a stúdióban.
1. Minden ismétlés végén a futtatás a hívással `run.complete()`fejeződik be.

Miután a betanítás `experiment` befejeződött, hívja meg a változót a kísérletre mutató hivatkozás lekéréséhez a stúdióban.

```python
experiment
```

<table style="width:100%"><tr><th>Név</th><th>Munkaterület</th><th>Jelentés lap</th><th>Dokumentumok lap</th></tr><tr><td>cukorbetegség-kísérlet</td><td>munkaterületi név</td><td>Hivatkozás az Azure Machine Learning stúdióra</td><td>Dokumentációra mutató hivatkozás</td></tr></table>

## <a name="view-training-results-in-studio"></a>Képzési eredmények megtekintése a stúdióban

Miután a **Link to Azure Machine Learning studio** viszi a fő kísérlet oldalon. Itt láthatja az összes egyes fut a kísérletben. Az egyéni naplózott`alpha_value` `rmse`értékek ( és ebben az esetben) mezőkké válnak az egyes futtatásokhoz, és a kísérletoldal tetején lévő diagramokhoz és csempékhez is elérhetővé válnak. Ha naplózott mérőszámot szeretne hozzáadni egy diagramhoz vagy csempéhez, mutasson rá, kattintson a szerkesztés gombra, és keresse meg az egyénileg naplózott mérőszámot.

Ha több száz és ezer különálló futtatáson keresztül nagy méretekben tanít be modelleket, ez az oldal megkönnyíti az összes betanított modell megtekintését, különösen a betanításmódját és az egyedi mérőszámok idővel történő változásának módját.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/experiment-main.png" alt-text="Fő kísérlet oldal a stúdióban.":::


Jelöljön ki egy `RUN NUMBER` futtatási számhivatkozást az oszlopban az egyes futtatások lapjának megtekintéséhez. Az alapértelmezett **lap Részletek** részletesebb információkat jelenít meg az egyes futtatásokról. Keresse meg a **Kimenetek + naplók** lapot, és megjelenik a `.pkl` modell, amely az egyes betanítási ismétlések során feltöltött modell fájlját látja. Itt letöltheti a modellfájlt, ahelyett, hogy manuálisan kellene újrabetanítania.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/model-download.png" alt-text="Futtassa a részleteket tartalmazó lapot a stúdióban.":::

## <a name="get-the-best-model"></a>Szerezd meg a legjobb modell

Amellett, hogy képes letölteni modell fájlokat a kísérlet a stúdióban, akkor is letölthető őket programozott módon. A következő kód végighalad a kísérlet minden egyes futtatásán, és hozzáfér mind a naplózott futtatási metrikákhoz, mind a futtatás részleteihez (amely a run_id tartalmazza). Ez nyomon követi a legjobb futtatást, ebben az esetben a futtatást a legalacsonyabb root-mean-squared-error-tal.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

A legjobb futtatási azonosító használatával az `Run` egyes futtatásokat a konstruktor és a kísérletobjektum használatával szeretné lekérni. Ezután `get_file_names()` hívja meg az összes letölthető fájlt erről a futásról. Ebben az esetben csak egy fájlt töltött fel minden egyes futtatáshoz az edzés során.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Hívja `download()` meg a futtatási objektumot, megadva a letöltendő modellfájl nevét. Alapértelmezés szerint ez a függvény az aktuális könyvtárba töltődik le.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ne fejezze be ezt a szakaszt, ha más Azure Machine Learning-oktatóanyagok futtatását tervezi.

### <a name="stop-the-compute-instance"></a>A számítási példány leállítása

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Mindent töröljön

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő feladatokat végezte el:

> [!div class="checklist"]
> * A munkaterület csatlakoztatása és kísérlet létrehozása
> * Betöltött adatok és betanított scikit-learn modellek
> * Megtekintett képzési eredmények a stúdióban és a lekért modellek

[Telepítse a modellt az](tutorial-deploy-models-with-aml.md) Azure Machine Learning használatával.
Ismerje meg, hogyan fejleszthet [automatizált gépi tanulási](tutorial-auto-train-models.md) kísérleteket.
