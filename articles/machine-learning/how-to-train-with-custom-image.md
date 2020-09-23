---
title: Modell betanítása egyéni Docker-rendszerkép használatával
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan taníthat modelleket egyéni Docker-rendszerképekkel Azure Machine Learningban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d90b56366cb22e80162983c982e861de608e4e9e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893115"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Modell betanítása egyéni Docker-rendszerkép használatával

Ebből a cikkből megtudhatja, hogyan használhat egyéni Docker-rendszerképet a modellek Azure Machine Learning használatával történő betanításakor. 

Az ebben a cikkben szereplő szkriptek a PET-képek besorolására szolgálnak. 

Míg Azure Machine Learning biztosít egy alapértelmezett Docker-alapképet, a Azure Machine Learning környezetekben is megadhat egy adott alapképet, például az Azure ML-alapú karbantartott [alapképek](https://github.com/Azure/AzureML-Containers) egyikét vagy a saját [Egyéni rendszerképét](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Az egyéni alaplemezképek lehetővé teszik a függőségek kezelését, valamint a betanítási feladatok végrehajtásakor szigorúbb vezérlést biztosít az összetevők verzióihoz. 

## <a name="prerequisites"></a>Előfeltételek 
Futtassa ezt a kódot ezen környezetek bármelyikén:
* Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre
    * Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    * A Azure Machine Learning [példák tárházában](https://github.com/Azure/azureml-examples)keressen egy befejezett jegyzetfüzetet a következő könyvtárra való navigálással: **jegyzetfüzetek > fastai > Train-pets-resnet34. ipynb** 

* Saját Jupyter Notebook-kiszolgáló
    * Hozzon létre egy [munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    * A [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
    * Az interneten elérhető [Azure Container Registry](/azure/container-registry) vagy más Docker-beállításjegyzék.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása 
Ez a szakasz a betanítási kísérletet egy munkaterület inicializálásával, egy kísérlet létrehozásával, valamint a betanítási adat és a betanítási parancsfájlok feltöltésével állítja be.

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása
A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy objektum létrehozásával érheti el a munkaterület összetevőit [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) .

Hozzon létre egy munkaterület-objektumot az `config.json` [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Parancsfájlok előkészítése
Ebben az oktatóanyagban [itt](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py)találja a betanítási szkriptek **Train.py** . A gyakorlatban bármilyen egyéni tanítási szkriptet igénybe vehet, és futtathatja a Azure Machine Learning használatával.

### <a name="define-your-environment"></a>A környezet meghatározása
Hozzon létre egy környezeti objektumot, és engedélyezze a Docker-t. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Ez a megadott alaprendszerkép támogatja a fast.ai könyvtárat, amely lehetővé teszi az elosztott mély tanulási képességeket. További információ: [Fast.ai DockerHub](https://hub.docker.com/u/fastdotai). 

Ha egyéni Docker-rendszerképet használ, lehetséges, hogy már megfelelően beállította a Python-környezetet. Ebben az esetben állítsa igaz értékre a `user_managed_dependencies` jelzőt úgy, hogy kihasználja az egyéni rendszerkép beépített Python-környezetét. Alapértelmezés szerint az Azure ML Conda-környezetet hoz létre a megadott függőségekkel, és az adott környezetben futtatja a futtatást ahelyett, hogy az alapképre telepített Python-kódtárakat kellene használnia.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Ha olyan rendszerképet szeretne használni a saját tároló-beállításjegyzékből, amely nem szerepel a munkaterületén, a használatával `docker.base_image_registry` meg kell adnia a tárház és a Felhasználónév és jelszó nevét:

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Az is lehetséges, hogy egyéni Docker is használhat. Akkor használja ezt a módszert, ha nem Python-csomagokat szeretne függőségként telepíteni, és ne feledje, hogy az alaprendszerképet nincs értékre állítja.

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

### <a name="create-or-attach-existing-amlcompute"></a>Meglévő AmlCompute létrehozása vagy csatolása
Létre kell hoznia egy [számítási célt](concept-azure-machine-learning-architecture.md#compute-targets) a modell betanításához. Ebben az oktatóanyagban AmlCompute hoz létre a képzési számítási erőforrásként.

A AmlCompute létrehozása körülbelül 5 percet vesz igénybe. Ha az adott névvel rendelkező AmlCompute már szerepel a munkaterületen, akkor ez a kód kihagyja a létrehozási folyamatot.

A többi Azure-szolgáltatáshoz hasonlóan a Azure Machine Learning szolgáltatáshoz társított bizonyos erőforrások (például AmlCompute) is korlátozottak. Kérjük, olvassa el [ezt a cikket](how-to-manage-quotas.md) az alapértelmezett korlátokkal kapcsolatban, és hogyan kérhet további kvótát. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása
Ez a ScriptRunConfig a kívánt [számítási célra](how-to-set-up-training-targets.md)konfigurálja a feladatot a végrehajtáshoz.

```python
from azureml.core import ScriptRunConfig

fastai_config = ScriptRunConfig(source_directory='fastai-example', script='train.py')
fastai_config.run_config.environment = fastai_env
fastai_config.run_config.target = compute_target
```

### <a name="submit-your-run"></a>A Futtatás beküldése
Ha egy ScriptRunConfig objektummal küld egy tanítási futtatást, a Submit metódus ScriptRun típusú objektumot ad vissza. A visszaadott ScriptRun objektum programozott hozzáférést biztosít a betanítási futtatással kapcsolatos információkhoz. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(fastai_config)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning a teljes forrás könyvtár másolásával futtatja a betanítási parancsfájlokat. Ha olyan bizalmas adatokkal rendelkezik, amelyeket nem szeretne felvenni, használja a [. ignore fájlt](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) , vagy ne adja meg a forrás könyvtárában. Ehelyett egy [adattár](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)használatával férhet hozzá az adataihoz.

A Python-környezet testreszabásával kapcsolatos további információkért lásd: [& szoftveres környezetek használata](how-to-use-environments.md). 

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben egy modellt egy egyéni Docker-rendszerkép használatával tanított ki. Ezekről a cikkekről további tudnivalókat talál a Azure Machine Learningról.
* A [futtatási metrikák nyomon követése](how-to-track-experiments.md) a betanítás során
* [Modell üzembe helyezése](how-to-deploy-custom-docker-image.md) egyéni Docker-rendszerkép használatával.
