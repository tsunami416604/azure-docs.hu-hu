---
title: Mély tanulási neurális hálózat betanítása a PyTorch
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan futtathatja nagyvállalati szintű PyTorch-betanítási parancsfájljait Azure Machine Learning PyTorch kalkulátor-osztályának használatával.  A példaként szolgáló parancsfájlok a csirkét és a pulyka képét sorolják fel, hogy PyTorch az adatátviteli tanulási oktatóanyag alapján mély tanulási neurális hálózatot hozzanak létre.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: f08e19ac08d39b19838582cdfbb0d00dfde9de62
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584484"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>A Pytorch mély tanulási modelljeinek betanítása Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan futtathatja nagyvállalati szintű [PyTorch](https://pytorch.org/) -betanítási parancsfájljait Azure Machine learning [PyTorch kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) -osztályának használatával.  

Az ebben a cikkben szereplő szkriptek a csirkék és a Törökország képeinek osztályozására szolgálnak, hogy a PyTorch az adatátviteli tanulási [oktatóanyaga](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)alapján mély tanulási neurális hálózatot hozzanak létre. 

Legyen szó akár egy mély tanulási PyTorch modellről az alapoktól, akár egy meglévő modellt hoz létre a felhőben, a Azure Machine Learning segítségével rugalmas Felhőbeli számítási erőforrásokkal bővítheti a nyílt forráskódú képzési feladatokat. A Azure Machine Learning használatával előkészítheti, üzembe helyezheti, telepítheti és figyelheti a termelési szintű modelleket. 

További információ a [Deep learning és a Machine learning](concept-deep-learning-vs-machine-learning.md)szolgáltatásról.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:

 - Azure Machine Learning notebook VM – nincs szükség letöltésre vagy telepítésre

    - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló minták Deep learning mappájában keresse meg a befejezett és kibontott jegyzetfüzetet a következő könyvtárra való navigálással: **How-to-use-azureml > Training-with-Deep-learning > Train-hiperparaméter-Tune-Deploy-with-pytorch** Folder. 
 
 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-t.
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - [A minta parancsfájl fájljainak letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, egy munkaterület inicializálásával, egy kísérlet létrehozásával, valamint a betanítási adat és a betanítási parancsfájlok feltöltésével állítja be.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a szükséges Python-kódtárakat.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterület összetevőit.

Hozzon létre egy munkaterület-objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott `config.json` fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Mélyreható tanulási kísérlet létrehozása

Hozzon létre egy kísérletet és egy mappát a betanítási szkriptek tárolásához. Ebben a példában hozzon létre egy "pytorch-Birds" nevű kísérletet.

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Az adatok lekérése

Az adatkészlet körülbelül 120 betanítási képet tartalmaz a pulykák és csirkék számára, az egyes osztályokhoz 100 érvényesítési lemezképekkel. Az adathalmazt a betanítási szkript `pytorch_train.py`jának részeként fogjuk letölteni és kinyerni. A képek a [megnyitott lemezképek V5-adatkészlet](https://storage.googleapis.com/openimages/web/index.html)részhalmazai.

### <a name="prepare-training-scripts"></a>Betanítási parancsfájlok előkészítése

Ebben az oktatóanyagban már meg van biztosítva a betanítási parancsfájl, `pytorch_train.py`. A gyakorlatban bármilyen egyéni tanítási szkriptet igénybe vehet, és futtathatja a Azure Machine Learning használatával.

Töltse fel a Pytorch betanítási parancsfájlt, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Ha azonban Azure Machine Learning követési és mérőszám-képességeket szeretne használni, egy kis mennyiségű kódot kell hozzáadnia a betanítási parancsfájlban. A metrikák nyomon követésének példái a `pytorch_train.py`ban találhatók.

## <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a PyTorch-feladatokhoz a futtatásához. Ebben a példában hozzon létre egy GPU-kompatibilis Azure Machine Learning számítási fürtöt.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

A számítási célokkal kapcsolatos további információkért tekintse meg a [Mi az a számítási cél](concept-compute-target.md) című cikket.

## <a name="create-a-pytorch-estimator"></a>PyTorch-kalkulátor létrehozása

A [PyTorch kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) egyszerű módszert kínál a PyTorch-betanítási feladatok számítási célra való elindítására.

A PyTorch-kalkulátor az általános [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármilyen keretrendszer támogatásához használható. További információ a képzési modellekről az általános kalkulátor használatával: [modellek betanítása Azure Machine learning a kalkulátor használatával](how-to-train-ml-models.md)

Ha a betanítási szkript további pip-vagy Conda-csomagokat igényel, akkor az eredményül kapott Docker-rendszerképre is telepíthetők, ha a `pip_packages` és `conda_packages` argumentumokkal átadják a nevüket.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Futtatás küldése

A [Run objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Előkészítés**: a Docker-rendszerkép létrehozása a PyTorch-kalkulátor alapján történik. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők.

- **Skálázás**: a fürt akkor kísérli meg a skálázást, ha a Batch AI fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: a rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és a entry_script hajtja végre. Az stdout és a./Logs mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Utómunka**: a Futtatás./outputs mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="register-or-download-a-model"></a>Modell regisztrálása vagy letöltése

A modell kiképzése után regisztrálhatja azt a munkaterületre. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

A modell helyi másolatát a Run objektum használatával is letöltheti. A betanítási parancsfájlban `pytorch_train.py`egy PyTorch objektum megőrzi a modellt egy helyi mappába (helyi a számítási célra). A Futtatás objektum használatával letöltheti a másolatokat.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Elosztott betanítás

A [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) kalkulátor a processzor-és a GPU-fürtökön egyaránt támogatja az elosztott képzést. Könnyedén futtathatja az elosztott PyTorch-feladatokat, és Azure Machine Learning felügyeli az Ön számára.

### <a name="horovod"></a>Horovod
A [Horovod](https://github.com/uber/horovod) egy nyílt forráskódú, amely az Über által fejlesztett elosztott képzések összes csökkentési keretrendszerét csökkenti. Az elosztott GPU PyTorch feladatok egyszerű elérési útját kínálja.

A Horovod használatához meg kell adnia egy [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objektumot a `distributed_training` paraméterhez a PyTorch konstruktorban. Ez a paraméter biztosítja, hogy a Horovod-függvénytár telepítve legyen a betanítási parancsfájlban.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
A Horovod és függőségeit a rendszer telepíti, így a következő módon importálhatja a betanítási szkriptbe `train.py`:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportálás ONNX

Ha optimalizálni szeretné a következtetést a [ONNX futtatókörnyezettel](concept-onnx.md), alakítsa át a betanított PyTorch modelljét a ONNX formátumba. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modellt az előrejelzéshez használják, leggyakrabban a termelési adatforgalomban. Példaként tekintse meg az [oktatóanyagot](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>További lépések

Ebben a cikkben a PyTorch-on Azure Machine Learning-on keresztül tanult és regisztrált egy mély tanulási és neurális hálózatot. A modellek üzembe helyezésének megismeréséhez folytassa a modell üzembe helyezésével kapcsolatos cikket.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)
* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [Hiperparaméterek beállítása hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
