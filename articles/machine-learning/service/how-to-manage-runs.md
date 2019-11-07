---
title: A betanítási futtatások elindítása, figyelése és megszakítása a Pythonban
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan indíthatja el, állíthatja be a gépi tanulási kísérletek állapotát, címkézheti és rendszerezheti azokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 32b477a108649abd3faedd70d1a3b54b31089b9a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574316"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>A betanítási futtatások elindítása, figyelése és megszakítása a Pythonban
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

A Python és [Machine learning parancssori](reference-azure-machine-learning-cli.md) felülethez készült [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) különböző módszereket biztosít a futtatások monitorozásához, rendszerezéséhez és felügyeletéhez.

Ez a cikk a következő feladatokra mutat be példákat:

* A futtatási teljesítmény figyelése.
* Megszakítás vagy sikertelen Futtatás.
* Gyermek-futtatások létrehozása.
* Címke és keresés futtatása.

## <a name="prerequisites"></a>Előfeltételek

A következő elemekre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).

* A Pythonhoz készült Azure Machine Learning SDK (1.0.21 vagy újabb verzió). Az SDK legújabb verziójának telepítéséhez vagy frissítéséhez lásd: [az SDK telepítése vagy frissítése](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    A Azure Machine Learning SDK verziójának vizsgálatához használja a következő kódot:

    ```python
    print(azureml.core.VERSION)
    ```

* Az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) és [cli bővítmény a Azure Machine Learninghoz](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Futtatás és a naplózási folyamat elindítása

### <a name="using-the-sdk"></a>Az SDK használata

Állítsa be a kísérletet a [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), a [kísérlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), a [Futtatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)és a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) osztályok importálásával a [azureml. Core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) csomagból.

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Indítsa el a futtatást és a naplózási folyamatot a [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) metódussal.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>A parancssori felület használata

A kísérlet futtatásának elindításához kövesse az alábbi lépéseket:

1. Egy rendszerhéjból vagy parancssorból az Azure CLI használatával hitelesítheti az Azure-előfizetését:

    ```azurecli-interactive
    az login
    ```

1. Csatoljon egy munkaterület-konfigurációt a betanítási parancsfájlt tartalmazó mappához. Cserélje le a `myworkspace` értéket a Azure Machine Learning munkaterületére. Cserélje le a `myresourcegroup` értéket a munkaterületet tartalmazó Azure-erőforráscsoport helyére:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Ez a parancs egy `.azureml` alkönyvtárat hoz létre, amely tartalmazza például a runconfig és a Conda környezeti fájlokat. Emellett tartalmaz egy `config.json` fájlt is, amely a Azure Machine Learning munkaterülettel való kommunikációhoz használható.

    További információ: [az ml mappa csatolása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. A Futtatás elindításához használja a következő parancsot. A parancs használatakor adja meg a runconfig-fájl nevét (a \*. runconfig előtt található szöveget, ha a fájlrendszert keresi) a-c paraméterrel.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > A `az ml folder attach` parancs létrehozta a `.azureml` alkönyvtárat, amely két példa runconfig-fájlt tartalmaz.
    >
    > Ha olyan Python-szkripttel rendelkezik, amely programozott módon hozza létre a futtatási konfigurációs objektumot, a [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) paranccsal mentheti RunConfig-fájlként.
    >
    > További példák a runconfig-fájlokra: [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    További információ: [az ml Run Submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

## <a name="monitor-the-status-of-a-run"></a>Futtatás állapotának figyelése

### <a name="using-the-sdk"></a>Az SDK használata

Egy Futtatás állapotának lekérése a [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) metódussal.

```python
print(notebook_run.get_status())
```

A futtatási azonosító, a végrehajtási idő és a Futtatás további részleteinek beszerzéséhez használja a [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) metódust.

```python
print(notebook_run.get_details())
```

A Futtatás sikeres befejeződése után a [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metódussal jelölheti meg befejezettként.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Ha a Python `with...as` tervezési mintát használja, a Futtatás automatikusan befejezettként fog megjelenni, ha a Futtatás nem a hatókörön kívül van. Nem kell manuálisan megjelölni a futtatást befejezettként.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>A parancssori felület használata

1. A kísérlet futtatási listájának megtekintéséhez használja a következő parancsot. Cserélje le a `experiment` értéket a kísérlet nevére:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Ez a parancs egy JSON-dokumentumot ad vissza, amely felsorolja a kísérlet futtatásával kapcsolatos információkat.

    További információ: [az ml-kísérletek listája](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Egy adott futtatással kapcsolatos információk megtekintéséhez használja az alábbi parancsot. Cserélje le a `runid` értéket a Futtatás AZONOSÍTÓjának helyére:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Ez a parancs egy JSON-dokumentumot ad vissza, amely felsorolja a futtatással kapcsolatos információkat.

    További információ: [az ml Run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).

## <a name="cancel-or-fail-runs"></a>Megszakítás vagy sikertelen Futtatás

Ha hibát észlel, vagy ha a futtatása túl sokáig tart, megszakíthatja a futtatást.

### <a name="using-the-sdk"></a>Az SDK használata

Ha az SDK használatával szeretne lemondani egy futtatást, használja a [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metódust:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Ha a Futtatás véget ér, de hibát tartalmaz (például a helytelen tanítási parancsfájlt használta), a [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) metódus használatával megjelölheti azt sikertelenként.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>A parancssori felület használata

Ha a parancssori felületen szeretné megszakítani a futtatást, használja a következő parancsot. Cserélje le a `runid` értéket a Futtatás azonosítójával.

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

További információ: [az ml Run Cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

## <a name="create-child-runs"></a>Gyermek-futtatások létrehozása

Hozzon létre egy alárendelt futtatásokat a kapcsolódó futtatások csoportosításához, például a különböző hiperparaméter-hangolási ismétlésekhez.

> [!NOTE]
> A gyermek-futtatásokat csak az SDK használatával lehet létrehozni.

Ez a példa a `hello_with_children.py` parancsfájl használatával hoz létre öt gyermekből álló köteget egy elküldött futtatásból a [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metódussal:

```python
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
> A hatókörből való kilépéskor a gyermek-futtatások automatikusan befejezettként vannak megjelölve.

Sok gyermek hatékony futtatásához használja a [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) metódust. Mivel minden létrehozás egy hálózati hívást eredményez, a futtatások kötegének létrehozása hatékonyabb, mint az egyiket a létrehozásuk.

### <a name="submit-child-runs"></a>Alárendelt futtatások küldése

Alárendelt futtatásokat is el lehet küldeni egy szülő futtatásból. Ez lehetővé teszi a szülő-és gyermek-futtatási hierarchiák létrehozását, amelyek mindegyike különböző számítási célokon fut.

A ["submit_child ()"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-) metódus használatával küldheti el a gyermek futtatását szülő futtatásból. Ha ezt a szülő futtatási parancsfájlban szeretné elvégezni, szerezze be a futtatási környezetet, és küldje el a gyermek futtatását a környezeti példány ``submit_child`` metódusának használatával.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

Gyermeken belüli futtatáskor megtekintheti a szülő futtatási AZONOSÍTÓját:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Lekérdezési gyermek futtatása

Egy adott szülő alárendelt futtatásának lekérdezéséhez használja a [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metódust. A ``recursive = True`` argumentum lehetővé teszi gyermekek és unokák beágyazott fájának lekérdezését.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Címke és keresés futtatása

Azure Machine Learning a tulajdonságok és címkék segítségével rendszerezheti és lekérdezheti a futtatásokat a fontos információkhoz.

### <a name="add-properties-and-tags"></a>Tulajdonságok és Címkék hozzáadása

#### <a name="using-the-sdk"></a>Az SDK használata

A kereshető metaadatok a futtatásokhoz való hozzáadásához használja a [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) metódust. A következő kód például hozzáadja a `"author"` tulajdonságot a futtatáshoz:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

A tulajdonságok nem változtathatók meg, ezért állandó rekordot hoznak létre naplózási célokra. A következő kódrészlet egy hibát eredményez, mert már hozzáadta a `"azureml-user"` értéket a `"author"` tulajdonság értékeként az előző kódban:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

A tulajdonságoktól eltérően a címkék változhatnak. A kísérlet felhasználói számára kereshető és hasznos információk hozzáadásához használja a [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) metódust.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Hozzáadhat egyszerű karakterlánc-címkéket is. Ha ezek a címkék a címke szótárában kulcsként jelennek meg, `None` értékkel rendelkeznek.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>A parancssori felület használata

> [!NOTE]
> A CLI használatával csak címkéket adhat hozzá vagy frissíthet.

Címke hozzáadásához vagy frissítéséhez használja a következő parancsot:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

További információ: [az ml Run Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Lekérdezés tulajdonságai és címkék

A kísérletek futtatásával lekérdezheti az adott tulajdonságokkal és címkékkel egyező futtatások listáját.

#### <a name="using-the-sdk"></a>Az SDK használata

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>A parancssori felület használata

Az Azure CLI támogatja a [JMESPath](http://jmespath.org) -lekérdezéseket, amelyek a tulajdonságok és címkék alapján szűrhetik a futtatásokat. Ha JMESPath-lekérdezést kíván használni az Azure CLI-vel, akkor a `--query` paraméterrel kell megadnia. Az alábbi példák a tulajdonságok és címkék használatával történő alapszintű lekérdezéseket mutatják be:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Az Azure CLI eredményeinek lekérdezésével kapcsolatos további információkért lásd: az [Azure CLI-parancs kimenetének lekérdezése](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

## <a name="example-notebooks"></a>Jegyzetfüzetek – példa

A következő jegyzetfüzetek a cikkben ismertetett fogalmakat mutatják be:

* A naplózási API-kkal kapcsolatos további tudnivalókért tekintse meg a [naplózási API jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* A Azure Machine Learning SDK-val való futtatásával kapcsolatos további információkért tekintse meg a [futtatási jegyzetfüzet kezelése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)című témakört.

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan naplózhatja a kísérletek mérőszámait, tekintse meg a következő témakört: a [betanítási mérőszámok](how-to-track-experiments.md).
* Ha szeretné megismerni, hogyan figyelheti Azure Machine Learning erőforrásait és naplóit, tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).