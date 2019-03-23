---
title: Engedélyezze a naplózást az Azure Machine Learning szolgáltatás
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan engedélyezze a naplózási Python-csomag az Azure Machine Learning szolgáltatáshoz is az alapértelmezett naplózás, valamint SDK-specifikus funkciójával.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 02/20/2019
ms.openlocfilehash: ce510168e2aa92758a3468fa55ff7b2a8d39b547
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360267"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Engedélyezze a naplózást az Azure Machine Learning szolgáltatás

Az Azure Machine Learning Python SDK engedélyezze a naplózást is az alapértelmezett Python naplózási csomag használatával, valamint SDK-specifikus funkciókat, mind a helyi és bejelentkezés a munkaterülethez, a portál használatával teszi lehetővé. Naplók valós idejű, az alkalmazás állapotára vonatkozó információkat nyújtanak a fejlesztők számára, és segíthetnek a diagnosztizálás hibákat vagy figyelmeztetéseket. Ez a cikk bemutatja a következő területeken naplózásának engedélyezéséről különböző módszert is:

> [!div class="checklist"]
> * Képzési modelleket és számítási célnak
> * Lemezkép létrehozása
> * Üzembe helyezett modellnél
> * Python `logging` beállításai

[Hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](setup-create-workspace.md). Használja a [útmutató](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) további információ az SDK-t.

## <a name="training-models-and-compute-target-logging"></a>Képzési modelleket és számítási tároló naplózása

A modell betanítási folyamat során a naplózás engedélyezése több módon, és a példák gyakori tervezési minták mutatja be. Könnyedén bejelentkezhet Futtatás kapcsolatos adatokat a felhőben a munkaterülethez használatával a `start_logging` működnek a `Experiment` osztály.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Lásd: a dokumentáció a a [futtatása](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) további naplózás funkciók osztályt.

Ahhoz, hogy a betanítási folyamat során az alkalmazásállapot helyi naplózását, használja a `show_output` paraméter. Részletes naplózás engedélyezése lehetővé teszi, hogy a betanítási folyamat, valamint minden olyan távoli erőforrásokkal kapcsolatos információkat a részletek megtekintéséhez vagy számítási céljainak. A következő kód használatával kísérlet beküldéskor naplózás engedélyezése.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Használhatja ugyanezt a paramétert a a `wait_for_completion` függvényt az eredményül kapott futtatásakor.

```python
run.wait_for_completion(show_output=True)
```

Az SDK-t is támogatja az alapértelmezett python naplózási csomag bizonyos esetekben a betanításhoz. Az alábbi példában egy naplózási szintjét `INFO` a egy `AutoMLConfig` objektum.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task = 'regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Is használhatja a `show_output` paraméter egy állandó számítási célnak létrehozásakor. Adja meg a paramétert a `wait_for_completion` függvény számítási cél létrehozása során a naplózás engedélyezéséhez.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Lemezkép létrehozása során-naplózás

Lemezkép létrehozása során naplózás engedélyezése lehetővé teszi, hogy lát hibát az összeállítási folyamat során. Állítsa be a `show_output` a param a `wait_for_deployment()` függvény.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                            image=image,
                                            name="example-image",
                                            workspace=ws)

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Üzembe helyezett modellnél naplózása

Naplók lekérése egy korábban már üzembe helyezett webszolgáltatás, a szolgáltatás betöltése, és használja a `get_logs()` függvény. A naplók minden üzembe helyezés során fellépő hibák részletes információkat is tartalmazhat.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Is bejelentkezhet egyéni veremkiíratásokat Application Insights, amely lehetővé teszi a figyelő kérés/válasz többször, hibaarányok és kivételek engedélyezésével a webszolgáltatást. Hívja a `update()` funkciót, egy meglévő webes szolgáltatás, Application Insights engedélyezése.

```python
service.update(enable_app_insights=True)
```

Tekintse meg a [útmutató](how-to-enable-app-insights.md#enable-and-disable-in-the-portal) további információ az Application Insights használata az Azure Portalon.

## <a name="python-native-logging-settings"></a>Python natív naplózási beállítások

Az SDK bizonyos naplók tartalmazhat, amely arra utasítja, hogy a naplózási szint megadásához hibakeresési hiba. A naplózási szint megadásához adja hozzá a következő kódot a szkriptbe.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```