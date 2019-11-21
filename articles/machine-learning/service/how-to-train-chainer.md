---
title: Mély tanulási Láncer-modellek betanítása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan futtathatja nagyvállalati szintű PyTorch-betanítási parancsfájljait Azure Machine Learning Chainer kalkulátor-osztályának használatával.  A példaként szolgáló parancsfájl a kézzel írt számjegyek rendszerképeit sorolja fel egy Deep learning neurális hálózat kiépítéséhez a NumPy-on futó, a láncot használó Python-könyvtár használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: f384a6a870d891bbaf6fa20a896b0251e62b8d4f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224980"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Láncolt modellek betanítása és regisztrálása Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan futtathatja nagyvállalati szinten a [chainer](https://chainer.org/) -betanítási szkripteket Azure Machine learning [chainer kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) -osztályának használatával. Az ebben a cikkben ismertetett betanítási szkript a népszerű [MNIST adatkészletet](http://yann.lecun.com/exdb/mnist/) használja a kézzel írt számjegyek besorolásához a [NumPy](https://www.numpy.org/)-on futó, a chainer Python-kódtár használatával létrehozott Deep neurális hálózat (DNN) használatával.

Akár az alapoktól, akár egy meglévő modellt szeretne készíteni a felhőbe, az Azure Machine Learning segítségével rugalmas Felhőbeli számítási erőforrásokkal bővítheti a nyílt forráskódú képzési feladatokat. A Azure Machine Learning használatával előkészítheti, üzembe helyezheti, telepítheti és figyelheti a termelési szintű modelleket. 

További információ a [Deep learning és a Machine learning](concept-deep-learning-vs-machine-learning.md)szolgáltatásról.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:

- Azure Machine Learning notebook VM – nincs szükség letöltésre vagy telepítésre

    - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló minták mély tanulási mappájában keresse meg az elkészült jegyzetfüzeteket és fájlokat a **használati útmutató-azureml > ml-keretrendszerek > a láncolt > üzembe helyezés > a Train-hiperparaméter-Tune-Deploy-with-chainer** mappában.  A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

- Saját Jupyter Notebook-kiszolgáló

    - [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-t.
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - Töltse le a [chainer_mnist. a.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer)rajzfájl parancsfájlt.
     - Megtalálhatja az útmutató befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) is a GitHub-minták oldalon. A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, egy munkaterület inicializálásával, egy kísérlet létrehozásával, valamint a betanítási adat és a betanítási parancsfájlok feltöltésével állítja be.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a azureml. Core Python-függvénytárat, és jelenítse meg a verziószámot.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterület összetevőit.

Hozzon létre egy munkaterület-objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott `config.json` fájl beolvasásával:

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Projekt könyvtárának létrehozása
Hozzon létre egy könyvtárat, amely tartalmazni fogja a helyi gépről az összes szükséges kódot, amelyhez hozzá kell férnie a távoli erőforráshoz. Ez magában foglalja a betanítási szkriptet és a betanítási szkripttől függő további fájlokat.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Betanítási szkript előkészítése

Ebben az oktatóanyagban már meg van biztosítva a **chainer_mnist. a.** a betanítási szkript. A gyakorlatban a kód módosítása nélkül is elvégezheti az egyéni betanítási szkriptek futtatását, és futtathatja azt az Azure ML-ben.

Az Azure ML követési és metrikái képességeinek használatához vegyen fel egy kis mennyiségű Azure ML-kódot a betanítási szkriptbe.  A **chainer_mnist.** a betanítási szkript azt mutatja be, hogyan naplózhat néhány mérőszámot az Azure ml-re a szkripten belüli `Run` objektum használatával.

A megadott betanítási parancsfájl példákat használ a Chain `datasets.mnist.get_mnist` függvényből.  A saját adatok esetében előfordulhat, hogy olyan lépéseket kell használnia, mint például az [adatkészlet és a parancsfájlok feltöltése](how-to-train-keras.md#data-upload) , hogy az adatok elérhetők legyenek a képzés során.

Másolja a **chainer_mnist.** Copy betanítási szkriptet a projekt könyvtárába.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Mélyreható tanulási kísérlet létrehozása

Hozzon létre egy kísérletet. Ebben a példában hozzon létre egy "chainer-mnist" nevű kísérletet.

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Számítási cél létrehozása vagy beszerzése

Szüksége lesz egy [számítási célra](concept-compute-target.md) a modell betanításához. Ebben a példában az Azure ML felügyelt számítási feladatokhoz (AmlCompute) használja a távoli képzés számítási erőforrását.

**A AmlCompute létrehozása körülbelül 5 percet vesz igénybe**. Ha az adott névvel rendelkező AmlCompute már szerepel a munkaterületen, ez a kód kihagyja a létrehozási folyamatot.  

```Python
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

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

A számítási célokkal kapcsolatos további információkért tekintse meg a [Mi az a számítási cél](concept-compute-target.md) című cikket.

## <a name="create-a-chainer-estimator"></a>Láncolási kalkulátor létrehozása

A [láncolási kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) egyszerű módszert kínál a chainer-betanítási feladatok elindítására a számítási célra.

A láncolási kalkulátor az általános [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármilyen keretrendszer támogatásához használható. További információ a képzési modellekről az általános kalkulátor használatával: [modellek betanítása Azure Machine learning a kalkulátor használatával](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Futtatás küldése

A [Run objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Előkészítés**: a Docker-rendszerkép létrehozása a láncolási kalkulátor alapján történik. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők.

- **Skálázás**: a fürt akkor kísérli meg a skálázást, ha a Batch AI fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: a rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és a entry_script végre lesz hajtva. Az stdout és a./Logs mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Utómunka**: a Futtatás./outputs mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="save-and-register-the-model"></a>A modell mentése és regisztrálása

A modell kiképzése után mentheti és regisztrálhatja azt a munkaterületen. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.


A modell betanításának befejeződése után regisztrálja a modellt a munkaterületen a következő kóddal.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> A korábban regisztrált modell pontosan ugyanúgy van telepítve, mint bármely más regisztrált modell Azure Machine Learningban, függetlenül attól, hogy milyen kalkulátort használtak a képzéshez. Az üzembe helyezési útmutató egy szakaszt tartalmaz a modellek regisztrálásához, de közvetlenül kihagyhatja a központi telepítéshez szükséges [számítási cél létrehozását](how-to-deploy-and-where.md#choose-a-compute-target) , mivel már rendelkezik regisztrált modellel.

Letöltheti a modell helyi példányát is. Ez akkor lehet hasznos, ha a modell további ellenőrzése helyileg működik. A betanítási parancsfájlban `chainer_mnist.py`egy megtakarító objektum megőrzi a modellt egy helyi mappába (helyi a számítási célra). A Futtatás objektum használatával letöltheti az adattárból származó másolatot.

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

Ebben a cikkben a Azure Machine Learning láncolásával foglalkozó, mély tanulási és neurális hálózatot oktatott és regisztrált. A modellek üzembe helyezésének megismeréséhez folytassa a [modell üzembe](how-to-deploy-and-where.md) helyezésével kapcsolatos cikket.

* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)

* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)

* [Tekintse meg az Azure-ban elosztott Deep learning-képzés hivatkozási architektúráját](/azure/architecture/reference-architectures/ai/training-deep-learning)
