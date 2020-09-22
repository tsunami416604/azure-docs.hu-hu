---
title: 'Oktatóanyag: ""Helló világ!"alkalmazás" Python-szkript futtatása'
titleSuffix: Azure Machine Learning
description: Az Azure ML első lépések sorozatának 2. része azt mutatja be, hogyan lehet beküldeni egy triviális "Hello World" Python-szkriptet a felhőbe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 98f4c30d03763e070b1bdc32a5e6e099556916ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946571"
---
# <a name="tutorial-run-hello-world-python-script-part-2-of-4"></a>Oktatóanyag: ""Helló világ!"alkalmazás" Python-szkript futtatása (2. rész/4)

Ebből az oktatóanyagból megtudhatja, hogyan küldhet és futtathat Python ""Helló világ!"alkalmazás" parancsfájlt a Azure Machine Learning Python SDK használatával.

Ez az oktatóanyag **egy négy részből álló oktatóanyag-sorozat második része** , amelyben megismerheti a Azure Machine learning és az Azure-ban végzett feladatok-alapú gépi tanulási feladatok alapjait. Ez az oktatóanyag az oktatóanyag 1. részében kiépített munkából áll, [amely a helyi gépet Azure Machine learning számára állítja be](
tutorial-1st-experiment-sdk-setup-local.md).

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * ""Helló világ!"alkalmazás" Python-szkript létrehozása és futtatása helyileg
> * Hozzon létre egy Python-vezérlő parancsfájlt a "Hello World" beküldéséhez Azure Machine Learning
> * A Azure Machine Learning fogalmak ismertetése a vezérlő parancsfájlban
> * ""Helló világ!"alkalmazás" elküldése és futtatása
> * A kód kimenetének megtekintése a felhőben

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem rendelkezik Azure Machine Learning munkaterülettel, fejezze be [az oktatóanyag 1. részét a helyi gép beállításához](tutorial-1st-experiment-sdk-setup-local.md) .
- A Python nyelv és a gépi tanulási munkafolyamatok bevezető ismerete.
- Helyi fejlesztési környezet. Ez magában foglalja a következőket: de nem korlátozódik a Visual Studio Code, a Jupyter vagy a Notebookshoz.
- Python (3.5-3.7-es verzió).

## <a name="create-and-run-a-python-script-locally"></a>Python-szkriptek helyi létrehozása és futtatása

Hozzon létre egy új alkönyvtárat `src` a könyvtár alatt a `tutorial` Azure Machine learning számítási fürtön futtatni kívánt kód tárolásához. Az `src` alkönyvtárban hozza létre a `hello.py` Python-szkriptet:

```python
# src/hello.py
print("Hello world!")
```

A Project Directory-struktúra most a következőképpen fog kinézni:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Parancsfájl helyi tesztelése

A kódot helyileg is futtathatja, amely a kód interaktív hibakeresésének előnyeit használja kedvenc IDE vagy egy terminálon keresztül:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Vezérlő parancsfájl létrehozása

A *vezérlési parancsfájlok* lehetővé teszik a szkript futtatását `hello.py` a felhőben.  A vezérlő parancsfájl segítségével szabályozhatja, hogy a gépi tanulási kód hogyan és hol fusson.  

Az oktatóanyag könyvtárában hozzon létre egy nevű új Python `03-run-hello.py` -fájlt, és másolja és illessze be az alábbi kódot a fájlba:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>A kód értelmezése

Itt látható a vezérlő parancsfájl működésének leírása:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      A [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) a Azure Machine learning munkaterülethez csatlakozik, hogy kommunikálni tudjon a Azure Machine learning erőforrásaival.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      A [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) egyszerű módszert kínál több Futtatás egyetlen névvel való rendszerezésére. Később láthatja, hogyan könnyíti meg a kísérletek a több tucat futtatás közötti mérőszámok összehasonlítását.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      A [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) becsomagolja a `hello.py` kódot, és átadja a munkaterületnek. Ahogy a neve is sugallja, ezt az osztályt használhatja annak _konfigurálásához_ , hogy a _parancsfájl_ hogyan _fusson_ Azure Machine learning. Azt is meghatározza, hogy a parancsfájl milyen számítási célt fog futni.  Ebben a kódban a cél a [telepítési oktatóanyagban](tutorial-1st-experiment-sdk-setup-local.md)létrehozott számítási fürt.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Elküldi a parancsfájlt. Ezt a beküldést [futtatásnak](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true)nevezzük.  A Futtatás egyetlen végrehajtást ágyaz be a kódjába. Egy futtatással figyelheti a parancsfájl előrehaladását, rögzítheti a kimenetet, elemezheti az eredményeket, megjelenítheti a metrikákat és egyéb műveleteket is alkalmazhat.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        Az `run` objektum egy leírót biztosít a kód végrehajtásához. A Azure Machine Learning Studio és a Python-szkriptből kinyomtatott URL-címmel figyelheti a folyamat előrehaladását.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>A kód elküldése és futtatása a felhőben

Futtassa a vezérlési parancsfájlt, amely viszont a `hello.py` [telepítési oktatóanyagban](tutorial-1st-experiment-sdk-setup-local.md)létrehozott számítási fürtön fut.

```bash
python 03-run-hello.py
```

## <a name="monitor-your-code-in-the-cloud-using-studio"></a>A kód figyelése a felhőben a Studio használatával

A kimenet tartalmazni fog egy hivatkozást a Azure Machine Learning studióhoz, amely a következőképpen néz ki: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Kövesse a hivatkozást, és navigáljon a **kimenetek és naplók** lapra. Itt a következőhöz hasonló fájl látható `70_driver_log.txt` :

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

A 8. sorban a "Helló világ!" kifejezés látható kimeneti.

A `70_driver_log.txt` fájl egy futtatásból származó standard kimenetet tartalmaz. Ez a fájl hasznos lehet a felhőben futtatott távoli futtatások hibakeresése során.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy egyszerű "Hello World" szkriptet vettünk igénybe, és az Azure-on futtatták. Megismerte, hogyan csatlakozhat a Azure Machine Learning-munkaterülethez, létrehozhat egy kísérletet, és elküldheti a `hello.py` kódot a felhőbe.

A következő oktatóanyagban ezeket a betanulásokat úgy építheti be, hogy valami érdekesebb, mint a `print("Hello world!")` .

> [!div class="nextstepaction"]
> [Oktatóanyag: Modell betanítása](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Ha itt szeretné befejezni az oktatóanyag-sorozatot, és nem halad a következő lépéssel, ne feledje, hogy [kiüríti az erőforrásokat](tutorial-1st-experiment-bring-data.md#clean-up-resources)
