---
title: 'Oktatóanyag: az első Azure ML-modell betanítása a Pythonban'
titleSuffix: Azure Machine Learning
description: Ebben az oktatóanyagban megismerheti a Azure Machine Learning alapvető tervezési mintáit, és betanít egy egyszerű scikit-modellt a diabétesz adatkészlete alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: aa90655ecb14abe38ec8fdfc6c18e7d292abbef3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238677"
---
# <a name="tutorial-train-your-first-ml-model"></a>Oktatóanyag: az első ML-modell betanítása

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az előző oktatóanyagban [létrehozott egy munkaterületet, és kiválasztott egy fejlesztési környezetet](tutorial-1st-experiment-sdk-setup.md). Ebben az oktatóanyagban megismerheti a Azure Machine Learning alapvető tervezési mintáit, és betanít egy egyszerű scikit-modellt a diabétesz adatkészlete alapján. Az oktatóanyag elvégzése után gyakorlati ismeretekkel fog rendelkezni az SDK-ról, hogy az összetettebb kísérleteket és munkafolyamatokat fejlesszen.

Ez az oktatóanyag a következő feladatokat ismerteti:

> [!div class="checklist"]
> * A munkaterület összekötése és kísérlet létrehozása
> * Az adatterhelés és a scikit betanítása – modellek
> * Betanítási eredmények megtekintése a portálon
> * A legjobb modellt beolvasása

## <a name="prerequisites"></a>Előfeltételek

Az egyetlen előfeltétel az oktatóanyag, a [telepítési környezet és a munkaterület](tutorial-1st-experiment-sdk-setup.md)első részének futtatása.

Az oktatóanyag ezen részében futtatja a kódot a minta Jupyter notebook *oktatóanyagok/Create-First-ml-Experiment/tutorial-1st-Experiment-SDK-Train. ipynb* , az első rész végén megnyitva. Ez a cikk a jegyzetfüzetben található kódot mutatja be.

## <a name="open-the-notebook"></a>A jegyzetfüzet megnyitása

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).

1. Nyissa meg az **oktatóanyag-1st-Experiment-SDK-Train. ipynb** a mappában az első [részben](tutorial-1st-experiment-sdk-setup.md#open)látható módon.


> [!Warning]
> Ne **hozzon** létre *új* jegyzetfüzetet a Jupyter felületen! A notebook *oktatóanyagok/Create-First-ml-Experiment/tutorial-1st-Experiment-SDK-Train. ipynb* tartalmazza az oktatóanyaghoz **szükséges összes kódot és** adatmennyiséget.

## <a name="connect-workspace-and-create-experiment"></a>Munkaterület összekötése és kísérlet létrehozása

> [!Important]
> A cikk többi része ugyanazt a tartalmat tartalmazza, mint amit a jegyzetfüzetben lát.  
>
> Váltson a Jupyter jegyzetfüzetre, ha a kód futtatása közben szeretné olvasni. 
> Ha egyetlen kód cellát szeretne futtatni egy jegyzetfüzetben, kattintson a kód cellára, és nyomja le a **SHIFT + ENTER billentyűkombinációt**. Vagy futtassa a teljes jegyzetfüzetet úgy, hogy az **összes futtatása** lehetőséget választja a felső eszköztáron.

Importálja a `Workspace` osztályt, és töltse be az előfizetési adatokat a fájlból `config.json` a következő függvény használatával: `from_config().` ez az aktuális könyvtárban található JSON-fájl alapértelmezettként való megadását teszi elérhetővé, de megadhat egy Path paramétert is, amely a fájlra mutat a `from_config(path="your/file/path")`használatával. A Felhőbeli jegyzetfüzet-kiszolgálókon a fájl automatikusan megjelenik a gyökérkönyvtárban.

Ha a következő kód további hitelesítést kér, egyszerűen illessze be a hivatkozást egy böngészőben, és adja meg a hitelesítési jogkivonatot.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Most hozzon létre egy kísérletet a munkaterületen. A kísérlet egy másik, alapszintű felhőalapú erőforrás, amely a próbaverziók gyűjteményét jelöli (az egyes modellek futtatása). Ebben az oktatóanyagban futtatja a kísérletet a futtatások létrehozásához és a modell képzésének nyomon követéséhez a Azure Machine Learning Studióban. A paraméterek közé tartozik a munkaterület-hivatkozás, valamint a kísérlet karakterlánc-neve.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Adatgyűjtés és felkészülés a képzésre

Ebben az oktatóanyagban a diabétesz-adatkészletet használja, amely a diabéteszes megbetegedések előrehaladásának előrejelzéséhez olyan szolgáltatásokat használ, mint az Age, a gender és a BMI. Töltse be az adatokat az [Azure Open adatkészletek](https://azure.microsoft.com/services/open-datasets/) osztályból, és ossza ki a képzési és tesztelési készletekbe `train_test_split()`használatával. Ez a függvény elkülöníti az adattípusokat, így a modell nem tartalmaz olyan, a következő képzések teszteléséhez szükséges adatait.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Modell betanítása

Egy egyszerű scikit-modell betanítása könnyen elvégezhető helyileg a kis léptékű képzések esetében, de ha sok ismétlést tanítanak több tucat különböző funkció-és hiperparaméter-beállítással, egyszerűen elveszítheti a betanított modelleket, és hogyan betanítva. Az alábbi kialakítási minta bemutatja, hogyan használhatja ki az SDK-t a Felhőbeli képzések egyszerű nyomon követéséhez.

Hozzon létre egy szkriptet, amely a Ridge-modelleket egy hurokban különböző hiperparaméter alfa-értékekkel hajtja össze.


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

A fenti kód a következőket hajtja végre:

1. A `alphas` tömb mindegyik alfa-hiperparaméter értéke esetén létrejön egy új Futtatás a kísérleten belül. A rendszer naplózza az alfa-értéket az egyes futtatások megkülönböztetése érdekében.
1. Az egyes futtatások során a Ridge-modell példánya, betanítása és előrejelzések futtatására szolgál. A rendszer kiszámítja a tényleges és az előre jelzett értékeket, majd naplózza a futtatást. Ezen a ponton a futtatáshoz metaadatok vannak csatolva az Alpha értékhez és a gyökátlagos pontosságához.
1. Ezután az egyes futtatásokhoz tartozó modell szerializálva lesz, és a futtatásra van feltöltve. Ez lehetővé teszi a modell letöltését a portálon futtatott fájlból.
1. Az egyes iterációk végén a Futtatás `run.complete()`meghívásával fejeződik be.

A képzés befejezését követően hívja meg a `experiment` változót, hogy beolvassa a kísérletre mutató hivatkozást a portálon.

```python
experiment
```

<table style="width:100%"><tr><th>Name (Név)</th><th>Munkaterület</th><th>Jelentés lapja</th><th>Docs oldal</th></tr><tr><td>cukorbetegség – kísérlet</td><td>saját-munkaterület neve</td><td>Hivatkozás Azure Portal</td><td>Hivatkozás a dokumentációra</td></tr></table>

## <a name="view-training-results-in-portal"></a>Képzés eredményeinek megtekintése a portálon

A **Azure Portalre mutató hivatkozást** követve a fő kísérlet oldalára kerül. Itt láthatja a kísérletben szereplő összes egyéni futtatást. Minden egyéni naplózott érték (ebben az esetben a`alpha_value` és a `rmse`) minden futtatásnál mezővé válik, és a kísérlet oldal tetején található diagramok és csempék számára is elérhetővé válik. Egy naplózott metrika diagramhoz vagy csempéhez való hozzáadásához vigye fölé a kurzort, kattintson a Szerkesztés gombra, és keresse meg az egyéni naplózott metrikát.

Ha több száz vagy több ezer különálló futtatást használ, ezen az oldalon könnyedén megtekintheti a betanított modelleket, és hogy miként változnak az egyedi mérőszámok az idő múlásával.

![A fő kísérlet oldala a portálon](./media/tutorial-1st-experiment-sdk-train/experiment-main.png)

A `RUN NUMBER` oszlopban a futtatási szám hivatkozásra kattintva megtekintheti az egyes futtatások lapját. Az alapértelmezett lapon a **részletek** részletesebb információkat jelenítenek meg az egyes futtatásokról. Navigáljon a **kimenetek** lapra, és megtekintheti a modellhez tartozó `.pkl` fájlt, amelyet az egyes képzések ismétlése során a futtatásra töltöttek fel. Itt letöltheti a modell fájlját, nem kell manuálisan áttanítania.

![A portálon a Részletek lap futtatása](./media/tutorial-1st-experiment-sdk-train/model-download.png)

## <a name="get-the-best-model"></a>A legjobb modell beszerzése

A modell fájljainak a portálon történő kipróbálása mellett a szoftveresen is letöltheti őket. A következő kód a kísérletben szereplő minden egyes futtatást megismétli, és a naplózott futtatási metrikákat és a Futtatás részleteit (amely a run_id tartalmazza) is eléri. Ezzel a lépéssel nyomon követheti a legjobb futtatást, ebben az esetben a futtatást a legalacsonyabb, legfelső szintű, négyzetes hibával.

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

A legjobb futtatási AZONOSÍTÓval lekérheti az egyes futtatásokat a `Run` konstruktorral együtt a kísérlet objektummal. Ezután hívja meg a `get_file_names()`t, és tekintse meg a futtatásból letölthető összes fájlt. Ebben az esetben csak egy fájlt töltött fel minden futtatáshoz a betanítás során.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Hívja meg `download()` a Run objektumon, adja meg a letölteni kívánt modell fájlnevét. Alapértelmezés szerint ez a függvény letölti az aktuális könyvtárat.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ne hajtsa végre ezt a szakaszt, ha más Azure Machine Learning oktatóanyagok futtatását tervezi.

### <a name="stop-the-compute-instance"></a>A számítási példány leállítása

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Mindent törölni

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következő feladatokat végezte el:

> [!div class="checklist"]
> * Csatlakoztatta a munkaterületet, és létrehozott egy kísérletet
> * Betöltött adatfeldolgozási és betanított scikit-modellek
> * Megtekintett képzések eredményei a Portálon és a beolvasott modellek

[A modell üzembe helyezése](tutorial-deploy-models-with-aml.md) Azure Machine Learningsal.
Ismerje meg, hogyan fejleszthet [automatizált gépi tanulási](tutorial-auto-train-models.md) kísérleteket.
