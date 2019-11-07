---
title: Betanítás azureml-adatkészletekkel
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatók az adatkészletek a képzésben
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/25/2019
ms.openlocfilehash: 53a190a611af42f0eaca8d4348d1d066d3d4f9e4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584417"
---
# <a name="train-with-datasets-preview-in-azure-machine-learning"></a>Az adatkészletek (előzetes verzió) betanítása Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan használhatja fel [Azure Machine learning adatkészleteket](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) a távoli kísérletek képzésében, és nem kell aggódnia a kapcsolódási karakterláncok vagy az adatelérési utak miatt.

- 1\. lehetőség: Ha strukturált adattal rendelkezik, hozzon létre egy TabularDataset, és használja közvetlenül a betanítási parancsfájlban.

- 2\. lehetőség: Ha strukturálatlan adatokkal rendelkezik, hozzon létre egy FileDataset, és csatlakoztassa vagy töltsön le fájlokat egy távoli számítási képzéshez.

Azure Machine Learning adatkészletek zökkenőmentes integrációt biztosítanak Azure Machine Learning képzési termékekkel, például a [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), a [kalkulátorsal](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) és a [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py).

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és betanításához a következők szükségesek:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).

* A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a azureml-adatkészletek csomagot.

> [!Note]
> Egyes adatkészlet-osztályok (előzetes verzió) függőségei vannak a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomagon. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>1\. lehetőség: adatkészletek használata közvetlenül a betanítási szkriptekben

Ebben a példában egy [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) hoz létre, és közvetlen bemenetként használja a `estimator`-objektum képzéséhez. 

### <a name="create-a-tabulardataset"></a>TabularDataset létrehozása

A következő kód létrehoz egy nem regisztrált TabularDataset egy webes URL-címről. Létrehozhat adatkészleteket helyi fájlokból vagy adattárolók elérési útjaiból is. További információ az [adatkészletek létrehozásáról](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>A bemeneti adatkészlet elérése a betanítási parancsfájlban

A TabularDataset-objektumok lehetővé teszik az adatgyűjtés egy Panda vagy Spark DataFrame való betöltését, hogy az ismerős adatelőkészítési és-betanítási könyvtárakkal is működjön. Ennek a képességnek a kihasználásához átadhat egy TabularDataset a betanítási konfiguráció bemenetként, majd lekérdezheti azt a parancsfájlban.

Ehhez a betanítási parancsfájl [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) objektumán keresztül nyissa meg a bemeneti adatkészletet, és használja a [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--) metódust. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic_ds']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>A kalkulátor konfigurálása

A kísérlet futtatásának elküldéséhez egy [kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objektumot kell használni. A Azure Machine Learning előre konfigurált becslések rendelkezik a gyakori gépi tanulási keretrendszerekhez, valamint egy általános becsléshez is.

Ez a kód egy általános kalkulátor-objektumot hoz létre, `est`, amely megadja a

* A parancsfájlok parancsfájl-könyvtára. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A *train_titanic.* a betanítási szkript.
* A betanításhoz használt bemeneti adatkészlet `titanic`.
* A kísérlet számítási célja.
* A kísérlet környezeti definíciója.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>2\. lehetőség: fájlok csatlakoztatása távoli számítási célhoz

Ha szeretné, hogy az adatfájlok elérhetők legyenek a számítási célra a betanításhoz, használja a [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) az általa hivatkozott fájlok csatlakoztatásához vagy letöltéséhez.

Fájlrendszer csatlakoztatásakor a fájlrendszert csatolja egy könyvtárhoz (csatlakoztatási ponthoz), és elérhetővé teszi azt a számítási célra. A csatlakoztatás azonnali, mert a rendszer csak a feldolgozáskor tölti be a fájlokat. A csatlakoztatás támogatott és Linux-alapú számításokhoz ajánlott, beleértve a Azure Machine Learning számítási, virtuális gépeket és HDInsight. A nem Linux-alapú számítási feladatokhoz csak a letöltés támogatott.  

>[!WARNING]
> Ha az adatok mérete meghaladja a számítási cél tárolási korlátját, a letöltés sikertelen lesz.

### <a name="create-a-filedataset"></a>FileDataset létrehozása

A következő példa egy nem regisztrált FileDataset hoz létre a webes URL-címekről. További információ az [adathalmazok](https://aka.ms/azureml/howto/createdatasets) más forrásokból való létrehozásáról.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>A kalkulátor konfigurálása

Ahelyett, hogy az adatkészletet a kalkulátor `inputs` paraméterén keresztül kellene átadnia, az adatkészletet átadhatja a `script_params`on keresztül, és az adatelérési utat (csatlakoztatási pont) is átadhatja az oktatóanyagban argumentumokkal Így férhet hozzá az adataihoz, és használhat meglévő betanítási szkriptet is.

A [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) -kalkulátor objektum a scikit-Learn kísérletek futtatásának elküldésére szolgál. További információ a [SKlearn-kalkulátor](how-to-train-scikit-learn.md)betanításáról.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>A betanítási parancsfájlban szereplő adatlekérdezés

Miután elküldte a futtatást, a `mnist` adatkészlet által hivatkozott adatfájlok a számítási célhoz lesznek csatlakoztatva. A következő kód bemutatja, hogyan kérheti le az adatait a parancsfájlban.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>Jegyzetfüzet-példák

Az [adatkészlet jegyzetfüzetei](https://aka.ms/dataset-tutorial) bemutatják és kibővítik az ebben a cikkben szereplő fogalmakat. 

## <a name="next-steps"></a>További lépések

* [Gépi tanulási modellek automatikus tanítása](how-to-auto-train-remote.md) a TabularDatasets.

* [Képosztályozási modellek betanítása](https://aka.ms/filedataset-samplenotebook) a FileDatasets.

