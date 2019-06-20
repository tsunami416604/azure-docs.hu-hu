---
title: Az Azure Machine Learning szolgáltatás MLflow használata
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan jelentkezhet, metrikákat és összetevők MLflow kódtár az Azure Machine Learning szolgáltatás használatával
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: b64051a4ef7b6a816b03562fef0452cbe9ce949a
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144029"
---
# <a name="how-to-use-mlflow-with-azure-machine-learning-service-preview"></a>MLflow használata az Azure Machine Learning szolgáltatás (előzetes verzió)

Ez a cikk bemutatja, hogyan MLflow a URI nyomon követése és naplózása API-t, együttesen néven is ismert MLflow követése, nyomon követése és naplózása a kísérlet metrikák és összetevők az Azure Machine Learning szolgáltatás a [Azure Machine Learning szolgáltatás munkaterületén](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace). Ha már használ MLflow nyomon követése a kísérletek, a munkaterület itt központosított, biztonságos és skálázható a betanítási metrikák és a modellek tárolására.

[MLflow](https://www.mlflow.org) egy nyílt forráskódú kódtár, a machine learning-példakísérleteket életciklusának kezelésére. [MLFlow követési](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) MLflow, naplózza, és nyomon követi a metrikák futtatása képzési részét képező és modell összetevők, a kísérletek helyileg, futnak-e a virtuális gépen, vagy egy távoli számítási fürt.
![az azure machine learning diagram mlflow](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-service-clients"></a>Hasonlítsa össze a MLflow és az Azure Machine Learning szolgáltatás ügyfelek

 Az alábbi táblázat foglalja össze a különböző ügyfelek által használható az Azure Machine Learning szolgáltatás és a megfelelő függvény képességeikkel.

 MLflow követési kínál metrika naplózást és az összetevő tárolási funkciókat, amelyek egyébként csak keresztül elérhető a [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

| | MLflow nyomon követése | Azure Machine Learning <br> Python SDK |  Azure Machine Learning <br> parancssori felület | Azure Portal|
|-|-|-|-|-|
| Munkaterület kezelése |   | ✓ |  ✓ | ✓  |
| Adattárak használata  |   | ✓ |  ✓ |    |
| Napló-metrikák      | ✓ | ✓ |    |    |
| Összetevők feltöltése | ✓ | ✓ |    |    |
| Metrikák megtekintése     | ✓ | ✓ | ✓  | ✓ |
| Számítások kezelése   |   | ✓ | ✓  | ✓ |
| Modellek üzembe helyezése    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>Előfeltételek

* [Telepítse a MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Az Azure Machine Learning Python SDK telepítése a helyi számítógépen, és hozzon létre egy Azure Machine Learning-munkaterület](setup-create-workspace.md#sdk). Az SDK-t biztosít a munkaterület eléréséhez MLflow esetén a kapcsolat.

## <a name="track-local-runs"></a>Nyomon követheti a helyi Futtatás

Telepítse a `azureml-contrib-run` használandó MLflow nyomon követése az Azure Machine Learning a kísérletek egy Jupyter Notebookot, vagy a code szerkesztőben helyileg futtassa a csomag.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>A azureml.contrib névtér gyakran, változik, mert dolgozunk a szolgáltatás fejlesztéséhez. Ezért semmit a névtérben lévő kell előzetes minősül, és nincs teljes egészében a Microsoft támogatja.

Importálás a `mlflow` és [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) osztályok MLflow eléréséhez a követési URI-t, és a munkaterület konfigurálása.

Az alábbi kódban a `get_mlflow_tracking_uri()` metódus a munkaterületet, rendeli hozzá egy egyedi URI-cím követési `ws`, és `set_tracking_uri()` a MLflow nyomon követése az URI-t arra a címre mutat.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>A nyomon követési URI érvényes, legfeljebb egy óráig vagy kevesebb. A szkript néhány üresjárati idő után indítsa újra, ha új URI használhatják a get_mlflow_tracking_uri API-t.

Állítsa be a MLflow kísérlet elé `set_experiment()` indítsa el a betanítási Futtatás `start_run()`. Ezután `log_metric()` aktiválja a MLflow naplózási API és a metrikák futtatása a tanítási naplózást.

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Nyomon követheti a távoli Futtatás

Távoli Futtatás lehetővé teszik a nagyobb teljesítményű számítási erőforrások, a modellek betanításához, például GPU-t a virtuális gépek vagy a Machine Learning Compute-fürtöket. Lásd: [állítsa be a modell betanítása és számítási célnak](how-to-set-up-training-targets.md) a különböző számítási lehetőségek ismertetése.

A számítási és a betanítási Futtatás környezet konfigurálása a [ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) osztály. Például `mlflow` és `azure-contrib-run` csomagokat a környezetben a pip [ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) szakaszban. Ezután hozhat létre [ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) a távoli számítási számítási célként.

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

A tanítási szkriptet importálni `mlflow` az API-k naplózása MLflow használatára, majd indítsa el a futtatási metrikákat naplózása.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

A számítási és a betanítási Futtatás konfigurációt, használja a `Experiment.submit("train.py")` metódust futtató elküldéséhez. Ez automatikusan beállítja a MLflow nyomon követése az URI-t, és arra utasítja a naplózást a MLflow a munkaterülethez.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Metrikák megtekintése és összetevők a munkaterületen

A metrikák és MLflow bejelentkezzenek az összetevők tartják a munkaterület a [az Azure portal](https://portal.azure.com). Bármikor megtekintheti őket, nyissa meg a munkaterületet, és a kísérlet keresése név alapján.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szeretné használni az a naplózott mérőszámok és összetevők a munkaterületen található, jelenleg nem érhető el arra, hogy törli őket külön. Helyette törölje az erőforráscsoportot, amely tartalmazza a tárfiók és a munkaterület, így nem kell díjat fizetniük:

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.

   ![Az Azure Portalon törlése](media/how-to-use-mlflow/delete-resources.png)

1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Adja meg az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet.

## <a name="example-notebooks"></a>Példa notebookok

A [Azure ML böngészését MLflow](https://aka.ms/azureml-mlflow-examples) mutatja be a jelen cikk fogalmait.

## <a name="next-steps"></a>További lépések

* [A modell üzembe](how-to-deploy-and-where.md).