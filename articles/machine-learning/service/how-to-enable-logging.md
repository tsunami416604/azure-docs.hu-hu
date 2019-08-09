---
title: Bejelentkezés engedélyezése Azure Machine Learning szolgáltatásban
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan engedélyezheti a naplózást Azure Machine Learning szolgáltatásban az alapértelmezett Python-naplózási csomaggal, valamint az SDK-specifikus funkciók használatával.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 9deb369dc812616cd7256bbab4101531ca08f127
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847924"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Bejelentkezés engedélyezése Azure Machine Learning szolgáltatásban

A Azure Machine Learning Python SDK lehetővé teszi, hogy az alapértelmezett Python-naplózási csomaggal engedélyezze a naplózást, valamint a helyi naplózáshoz és a portálon a munkaterületre való bejelentkezéshez egyaránt használja az SDK-specifikus funkciókat. A naplók valós idejű információkat biztosítanak a fejlesztőknek az alkalmazás állapotáról, és segíthetnek a hibák vagy figyelmeztetések diagnosztizálásában. Ebből a cikkből megismerheti a naplózás engedélyezésének különböző módjait a következő területeken:

> [!div class="checklist"]
> * Modellek és számítási célok betanítása
> * Lemezkép létrehozása
> * Üzembe helyezett modellek
> * Python `logging` -beállítások

[Hozzon létre egy Azure Machine learning szolgáltatás](how-to-manage-workspace.md)munkaterületet. Az SDK-val kapcsolatos további információkért tekintse meg az [útmutatót](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) .

## <a name="training-models-and-compute-target-logging"></a>Képzési modellek és számítási cél naplózása

Több módon is engedélyezheti a naplózást a modell betanítási folyamata során, és a bemutatott példák a közös tervezési mintákat szemléltetik. A futtatással kapcsolatos adatait könnyedén naplózhatja a munkaterületen a felhőben, ha `start_logging` a függvényt `Experiment` használja az osztályban.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

További naplózási függvényekért tekintse meg a [futtatási](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) osztály dokumentációját.

Az alkalmazás állapotának helyi naplózásának engedélyezéséhez a betanítási `show_output` folyamat során használja a (z) paramétert. A részletes naplózás engedélyezése lehetővé teszi, hogy megtekintse a betanítási folyamat részleteit, valamint a távoli erőforrásokkal vagy számítási célokkal kapcsolatos információkat. A következő kód használatával engedélyezheti a naplózást a kísérlet beküldéséhez.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Ugyanezt a paramétert `wait_for_completion` használhatja a függvényben az eredményül kapott futtatásnál is.

```python
run.wait_for_completion(show_output=True)
```

Az SDK az alapértelmezett Python-naplózási csomag használatát is támogatja bizonyos helyzetekben a betanításhoz. A következő példa egy `INFO` `AutoMLConfig` objektum naplózási szintjét engedélyezi.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

A `show_output` paramétert állandó számítási cél létrehozásakor is használhatja. A `wait_for_completion` függvény paraméterének megadásával engedélyezheti a naplózást a számítási cél létrehozásakor.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Naplózás a rendszerkép létrehozásakor

A naplózás engedélyezése a rendszerkép létrehozásakor lehetővé teszi, hogy a felépítési folyamat során hibákat láthasson. Állítsa be a `wait_for_deployment()`paramétert a függvényen. `show_output`

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                       image=image,
                                       name="example-image",
                                       workspace=ws
                                       )

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Üzembe helyezett modellek naplózása

A korábban üzembe helyezett webszolgáltatás naplófájljainak beolvasásához töltse be a szolgáltatást, és használja `get_logs()` a függvényt. Előfordulhat, hogy a naplók részletes információkat tartalmaznak az üzembe helyezés során felmerülő hibákról.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

A webszolgáltatás egyéni verem-nyomkövetéseit a Application Insights engedélyezésével is naplózhatja, amely lehetővé teszi a kérés/válasz időpontjának, a meghibásodási arányok és a kivételek figyelését. A Application Insights `update()` engedélyezéséhez hívja meg a függvényt egy meglévő webszolgáltatáson.

```python
service.update(enable_app_insights=True)
```

További információt [](how-to-enable-app-insights.md) a Azure Portal Application Insights használatáról a következő témakörben talál:.

## <a name="python-native-logging-settings"></a>Python natív naplózási beállításai

Az SDK egyes naplói tartalmazhatnak olyan hibát, amely arra utasítja a naplózási szint beállítását, hogy a HIBAKERESÉSt végezzen. A naplózási szint megadásához adja hozzá a következő kódot a parancsfájlhoz.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
