---
title: Vonat mély tanulás Chainer modellek
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathatja a PyTorch képzési parancsfájlokat nagyvállalati méretekben az Azure Machine Learning Chainer estimator osztály használatával.  A példa szkript sorolja kézzel írt számjegyű képeket, hogy hozzon létre egy mély tanulási neurális hálózat segítségével a Chainer Python könyvtár fut a numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536615"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Az Azure Machine Learning segítségével nagy méretekben betaníthatja és regisztrálhatja a Chainer-modelleket
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan futtathatja [a Chainer](https://chainer.org/) betanítási parancsfájlok nagyvállalati szinten az Azure Machine Learning [Chainer estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) osztály használatával. A cikkben szereplő példabetanítási parancsfájl a népszerű [MNIST adatkészletet](http://yann.lecun.com/exdb/mnist/) használja a kézzel írt számjegyek osztályozására egy mély neurális hálózat (DNN) használatával, amely a [numpy](https://www.numpy.org/)tetején futó Chainer Python-kódtár használatával készült.

Akár egy mélytanulási Chainer-modellt próbál beképezni az alapoktól kezdve, akár egy meglévő modellt hoz a felhőbe, az Azure Machine Learning segítségével kiskálázhatja a nyílt forráskódú betanítási feladatokat rugalmas felhőalapú számítási erőforrások használatával. Az Azure Machine Learning használatával éles környezetben modelleket hozhat létre, helyezhet üzembe, hozhat létre és figyelhet. 

További információ a [mélytanulásról és a gépi tanulásról.](concept-deep-learning-vs-machine-learning.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt elkezdené. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot az alábbi környezetek valamelyikén:

- Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

    - Töltse ki az [oktatóanyag: Telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) hozzon létre egy dedikált notebook-kiszolgáló előre betöltött az SDK és a mintatár.
    - A minták deep learning mappában a jegyzetfüzet-kiszolgáló, keressen egy befejezett jegyzetfüzetet és fájlokat a **how-to-use-azureml > ml-keretrendszerek > chainer > a telepítés > a vonat-hiperparaméter-tune-deploy-with-chainer** mappában.  A jegyzetfüzet tartalmaz kibővített szakaszok, amelyek intelligens hyperparameter tuning, modell üzembe helyezés, és a notebook widgetek.

- Saját Jupyter Notebook szerver

    - [Telepítse az Azure Machine Learning SDK-t.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Munkaterület-konfigurációs fájl létrehozása](how-to-configure-environment.md#workspace).
    - Töltse le a [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - Az útmutató elkészült [Jupyter Notebook-verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) is megtalálhatja a GitHub mintalapján. A jegyzetfüzet tartalmaz kibővített szakaszok, amelyek intelligens hyperparameter tuning, modell üzembe helyezés, és a notebook widgetek.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz úgy állítja be a betanítási kísérletet, hogy betölti a szükséges python-csomagokat, inicializálja a munkaterületet, létrehoz egy kísérletet, és feltölti a betanítási adatokat és a betanítási parancsfájlokat.

### <a name="import-packages"></a>Csomagok importálása

Először importálja az azureml.core Python-könyvtárat, és jelenítse meg a verziószámot.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

Az [Azure Machine Learning-munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Ez biztosítja az Ön számára egy központosított hely, ahol az összes létrehozott műtermék. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterületi összetevőket.

Munkaterületi objektum létrehozása az `config.json` [előfeltételek szakaszban](#prerequisites)létrehozott fájl olvasásával :

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Projektkönyvtár létrehozása
Hozzon létre egy könyvtárat, amely tartalmazza az összes szükséges kódot a helyi számítógépről, amelyhez hozzá kell férnie a távoli erőforráson. Ez magában foglalja a betanítási parancsfájlt és a további fájlokat a betanítási parancsfájl függ.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Betanítási parancsfájl előkészítése

Ebben az oktatóanyagban a **chainer_mnist.py** betanítási parancsfájl már biztosított. A gyakorlatban képesnek kell lennie arra, hogy bármilyen egyéni betanítási parancsfájlt, ahogy van, és futtassa az Azure ML-vel anélkül, hogy módosítania kell a kódot.

Az Azure ML-követés és metrikák képességeinek használatához adjon hozzá egy kis mennyiségű Azure ML-kódot a betanítási parancsfájlban.  A **chainer_mnist.py** betanítási parancsfájl bemutatja, hogyan naplózhat bizonyos metrikákat az Azure ML-futása a `Run` parancsfájlon belüli objektum használatával.

A megadott betanítási parancsfájl a `datasets.mnist.get_mnist` láncolati függvény ből származó példaadatokat használ.  A saját adatok, előfordulhat, hogy olyan lépéseket kell [használnia, mint az adatkészlet feltöltése és a parancsfájlok,](how-to-train-keras.md#data-upload) hogy az adatok elérhetővé a betanítás során.

Másolja a **chainer_mnist.py** betanítási parancsfájlt a projektkönyvtárába.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Mélytanulási kísérlet létrehozása

Kísérlet létrehozása. Ebben a példában hozzon létre egy "chainer-mnist" nevű kísérletet.

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Számítási cél létrehozása vagy bekésezése

Számítási [célszükséges](concept-compute-target.md) a modell betanításához. Ebben a példában az Azure ML által felügyelt számítási (AmlCompute) a távoli betanítási számítási erőforrás.

**Az AmlCompute létrehozása körülbelül 5 percet vesz igénybe.** Ha az AmlCompute ezzel a névvel már a munkaterületen, ez a kód kihagyja a létrehozási folyamatot.  

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

A számítási célokról további információt a [számítási célcikkben](concept-compute-target.md) talál.

## <a name="create-a-chainer-estimator"></a>Láncoló becslő létrehozása

A [Chainer estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) egyszerű módot kínál a Chainer képzési feladatok elindítására a számítási célon.

A Chainer estimator az [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) általános osztályon keresztül valósul meg, amely bármely keretrendszer támogatására használható. Az általános becslést használó betanítási modellekről további információt az [Azure Machine Learning használatával a becsléssel rendelkező modellek betanítása című](how-to-train-ml-models.md) témakörben talál.

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

## <a name="submit-a-run"></a>Futtatás beküldése

A [Futtatás objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a kapcsolatot a futtatási előzményekhez a feladat futása közben és befejezése után.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A futtatás végrehajtásakor a következő szakaszokon megy keresztül:

- **Előkészítés:** A docker-rendszerkép a Chainer-becslés nek megfelelően jön létre. A rendszerképet a rendszer feltölti a munkaterület tárolójának rendszerleíró adatbázisába, és gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók is streamelt a futtatási előzmények, és megtekinthető konkretizált a folyamat figyelése érdekében.

- **Méretezés**: A fürt megkísérli a felskálázást, ha a Batch AI-fürtnek a jelenleg rendelkezésre állónál több csomópontra van szüksége a futtatás végrehajtásához.

- **Futtatás:** A parancsfájlmappában lévő összes parancsfájl feltöltésre kerül a számítási célba, az adattárak csatlakoztatva vagy másolva, és a entry_script végrehajtása. Az stdout és a ./logs mappa kimenetei a futtatási előzményekbe kerülnek, és a futtatás figyelésére használhatók.

- **Utófeldolgozás:** A futtatás ./outputok mappája átkerül a futtatási előzményekbe.

## <a name="save-and-register-the-model"></a>A modell mentése és regisztrálása

Miután betanította a modellt, mentheti és regisztrálhatja azt a munkaterületre. A modellregisztráció lehetővé teszi a modellek tárolását és verziózását a munkaterületen a [modellkezelés és -telepítés egyszerűsítése érdekében.](concept-model-management-and-deployment.md)


Miután a modell betanítása befejeződött, regisztrálja a modellt a munkaterületre a következő kóddal.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Az imént regisztrált modell ugyanúgy van üzembe helyezve, mint bármely más regisztrált modell az Azure Machine Learningben, függetlenül attól, hogy melyik betanításhoz használt becslést használja. Az üzembe helyezési útmutató tartalmaz egy szakaszt a modellek regisztrálása, de közvetlenül átugorhatja [a számítási cél](how-to-deploy-and-where.md#choose-a-compute-target) létrehozása a központi telepítéshez, mivel már rendelkezik egy regisztrált modell.

A modell helyi példányát is letöltheti. Ez további modellérvényesítési munka helyi szinten való munkavégzéshez lehet hasznos. A betanítási `chainer_mnist.py`parancsfájlban a megtakarító objektum egy helyi mappában (a számítási cél helyi) marad. A Futtatás objektum segítségével letöltheti a másolatot az adattárból.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy mélytanulási, neurális hálózatot tanított be és regisztrált az Azure Machine Learning Chainer használatával. Ha meg szeretné tudni, hogyan telepíthet egy modellt, folytassa a [modell üzembe helyezési](how-to-deploy-and-where.md) cikket.

* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)

* [Futási mérőszámok nyomon követése edzés közben](how-to-track-experiments.md)

* [Tekintse meg az elosztott mélytanulási képzés referenciaarchitektúráját az Azure-ban](/azure/architecture/reference-architectures/ai/training-deep-learning)
