---
title: Betanítás azureml-adatkészletekkel
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatók az adatkészletek a képzésben
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/16/2019
ms.openlocfilehash: ceccc515b73bd41c7933889c61617c360c678eb7
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059293"
---
# <a name="train-with-datasets-preview-in-azure-machine-learning"></a>Az adatkészletek (előzetes verzió) betanítása Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan használhatja fel [Azure Machine learning adatkészleteket](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) a távoli kísérletek képzésében, és nem kell aggódnia a kapcsolódási karakterláncok vagy az adatelérési utak miatt.

- 1\. módszer: Az adatkészleteket közvetlenül a betanítási parancsfájlban továbbíthatja.

- 2\. lehetőség: Adatkészletek használatával csatlakoztathatja vagy letöltheti a fájlokat egy távoli számítási feladatokhoz.

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és betanításához a következők szükségesek:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine learning szolgáltatás](how-to-manage-workspace.md)munkaterülete.

* A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a azureml-adatkészletek csomagot.

> [!Note]
> Egyes adatkészlet-osztályok (előzetes verzió) függőségei vannak a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomagon. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak:  Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="option-1-pass-datasets-as-inputs-to-training-scripts"></a>1\. módszer: Adatkészletek továbbítása bemenetként a betanítási szkriptek számára

Azure Machine Learning adatkészletek zökkenőmentes integrációt biztosítanak Azure Machine Learning képzési termékekkel, például a [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), a [kalkulátorsal](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) és a [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py). Ebben a példában egy [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) hoz létre, és bemenetként `estimator` használja az objektum számára a betanításhoz. 

### <a name="create-a-tabulardataset"></a>TabularDataset létrehozása

A következő kód létrehoz egy nem regisztrált TabularDataset egy webes URL-címről. Létrehozhat adatkészleteket helyi fájlokból vagy adattárolók elérési útjaiból is. További információ az [adatkészletek létrehozásáról](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="configure-the-estimator"></a>A kalkulátor konfigurálása

A kísérlet futtatásának elküldéséhez egy [kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objektumot kell használni. A Azure Machine Learning előre konfigurált becslések rendelkezik a gyakori gépi tanulási keretrendszerekhez, valamint egy általános becsléshez is.

Ez a kód egy általános kalkulátor-objektumot `est`hoz létre, amely megadja a

* A parancsfájlok parancsfájl-könyvtára. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A *train_titanic.* a betanítási szkript.
* A betanításhoz `titanic`használt bemeneti adatkészlet.
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

### <a name="access-the-input-dataset-in-your-training-script"></a>A bemeneti adatkészlet elérése a betanítási parancsfájlban

A TabularDataset-objektumok lehetővé teszik az adatgyűjtés egy Panda vagy Spark DataFrame való betöltését, hogy az ismerős adatelőkészítési és-betanítási könyvtárakkal is működjön. Ennek a képességnek a kihasználásához átadhat egy TabularDataset a betanítási konfiguráció bemenetként, majd lekérdezheti azt a parancsfájlban.

Ehhez a betanítási parancsfájlban található [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) objektumon keresztül nyissa meg a bemeneti adatkészletet, és használja a [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--) metódust. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>2\. lehetőség:  Fájlok csatlakoztatása távoli számítási célhoz

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

Ahelyett, hogy az adatkészletet `inputs` a kalkulátorban lévő paraméterrel kellene átadnia, az `script_params` adatkészletet átadhatja a (z) és az adatelérési utat (csatlakoztatási pont) a betanítási szkriptben argumentumokkal. Így elkerülhető a betanítási szkript Azure Machine Learning SDK-függősége.

A [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) -kalkulátor objektum a scikit-Learn kísérletek futtatásának elküldésére szolgál.

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

Miután elküldte a futtatást, az `mnist` adatkészlet által hivatkozott adatfájlok a számítási célhoz lesznek csatlakoztatva. A következő kód bemutatja, hogyan kérheti le az adatait a parancsfájlban.

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

A [minta notebookok](https://aka.ms/dataset-tutorial) bemutatják és kibővítik a jelen cikkben szereplő fogalmakat, például a ScriptRun és [HyperdDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) objektumokat tartalmazó adatkészletek használatával.

## <a name="next-steps"></a>További lépések

* [Gépi tanulási modellek automatikus tanítása](how-to-auto-train-remote.md) a TabularDatasets.

* [Képosztályozási modellek betanítása](https://aka.ms/filedataset-samplenotebook) a FileDatasets.
