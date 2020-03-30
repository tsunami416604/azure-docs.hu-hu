---
title: A Pythonban futó betanítási futtatások indítása, figyelése és megszakítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan indíthatja el, állíthatja be a gépi tanulási kísérletek állapotát, címkézheti és rendszerezheti a gépi tanulási kísérleteket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: 8c261a010a1e8f4d1be9b3883510eb38c37a15ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296867"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>A Pythonban futó betanítási futtatások indítása, figyelése és megszakítása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az [Azure Machine Learning SDK for Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)Machine Learning [CLI](reference-azure-machine-learning-cli.md)és [Az Azure Machine Learning stúdió](https://ml.azure.com) különböző módszereket biztosít a tanfolyamok figyelésére, rendszerezésére és kezelésére a betanítás és kísérletezés.

Ez a cikk példákat mutat be a következő feladatokra:

* Monitor futtatási teljesítmény.
* Megszakítás vagy sikertelen futtatás.
* Gyermekfuttatások létrehozása.
* Tag és megtalálni fut.

## <a name="prerequisites"></a>Előfeltételek

A következő elemekre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* Egy [Azure Machine Learning-munkaterület.](how-to-manage-workspace.md)

* Az Azure Machine Learning SDK python (1.0.21-es vagy újabb verzió). Az SDK legújabb verziójának telepítéséhez vagy frissítéséhez olvassa [el az SDK telepítése vagy frissítése](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)című témakört.

    Az Azure Machine Learning SDK verziójának ellenőrzéséhez használja a következő kódot:

    ```python
    print(azureml.core.VERSION)
    ```

* Az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) és [CLI bővítmény az Azure Machine Learninghez.](reference-azure-machine-learning-cli.md)

## <a name="start-a-run-and-its-logging-process"></a>Futtatás és naplózási folyamat indítása

### <a name="using-the-sdk"></a>Az SDK használata

Állítsa be a kísérletet a [Munkaterület,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) [a Kísérlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [a Futtatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)és a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) osztályok importálásával az [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) csomagból.

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Indítsa el a futtatást [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) és a naplózási folyamatot a metódussal.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>A CLI használata

A kísérlet futtatásának megkezdéséhez kövesse az alábbi lépéseket:

1. Egy rendszerhéjból vagy parancssorból az Azure CLI segítségével hitelesítheti magát az Azure-előfizetésben:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

1. Munkaterület-konfiguráció csatolása a betanítási parancsfájlt tartalmazó mappához. Cserélje `myworkspace` le az Azure Machine Learning-munkaterületét. Cserélje `myresourcegroup` le a munkaterületet tartalmazó Azure-erőforráscsoportra:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Ez a `.azureml` parancs létrehoz egy alkönyvtárat, amely példa runconfig és conda környezeti fájlokat tartalmaz. Egy `config.json` olyan fájlt is tartalmaz, amely az Azure Machine Learning-munkaterülettel való kommunikációhoz használható.

    További információ: [az ml mappa csatolása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. A futtatás elindításához használja a következő parancsot. Ha ezt a parancsot használja, adja meg a \*runconfig fájl nevét (a .runconfig előtti szöveget, ha a fájlrendszert nézi) a -c paraméterhez.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > A `az ml folder attach` parancs `.azureml` létrehozott egy alkönyvtárat, amely két példa runconfig fájlt tartalmaz.
    >
    > Ha olyan Python-parancsfájllal rendelkezik, amely programozott módon hoz létre egy futtatási konfigurációs objektumot, a [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) segítségével runconfig fájlként mentheti azt.
    >
    > További példa a runconfig [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)fájlokról: .

    További információ: [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

### <a name="using-azure-machine-learning-studio"></a>Az Azure Machine Learning stúdió használata

Ha el szeretne indítani egy folyamatfuttatást a tervezőben (előzetes verzió), kövesse az alábbi lépéseket:

1. Állítsa be az alapértelmezett számítási célt a folyamathoz.

1. Válassza a **Futtatás** lehetőséget a folyamatvászon tetején.

1. Válasszon ki egy kísérletet a folyamatfuttatások csoportosításához.

## <a name="monitor-the-status-of-a-run"></a>Futás állapotának figyelése

### <a name="using-the-sdk"></a>Az SDK használata

A metódussal való futtatás [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) állapotának beírása.

```python
print(notebook_run.get_status())
```

A futtatási azonosító, a végrehajtási idő és a futtatással kapcsolatos további részletek lekérte a [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) módszert.

```python
print(notebook_run.get_details())
```

Ha a futtatás sikeresen [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) befejeződött, a módszerrel megjelölheti befejezettként.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Ha a Python `with...as` tervezési mintáját használja, a futtatás automatikusan befejezettként jelöli meg magát, amikor a futtatás hatókörén kívül esik. Nem kell manuálisan megjelölnie a futtatást befejezettként.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>A CLI használata

1. A kísérlet hez futó futtatások listájának megtekintéséhez használja a következő parancsot. Cserélje `experiment` le a kísérlet nevére:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Ez a parancs egy JSON-dokumentumot ad vissza, amely a kísérlet futtatásaival kapcsolatos információkat sorolja fel.

    További információ: [az ml kísérlet lista](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Egy adott futtatással kapcsolatos információk megtekintéséhez használja a következő parancsot. Cserélje `runid` le a futtatás azonosítójára:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Ez a parancs egy JSON-dokumentumot ad vissza, amely a futtatással kapcsolatos információkat sorolja fel.

    További információ: [az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Az Azure Machine Learning stúdió használata

A kísérlet aktív futtatásainak számának megtekintéséhez a stúdióban.

1. Keresse meg a **Kísérletek szakaszt.** 

1. Válasszon ki egy kísérletet.

    A kísérlet oldalon láthatja az aktív számítási célok számát és az egyes futtatások időtartamát. 

1. Válasszon ki egy adott futtatási számot.

1. A **Naplók** lapon diagnosztikai és hibanaplók találhatók a folyamat futtatásához.


## <a name="cancel-or-fail-runs"></a>Megszakítás vagy sikertelen futtatás

Ha hibát észlel, vagy ha a futás túl sokáig tart, megszakíthatja a futást.

### <a name="using-the-sdk"></a>Az SDK használata

Ha meg szeretne szakítani egy sdk-vel történő futtatást, használja a [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) következő módszert:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Ha a futtatás befejeződik, de hibát tartalmaz (például helytelen betanítási [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) parancsfájlt használt), a módszer rel jelölheti meg sikertelenként.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>A CLI használata

A CLI használatával történő futtatás megszakításához használja a következő parancsot. Csere `runid` a futtatás azonosítójára

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

További információ: [az ml run Cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Az Azure Machine Learning stúdió használata

A stúdióban való futtatás megszakításához a következő lépésekkel:

1. Lépjen a futó folyamatra a **Kísérletek** vagy a **Folyamatok** szakaszban. 

1. Válassza ki a törölni kívánt folyamatfuttatási számot.

1. Az eszköztáron válassza a **Mégse lehetőséget.**


## <a name="create-child-runs"></a>Gyermekfuttatások létrehozása

Hozzon létre gyermekfuttatásokat a kapcsolódó futtatások csoportosításához, például a különböző hiperparaméter-hangolási ismétlések.

> [!NOTE]
> Gyermekfuttatások csak az SDK használatával hozhatók létre.

Ez a példakód a `hello_with_children.py` parancsfájl segítségével hoz létre egy öt gyermekfuttatásból álló köteget egy elküldött futtatásból a [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) módszerrel:

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
> Ahatókörből való kilépésekkor a gyermekfuttatások automatikusan befejezettként lesznek megjelölve.

Ha sok gyermekfuttatást szeretne [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) hatékonyan létrehozni, használja a módszert. Mivel minden létrehozás hálózati hívást eredményez, a futtatási köteg létrehozása hatékonyabb, mint egyenként létrehozni őket.

### <a name="submit-child-runs"></a>Gyermekfuttatások beküldése

A gyermekfuttatások szülőfuttatásból is elküldhetők. Ez lehetővé teszi a szülő- és gyermekfuttatások hierarchiáit, amelyek mindegyike különböző számítási célokon fut, és közös szülőfuttatási azonosítóval csatlakozik.

A ["submit_child()"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-) metódussal küldje el a gyermek futtatását egy szülőfuttatáson belülről. Ehhez a szülő futtatási parancsfájl, get a futtatási környezetben, és küldje el a gyermek fut a metódus használatával a ``submit_child`` környezeti példány.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

Egy gyermekfuttatáson belül megtekintheti a szülőfuttatás azonosítója:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>A lekérdezés gyermekfuttatása

Egy adott szülő gyermekfuttatásainak lekérdezéséhez [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) használja a módszert. Az ``recursive = True`` argumentum lehetővé teszi a gyermekek és unokák beágyazott fájának lekérdezését.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Futások címkézése és keresése

Az Azure Machine Learningben tulajdonságok és címkék használatával rendszerezheti és lekérdezheti a futtatásokat a fontos információkért.

### <a name="add-properties-and-tags"></a>Tulajdonságok és címkék hozzáadása

#### <a name="using-the-sdk"></a>Az SDK használata

Ha kereshető metaadatokat szeretne hozzáadni [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) a futtatásokhoz, használja a módszert. A következő kód például `"author"` hozzáadja a tulajdonságot a futtatáshoz:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

A tulajdonságok nem módosíthatók, ezért állandó rekordot hoznak létre naplózási célokra. A következő kódpélda hibát eredményez, mert `"azureml-user"` az `"author"` előző kódban már hozzáadtuk tulajdonságértékként:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

A tulajdonságoktól eltérően a címkék módosíthatók. Ha kereshető és tartalmas információkat szeretne hozzáadni [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) a kísérlet fogyasztóiszámára, használja a módszert.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Egyszerű karakterlánc-címkéket is hozzáadhat. Ha ezek a címkék kulcsként jelennek meg a `None`címkeszótárban, értéke a .

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>A CLI használata

> [!NOTE]
> A CLI használatával csak címkéket adhat hozzá vagy frissíthet.

Címke hozzáadásához vagy frissítéséhez használja a következő parancsot:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

További információ: [az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Lekérdezési tulajdonságok és címkék

Lekérdezési futtatható egy kísérleten belül, hogy visszatérjen a futtatások listáját, amelyek megfelelnek az adott tulajdonságok és címkék.

#### <a name="using-the-sdk"></a>Az SDK használata

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>A CLI használata

Az Azure CLI támogatja a [JMESPath-lekérdezéseket,](http://jmespath.org) amelyek a tulajdonságok és címkék alapján futtatható futtatások szűrésére használhatók. JMESPath-lekérdezés használata az Azure CLI használatával `--query` adja meg azt a paraméterrel. Az alábbi példák az alapvető lekérdezéseket jelenítik meg tulajdonságok és címkék használatával:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Az Azure CLI-eredmények lekérdezéséről további információt a [Query Azure CLI parancskimenetcímű témakörben talál.](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)

### <a name="using-azure-machine-learning-studio"></a>Az Azure Machine Learning stúdió használata

1. Nyissa meg a **Folyamatok szakaszt.**

1. A keresősáv segítségével címkéket, leírásokat, kísérletneveket és küldőneveket használó folyamatokat szűrhet.

## <a name="example-notebooks"></a>Példa jegyzetfüzetekre

Az alábbi jegyzetfüzetek bemutatják a cikk ben található fogalmakat:

* A naplózási API-król a [naplózási API-jegyzetfüzetben olvashat bővebben.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)

* Az Azure Machine Learning SDK-val való futtatások kezeléséről további információt a [Manage Runs notebook című témakörben talál.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)

## <a name="next-steps"></a>További lépések

* A kísérletek metrikáinak naplózásáról a [Metrikák naplózása a betanítási futtatások során](how-to-track-experiments.md)című témakörben olvashat.
* Az Azure Machine Learning erőforrásainak és naplóinak figyeléséről az [Azure Machine Learning figyelése.](monitor-azure-machine-learning.md)