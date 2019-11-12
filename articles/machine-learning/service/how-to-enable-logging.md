---
title: Bejelentkezés engedélyezése Azure Machine Learning
description: Megtudhatja, hogyan engedélyezheti a naplózást Azure Machine Learning az alapértelmezett Python-naplózási csomaggal, valamint az SDK-specifikus funkciók használatával.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 91e00b8e1d13f69ae3ba446bcbc09f06f387c439
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931113"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Bejelentkezés engedélyezése Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

A Azure Machine Learning Python SDK lehetővé teszi, hogy az alapértelmezett Python-naplózási csomaggal engedélyezze a naplózást, valamint a helyi naplózáshoz és a portálon a munkaterületre való bejelentkezéshez egyaránt használja az SDK-specifikus funkciókat. A naplók valós idejű információkat biztosítanak a fejlesztőknek az alkalmazás állapotáról, és segíthetnek a hibák vagy figyelmeztetések diagnosztizálásában. Ebből a cikkből megismerheti a naplózás engedélyezésének különböző módjait a következő területeken:

> [!div class="checklist"]
> * Modellek és számítási célok betanítása
> * Rendszerkép létrehozása
> * Üzembe helyezett modellek
> * Python-`logging` beállításai

[Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md). Az SDK-val kapcsolatos további információkért tekintse meg az [útmutatót](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) .

## <a name="training-models-and-compute-target-logging"></a>Képzési modellek és számítási cél naplózása

Több módon is engedélyezheti a naplózást a modell betanítási folyamata során, és a bemutatott példák a közös tervezési mintákat szemléltetik. A futtatással kapcsolatos adatait könnyedén naplózhatja a munkaterületen a felhőben a `start_logging` függvény használatával a `Experiment` osztályban.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

További naplózási függvényekért tekintse meg a [futtatási](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) osztály dokumentációját.

Az alkalmazás állapotának helyi naplózásának engedélyezéséhez a betanítási folyamat során használja a `show_output` paramétert. A részletes naplózás engedélyezése lehetővé teszi, hogy megtekintse a betanítási folyamat részleteit, valamint a távoli erőforrásokkal vagy számítási célokkal kapcsolatos információkat. A következő kód használatával engedélyezheti a naplózást a kísérlet beküldéséhez.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Ugyanezt a paramétert használhatja a `wait_for_completion` függvényben is az eredményül kapott futtatásnál.

```python
run.wait_for_completion(show_output=True)
```

Az SDK az alapértelmezett Python-naplózási csomag használatát is támogatja bizonyos helyzetekben a betanításhoz. A következő példa lehetővé teszi `INFO` naplózási szintjét egy `AutoMLConfig` objektumban.

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

Állandó számítási cél létrehozásakor a `show_output` paramétert is használhatja. A `wait_for_completion` függvény paraméterének megadásával engedélyezheti a naplózást a számítási cél létrehozásakor.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Naplózás a rendszerkép létrehozásakor

A naplózás engedélyezése a rendszerkép létrehozásakor lehetővé teszi, hogy a felépítési folyamat során hibákat láthasson. Állítsa be a `show_output` paramétert a `wait_for_deployment()` függvényen.

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

A korábban üzembe helyezett webszolgáltatás naplófájljainak beolvasásához töltse be a szolgáltatást, és használja a `get_logs()` függvényt. Előfordulhat, hogy a naplók részletes információkat tartalmaznak az üzembe helyezés során felmerülő hibákról.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

A webszolgáltatás egyéni verem-nyomkövetéseit a Application Insights engedélyezésével is naplózhatja, amely lehetővé teszi a kérés/válasz időpontjának, a meghibásodási arányok és a kivételek figyelését. A Application Insights engedélyezéséhez hívja meg a `update()` függvényt egy meglévő webszolgáltatáson.

```python
service.update(enable_app_insights=True)
```

További információ a Application Insights Azure Machine Learning Studióban való használatáról: [útmutató](how-to-enable-app-insights.md) .

## <a name="python-native-logging-settings"></a>Python natív naplózási beállításai

Az SDK egyes naplói tartalmazhatnak olyan hibát, amely arra utasítja a naplózási szint beállítását, hogy a HIBAKERESÉSt végezzen. A naplózási szint megadásához adja hozzá a következő kódot a parancsfájlhoz.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
