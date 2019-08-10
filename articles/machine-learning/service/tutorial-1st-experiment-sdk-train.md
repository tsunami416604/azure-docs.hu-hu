---
title: 'Oktatóanyag: Az első modell betanítása.'
titleSuffix: Azure Machine Learning service
description: Ebben az oktatóanyagban megismerheti a Azure Machine Learning szolgáltatás alapszintű kialakítási mintáit, és betanít egy egyszerű scikit-modellt a diabétesz adatkészlete alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: a3db70925d59f9d0e2793b04824b2e8606719dd4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934236"
---
# <a name="tutorial-train-your-first-model"></a>Oktatóanyag: Az első modell betanítása

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az előző oktatóanyagban [létrehozott egy munkaterületet, és kiválasztott egy fejlesztési környezetet](tutorial-1st-experiment-sdk-setup.md). Ebben az oktatóanyagban megismerheti a Azure Machine Learning szolgáltatás alapszintű kialakítási mintáit, és betanít egy egyszerű scikit-modellt a diabétesz adatkészlete alapján. Az oktatóanyag elvégzése után gyakorlati ismeretekkel fog rendelkezni az SDK-ról, hogy az összetettebb kísérleteket és munkafolyamatokat fejlesszen.

Ez az oktatóanyag a következő feladatokat ismerteti:

> [!div class="checklist"]
> * A munkaterület összekötése és kísérlet létrehozása
> * Az adatterhelés és a scikit betanítása – modellek
> * Betanítási eredmények megtekintése a portálon
> * A legjobb modellt beolvasása

## <a name="prerequisites"></a>Előfeltételek

Az egyetlen előfeltétel az előző oktatóanyag, a [telepítési környezet és a munkaterület](tutorial-1st-experiment-sdk-setup.md)futtatása.

## <a name="connect-workspace-and-create-experiment"></a>Munkaterület összekötése és kísérlet létrehozása

Importálja `Workspace` az osztályt, és töltse be az előfizetési `config.json` adatokat a fájlból az aktuális könyvtárban található JSON-fájlhoz tartozó függvény `from_config().` használatával, de a fájlra mutató elérésiút-paramétert is megadhat. a `from_config(path="your/file/path")`használata. Ha ezt a jegyzetfüzetet a munkaterületen lévő felhőalapú jegyzetfüzet-kiszolgálón futtatja, a fájl automatikusan megjelenik a gyökérkönyvtárban.

Ha a következő kód további hitelesítést kér, egyszerűen illessze be a hivatkozást egy böngészőben, és adja meg a hitelesítési jogkivonatot.


```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Most hozzon létre egy kísérletet a munkaterületen. A kísérlet egy másik, alapszintű felhőalapú erőforrás, amely a próbaverziók gyűjteményét jelöli (az egyes modellek futtatása). Ebben az oktatóanyagban a kísérletet használja a futtatások létrehozásához és a modell képzésének nyomon követéséhez a Azure Portal. A paraméterek közé tartozik a munkaterület-hivatkozás, valamint a kísérlet karakterlánc-neve.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Adatgyűjtés és felkészülés a képzésre

Ebben az oktatóanyagban a diabétesz-adatkészletet használja, amely a scikit-Learn csomagban található, előre normalizált adathalmaz. Ez az adathalmaz olyan szolgáltatásokat használ, mint az Age, a nemek és a BMI a diabéteszes megbetegedések előrehaladásának előrejelzéséhez. Töltse be az adatait a `load_diabetes()` statikus függvényből, és ossza ki őket képzésbe és tesztelési készletekbe a használatával. `train_test_split()` Ez a függvény elkülöníti az adattípusokat, így a modell nem tartalmaz olyan, a következő képzések teszteléséhez szükséges adatait.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
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

1. A `alphas` tömbben lévő összes alfa-hiperparaméter esetében új Futtatás jön létre a kísérleten belül. A rendszer naplózza az alfa-értéket az egyes futtatások megkülönböztetése érdekében.
1. Az egyes futtatások során a Ridge-modell példánya, betanítása és előrejelzések futtatására szolgál. A rendszer kiszámítja a tényleges és az előre jelzett értékeket, majd naplózza a futtatást. Ezen a ponton a futtatáshoz metaadatok vannak csatolva az Alpha értékhez és a gyökátlagos pontosságához.
1. Ezután az egyes futtatásokhoz tartozó modell szerializálva lesz, és a futtatásra van feltöltve. Ez lehetővé teszi a modell letöltését a portálon futtatott fájlból.
1. Minden iteráció végén meghívja `run.complete()`a futtatást.

A képzés befejezését követően hívja meg a `experiment` változót, hogy beolvassa a kísérletre mutató hivatkozást a portálon.

```python
experiment
```

<table style="width:100%"><tr><th>Name (Név)</th><th>Munkaterület</th><th>Jelentés lapja</th><th>Docs oldal</th></tr><tr><td>cukorbetegség – kísérlet</td><td>saját-munkaterület neve</td><td>Az Azure Portalra mutató hivatkozás</td><td>Hivatkozás a dokumentációra</td></tr></table>

## <a name="view-training-results-in-portal"></a>Képzés eredményeinek megtekintése a portálon

Az **Azure Portalra mutató hivatkozást** követve a fő kísérlet oldalára kerül. Itt láthatja a kísérletben szereplő összes egyéni futtatást. Minden egyéni naplózott érték (`alpha_value` és `rmse`ebben az esetben) az egyes futtatások mezői lesznek, és elérhetővé válnak a kísérlet oldal tetején található diagramok és csempék számára is. Egy naplózott metrika diagramhoz vagy csempéhez való hozzáadásához vigye fölé a kurzort, kattintson a Szerkesztés gombra, és keresse meg az egyéni naplózott metrikát.

Ha több száz vagy több ezer különálló futtatást használ, ezen az oldalon könnyedén megtekintheti a betanított modelleket, és hogy miként változnak az egyedi mérőszámok az idő múlásával.

![A fő kísérlet oldala a portálon](./media/tutorial-quickstart/experiment-main.png)

Az `RUN NUMBER` oszlopban található futtatási szám hivatkozásra kattintva megtekintheti az egyes futtatások lapját. Az alapértelmezett lapon a **részletek** részletesebb információkat jelenítenek meg az egyes futtatásokról. Navigáljon a **kimenetek** lapra, és láthatja `.pkl` , hogy melyik modellhez lett feltöltve a Futtatás során az egyes képzések ismétlése során. Itt letöltheti a modell fájlját, nem kell manuálisan áttanítania.

![A portálon a Részletek lap futtatása](./media/tutorial-quickstart/model-download.png)

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

A legjobb futtatási azonosítóval lekérheti az egyes futtatásokat a `Run` konstruktorral együtt a kísérlet objektummal. Ezután hívja `get_file_names()` meg az erről a futtatásról letölthető összes fájl megtekintését. Ebben az esetben csak egy fájlt töltött fel minden futtatáshoz a betanítás során.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Hívja `download()` meg a Futtatás objektumot, és adja meg a letölteni kívánt modell fájlnevét. Alapértelmezés szerint ez a függvény letölti az aktuális könyvtárat.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ne hajtsa végre ezt a szakaszt, ha más Azure Machine Learning szolgáltatás-oktatóanyagokat szeretne futtatni.

### <a name="stop-the-notebook-vm"></a>A notebook virtuális gép leállítása

Ha Felhőbeli jegyzetfüzet-kiszolgálót használt, állítsa le a virtuális gépet, ha nem használja a költségeket.

1. A munkaterületen válassza a **notebook virtuális gépek**lehetőséget.

   ![A virtuális gép kiszolgálójának leállítása](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. Válassza ki a virtuális gépet a listából.

1. Válassza a **Leállítás**lehetőséget.

1. Ha készen áll a kiszolgáló ismételt használatára, válassza az **Indítás**lehetőséget.

### <a name="delete-everything"></a>Mindent törölni

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő feladatokat végezte el:

> [!div class="checklist"]
> * Csatlakoztatta a munkaterületet, és létrehozott egy kísérletet
> * Betöltött adatfeldolgozási és betanított scikit-modellek
> * Megtekintett képzések eredményei a Portálon és a beolvasott modellek

[A modell üzembe helyezése](tutorial-deploy-models-with-aml.md) az Azure Machine Learning.
Ismerje meg, hogyan fejleszthet [automatizált gépi tanulási](tutorial-auto-train-models.md) kísérleteket.
