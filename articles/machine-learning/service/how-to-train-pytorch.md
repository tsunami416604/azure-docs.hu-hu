---
title: Betanítása és PyTorch modellek regisztrálása
titleSuffix: Azure Machine Learning service
description: Ez a cikk bemutatja, hogyan betanítása és regisztrálnia kell egy PyTorch modell Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: 8def58eb003fcc817c21151416744cf391b5f38f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443930"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>Betanítása és ipari méretekben PyTorch modellek regisztrálása az Azure Machine Learning szolgáltatás

Ez a cikk bemutatja, hogyan betanítása és regisztrálnia kell egy PyTorch modell Azure Machine Learning szolgáltatás használatával. -Alapú [learning oktatóanyagban a PyTorch átviteli](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) , amely összeállítja a Neurális hálózat (DNN) besorolás csirkék és pulyka-lemezképekhez.

[PyTorch](https://pytorch.org/) Neurális hálózatok (DNN) létrehozása gyakran használt nyílt forráskódú számítási keretrendszer. Az Azure Machine Learning szolgáltatás gyors horizontális felskálázása lehetséges nyílt forráskódú betanítási feladatokat a rugalmas felhőalapú számítási erőforrások használatával. Is nyomon követheti a betanítási futtatás, a verzió modellek üzembe helyezése a modelleket, és még sok más.

Akár az alapoktól a PyTorch modell fejleszt, vagy már meglévő modell üzembe a felhőben, Azure Machine Learning szolgáltatás segítségével éles használatra kész modelleket.

## <a name="prerequisites"></a>Előfeltételek

Ez a kód futtatása ezekben a környezetekben valamelyikét:

 - Az Azure Machine Learning Notebook Virtuálisgép - letöltések vagy nem szükséges telepítés

    - Végezze el a [felhőalapú notebook rövid](quickstart-run-cloud-notebook.md) hozhat létre egy dedikált notebook server előre betöltött az SDK-t és a mintaadattárat.
    - A notebook server minták mappájában található befejeződött, és a bővített Jegyzetfüzet az ebben a könyvtárban: **útmutatóval-to-használat – azureml > képzés a deep learning > train-hyperparameter-tune-deploy-with-pytorch** a mappa. 
 
 - A saját Jupyter Notebook server

    - [Telepítse az Azure Machine Learning SDK a Pythonhoz](setup-create-workspace.md#sdk)
    - [Munkaterület-konfigurációs fájl létrehozása](setup-create-workspace.md#write-a-configuration-file)
    - [A parancsfájl mintafájlok letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Is megtalálhatja a befejezett [Jupyter Notebook verzió](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) Ez az útmutató a GitHub-minták oldalon. A notebook intelligens hiperparaméter finomhangolása, a modell üzembe helyezése és a notebook widgetek kibontott szakaszokat tartalmazza.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz által a szükséges python-csomagok betöltése, a munkaterület inicializálása, egy kísérlet létrehozásától és feltöltése a betanítási adatok és a betanítási szkriptekhez beállítja a tanítási kísérlet.

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

A [Azure Machine Learning szolgáltatás munkaterület](concept-workspace.md) a szolgáltatás a legfelső szintű erőforrás. Is tartalmaz egy központi helyen hoz létre minden összetevő dolgozhat. A Python SDK-ban, hozzáférhet a munkaterület-összetevők létrehozásával egy [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum.

A munkaterület-objektum létrehozása a `config.json` létrehozott fájlt a [előfeltételeknél](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet és a egy mappát a betanítási szkriptekhez tárolásához. Ebben a példában egy "pytorch madarakkal" nevű kísérlet létrehozása.

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Az adatok lekérése

Az adatkészlet körülbelül 120 betanító kép minden egyes pulyka és csirke, az egyes osztályok 100 érvényesítési képekkel áll. Rendelkezéseitől töltse le és csomagolja ki az adatkészletet a tanítási szkriptet részeként `pytorch_train.py`. A lemezképek a részhalmazát képezik a [nyílt lemezképek v5 adatkészlet](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Készítse elő a betanítási szkriptekhez

Ebben az oktatóanyagban a tanítási szkriptet `pytorch_train.py`, már van megadva. A gyakorlatban bármilyen egyéni képzési parancsprogramot, mert, és futtassa azt Azure Machine Learning szolgáltatással.

Töltse fel a Pytorch tanítási szkriptet `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Azonban ha szeretné használni az Azure Machine Learning szolgáltatás nyomkövetési és metrikák képességeket, akkor a tanítási szkriptet egy kis mértékben kódot hozzáadni. Metrikák követése példái megtalálhatók `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Hozzon létre egy számítási célnak

Hozzon létre egy számítási célnak a PyTorch feladat futtatását. Ebben a példában az Azure Machine Learning GPU-kompatibilis számítási fürt létrehozása.

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

A számítási célokhoz további információkért lásd: a [mi egy számítási célnak](concept-compute-target.md) cikk.

## <a name="create-a-pytorch-estimator"></a>A PyTorch estimator létrehozása

A [PyTorch estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) állapotára egy PyTorch betanítási feladatot egy számítási célnak egyszerű módszert kínál.

A PyTorch estimator biztosítják az általános [ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztály, amely bármely keretrendszer támogatásához használható. Az általános estimator használó modellek betanítása kapcsolatos további információkért lásd: [modelleket taníthat be az Azure Machine Learning estimator használatával](how-to-train-ml-models.md)

Ha a tanítási szkriptet van szüksége, további pip vagy conda-csomagok futtatása, a nevük keresztül adja át az eredményül kapott docker-rendszerképet a telepített csomagokat használhat a `pip_packages` és `conda_packages` argumentumokat.

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

## <a name="submit-a-run"></a>Küldje el a Futtatás

A [objektumot futtatni](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) a futtatási előzmények a felületet biztosít, a feladat futása közben és után befejeződött.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

A Futtatás hajtja végre, a következő szakaszokra végighalad:

- **Felkészülés**: A PyTorch estimator alapján jön létre egy docker-rendszerképet. A rendszerkép feltöltött a munkaterület container Registry tárolóregisztrációs adatbázisba, és későbbi futtatások gyorsítótárazza. Naplók a is rendszer streamként továbbítja a futtatási előzmények és megtekinthetők a folyamat állapotának monitorozásához.

- **Méretezés**: A fürt próbál vertikális felskálázás, ha a Batch AI-fürt csomópontjait a Futtatás végrehajtásához, mint a jelenleg elérhető igényel.

- **Futó**: A parancsfájl mappában található összes parancsfájl töltenek fel a számítási célnak, adattárak csatlakoztatva van, vagy másolja és a entry_script hajtja végre. Az stdout adatsorból kimenetek és a. / logs mappában a rendszer streamként továbbítja a futtatási előzmények és a Futtatás figyelésére használható.

- **Utófeldolgozási**: A. / kimenete a Futtatás mappába másolja a rendszer a futtatási előzményekben.

## <a name="register-or-download-a-model"></a>Regisztráljon vagy egy modell letöltése

Ha a modell már betanított, regisztrálhatja a munkaterülethez. Modell regisztrálását lehetővé teszi a tároló és verzió egyszerűsítése érdekében a munkaterület a modellek [kezelés és üzembe helyezési modell](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

A modell egy helyi példányát futtató objektum segítségével is letöltheti. A képzési szkriptben `pytorch_train.py`, egy objektum mentése PyTorch továbbra is fennáll, a modell egy helyi mappába (helyi, a számítási célnak). Ha szeretné letölteni a Futtatás objektum segítségével.

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

A [ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) estimator is támogatja az elosztott képzési Processzor és GPU-fürtök között. Könnyedén futtathat elosztott PyTorch feladatok, és az Azure Machine Learning szolgáltatás fogja kezelni a vezénylési az Ön számára.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) egy nyílt forráskódú keretrendszer összes csökkentése, az Uber által fejlesztett elosztott képzési. Biztosít egy elosztott GPU PyTorch feladatok egyszerű elérési útját.

Horovod használatához adja meg egy [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objektum a `distributed_training` paraméter a PyTorch konstruktor. Ez a paraméter biztosítja, hogy Horovod könyvtár telepítve van-e a tanítási szkriptet a használatra.


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
Horovod és annak függőségeit lesz telepítve, így importálhat a tanítási szkriptet `train.py` módon:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>ONNX exportálása

Optimalizálhatja a következtetésekhez a [ONNX-futtatókörnyezet](concept-onnx.md), a PyTorch betanított modell ONNX-formátumra konvertálni. Következtetésekhez vagy a modell pontozása, nem a fázis, az üzembe helyezett modell előrejelzési leggyakrabban a termelési adatok szolgál. Tekintse meg a [oktatóanyag](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) példaként.

## <a name="next-steps"></a>További lépések

Ebben a cikkben betanított és a egy PyTorch modell Azure Machine Learning szolgáltatásban regisztrált. Megtudhatja, hogyan helyezhet üzembe modelleket, és továbbléphet a modell üzembe helyezési című cikkben.

> [!div class="nextstepaction"]
> [Hogyan és hol érdemes a modellek üzembe helyezése](how-to-deploy-and-where.md)
* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
