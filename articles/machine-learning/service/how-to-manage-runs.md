---
title: Indítsa el, figyelését és a Python a betanítási Futtatás megszakítása
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan indítsa el, a címke állapotát állítja, és a machine learning-példakísérleteket rendezéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: 82df2258116ce55fa440b67ec0a66b106d0d72c7
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471494"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Indítsa el, figyelését és a Python a betanítási Futtatás megszakítása

A [Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) figyelése, rendszerezése és kezelése a képzés és a Kísérletezési futtatások többféle módszerrel teszi lehetővé.

Ebben az útmutatóban példát a következő feladatok közül:

* [Teljesítmény figyelése](#monitor)
* [Szakítsa meg, vagy sikertelen futtatások](#cancel)
* [Gyermek-futtatások létrehozása](#children)
* [Címke és a futtatások keresése](#tag)

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Az Azure Machine Learning szolgáltatás munkaterületén. Lásd: [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](setup-create-workspace.md).

* Az Azure Machine Learning SDK telepítve van a Pythonhoz készült (1.0.21 verzió vagy újabb). Telepíteni, vagy az SDK legújabb verziójára frissítse, nyissa meg a [telepítése vagy frissítése az SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) lapot.

    Az Azure Machine Learning SDK verziójának ellenőrzéséhez használja a következő kódot.

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-a-run-and-set-its-status"></a>Indítsa el a futtató és az állapot beállítása

Állítsa be a kísérlethez importálásával a [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [kísérletezhet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [futtatása](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), és [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) származó a [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) csomagot.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Indítsa el a futtató és a és a naplózási folyamatot a [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) metódust.

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

A Futtatás a állapotának lekérése [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--).

```Python
print(notebook_run.get_status())
```

További részletes információkat a futtatási használatát [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--).

```Python
notebook_run.get_details()
```

Ha a Futtatás sikeres befejezését követően a [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) befejezettként jelöli azt a módszert.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Is használhatja a Python `with...as` mintát. Ebben a környezetben a Futtatás lesz automatikusan olvasottként jelöli meg magát befejezettként hatókörén kívül esik a futtatás során. Ezzel a módszerrel nem kell manuálisan megjelölése a Futtatás befejeződött.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>Szakítsa meg, vagy sikertelen futtatások

 Ha hibát láthatja, hogy vagy a Futtatás úgy tűnik, hogy időt igénybe Befejezés, használja a [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metódus a Futtatás leállítása, mielőtt végzett, és szabadként, meg lett szakítva.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Ha a Futtatás befejeződik, de hasonló hibát tartalmaz, a helytelen tanítási szkriptet használta, akkor használhatja a [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) megjelölése sikertelenként metódust.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>Gyermek-futtatások létrehozása

Hozzon létre gyermek csoportba a kapcsolódó fut le, mint a különböző hiperparaméter finomhangolása az ismétlések futtatások.

Ez a Kódpélda a hello_with_children.py parancsfájl használatával hozzon létre egy batch belül egy beküldött futtatási használata az öt gyermek futtatások a [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metódust.

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Gyermek teljes automatikusan fut, akkor helyezze át a hatókörén kívül.

Gyermek futtatások egyenként is elindíthatja, de egyes létrehozása hálózati hívását eredményezi, mert egy kötegelt futtatások-nél kevésbé hatékonyak.

Használja a [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) lekérdezni a gyermek módszer egy adott szülő futtatja.

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>Címke és a futtatások keresése

Az Azure Machine Learning szolgáltatás segítségével tulajdonságait és címkéit rendezése és lekérdezése a futtatások fontos információkat.

### <a name="add-properties-and-tags"></a>Tulajdonságok és címkék hozzáadása

Használja a [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) kereshető metaadatokkal hozzáadása a futtatások. Például a következő kódot a Futtatás ad hozzá a "Szerző" tulajdonság.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Tulajdonságok nem módosíthatók, ez akkor hasznos, mint egy állandó rekord a naplózási célokra. Az alábbi példakód azt eredményezi, hiba, mivel az előző kód "azureml-felhasználó" a "Szerző" tulajdonság már hozzáadtunk.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

A címkék, de még módosítható. Használat [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) hozzáadása a kísérlet a fogyasztók kereshető, és a egy értelmes információkat.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Egy egyszerű címkét is hozzáadhat. Megjelenik a címke értéke szótár `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Lekérdezés tulajdonságait és címkéit

Futtatások kérhet le, amelyek megfelelnek az adott tulajdonságait és címkéit-kísérleten belülről.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Példa notebookok

A következő notebookok a jelen cikk fogalmait bemutatása:

* API-k naplózása kapcsolatos további információkért tekintse meg a [naplózási API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* További információ a kezeléséről, az Azure Machine Learning SDK fut, lásd: a [futtatások notebook kezelése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>További lépések

* A kísérletek metrikáinak bejelentkezni, lásd: a [metrikák jelentkezzen a betanítási futtatások során](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment) cikk.