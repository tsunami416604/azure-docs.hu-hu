---
title: Vonat mély tanulás PyTorch modellek
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathatja PyTorch-betanítási parancsfájljait nagyvállalati méretekben az Azure Machine Learning PyTorch-becslési osztályával.  A példa szkriptek minősíteni csirke és pulyka képeket építeni egy mély tanulási neurális hálózat alapján PyTorch átviteli tanulási oktató.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834859"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>A Pytorch mélytanulási modelljeinek betanítása nagy méretekben az Azure Machine Learning segítségével
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan futtathatja [a PyTorch-betanítási](https://pytorch.org/) parancsfájlokat nagyvállalati méretekben az Azure Machine Learning [PyTorch-becslési](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) osztályával.  

A cikkben szereplő példaszkriptek a csirke- és pulykaképek osztályozására szolgálnak a PyTorch átviteli tanulási [oktatóanyaga](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)alapján épülő mélytanulási neurális hálózat felépítéséhez. 

Akár egy mélytanulásos PyTorch-modellt próbál beképezni az alapoktól kezdve, akár egy meglévő modellt hoz a felhőbe, az Azure Machine Learning segítségével kiskálázhatja a nyílt forráskódú betanítási feladatokat rugalmas felhőalapú számítási erőforrások használatával. Az Azure Machine Learning használatával éles környezetben modelleket hozhat létre, helyezhet üzembe, hozhat létre és figyelhet. 

További információ a [mélytanulásról és a gépi tanulásról.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot az alábbi környezetek valamelyikén:

- Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

    - Töltse ki az [oktatóanyag: Telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) hozzon létre egy dedikált notebook-kiszolgáló előre betöltött az SDK és a mintatár.
    - A minták deep learning mappában a jegyzetfüzet-kiszolgáló, keressen egy kitöltött és kibővített jegyzetfüzetet a címtárba navigálva: **how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-pytorch** mappát. 
 
 - Saját Jupyter Notebook szerver

    - [Telepítse az Azure Machine Learning SDK-t.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Munkaterület-konfigurációs fájl létrehozása](how-to-configure-environment.md#workspace).
    - [A mintaparancsfájl-fájlok letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Az útmutató elkészült [Jupyter Notebook-verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) is megtalálhatja a GitHub minták lapján. A jegyzetfüzet tartalmaz kibővített szakaszok, amelyek intelligens hyperparameter tuning, modell üzembe helyezés, és a notebook widgetek.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz úgy állítja be a betanítási kísérletet, hogy betölti a szükséges python-csomagokat, inicializálja a munkaterületet, létrehoz egy kísérletet, és feltölti a betanítási adatokat és a betanítási parancsfájlokat.

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

Az [Azure Machine Learning-munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Ez biztosítja az Ön számára egy központosított hely, ahol az összes létrehozott műtermék. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterületi összetevőket.

Hozzon létre egy `config.json` munkaterületi objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Mélytanulási kísérlet létrehozása

Hozzon létre egy kísérletet és egy mappát a betanítási parancsfájlok tárolására. Ebben a példában hozzon létre egy kísérletet az úgynevezett "pytorch-madarak".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Az adatok lekérése

Az adatkészlet körülbelül 120 betanítási képből áll, amelyek mindegyike pulykák és csirkék számára készült, és minden osztályhoz 100 érvényesítési kép tartozik. Az adatkészletet a képzési szkript `pytorch_train.py`részeként töltjük le és bontjuk ki. A képek a Képek [megnyitása v5 adatkészlet egy részhalmazát képezik.](https://storage.googleapis.com/openimages/web/index.html)

### <a name="prepare-training-scripts"></a>Képzési parancsfájlok előkészítése

Ebben az oktatóanyagban `pytorch_train.py`a betanítási parancsfájl, , már biztosított. A gyakorlatban bármilyen egyéni betanítási parancsfájlt elvehet, ahogy van, és futtathatja az Azure Machine Learning használatával.

Töltse fel a Pytorch képzési szkriptet, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Ha azonban azure Machine Learning-követés és metrikák képességeit szeretné használni, kis mennyiségű kódot kell hozzáadnia a betanítási parancsfájlban. Példák a mérőszámok nyomon `pytorch_train.py`követésére a ban található.

## <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a PyTorch-feladat futtatásához. Ebben a példában hozzon létre egy GPU-kompatibilis Azure Machine Learning számítási fürt.

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

A számítási célokról további információt a [számítási célcikkben](concept-compute-target.md) talál.

## <a name="create-a-pytorch-estimator"></a>PyTorch-becslő létrehozása

A [PyTorch-becslés](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) egyszerű módot biztosít a PyTorch képzési feladat elindítására egy számítási célon.

A PyTorch becslés az általános [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármely keretrendszer támogatására használható. Az általános becslést használó betanítási modellekről további információt az [Azure Machine Learning használatával a becsléssel rendelkező modellek betanítása című](how-to-train-ml-models.md) témakörben talál.

Ha a betanítási parancsfájl további pip- vagy conda-csomagokfuttatásához szükséges, a csomagok at az `pip_packages` `conda_packages` eredményül kapott docker-rendszerképre telepítheti, ha átadja a nevüket a és az argumentumokat.

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

A Python-környezet testreszabásáról további információt a [Környezetek létrehozása és kezelése betanításhoz és üzembe helyezéshez című témakörben talál.](how-to-use-environments.md)

## <a name="submit-a-run"></a>Futtatás beküldése

A [Futtatás objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a kapcsolatot a futtatási előzményekhez a feladat futása közben és befejezése után.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

A futtatás végrehajtásakor a következő szakaszokon megy keresztül:

- **Előkészítés:** A Docker-lemezkép a PyTorch becslésnek megfelelően jön létre. A rendszerképet a rendszer feltölti a munkaterület tárolójának rendszerleíró adatbázisába, és gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók is streamelt a futtatási előzmények, és megtekinthető konkretizált a folyamat figyelése érdekében.

- **Méretezés**: A fürt megkísérli a felskálázást, ha a Batch AI-fürtnek a jelenleg rendelkezésre állónál több csomópontra van szüksége a futtatás végrehajtásához.

- **Futtatás:** A parancsfájlmappában lévő összes parancsfájl feltöltésre kerül a számítási célba, az adattárak csatlakoztatva vagy másolva, és a entry_script végrehajtása. Az stdout és a ./logs mappa kimenetei a futtatási előzményekbe kerülnek, és a futtatás figyelésére használhatók.

- **Utófeldolgozás:** A futtatás ./outputok mappája átkerül a futtatási előzményekbe.

## <a name="register-or-download-a-model"></a>Modell regisztrálása vagy letöltése

Miután betanította a modellt, regisztrálhatja azt a munkaterületre. A modellregisztráció lehetővé teszi a modellek tárolását és verziózását a munkaterületen a [modellkezelés és -telepítés egyszerűsítése érdekében.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Az imént regisztrált modell ugyanúgy van üzembe helyezve, mint bármely más regisztrált modell az Azure Machine Learningben, függetlenül attól, hogy melyik betanításhoz használt becslést használja. Az üzembe helyezési útmutató tartalmaz egy szakaszt a modellek regisztrálása, de közvetlenül átugorhatja [a számítási cél](how-to-deploy-and-where.md#choose-a-compute-target) létrehozása a központi telepítéshez, mivel már rendelkezik egy regisztrált modell.

A modell helyi példányát is letöltheti a Futtatás objektum használatával. A betanítási parancsfájlban `pytorch_train.py`a PyTorch mentési objektum a modellt egy helyi mappában (a számítási cél helyi részén) megőrzi. A Futtatás objektummal letöltheti a másolatot.

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

A [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) becslés támogatja a CPU- és GPU-fürtök közötti elosztott oktatást is. Egyszerűen futtathatja az elosztott PyTorch-feladatokat, és az Azure Machine Learning kezeli a vezénylési feladatokat.

### <a name="horovod"></a>Horovod
[A Horovod](https://github.com/uber/horovod) egy nyílt forráskódú, mind csökkenti az Uber által kifejlesztett elosztott képzés keretrendszerét. Egyszerű utat kínál az elosztott GPU PyTorch feladatokhoz.

Horovod használatához adjon [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) meg egy `distributed_training` objektumot a PyTorch konstruktor paraméteréhez. Ez a paraméter biztosítja, hogy a Horovod könyvtár telepítve van a betanítási parancsfájlban való használatra.


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
Horovod és a függőségek lesznek telepítve az Ön számára, `train.py` így importálhatja azt a képzési szkript az alábbiak szerint:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportálás ONNX-be

Az [ONNX futásidejű](concept-onnx.md)következtetés optimalizálásához konvertálja a betanított PyTorch modellt ONNX formátumra. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modell előrejelzési, leggyakrabban az éles adatok. Lásd az [oktatóanyag](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) egy példa.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy mélytanulási, neurális hálózatot tanított be és regisztrált az Azure Machine Learningen a PyTorch használatával. Ha meg szeretné tudni, hogyan telepíthet egy modellt, folytassa a modell üzembe helyezési cikket.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezése és helye](how-to-deploy-and-where.md)
* [Futási mérőszámok nyomon követése edzés közben](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell telepítése](how-to-deploy-and-where.md)
* [Referenciaarchitektúra az elosztott mélytanulási képzéshez az Azure-ban](/azure/architecture/reference-architectures/ai/training-deep-learning)

