---
title: Indítsa el, figyelését és a Python a betanítási Futtatás megszakítása
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan indítsa el, a címke állapotát állítja, és a machine-learning kísérletek rendezéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543593"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Indítsa el, figyelését és a Python a betanítási Futtatás megszakítása

A [Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) figyelése, rendszerezése és kezelése a képzés és a Kísérletezési futtatások többféle módszerrel teszi lehetővé.

Ez a cikk bemutatja a példák a következő feladatok közül:

* Teljesítmény figyelése
* Szakítsa meg, vagy sikertelen futtatások.
* Gyermek-futtatások létrehozása.
* Címke, és keresse meg a futtatások.

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine Learning szolgáltatás munkaterület](setup-create-workspace.md).

* Az Azure Machine Learning SDK Pythonhoz készült (1.0.21 verzió vagy újabb). Telepíteni, vagy frissítsen az SDK legújabb verzióját, lásd: [telepítése vagy frissítése az SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Az Azure Machine Learning SDK verziójának ellenőrzéséhez használja a következő kódot:

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>Indítsa el a futtató és a naplózás folyamata

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

## <a name="monitor-the-status-of-a-run"></a>A futtatási állapotának figyelése

Az állapotát a Futtatás a [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) metódust.

```Python
print(notebook_run.get_status())
```

További részletek a Futtatás lekéréséhez használja a [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) metódust.

```Python
notebook_run.get_details()
```

Ha a Futtatás sikeres befejezését követően a [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) befejezettként jelöli azt a módszert.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

A Python használatakor `with...as` mintát, a Futtatás lesz automatikusan olvasottként jelöli meg magát, amikor a hatókörén kívül esik a Futtatás befejeződött. Nem kell manuálisan megjelölése a Futtatás befejeződött.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

## <a name="cancel-or-fail-runs"></a>Szakítsa meg, vagy sikertelen futtatások

 Ha figyelje meg, hogy a hibát, vagy ha a Futtatás befejeződik, túl sokáig tart az [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metódus a Futtatás leállítása, mielőtt végzett, és szabadként, meg lett szakítva.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Ha a Futtatás befejeződik, de hibát tartalmaz (például a helytelen tanítási szkriptet használta), használhatja a [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) megjelölése sikertelenként metódust.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>Gyermek-futtatások létrehozása

Hozzon létre gyermek futtatások csoportosíthatja kapcsolódó fut le, például különböző hiperparaméter finomhangolása iteráció során.

Ez a kód példa a `hello_with_children.py` hozhat létre egy beküldött sorozaton belül az öt gyermek futtatások kötegelt parancsprogram a [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) módszer:

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
> Mozgás hatókörén kívül, gyermek fut automatikusan állapotúként befejeződött.

Gyermek futtatások egyenként is elindíthatja, de egyes létrehozása hálózati hívását eredményezi, mert egy kötegelt futtatások-nél kevésbé hatékonyak.

Lekérdezés az egy adott szülő-gyermek fut, használja a [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metódust.

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Címke és a futtatások keresése

Az Azure Machine Learning szolgáltatás segítségével tulajdonságait és címkéit rendezése és lekérdezése a futtatások fontos információkat.

### <a name="add-properties-and-tags"></a>Tulajdonságok és címkék hozzáadása

A futtatások kereshető metaadatokkal hozzáadásához használja a [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) metódust. Például az alábbi kód hozzáadja a `"author"` a Futtatás tulajdonságát:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Tulajdonságok nem módosíthatók, így azok naplózási célokra állandó rekord létrehozása. A következő kód például eredményez hibát, mert már hozzáadott `"azureml-user"` , a `"author"` tulajdonság értéke az a fenti kód:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Tulajdonságok, ellentétben a címkék olyan módosítható. A kísérlet a fogyasztók kereshető és jelentéssel bíró adatokat hozzáadásához használja a [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) metódust.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Egyszerű karakterlánc-címkéket is hozzáadhat. Ezekkel a címkékkel a címke szótár jelenik meg, ha rendelkeznek-e érték `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Lekérdezés tulajdonságait és címkéit

Futtatások lekérdezheti a konkrét tulajdonságok és címkék megfelelő futtatások listáját adja vissza egy kísérleten belülről.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Példa notebookok

A következő notebookok ebben a cikkben fogalmakat mutatják be:

* Az API-k naplózásával kapcsolatos további tudnivalókért tekintse meg a [naplózási API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Az Azure Machine Learning SDK fut, kezelésével kapcsolatos további információért, lásd: a [futtatások notebook kezelése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>További lépések

* Című cikk nyújt tájékoztatást, hogyan jelentkezhet a kísérletek metrikáit, [metrikák jelentkezzen a betanítási futtatások során](how-to-track-experiments.md).