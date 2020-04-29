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
ms.date: 03/05/2020
ms.openlocfilehash: 73b9ae6bc3c15526bfdafd74330c7b86286631b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78396149"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Bejelentkezés engedélyezése Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A Azure Machine Learning Python SDK lehetővé teszi, hogy az alapértelmezett Python-naplózási csomaggal engedélyezze a naplózást, valamint a helyi naplózáshoz és a portálon a munkaterületre való bejelentkezéshez egyaránt használja az SDK-specifikus funkciókat. A naplók valós idejű információkat biztosítanak a fejlesztőknek az alkalmazás állapotáról, és segíthetnek a hibák vagy figyelmeztetések diagnosztizálásában. Ebből a cikkből megismerheti a naplózás engedélyezésének különböző módjait a következő területeken:

> [!div class="checklist"]
> * Modellek és számítási célok betanítása
> * Rendszerkép létrehozása
> * Üzembe helyezett modellek
> * Python `logging` -beállítások

[Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md). Az SDK-val kapcsolatos további információkért tekintse meg az [útmutatót](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) .

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

Ugyanezt a paramétert használhatja a `wait_for_completion` függvényben az eredményül kapott futtatásnál is.

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

További információ: [adatok figyelése és gyűjtése a ml webszolgáltatás-végpontokról](how-to-enable-app-insights.md).

## <a name="python-native-logging-settings"></a>Python natív naplózási beállításai

Az SDK egyes naplói tartalmazhatnak olyan hibát, amely arra utasítja a naplózási szint beállítását, hogy a HIBAKERESÉSt végezzen. A naplózási szint megadásához adja hozzá a következő kódot a parancsfájlhoz.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>További lépések

* [A ML webszolgáltatás-végpontokról származó adatok figyelése és gyűjtése](how-to-enable-app-insights.md)