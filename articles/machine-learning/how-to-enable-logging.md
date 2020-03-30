---
title: Naplózás engedélyezése az Azure Machine Learningben
description: Ismerje meg, hogyan engedélyezheti a naplózást az Azure Machine Learningben mind az alapértelmezett Python-naplózási csomag, mind az SDK-specifikus funkciók használatával.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 03/05/2020
ms.openlocfilehash: 73b9ae6bc3c15526bfdafd74330c7b86286631b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78396149"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Naplózás engedélyezése az Azure Machine Learningben
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning Python SDK lehetővé teszi a naplózás engedélyezését mind az alapértelmezett Python-naplózási csomag használatával, valamint az SDK-specifikus funkciók használatával mind a helyi naplózáshoz, mind a munkaterületre való naplózáshoz a portálon. A naplók valós idejű információkat nyújtanak a fejlesztőknek az alkalmazás állapotáról, és segíthetnek a hibák vagy figyelmeztetések diagnosztizálásában. Ebben a cikkben a naplózás engedélyezésének különböző módjait ismerteti a következő területeken:

> [!div class="checklist"]
> * Képzési modellek és számítási célok
> * Kép létrehozása
> * Telepített modellek
> * Python-beállítások `logging`

[Hozzon létre egy Azure Machine Learning-munkaterületet.](how-to-manage-workspace.md) Az SDK-val kapcsolatos további információkért használja az [útmutatót.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

## <a name="training-models-and-compute-target-logging"></a>Képzési modellek és számítási cél naplózása

A modell betanítási folyamata során többféle módon engedélyezheti a naplózást, és a bemutatott példák bemutatják a gyakori tervezési mintákat. Az `start_logging` `Experiment` osztály függvényének használatával könnyedén naplózhatja a futtatással kapcsolatos adatokat a felhőben lévő munkaterületre.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

További naplózási funkciókat a [Futtatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) osztály referenciadokumentációjában talál.

Ha engedélyezni szeretné az alkalmazás állapotának `show_output` helyi naplózását a betanítási folyamat során, használja a paramétert. A részletes naplózás engedélyezése lehetővé teszi a betanítási folyamat részleteinek, valamint a távoli erőforrásokra vagy számítási célokra vonatkozó információk megtekintését. A következő kód segítségével engedélyezheti a naplózást a kísérlet beküldésekor.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Ugyanazt a paramétert is `wait_for_completion` használhatja a függvényben az eredményül kapott futtatáskor.

```python
run.wait_for_completion(show_output=True)
```

Az SDK is támogatja az alapértelmezett python naplózási csomag bizonyos forgatókönyvekben a betanítás. A következő példa lehetővé `INFO` teszi `AutoMLConfig` egy objektum naplózási szintjét.

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

A paramétert `show_output` is használhatja állandó számítási cél létrehozásakor. Adja meg a `wait_for_completion` paramétert a függvényben a naplózás engedélyezéséhez a számítási cél létrehozása során.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Telepített modellek naplózása

Naplók lekérése egy korábban telepített webszolgáltatás, töltse `get_logs()` be a szolgáltatást, és használja a funkciót. A naplók részletes információkat tartalmazhatnak a telepítés során előforduló hibákról.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Egyéni veremnyomkövetéseket is naplózhat a webszolgáltatáshoz az Application Insights engedélyezésével, amely lehetővé teszi a kérelmek/válaszok, a hibaarányok és a kivételek figyelését. Hívja `update()` meg a függvényt egy meglévő webszolgáltatáson az Application Insights engedélyezéséhez.

```python
service.update(enable_app_insights=True)
```

További információ: [Figyelés és adatok gyűjtése a pénzmosás elleni webszolgáltatás végpontjairól.](how-to-enable-app-insights.md)

## <a name="python-native-logging-settings"></a>Python natív naplózási beállításai

Az SDK bizonyos naplói olyan hibát tartalmazhatnak, amely arra utasítja, hogy a naplózási szintet DEBUG-ra állítsa. A naplózási szint beállításához adja hozzá a következő kódot a parancsfájlhoz.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>További lépések

* [Az ml-es webszolgáltatás végpontjaiból származó adatok figyelése és gyűjtése](how-to-enable-app-insights.md)