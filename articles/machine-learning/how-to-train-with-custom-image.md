---
title: Modell betanítása egyéni Docker-rendszerkép használatával
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan taníthat modelleket egyéni Docker-rendszerképekkel Azure Machine Learningban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8c971168add1aa63599a22f81a3b517d6cc561a1
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281296"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Modell betanítása egyéni Docker-rendszerkép használatával

Ebből a cikkből megtudhatja, hogyan használhatja az egyéni Docker-rendszerképeket, ha Azure Machine Learningkal rendelkező modelleket tanít. Az ebben a cikkben ismertetett szkriptekkel a PET-lemezképeket létrehozva egy, a rendszerhez tartozó, egy-egy példaként szolgáló neurális hálózatot hozhat létre. 

Azure Machine Learning alapértelmezett Docker-alapképet biztosít. Azure Machine Learning környezeteket is használhat más alaplemezképek megadásához, például a karbantartott [Azure Machine learning](https://github.com/Azure/AzureML-Containers) alaplemezképek vagy a saját [Egyéni rendszerképének](how-to-deploy-custom-docker-image.md#create-a-custom-base-image)egyikét. Az egyéni alapképek segítségével szorosan kezelheti a függőségeket, és szigorúbban szabályozhatja az összetevők verzióit a betanítási feladatok futtatásakor. 

## <a name="prerequisites"></a>Előfeltételek 
Futtassa a kódot a következő környezetek bármelyikén:
* Azure Machine Learning számítási példány (nincs letöltés vagy telepítés szükséges):
  * A [környezet és munkaterület beállítása](tutorial-1st-experiment-sdk-setup.md) oktatóanyag segítségével hozzon létre egy dedikált jegyzetfüzet-kiszolgálót, amely előre be van töltve az SDK-val és a minta adattárral.
  * A Azure Machine learning [példák tárházában](https://github.com/Azure/azureml-examples)keresse **meg a kész jegyzetfüzetet a**  >  **fastai**  >  **Train-pets-resnet34. ipynb** könyvtárban. 
* Saját Jupyter Notebook-kiszolgáló:
  * Hozzon létre egy [munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
  * Telepítse a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)-t. 
  * Hozzon létre egy [Azure Container registryt](/azure/container-registry) vagy más, az interneten elérhető Docker-beállításjegyzéket.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása 
Ebben a szakaszban a betanítási kísérletet egy munkaterület inicializálásával, egy kísérlet létrehozásával, valamint a betanítási és betanítási parancsfájlok feltöltésével állíthatja be.

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása
A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy objektum létrehozásával érheti el a munkaterület összetevőit [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) .

Hozzon létre egy `Workspace` objektumot a config.jsaz [előfeltételként](#prerequisites)létrehozott fájlból.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Parancsfájlok előkészítése
Ebben az oktatóanyagban használja a *Train.py* betanítási szkriptjét a [githubon](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py). A gyakorlatban bármilyen egyéni tanítási szkriptet végrehajthat, és futtathatja a Azure Machine Learning használatával.

### <a name="define-your-environment"></a>A környezet meghatározása
Hozzon létre egy `Environment` objektumot, és engedélyezze a Docker-t. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

A következő kódban megadott alaprendszerkép támogatja a fast.ai könyvtárat, amely lehetővé teszi az elosztott, mélyebb tanulási képességeket. További információ: [Fast.ai Docker hub adattár](https://hub.docker.com/u/fastdotai). 

Ha az egyéni Docker-rendszerképet használja, lehet, hogy már megfelelően beállította a Python-környezetet. Ebben az esetben állítsa be a jelölőt úgy, hogy az `user_managed_dependencies` `True` Egyéni rendszerkép beépített Python-környezetét használja. Alapértelmezés szerint a Azure Machine Learning Conda-környezetet hoz létre a megadott függőségekkel. A szolgáltatás a parancsfájlt futtatja az adott környezetben ahelyett, hogy az alapképre telepített Python-kódtárakat használja.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Ha a saját munkaterületén nem szereplő saját tároló-beállításjegyzékből származó rendszerképet szeretne használni, a használatával `docker.base_image_registry` adja meg az adattár és a Felhasználónév és a jelszó nevét:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Az is lehetséges, hogy egyéni Docker is használhat. Akkor használja ezt a módszert, ha a nem Python-csomagokat függőségként kell telepíteni. Ne felejtse el beállítani az alaprendszerképet a következőre: `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Azure Machine Learning környezetek létrehozásával és kezelésével kapcsolatos további információkért lásd: [Szoftverkörnyezet létrehozása és használata](how-to-use-environments.md). 

### <a name="create-or-attach-an-amlcompute-resource"></a>AmlCompute-erőforrás létrehozása vagy csatolása
Létre kell hoznia egy [számítási célt](concept-azure-machine-learning-architecture.md#compute-targets) a modell képzéséhez. Ebben az oktatóanyagban `AmlCompute` a képzési számítási erőforrásként jön létre.

A létrehozása `AmlCompute` körülbelül öt percet vesz igénybe. Ha az `AmlCompute` erőforrás már szerepel a munkaterületen, akkor ez a kód kihagyja a létrehozási folyamatot.

A többi Azure-szolgáltatáshoz hasonlóan a Azure Machine Learning szolgáltatáshoz társított bizonyos erőforrásokra (például) korlátozva vannak `AmlCompute` . További információ: az [alapértelmezett korlátok és a magasabb kvóta igénylése](how-to-manage-quotas.md). 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig-resource"></a>ScriptRunConfig-erőforrás létrehozása
Hozzon létre egy `ScriptRunConfig` erőforrást, amely a kívánt [számítási célra](how-to-set-up-training-targets.md)való futáshoz konfigurálja a feladatot.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

### <a name="submit-your-run"></a>A Futtatás beküldése
Ha egy objektum használatával küld el egy képzést `ScriptRunConfig` , a metódus egy `submit` típusú objektumot ad vissza `ScriptRun` . A visszaadott `ScriptRun` objektum programozási szintű hozzáférést biztosít a betanítási futtatással kapcsolatos információkhoz. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning a teljes forrás könyvtár másolásával futtatja a betanítási parancsfájlokat. Ha olyan bizalmas adatokkal rendelkezik, amelyeket nem szeretne felvenni, használja az [. ignore fájlt](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) , vagy ne adja meg a forrás könyvtárában. Ehelyett egy [adattár](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)használatával férhet hozzá az adataihoz.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben egy modellt egy egyéni Docker-rendszerkép használatával oktatott. Ezekről a cikkekről további tudnivalókat talál a Azure Machine Learning:
* A [futtatási metrikák nyomon követése](how-to-track-experiments.md) a betanítás során.
* [Modell üzembe helyezése](how-to-deploy-custom-docker-image.md) egyéni Docker-rendszerkép használatával.
