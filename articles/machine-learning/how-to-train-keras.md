---
title: Mély tanulási kerasz-modellek betanítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan lehet betanítani és regisztrálni a TensorFlow-on futó kerasz Deep neurális hálózati besorolási modellt Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 14649d3e7bc12205283863f725a902a3cef20290
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433858"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Kerasz besorolási modell betanítása és regisztrálása Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan végezheti el a TensorFlow-ra épülő kerasz-besorolási modell betanítását és regisztrálását Azure Machine Learning használatával. A szolgáltatás a népszerű [MNIST-adatkészletet](http://yann.lecun.com/exdb/mnist/) használja a kézzel írt számjegyek besorolására egy olyan Deep neurális hálózat (DNN) használatával, amely a [TensorFlow](https://www.tensorflow.org/overview)-on futó [kerasz Python-kódtár](https://keras.io) használatával készült.

A kerasz egy magas szintű neurális hálózati API, amely a fejlesztés egyszerűsítése érdekében más népszerű DNN-keretrendszerek felett is futtatható. A Azure Machine Learning segítségével rugalmas felhőalapú számítási erőforrásokkal gyorsan kibővítheti a betanítási feladatokat. Nyomon követheti a képzések futtatását, a verziók modelljeit, a modellek üzembe helyezését és még sok mást is.

Függetlenül attól, hogy a kerasz-modellt fejleszti az alapoktól, vagy egy meglévő modellt hoz létre a felhőben, Azure Machine Learning segíthet a termelésre kész modellek létrehozásában.

A gépi tanulás és a mély tanulás közötti különbségekkel kapcsolatos információkat a [fogalmi cikkben](concept-deep-learning-vs-machine-learning.md) találja.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:

- Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

     - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló Samples (minták) mappájában keresse meg a befejezett és kibontott jegyzetfüzetet, ehhez a következő könyvtárra navigálva: **útmutató – használat – azureml > képzés – Deep-learning > Train-hiperparaméter-Tune-Deploy-with-kerasz** Folder.

 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-t.
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - [A minta parancsfájl fájljainak letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` és`utils.py`

    Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, egy munkaterület inicializálásával, egy kísérlet létrehozásával, valamint a betanítási adat és a betanítási parancsfájlok feltöltésével állítja be.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a szükséges Python-kódtárakat.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy objektum létrehozásával érheti el a munkaterület összetevőit [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Hozzon létre egy munkaterület-objektumot az `config.json` [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy "kerasz-mnist" nevű kísérletet a munkaterületen.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Fájl adatkészletének létrehozása

Egy `FileDataset` objektum egy vagy több fájlra hivatkozik a munkaterület adattárában vagy a nyilvános URL-címekben. A fájlok bármilyen formátumúak lehetnek, és a osztály lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számítási feladatokhoz. A létrehozásával `FileDataset` létrehoz egy hivatkozást az adatforrás helyére. Ha az adatkészletbe átalakításokat alkalmazott, azokat az adatkészletben is tárolja a rendszer. Az adattárolók a meglévő helyükön maradnak, így nem merülnek fel extra tárolási költségek. További információért tekintse [meg a csomag útmutatóját](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) `Dataset` .

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

A `register()` módszer használatával regisztrálja az adatkészletet a munkaterületen, hogy másokkal is megoszthatók legyenek, újra felhasználható a különböző kísérletekben, és nevük szerint a képzési szkriptben.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a TensorFlow-feladatokhoz a futtatásához. Ebben a példában hozzon létre egy GPU-kompatibilis Azure Machine Learning számítási fürtöt.

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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>TensorFlow-kalkulátor létrehozása és kerasz importálása

A [TensorFlow kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) egyszerű módszert kínál a TensorFlow-betanítási feladatok számítási célra való elindítására. Mivel a kerasz a TensorFlow-on fut, használhatja a TensorFlow-kalkulátort, és importálhatja a kerasz könyvtárat az `pip_packages` argumentum használatával.

Először kérje le a munkaterület-adattár adatait a `Dataset` osztály használatával.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

A TensorFlow kalkulátor az általános [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármely keretrendszer támogatásához használható. Emellett hozzon létre egy szótárt, `script_params` amely tartalmazza a DNN hiperparaméter beállításait. További információ a képzési modellekről az általános kalkulátor használatával: [modellek betanítása Azure Machine learning a kalkulátor használatával](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Futtatás küldése

A [Run objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Előkészítés**: a Docker-rendszerkép létrehozása a TensorFlow-kalkulátor alapján történik. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők.

- **Skálázás**: a fürt akkor kísérli meg a skálázást, ha a Batch AI fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: a rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és a entry_script végre lesz hajtva. Az stdout és a./Logs mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Utómunka**: a Futtatás./outputs mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="register-the-model"></a>Regisztrálja a modellt

Miután betöltötte a DNN modellt, regisztrálhatja azt a munkaterületen. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> A korábban regisztrált modell pontosan ugyanúgy van telepítve, mint bármely más regisztrált modell Azure Machine Learningban, függetlenül attól, hogy milyen kalkulátort használtak a képzéshez. Az üzembe helyezési útmutató egy szakaszt tartalmaz a modellek regisztrálásához, de közvetlenül kihagyhatja a központi telepítéshez szükséges [számítási cél létrehozását](how-to-deploy-and-where.md#choose-a-compute-target) , mivel már rendelkezik regisztrált modellel.

Letöltheti a modell helyi példányát is. Ez akkor lehet hasznos, ha a modell további ellenőrzése helyileg működik. A betanítási parancsfájlban `mnist-keras.py` egy TensorFlow-megtakarítási objektum a modellt egy helyi mappába (a számítási cél számára) őrzi meg. A Futtatás objektum használatával letöltheti az adattárból származó másolatot.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy kerasz-modellt képzett és regisztrált Azure Machine Learningon. A modellek üzembe helyezésének megismeréséhez folytassa a modell üzembe helyezésével kapcsolatos cikket.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)
* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
